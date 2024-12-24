# Hardware Hacking 101

Difficulty: :material-star::material-star-outline::material-star-outline::material-star-outline::material-star-outline:

## Objective

!!! question "Task description"

    Ready your tools and sharpen your wits—only the cleverest can untangle the wires and unlock Santa’s hidden secrets!

    !!! question "Part 1"

        Jingle all the wires and connect to Santa's Little Helper to reveal the merry secrets locked in his chest!

    !!! question "Part 2"

        Santa’s gone missing, and the only way to track him is by accessing the Wish List in his chest—modify the access_cards database to gain entry!

??? quote "Jewel Loggins"

    Hello there! I’m Jewel Loggins.

    I hate to trouble you, but I really need some help. Santa’s Little Helper tool isn’t working, and normally, Santa takes care of this… but with him missing, it’s all on me.

    I need to connect to the {==UART interface==} to get things running, but it’s like the device just refuses to respond every time I try.

    I've got all the right tools, but I must be overlooking something important. I've seen a few elves with similar setups, but everyone’s so busy preparing for Santa’s absence.

    If you could guide me through the connection process, I’d be beyond grateful. It’s critical because this interface controls access to our North Pole access cards!

    We used to have a {==note with the serial settings==}, but apparently, one of Wombley’s elves shredded it! You might want to {==check with Morcel Nougat==}—he might have a way to recover it.

## Part 1

### Hints

??? tip "On the Cutting Edge"

    Hey, I just caught wind of this neat way to {==piece back shredded paper==}! It's a fancy heuristic detection technique—sharp as an elf’s wit, I tell ya! Got a sample Python script right here, courtesy of Arnydo. Check it out when you have a sec: [heuristic_edge_detection.py](https://gist.github.com/arnydo/5dc85343eca9b8eb98a0f157b9d4d719)."

??? tip "Shredded to Pieces"

    Have you ever wondered how elves manage to dispose of their sensitive documents? Turns out, they use this fancy shredder that is quite the marvel of engineering. It slices, it dices, it makes the paper practically disintegrate into a thousand tiny pieces. Perhaps, just perhaps, we could {==reassemble the pieces==}?

### Solution

