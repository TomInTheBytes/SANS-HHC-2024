# Microsoft KC7

Difficulty: :material-star::material-star::material-star::material-star-outline::material-star-outline:

## Objective

!!! question "Task description"

    [Answer](http://kc7cyber.com/go/hhc24) two sections for silver, all four sections for gold.

    !!! question "KQL 101"

        Learn and practice basic KQL queries to analyze data logs for North Pole operations.

    !!! question "Operation Surrender"

        Investigate a phishing attack targeting Wombley’s team, uncovering espionage activities.

    !!! question "Operation Snowfall"

        Track and analyze the impacts of a ransomware attack initiated by Wombley’s faction.

    !!! question "Echoes in the Frost"

        Use logs to trace an unknown phishing attack targeting Alabaster’s faction.

??? quote "Pepper Minstix"

    This is weird, I got some intel about an imminent attack.

    Pepper Minstix here! I’ve got urgent news from neutral ground.

    The North Pole is facing a serious cyber threat, and it’s putting all the factions on edge. The culprits? Some troublemakers from Team Wombley.

    They’ve launched a barrage of phishing attacks, ransomware, and even some sneaky espionage, causing quite the stir.

    It’s time to jump into action and get cracking on this investigation—there’s plenty of cyber-sleuthing to do.

    You’ll be digging into KQL logs, tracking down phishing schemes, and tracing compromised accounts like a seasoned pro.

    Malware infections have already breached Alabaster Snowball’s systems, so we need swift action.

    Your top mission: neutralize these threats, with a focus on the ransomware wreaking havoc from Team Wombley.

    It’s a hefty challenge, but I know you’re up to it. We need your expertise to restore order and keep the peace.

    You’ve got the tools, the skills, and the know-how—let’s show Team Wombley we mean business.

    Ready to dive in? Let's defend the North Pole and bring back the holiday harmony!

??? quote "Wunorse Openslae"

    Hey, Wunorse here. We at Team Wombley pulled off some nasty stuff.

    Phishing attacks, ransomware, and cyber espionage, oh yeah!

    We pulled loads of all-nighters to make it all happen. Energy drinks rock!

    Our teams did what Alabaster said we never could and breached Santa's network. We're so rad.

    It would take a master defender to fix all the damage we caused. But defense is so lame! Offense is where it's at.

    You should just leave them to panic and join our side. We're the coolest, don't you want to be like us?

## Hints

There are no hints available.

## Solution

=== "Section 1: KQL 101"

    ??? success "Solution for Question 1"

        Answer: ```let's do this```

    ??? success "Solution for Question 2"

        Answer: ```when in doubt take 10```

    ??? success "Solution for Question 3"

        ``` kql
        Employees
        | count
        ```
        Answer: ```90```

    ??? success "Solution for Question 4"

        ``` kql
        Employees
        | where role == "Chief Toy Maker"
        | project name
        ```
        Answer: ```Shinny Upatree```

    ??? success "Solution for Question 5"

        Answer: ```Operator```

    ??? success "Solution for Question 6"

        ``` kql
        let email = Employees
        | where name == "Angel Candysalt"
        | project email_addr;
        Email
        | where recipient in (email)
        | count
        ```

        Answer: ```31```

    ??? success "Solution for Question 7"

        ``` kql
        Email
        | where sender == "twinkle_frostington@santaworkshopgeeseislands.org"
        | distinct recipient
        | count
        ```

        Answer: ```32```

    ??? success "Solution for Question 8"

        ``` kql
        let ip = Employees
        | where name == "Twinkle Frostington"
        | project ip_addr;
        OutboundNetworkEvents
        | where src_ip in (ip)
        | distinct url
        | count
        ```

        Answer: ```4```

    ??? success "Solution for Question 9"

        ``` kql
        PassiveDns
        | where domain contains "green"
        | distinct domain
        | count
        ```

        Answer: ```10```

    ??? success "Solution for Question 10"

        ``` kql
        let twinkle_ips =
        Employees
        | where name has "Twinkle"
        | distinct ip_addr;
        OutboundNetworkEvents
        | where src_ip in (twinkle_ips)
        | distinct url
        | count
        ```

        Answer: ```8```

=== "Section 2: Operation Surrender: Alabaster's Espionage"

    ??? success "Solution for Question 1"

        Answer: ```surrender``

    ??? success "Solution for Question 2"

        ``` kql
        Email
        | where subject contains "surrender"
        | distinct sender
        ```

        Answer: ```surrender@northpolemail.com```

    ??? success "Solution for Question 3"

        ``` kql
        Email
        | where subject contains "surrender"
        | distinct recipient
        | count
        ```

        Answer: ```22```

    ??? success "Solution for Question 4"

        ``` kql
        Email
        | where subject contains "surrender"
        | project link
        | distinct link
        ```

        Answer: ```Team_Wombley_Surrender.doc```

    ??? success "Solution for Question 5"

        ``` kql
        Employees
        | join kind=inner (
            OutboundNetworkEvents
        ) on $left.ip_addr == $right.src_ip
        | where url contains "Team_Wombley_Surrender.doc"
        | project name, ip_addr, url, timestamp
        | sort by timestamp asc
        | take 1
        ```

        Answer: ```Joyelle Tinseltoe```

    ??? success "Solution for Question 6"

        ``` kql
        let user_host = Employees
        | where name == "Joyelle Tinseltoe"
        | project hostname;
        ProcessEvents
        | where timestamp between(datetime("2024-11-27T14:11:45Z") .. datetime("2024-11-27T14:15:45Z"))
        | where hostname in (user_host)
        | distinct process_name
        ```

        Answer: ```keylogger.exe```

    ??? success "Solution for Question 7"

        ``` kql
        let flag = "keylogger.exe";
        let base64_encoded = base64_encode_tostring(flag);
        print base64_encoded
        ```

        Answer: ```a2V5bG9nZ2VyLmV4ZQ==```

=== "Section 3: Operation Snowfall: Team Wombley's Ransomware Raid"

    ??? success "Solution for Question 1"

        Answer: ```snowfall```

    ??? success "Solution for Question 2"

        ``` kql
        AuthenticationEvents
        | where result == "Failed Login"
        | summarize FailedAttempts = count() by username, src_ip, result
        | where FailedAttempts >= 5
        | sort by FailedAttempts desc
        ```

        Answer: ```59.171.58.12```

    ??? success "Solution for Question 3"

        ``` kql
        AuthenticationEvents
        | where src_ip == "59.171.58.12"
        | where result contains "Success"
        | distinct username
        | count
        ```

        Answer: ```23```

    ??? success "Solution for Question 4"

        ``` kql
        AuthenticationEvents
        | where src_ip == "59.171.58.12"
        | where result contains "Success"
        | project description
        ```

        Answer: ```RDP```

    ??? success "Solution for Question 5"

        ``` kql
        let user_host = Employees
        | where name contains "Alabaster"
        | project hostname;
        let start_time = toscalar(AuthenticationEvents
        | where src_ip == "59.171.58.12"
        | where result contains "Success"
        | where hostname in (user_host)
        | project timestamp);
        ProcessEvents
        | where hostname in (user_host)
        | where timestamp > start_time
        ```

        Answer: ```Secret_Files.zip```

    ??? success "Solution for Question 6"

        ``` kql
        let user_host = Employees
        | where name contains "Alabaster"
        | project hostname;
        let start_time = toscalar(AuthenticationEvents
        | where src_ip == "59.171.58.12"
        | where result contains "Success"
        | where hostname in (user_host)
        | project timestamp);
        ProcessEvents
        | where hostname in (user_host)
        | where timestamp > start_time
        ```

        Answer: ```EncryptEverything.exe```

    ??? success "Solution for Question 7"

        ``` kql
        let flag = "EncryptEverything.exe";
        let base64_encoded = base64_encode_tostring(flag);
        print base64_encoded
        ```

        Answer: ```RW5jcnlwdEV2ZXJ5dGhpbmcuZXhl```

=== "Section 4: Echoes in the Frost: Tracking the Unknown Threat"

    ??? success "Solution for Question 1"

        Answer: ```stay frosty```

    ??? success "Solution for Question 2"

        ``` kql
        Email
        | where subject contains "credential"
        | sort by timestamp asc
        | take 1
        ```

        Answer: ```2024-12-12T14:48:55Z```

    ??? success "Solution for Question 3"

        ``` kql
        let ip = Employees
        | where name == "Noel Boetie"
        | project ip_addr;
        OutboundNetworkEvents
        | where src_ip in (ip)
        | where url contains "holidaybargainhunt"
        | sort by timestamp asc
        | take 1
        ```

        Answer: ```2024-12-12T15:13:55Z```

    ??? success "Solution for Question 4"

        ``` kql
        PassiveDns
        | where domain == "holidaybargainhunt.io"
        | distinct ip
        ```

        Answer: ```182.56.23.122```

    ??? success "Solution for Question 5"

        ``` kql
        AuthenticationEvents
        | where src_ip == "182.56.23.122"
        ```

        Answer: ```WebApp-ElvesWorkshop```

    ??? success "Solution for Question 6"

        ``` kql
        let auth_time = toscalar(AuthenticationEvents
        | where src_ip == "182.56.23.122"
        | project timestamp);
        ProcessEvents
        | where hostname == "WebApp-ElvesWorkshop"
        | where timestamp > auth_time
        ```

        Answer: ```Invoke-Mimikatz.ps1```

    ??? success "Solution for Question 7"

        ``` kql
        let ip = Employees
        | where name == "Noel Boetie"
        | project ip_addr;
        let download_time = toscalar(OutboundNetworkEvents
        | where src_ip in (ip)
        | where url contains "holidaybargainhunt"
        | sort by timestamp asc
        | take 1
        | project timestamp);
        ProcessEvents
        | where hostname == "Elf-Lap-A-Boetie"
        | where timestamp > download_time
        | sort by timestamp asc
        ```

        Answer: ```2024-12-12T15:14:38Z```

    ??? success "Solution for Question 8"

        ``` kql
        let user_ip = Employees
        | where name == "Noel Boetie"
        | project ip_addr;
        let download_time = toscalar(FileCreationEvents
        | where hostname == "Elf-Lap-A-Boetie"
        | where filename == "holidaycandy.hta"
        | sort by timestamp asc
        | project timestamp
        | take 1);
        OutboundNetworkEvents
        | where timestamp > download_time
        | where src_ip in (user_ip)
        | sort by timestamp asc
        ```

        Answer: ```compromisedchristmastoys.com```

    ??? success "Solution for Question 9"

        ``` kql
        let ip = Employees
        | where name == "Noel Boetie"
        | project ip_addr;
        let download_time = toscalar(OutboundNetworkEvents
        | where src_ip in (ip)
        | where url contains "holidaybargainhunt"
        | sort by timestamp asc
        | take 1
        | project timestamp);
        ProcessEvents
        | where hostname == "Elf-Lap-A-Boetie"
        | where timestamp > download_time
        | sort by timestamp asc
        ```

        Answer: ```sqlwriter.exe```

    ??? success "Solution for Question 10"

        ``` kql
        let ip = Employees
        | where name == "Noel Boetie"
        | project ip_addr;
        let download_time = toscalar(OutboundNetworkEvents
        | where src_ip in (ip)
        | where url contains "holidaybargainhunt"
        | sort by timestamp asc
        | take 1
        | project timestamp);
        ProcessEvents
        | where hostname == "Elf-Lap-A-Boetie"
        | where timestamp > download_time
        | sort by timestamp asc
        ```

        Answer: ```frosty```

    ??? success "Solution for Question 11"

        ``` kql
        let finalflag = "frosty";
        let base64_encoded = base64_encode_tostring(finalflag);
        print base64_encoded
        ```

        Answer: ```ZnJvc3R5```

## Response

??? quote "Pepper Minstix"

    Outstanding work! You've expertly sifted through the chaos of the KQL logs and uncovered crucial evidence. We're one step closer to saving the North Pole!

    Bravo! You've traced those phishing emails back to their devious source. Your sharp detective skills are keeping our elves safe from harm!

    Fantastic! You've tracked down the compromised accounts and put a stop to the malicious activity. Our systems are stronger thanks to you!

    Incredible! You've neutralized the ransomware and restored order across the North Pole. Peace has returned, and it's all thanks to your relentless determination!

    Ho-ho-holy snowflakes! You've done it! With the precision of a candy cane craftsman and the bravery of a reindeer on a foggy night, you've conquered all four tasks! You're a true holiday hero!

??? quote "Wunorse Openslae"

    Dude, c'mon. I thought we were bros! Why are you messin' with our achievements like that?

    Those phishing emails were totally devious, you gotta admit. Now let's head over to Wombley HQ and I'll show you how we craft them.

    Hey, we needed those accounts! Alright, broseph, you got one more chance or I'm telling Wombley how lame you are.

    Why you gotta be like that? Not cool. We worked so hard on that ransomware, and you dismantled the whole thing. So many wasted energy drinks...

    Whatevs, it's all good. This was just a practice run anyways. The real attack is going down later. And it's gonna be sick!
