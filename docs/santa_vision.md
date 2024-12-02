# Santa Vision

Difficulty: :material-star::material-star::material-star::material-star::material-star-outline:

## Objective

!!! question "Task description"

    Alabaster and Wombley have poisoned the Santa Vision feeds! Knock them out to restore everyone back to their regularly scheduled programming.

    - Santa Vision A (:material-star::material-star::material-star-outline::material-star-outline::material-star-outline:): What username logs you into the SantaVision portal?

    - Santa Vision B (:material-star::material-star::material-star::material-star-outline::material-star-outline:): Once logged on, authenticate further without using Wombley's or Alabaster's accounts to see the ```northpolefeeds``` on the monitors. What username worked here?

    - Santa Vision C (:material-star::material-star::material-star::material-star::material-star-outline:): Using the information available to you in the SantaVision platform, subscribe to the ```frostbitfeed``` MQTT topic. Are there any other feeds available? What is the code name for the elves' secret operation?

    - Santa Vision D (:material-star::material-star::material-star::material-star::material-star-outline:): There are too many admins. Demote Wombley and Alabaster with a single MQTT message to correct the ```northpolefeeds``` feed. What type of contraption do you see Santa on?

??? quote "Ribb Bonbowford"

    Hi, Ribb Bonbowford here, ready to guide you through the SantaVision dilemma!

    The Santa Broadcast Network (SBN) has been hijacked by Wombley's goons—they're using it to spread propaganda and recruit elves! And Alabaster joined in out of necessity. Quite the predicament, isn’t it?

    To access this challenge, use this terminal to access your own instance of the SantaVision infrastructure.

    Once it's done baking, you'll see an {==IP address that you'll need to scan for listening services==}.

    Our target is the technology behind the SBN. We need make a {==key change to its configuration==}.

    We’ve got to {==remove their ability to use their admin privileges==}. This is a delicate maneuver—are you ready?

    We need to {==change the application so that multiple administrators are not permitted==}. A misstep could cause major issues, so {==precision is key==}.

    Once that’s done, positive, cooperative images will return to the broadcast. The holiday spirit must prevail!

    This means connecting to the network and pinpointing the right accounts. Don’t worry, we'll get through this.

    Let’s ensure the broadcast promotes unity among the elves. They deserve to see the season’s spirit, don't you think?

    Remember, it’s about cooperation and togetherness. Let's restore that and bring back the holiday cheer. Best of luck!

    The first step to unraveling this mess is gaining access to the SantaVision portal. You'll {==need the right credentials to slip through the front door==}—what username will get you in?

## Hints

??? tip "Misplaced Credentials (Santa Vision A)"

    See if any credentials you find allow you to subscribe to any MQTT feeds.

??? tip "Filesystem Analysis (Santa Vision A)"

    [jefferson](https://github.com/onekey-sec/jefferson/) is great for analyzing JFFS2 file systems.

??? tip "Database Pilfering (Santa Vision A)"

    Consider checking any database files for credentials...

??? tip "Mosquito Mosquitto"

    [Mosquitto](https://mosquitto.org/) is a great client for interacting with MQTT, but their spelling may be suspect. Prefer a GUI? Try [MQTTX](https://mqttx.app/)

## Solution

!!! success "Answer"

    ...

## Response

??? quote "Ribb Bonbowford"

    ...
