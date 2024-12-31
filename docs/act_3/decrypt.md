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

    Well, I’ll be a reindeer’s uncle! You've done it, Gumshoe! You cracked that frosty code and saved the Naughty-Nice List just in the nick of time. The elves’ll be singin’ your praises from here to the South Pole!

    I knew you had it in ya. Now, let’s get these toys delivered and make this a holiday to remember. You're a true North Pole hero!

??? quote "Wombley Cube"

    Blast it all! My plan was so close to succeeding. Our strike against their communications tower was successful, but it seems we were too late.

    Santa received their distress signal, and now here he is, as wrathful as I've ever seen him.

    I never intended to stop or ruin the holidays. I perceived Santa as taking them in the wrong direction, and I only wanted to ensure their integrity.

    Like any self-respecting commander, I accept defeat gracefully and with dignity. I await my punishment, whatever it may be. But for now, I will assist with the recovery efforts.

    My laptop was the only place I stored the source code and SSH keys to the Frostbit ransomware server, which holds the decryption key for the Naughty-Nice List.

    But with my laptop destroyed in the snowball fight, our best hope is that the North Pole SOC captured enough forensics on the ransomware for us to reverse-engineer it, recover the Naughty-Nice List, and then find a way to shut down the server before it publishes the list.

    Please assist the others with this effort.

    Great work, but successfully investigating our attack chain is only the first step.

    Oh right, I had forgotten about that broadcast. Thank you for shutting it down. There's no need for it now that the conflict is no more.

    What was perhaps my greatest technical achievement was also my greatest misstep. FrostBit is no more, and with that, the Naughty-Nice List is restored. Impressive work.

## Hints

??? tip "Frostbit Hashing"

    The Frostbit infrastructure might be using a reverse proxy, which may resolve certain URL encoding patterns before forwarding requests to the backend application. A reverse proxy may reject requests it considers invalid. You may need to employ creative methods to ensure the request is properly forwarded to the backend. There could be a way to exploit the cryptographic library by crafting a specific request using relative paths, encoding to pass bytes and using known values retrieved from other forensic artifacts. If successful, this could be the key to tricking the Frostbit infrastructure into revealing a secret necessary to decrypt files encrypted by Frostbit.

??? tip "Frostbit Dev Mode"

    There's a new ransomware spreading at the North Pole called Frostbit. Its infrastructure looks like code I worked on, but someone modified it to work with the ransomware. If it is our code and they didn't disable dev mode, we might be able to pass extra options to reveal more information. If they are reusing our code or hardware, it might also be broadcasting MQTT messages.

??? tip "Frostbit Crypto"

    The Frostbit ransomware appears to use multiple encryption methods. Even after removing TLS, some values passed by the ransomware seem to be asymmetrically encrypted, possibly with PKI. The infrastructure may also be using custom cryptography to retrieve ransomware status. If the creator reused our cryptography, the infrastructure might depend on an outdated version of one of our libraries with known vulnerabilities. There may be a way to have the infrastructure reveal the cryptographic library in use.

??? tip "Frostbit Forensics"

    I'm with the North Pole cyber security team. We built a powerful EDR that captures process memory, network traffic, and malware samples. It's great for incident response - using tools like strings to find secrets in memory, decrypt network traffic, and run strace to see what malware does or executes.


## Solution

## Response