=== "Silver"

    The challenge presents us the following:

    ![Code request](../images/hardware_hacking_101/hardware_hacking_101_1.png)
    /// caption
    Challenge overview with a manual in the top right. We see two pieces of hardware, an UART-bridge, and some cables. We can drag and drop some of these items.
    ///

    We can drag and drop the wires and switch some settings on the hardware. Following the manual and using some of our common hardware knowledge, we take the following steps:

    - [x] Connect the wires to the hardware and UART-bridge as follows: ```G <-> GND, R <-> TX, T <-> RX, V <-> VCC```
    - [x] Connect the USB-C cable to the UART-bridge.
    - [x] Flip the voltage switch on the UART-bridge to '3V'.
    - [x] Power on 'Santa's Little Helper' (or 'SLH') with the green button.

    The SLH will turn on but when pressing the 'S' button to connect, we get an error. We will need the correct settings.

    The conversation with Jewel reveals that we must first work on the challenge 'Frosty Keypad' related to Morcel Nougat to get some shredded note. Once we have the item, it says the following:

    !!! note "One Thousand Little Teeny Tiny Shredded Pieces of Paper"

        A mountain of one thousand little tiny [shredded pieces of paper](https://holidayhackchallenge.com/2024/shreds.zip)—each scrap whispering a secret, waiting for the right hardware hacker to piece the puzzle back together!

    The .zip archive contains a set of .png images that represent slices of the shredded paper. One of the hints gives us a Python script that should be able to piece back shredded paper. When running this script in the 'shreds' folder, we get the following image:

    ![Code request](../images/hardware_hacking_101/hardware_hacking_101_2.png){width="500"}
    /// caption
    Piece of paper containing the configuration needed for the SLH.
    ///

    We flip the image over the X-axis with [CyberChef](https://gchq.github.io/CyberChef/#recipe=Render_Image\('Raw'\)Flip_Image\('Horizontal'\)) and get the following settings (assuming port to be the USB):

    !!! success "Answer"

        | Name          | Setting   |
        | -             | -         |
        | Port          | USB0      |
        | Baud Rate     | 115200    |
        | Parity        | even      |
        | Data          | 7 bits    |
        | Stop bits     | 1 bit     |
        | Flow control  | RTS       |

    !!! quote "Jewel Loggins"

        Fantastic! You managed to connect to the UART interface—great work with those tricky wires! I couldn't figure it out myself…

        Rumor has it you might be able to {==bypass the hardware altogether for the gold medal==}. Why not see if you can find that shortcut?

=== "Gold"

    Jewel tells us that we might be able to bypass the hardware completely. This suggests we can do something with the JavaScript code as we have seen in previous challenges as well. In find the following:

    ``` js title="Hint contained in 'main.js'" linenums="1" hl_lines="4 5 6"
    async function checkit(serial, uV) {
        //..
        // Build the URL with the request ID as a query parameter
        // Word on the wire is that some resourceful elves managed to brute-force their way in through the v1 API.
        // We have since updated the API to v2 and v1 "should" be removed by now.
        // const url = new URL(`${window.location.protocol}//${window.location.hostname}:${window.location.port}/api/v1/complete`);
        const url = new URL(`${window.location.protocol}//${window.location.hostname}:${window.location.port}/api/v2/complete`);
        //..
    }
    ```

    !!! success "Answer"
        We use Burp to capture our final request for the silver achievement. We then simply change the endpoint to the v1 API using the repeater and receive the gold achievement. We don't need to brute-force it this way.

        Example request:
        ```
        POST /api/v1/complete HTTP/2
        Host: hhc24-hardwarehacking.holidayhackchallenge.com
        Cookie: _ga=<FILL_IN>; _ga_F6ZZNPR5E5=<FILL_IN>; GCLB="<FILL_IN>"
        Content-Type: application/json
        Accept: */*
        Accept-Encoding: gzip, deflate, br

        {"requestID":"<YOUR_REQUEST_ID>","serial":[3,9,2,2,0,3],"voltage":3}
        ```

### Response

!!! quote "Jewel Loggins"

    Next, we need to access the terminal and modify the access database. We're looking to grant access to card number 42.

    Start by using the slh application—that’s the key to getting into the access database. Problem is, the ‘slh’ tool is password-protected, so we need to find it first.

    Search the terminal thoroughly; passwords sometimes get left out in the open.

## Part 2

### Hints

??? tip "It's In the Signature"

    I seem to remember there being a handy HMAC generator included in [CyberChef](https://gchq.github.io/CyberChef/#recipe=HMAC(%7B'option':'UTF8','string':''%7D,'SHA256')).

??? tip "Hidden in Plain Sight"

    It is so important to keep sensitive data like passwords secure. Often times, when typing passwords into a CLI (Command Line Interface) they get {==added to log files and other easy to access locations==}. It makes it trivial to step back in {==history==} and identify the password.

### Solution

### Response

## Response

!!! quote "Jewel Loggins"

    Fantastic! You managed to connect to the UART interface—great work with those tricky wires! I couldn't figure it out myself…

    Rumor has it you might be able to bypass the hardware altogether for the gold medal. Why not see if you can find that shortcut?

    Next, we need to access the terminal and modify the access database. We're looking to grant access to card number 42.

    Start by using the slh application—that’s the key to getting into the access database. Problem is, the ‘slh’ tool is password-protected, so we need to find it first.

    Search the terminal thoroughly; passwords sometimes get left out in the open.

    Once you've found it, modify the entry for card number 42 to grant access. Sounds simple, right? Let’s get to it!

    Wow! You're amazing at this! Clever move finding the password in the command history. It’s a good reminder about keeping sensitive information secure…

    There’s a tougher route if you're up for the challenge to earn the Gold medal. It involves directly modifying the database and generating your own HMAC signature.

    I know you can do it—come back once you've cracked it!

    Brilliant work! We now have access to… the Wish List! I couldn't have done it without you—thank you so much!
