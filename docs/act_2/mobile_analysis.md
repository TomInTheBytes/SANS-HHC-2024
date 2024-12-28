# Mobile Analysis

Difficulty: :material-star::material-star::material-star-outline::material-star-outline::material-star-outline:

## Objective

!!! question "Task description"

    Help find who has been left out of the naughty AND nice list this Christmas. Please speak with Eve Snowshoes for more information.

??? quote "Eve Snowshoes"

    Hi there, tech saviour! Eve Snowshoes and Team Alabaster in need of assistance.

    I've been busy creating and testing a modern solution to Santa’s Naughty-Nice List, and I even built an Android app to streamline things for Alabaster’s team.

    But here’s my tiny reindeer-sized problem: I made a debug version and a release version of the app.

    I accidentally left out a child's name on each version, but for the life of me, I can't remember who!

    Could you start with the debug version first, figure out which child’s name isn’t shown in the list within the app, then we can move on to release? I’d be eternally grateful!

## Hints

??? tip "Mobile Analysis Easy - Missing"

    Maybe look for what names are included and {==work back==} from that?

??? tip "Mobile Analysis Easy - Tools"

    Try using [apktool](https://github.com/iBotPeaches/Apktool/releases) or [jadx](https://github.com/skylot/jadx)

??? tip "Mobile Analysis Hard - Encryption and Obfuscation"

    Obfuscated and encrypted? Hmph. Shame you can't just run [strings](https://developer.android.com/guide/topics/resources/string-resource) on the file.

??? tip "Mobile Analysis Hard - Format"

    So yeah, have you heard about this new [Android app](https://developer.android.com/guide/app-bundle/app-bundle-format) format? Want to [convert it to an APK](https://github.com/HackJJ/apk-sherlock/blob/main/aab2apk.md) file?

## Solution

=== "Silver"

    This challenge gives us two files to unpack; one release version (.aab file) and one debug version (.apk). The debug version should contain the most information as it hasn't been optimized for size yet and should be easier to work with. I opted to go for APK tool to unpack the .apk file as my first time working with Android apps. I used [Waydroid](https://waydro.id/) to emulate the app on my desktop (1).
    { .annotate }

    1. ```waydroid app install SantaSwipe.apk```


    ??? example "AI usage"

        I prompted ChatGPT to explain typical folders you can expect when using APK tool on an .apk file:

        ```
        When you use **APKTool** to decompile an APK file, it extracts and organizes the APK's resources and code into several typical folders. Here are the most common folders and their purposes:

        1. **`smali/`**:
        - Contains the disassembled **Dalvik bytecode** files in Smali format (`.smali` files).
        - Each Smali file corresponds to a Java class and is written in a human-readable assembly-like syntax.

        2. **`res/`**:
        - Holds the **compiled resources** of the application (e.g., XML files for layouts, drawables, animations, etc.).
        - Subfolders include:
            - `drawable/` (images)
            - `layout/` (UI definitions)
            - `values/` (strings, dimensions, colors)
            - Others like `anim/`, `menu/`, `xml/`, depending on the APK.

        3. **`AndroidManifest.xml`**:
        - The **manifest file**, decompiled into readable XML.
        - Contains metadata about the app, including permissions, services, activities, and intent filters.

        4. **`assets/`**:
        - Stores **raw assets** that are bundled with the app.
        - These files are not compiled, so they remain in their original formats, like `.txt`, `.json`, or media files.

        5. **`lib/`**:
        - Contains native libraries in `.so` format for different CPU architectures (e.g., `armeabi-v7a`, `arm64-v8a`, `x86`).

        6. **`original/`**:
        - Stores original files from the APK that are not decompiled or modified.
        - May include a copy of the `META-INF/` folder (used for app signature information).

        7. **`META-INF/`** (optional):
        - May be extracted separately if `--keep-resources` or related options are used.
        - Contains APK signature files (`MANIFEST.MF`, `CERT.RSA`, `CERT.SF`).

        8. **`unknown/`** (optional):
        - Holds any files APKTool could not recognize or process (e.g., unusual file types or encrypted data).

        9. **`build/`**:
        - Temporary files created during the rebuilding process when recompiling the APK.
        - Generally absent after decompilation but appears during recompilation.

        These folders give you access to the application's structure for analysis, modification, or reverse engineering. You can modify the resources or Smali code, then recompile the APK using APKTool.
        ```

    The application shows us a bunch of names with apparently one missing. Exploring the extracted files, we find references to a file ```naughtynicelist.db``` which likely holds all the names. We can extract them using these commands in our Waydroid emulation session:

    ```
    sudo wayland shell

    :/data/data/com.northpole.santaswipe/databases # ls
    naughtynicelist.db  naughtynicelist.db-journal

    sqlite3 naughtynicelist.db
    sqlite> .tables
    NaughtyList       NiceList          NormalList        android_metadata
    sqlite> select * from NormalList;
    ```

    The 'NiceList' and 'NormalList' are empty which suggests that these are saturated by the application. The objective wants us to look for a missing name in the app, not in the database. We can therefore cross-check the names listed in the application and the database to find the missing name:

    !!! success "Answer"

        ```Ellie, Alabama, USA```

    !!! success "Alternative answer"

        The extracted files contain code to load the names except Ellie:

        ```
        grep -rn ./ -e 'Ellie'
        ./smali_classes3/com/northpole/santaswipe/MainActivity$WebAppInterface.smali:1090:    const-string v3, "SELECT Item FROM NormalList WHERE Item NOT LIKE \'%Ellie%\'"
        ```

=== "Gold"

    We are now told to work with the release version. We need to follow [additional steps](https://github.com/HackJJ/apk-sherlock/blob/main/aab2apk.md) to unpack this type of file as shared in the hints. There will be another name missing in this version.

    ??? example "AI usage"

        ChatGPT already gave me the method before I read the hints.

    The hints suggest to check the ```strings.xml``` file where we find the following strings related to encryption:

    ```
    <string name="ek">rmDJ1wJ7ZtKy3lkLs6X9bZ2Jvpt6jL6YWiDsXtgjkXw=</string>
    <string name="iv">Q2hlY2tNYXRlcml4</string>
    ```

    !!! example "AI usage: file analysis"

        We need to learn more about the encryption used in the application. I used ChatGPT to analyze the file "DatabaseHelper.smali" for interesting artifacts and it highlighted the following related to AES-GCM:

        ```const-string v0, "AES/GCM/NoPadding"```

    !!! example "AI usage: decryption script"

        We want to mimick the decryption done in the application. I supplied ChatGPT the full decryption function and tasked it to write a Python script that we can use to decrypt data with the strings we found. It gave us this:

        ``` py
        from base64 import b64decode
        from Crypto.Cipher import AES
        from Crypto.Util.Padding import unpad

        def decrypt_data(encrypted_data: str) -> str:
            try:
                # Base64-decoded key and IV
                encoded_key = "rmDJ1wJ7ZtKy3lkLs6X9bZ2Jvpt6jL6YWiDsXtgjkXw="
                encoded_iv = "Q2hlY2tNYXRlcml4"

                decoded_key = b64decode(encoded_key)
                decoded_iv = b64decode(encoded_iv)

                # Decode the encrypted data from Base64
                decoded_encrypted_data = b64decode(encrypted_data)

                # Initialize the AES cipher in GCM mode
                cipher = AES.new(decoded_key, AES.MODE_GCM, nonce=decoded_iv)

                # Decrypt the data (automatically verifies the tag)
                decrypted_bytes = cipher.decrypt_and_verify(
                    decoded_encrypted_data[:-16],  # Ciphertext (everything except last 16 bytes)
                    decoded_encrypted_data[-16:]  # Authentication tag (last 16 bytes)
                )

                # Convert decrypted bytes to UTF-8 string
                return decrypted_bytes.decode('utf-8')

            except Exception as e:
                print(f"Decryption failed: {e}")
                return None

        # Example Base64-encoded encrypted data (ciphertext + tag)
        encrypted_data = "L2HD1a45w7EtSN41J7kx/hRgPwR8lDBg9qUicgz1qhRgSg=="

        decrypted_text = decrypt_data(encrypted_data)

        if decrypted_text:
            print("Decrypted data:", decrypted_text)
        else:
            print("Decryption failed.")
        ```

    At the bottom of the file we see this line with encrypted data that is then decrypted:

    ```
    const-string v0, "IVrt+9Zct4oUePZeQqFwyhBix8cSCIxtsa+lJZkMNpNFBgoHeJlwp73l2oyEh1Y6AfqnfH7gcU9Yfov6u70cUA2/OwcxVt7Ubdn0UD2kImNsclEQ9M8PpnevBX3mXlW2QnH8+Q+SC7JaMUc9CIvxB2HYQG2JujQf6skpVaPAKGxfLqDj+2UyTAVLoeUlQjc18swZVtTQO7Zwe6sTCYlrw7GpFXCAuI6Ex29gfeVIeB7pK7M4kZGy3OIaFxfTdevCoTMwkoPvJuRupA6ybp36vmLLMXaAWsrDHRUbKfE6UKvGoC9d5vqmKeIO9elASuagxjBJ"
    invoke-direct {p0, v0}, Lcom/northpole/santaswipe/DatabaseHelper;->decryptData(Ljava/lang/String;)Ljava/lang/String;
    ```

    The decryption script outputs the following value that is once more encrypted:

    ```
    Decrypted data: CREATE TRIGGER DeleteIfInsertedSpecificValue
    AFTER INSERT ON NormalList
    FOR EACH ROW
    BEGIN
        DELETE FROM NormalList WHERE Item = 'KGfb0vd4u/4EWMN0bp035hRjjpMiL4NQurjgHIQHNaRaDnIYbKQ9JusGaa1aAkGEVV8=';
    END;
    ```

    !!! success "Answer"

        The decrypted item gives: ```Joshua, Birmingham, United Kingdom```

## Response

??? quote "Eve Snowshoes"

    Aha! Success! You found it!

    Thanks for staying on your toes and helping me out—every step forward keeps Alabaster’s plans on track. You're a real lifesaver!

    Nice job completing the debug version—smooth as a sleigh ride on fresh snow!

    But now, the real challenge lies in the obfuscated release version. Ready to dig deeper and show Alabaster’s faction your skills?
