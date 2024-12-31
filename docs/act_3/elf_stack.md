# Elf Stack

Difficulty: :material-star::material-star::material-star::material-star::material-star:


## Objective

!!! question "Task description"

    Help the ElfSOC analysts track down a malicious attack against the North Pole domain.

??? quote "Fitzy Shortstack"

    Greetings! I'm the genius behind the North Pole Elf Stack SIEM. And oh boy, we’ve got a situation on our hands.

    Our system was attacked—Wombley’s faction unleashed their FrostBit ransomware, and it’s caused a digital disaster.

    The logs are a mess, and Wombley’s laptop—the only backup of the Naughty-Nice List—was smashed to pieces.

    Now, it’s all up to you to help me trace the attack vectors and events. We need to figure out how this went down before it’s too late.

    You’ll be using a containerized ELK stack or Linux CLI tools. Sounds like a fun little puzzle, doesn't it?

    Your job is to analyze these logs... think of it as tracking snow tracks but in a digital blizzard.

    If you can find the attack path, maybe we can salvage what’s left and get Santa’s approval.

    Santa’s furious at the faction fighting, and he’s disappointed. We have to make things right.

    So, let’s show these attackers that the North Pole’s defenses are no joke!

## Hints

??? tip "Elf Stack Intro"

    I'm part of the ElfSOC that protects the interests here at the North Pole. We built the Elf Stack SIEM, but not everybody uses it. Some of our senior analysts choose to use their command line skills, while others choose to deploy their own solution. Any way is possible to hunt through our logs!

??? tip "Elf Stack WinEvent"

    One of our seasoned ElfSOC analysts told me about a great resource to have handy when hunting through event log data. I have it around here somewhere, or maybe it was online. Hmm.

??? tip "Elf Stack Powershell"

    Our Elf Stack SIEM has some minor issues when parsing log data that we still need to figure out. Our ElfSOC SIEM engineers drank many cups of hot chocolate figuring out the right parsing logic. The engineers wanted to ensure that our junior analysts had a solid platform to hunt through log data.

??? tip "Elf Stack Fields"

    If you are using your command line skills to solve the challenge, you might need to review the configuration files from the containerized Elf Stack SIEM.

??? tip "Elf Stack Hard - Email1"

    I was on my way to grab a cup of hot chocolate the other day when I overheard the reindeer talking about playing games. The reindeer mentioned trying to invite Wombley and Alabaster to their games. This may or may not be great news. All I know is, the reindeer better create formal invitations to send to both Wombley and Alabaster.

??? tip "Elf Stack Hard - Email2"

    Some elves have tried to make tweaks to the Elf Stack log parsing logic, but only a seasoned SIEM engineer or analyst may find that task useful.


## Solution

=== "Silver"
    
    ??? success "Solution to question 1"

        !!! question "Question"

            How many unique values are there for the event_source field in all logs?

        ``` sh
        cat log_chunk_1.log | cut -d " " -f 4 | sort | uniq | wc -l
        
        5
        ```

        Answer: ```5```

    ??? success "Solution to question 2"

        !!! question "Question"

            Which event_source has the fewest number of events related to it?

        ``` sh
        TODO
        ```

        Answer: ```Authlog```

    ??? success "Solution to question 3"

        !!! question "Question"

            Using the event_source from the previous question as a filter, what is the field name that contains the name of the system the log event originated from?

        ``` sh
        cat auth.log | head

        <134>1 2024-09-15T00:10:01-04:00 kringleSSleigH AuthLog - - - {"timestamp": "2024-09-15T03:10:01.304953-04:00", "hostname": "kringleSSleigH", "service": "CRON[4863]:", "message": "pam_unix(cron:session): session opened for user root(uid=0) by (uid=0)"}
        ```

        Answer: ```hostname```

    ??? success "Solution to question 4"

        !!! question "Question"

            Which event_source has the second highest number of events related to it?

        ``` sh
        TODO
        ```

        Answer: ```NetflowPmacct```

    ??? success "Solution to question 5"

        !!! question "Question"

            Using the event_source from the previous question as a filter, what is the name of the field that defines the destination port of the Netflow logs?

    ??? success "Solution to question 6"

        !!! question "Question"

            Which event_source is related to email traffic?

        Answer: ```SnowGlowMailPxy```

    ??? success "Solution to question 7"

        !!! question "Question"

            Looking at the event source from the last question, what is the name of the field that contains the actual email text?

        ``` sh
        cat mail.log | head -n1

        <134>1 2024-09-15T08:26:14-04:00 SecureElfGwy SnowGlowMailPxy - - - {"From": "elf_user00@northpole.local", "To": "asnowball04@northpole.local", "Subject": "Welcome to the North Pole!", "Date": null, "Message-ID": "<532A9346-9F5F-4C29-BD40-CA171DD0E7DE@SecureElfGwy.northpole.local>", "Return-Path": "elf_user00@northpole.local", "Body": "Dear asnowball04,\n\nI wanted to inform you that we have a new team member joining us, [New Hire]. They will be joining our department as [Job Title]. Please extend a warm welcome and assist them with any necessary introductions and onboarding processes.\n\nLooking forward to working together!\n\nBest regards,\nelf_user00\n", "Received_Time": "2024-09-15T08:26:14-04:00", "ReceivedIP1": "172.24.25.25", "ReceivedIP2": "172.24.25.20"}
        ```

        Answer: ```Body```

    ??? success "Solution to question 8"

        !!! question "Question"

            Using the 'GreenCoat' event_source, what is the only value in the hostname field?

        ``` sh
        cat greencoat.log | head -n1

        <134>1 2024-09-15T05:57:55-04:00 SecureElfGwy GreenCoat - - - {"ip": "172.24.25.93", "user_identifier": "elf_user03", "timestamp": "2024-09-15T05:57:55-04:00", "method": "CONNECT", "url": "disc601.prod.do.dsp.mp.microsoft.com:443", "http_protocol": "HTTP/1.1", "status_code": 200, "response_size": 0, "protocol": "HTTPS", "additional_info": "outgoing via 172.24.25.25", "host": "SnowSentry"}
        ```

        Answer: ```SecureElfGwy```

    ??? success "Solution to question 9"

        !!! question "Question"

            Using the 'GreenCoat' event_source, what is the name of the field that contains the site visited by a client in the network?

        ``` sh
        cat greencoat.log | head -n1
        <134>1 2024-09-15T05:57:55-04:00 SecureElfGwy GreenCoat - - - {"ip": "172.24.25.93", "user_identifier": "elf_user03", "timestamp": "2024-09-15T05:57:55-04:00", "method": "CONNECT", "url": "disc601.prod.do.dsp.mp.microsoft.com:443", "http_protocol": "HTTP/1.1", "status_code": 200, "response_size": 0, "protocol": "HTTPS", "additional_info": "outgoing via 172.24.25.25", "host": "SnowSentry"}
        ```

        Answer: ```url```

    ??? success "Solution to question 10"

        !!! question "Question"

            Using the 'GreenCoat' event_source, which unique URL and port (URL:port) did clients in the TinselStream network visit most?

        ``` sh
        cat greencoat.log | cut -d '"' -f 20 | sort | uniq -c | sort -n

        ```

        Answer: ```TODO```

    ??? success "Solution to question 11"

        !!! question "Question"

            Using the 'WindowsEvent' event_source, how many unique Channels is the SIEM receiving Windows event logs from?

        ??? example "AI usage: awk command"

            ChatGPT helped here to generate an AWK command.

        ``` sh
        awk -F'"' '{for (i=1; i<=NF; i++) if ($i == "Channel") print $(i+2)}' windows.log | sort | uniq | wc -l

        5
        ```

        Answer: ```5```

    ??? success "Solution to question 12"

        !!! question "Question"

            What is the name of the event.Channel (or Channel) with the second highest number of events?

        ``` sh
        awk -F'"' '{for (i=1; i<=NF; i++) if ($i == "Channel") print $(i+2)}' windows.log | sort | uniq -c | sort -n

        50 Windows PowerShell
        191 System
        11751 Microsoft-Windows-PowerShell/Operational
        17713 Microsoft-Windows-Sysmon/Operational
        2268402 Security
        ```

        Answer: ```Microsoft-Windows-Sysmon/Operational```

    ??? success "Solution to question 13"

        !!! question "Question"

            Our environment is using Sysmon to track many different events on Windows systems. What is the Sysmon Event ID related to loading of a driver?

        [Microsoft documentation](https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon)

        Answer: ```6```

    ??? success "Solution to question 14"

        !!! question "Question"

            What is the Windows event ID that is recorded when a new service is installed on a system?

        [Microsoft documentation](https://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-10/security/threat-protection/auditing/event-4697)

        Answer: ```4697```

    ??? success "Solution to question 15"

        !!! question "Question"

            Using the WindowsEvent event_source as your initial filter, how many user accounts were created?

        ``` sh
        cat windows.log | grep 'EventID": 4720'
        
        ```

        Answer: ```0```

=== "Gold"

    ??? success "Solution to question 1"

        !!! question "Question"

            What is the event.EventID number for Sysmon event logs relating to process creation?

        [Microsoft documentation](https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon)

        Answer: ```1```

    ??? success "Solution to question 2"

        !!! question "Question"

            How many unique values are there for the 'event_source' field in all of the logs?

        Answer: ```5``` (from question 1 of the silver achievement)

    ??? success "Solution to question 3"

        !!! question "Question"

            What is the event_source name that contains the email logs?

        Answer: ```SnowGlowMailPxy``` (from question 6 of the silver achievement)

    ??? success "Solution to question 4"

        !!! question "Question"

            The North Pole network was compromised recently through a sophisticated phishing attack sent to one of our elves. The attacker found a way to bypass the middleware that prevented phishing emails from getting to North Pole elves. As a result, one of the Received IPs will likely be different from what most email logs contain. Find the email log in question and submit the value in the event 'From:' field for this email log event.

        ``` sh
        cat mail.log | cut -d '"' -f 38 | sort | uniq

        172.24.25.20
        172.24.25.25
        34.30.110.62
        ReceivedIP1
        ReceivedIP2
        
        cat mail.log | grep "34.30.110.62"
        
        <134>1 2024-09-15T10:36:09-04:00 SecureElfGwy SnowGlowMailPxy - - - {"From": "kriskring1e@northpole.local", "To": "elf_user02@northpole.local", "Subject": "URGENT!", "Date": null, "Message-ID": "<F3483D7F-3DBF-4A92-813D-4D9738479E50@SecureElfGwy.northpole.local>", "Return-Path": "fr0sen@hollyhaven.snowflake", "Body": "We need to store the updated naughty and nice list somewhere secure. I posted it here http://hollyhaven.snowflake/howtosavexmas.zip. Act quickly so I can remove the link from the internet! I encrypted it with the password: n&nli$t_finAl1\n\nthx!\nkris\n- Sent from the sleigh. Please excuse any Ho Ho Ho's.", "Received_Time": "2024-09-15T10:36:09-04:00", "ReceivedIP1": "172.24.25.25", "ReceivedIP2": "34.30.110.62"}
        ```

        Answer: ```kriskring1e@northpole.local```

    ??? success "Solution to question 5"

        !!! question "Question"

            Our ElfSOC analysts need your help identifying the hostname of the domain computer that established a connection to the attacker after receiving the phishing email from the previous question. You can take a look at our GreenCoat proxy logs as an event source. Since it is a domain computer, we only need the hostname, not the fully qualified domain name (FQDN) of the system.

        ``` sh
        cat greencoat.log | grep "hollyhaven"
        <134>1 2024-09-15T10:36:26-04:00 SecureElfGwy GreenCoat - - - {"ip": "172.24.25.12", "user_identifier": "elf_user02", "timestamp": "2024-09-15T10:36:26-04:00", "method": "GET", "url": "http://hollyhaven.snowflake/howtosavexmas.zip", "http_protocol": "HTTP/1.1", "status_code": 200, "response_size": 1098, "protocol": "HTTP", "additional_info": "outgoing via 172.24.25.25", "host": "SleighRider"}
        ```

        Answer: ```SleighRider```

    ??? success "Solution to question 6"

        !!! question "Question"

            What was the IP address of the system you found in the previous question?

        Answer: ```172.24.25.12```

    ??? success "Solution to question 7"

        !!! question "Question"

            A process was launched when the user executed the program AFTER they downloaded it. What was that Process ID number (digits only please)?

        ``` sh
        cat *.log | grep "howtosavexmas"

        Look for first log not with .zip

        <134>1 2024-09-15T10:37:09-04:00 SleighRider.northpole.local WindowsEvent - - - {"EventTime": "2024-09-15 10:37:09", "Hostname": "SleighRider.northpole.local", "Keywords": -9223372036854775808, "EventType": "INFO", "SeverityValue": 2, "Severity": "INFO", "EventID": 11, "SourceName": "Microsoft-Windows-Sysmon", "ProviderGuid": "{5770385F-C22A-43E0-BF4C-06F5698FFBD9}", "Version": 2, "Task": 11, "OpcodeValue": 0, "RecordNumber": 714, "ProcessID": 10014, "ThreadID": 6340, "Channel": "Microsoft-Windows-Sysmon/Operational", "Domain": "NT AUTHORITY", "AccountName": "SYSTEM", "UserID": "S-1-5-18", "AccountType": "User", "Category": "File created (rule: FileCreate)", "Opcode": "Info", "RuleName": "Downloads", "UtcTime": "2024-09-15T10:37:09-04:00", "ProcessGuid": "{face0b26-e149-6606-9300-000000000700}", "Image": "C:\\Windows\\Explorer.EXE", "TargetFilename": "C:\\Users\\elf_user02\\Downloads\\howtosavexmas\\howtosavexmas.pdf.exe", "CreationUtcTime": "2024-09-15T10:37:09-04:00", "User": "NORTHPOLE\\elf_user02", "EventReceivedTime": "2024-09-15T10:37:09-04:00", "SourceModuleName": "inSysmon", "SourceModuleType": "im_msvistalog", "ProcessId": 5680, "MoreDetails": "File created:"}
        ```

        Answer: ```10014```

    ??? success "Solution to question 8"

        !!! question "Question"

            Did the attacker's payload make an outbound network connection? Our ElfSOC analysts need your help identifying the destination TCP port of this connection.

        ``` sh
        cat windows.log | grep "howtosavexmas.pdf.exe" | grep "connection"

        <134>1 2024-09-15T10:37:50-04:00 SleighRider.northpole.local WindowsEvent - - - {"EventTime": "2024-09-15 10:37:50", "Hostname": "SleighRider.northpole.local", "Keywords": -9214364837600034816, "EventType": "AUDIT_SUCCESS", "SeverityValue": 2, "Severity": "INFO", "EventID": 5156, "SourceName": "Microsoft-Windows-Security-Auditing", "ProviderGuid": "{54849625-5478-4994-A5BA-3E3B0328C30D}", "Version": 1, "Task": 12810, "OpcodeValue": 0, "RecordNumber": 734596, "ProcessID": 4, "ThreadID": 1460, "Channel": "Security", "Category": "Filtering Platform Connection", "Opcode": "Info", "Application": "\\device\\harddiskvolume3\\users\\elf_user02\\downloads\\howtosavexmas\\howtosavexmas.pdf.exe", "Direction": "%%14593", "SourceAddress": "172.24.25.12", "SourcePort": 64543, "DestAddress": "103.12.187.43", "DestPort": 8080, "Protocol": 6, "FilterRTID": 0, "LayerName": "%%14611", "LayerRTID": 48, "RemoteUserID": "S-1-0-0", "RemoteMachineID": "S-1-0-0", "EventReceivedTime": "2024-09-15T10:37:50-04:00", "SourceModuleName": "inSecurityEvent", "SourceModuleType": "im_msvistalog", "ApplicationInformation_ProcessID": 8096, "ApplicationInformation_ApplicationName": "\\device\\harddiskvolume3\\users\\elf_user02\\downloads\\howtosavexmas\\howtosavexmas.pdf.exe", "NetworkInformation_Direction": "Outbound", "NetworkInformation_SourceAddress": "172.24.25.12", "NetworkInformation_SourcePort": 64543, "NetworkInformation_DestinationAddress": "103.12.187.43", "NetworkInformation_DestinationPort": 8443, "NetworkInformation_Protocol": 6, "FilterInformation_FilterRunTimeID": 0, "FilterInformation_LayerName": "Connect", "FilterInformation_LayerRunTimeID": 48, "MoreDetails": "The Windows Filtering Platform has permitted a connection."}
        ```

        Answer: ```8443```

    ??? success "Solution to question 9"

        !!! question "Question"

            The attacker escalated their privileges to the SYSTEM account by creating an inter-process communication (IPC) channel. Submit the alpha-numeric name for the IPC channel used by the attacker.

        ``` sh
        cat windows.log | grep "sleighrider" -i | grep "cmd.exe" -i
        <134>1 2024-09-15T10:38:22-04:00 SleighRider.northpole.local WindowsEvent - - - {"EventTime": "2024-09-15 10:38:22", "Hostname": "SleighRider.northpole.local", "Keywords": -9187343239835811840, "EventType": "INFO", "SeverityValue": 2, "Severity": "INFO", "EventID": 7045, "SourceName": "Service Control Manager", "ProviderGuid": "{555908D1-A6D7-4695-8E1E-26931D2012F4}", "Version": 0, "Task": 0, "OpcodeValue": 0, "RecordNumber": 1571, "ProcessID": 628, "ThreadID": 5852, "Channel": "System", "Domain": "NORTHPOLE", "AccountName": "elf_user02", "UserID": "S-1-5-21-3699322559-1991583901-1175093138-1110", "AccountType": "User", "ServiceName": "ddpvccdbr", "ImagePath": "cmd.exe /c echo ddpvccdbr > \\\\.\\pipe\\ddpvccdbr", "ServiceType": "user mode service", "StartType": "demand start", "EventReceivedTime": "2024-09-15T10:38:22-04:00", "SourceModuleName": "inSystemEvent", "SourceModuleType": "im_msvistalog", "ServiceFileName": "cmd.exe /c echo ddpvccdbr > \\\\.\\pipe\\ddpvccdbr", "ServiceStartType": "demand start", "ServiceAccount": "LocalSystem", "MoreDetails": "A service was installed in the system."}
        ```

        Answer: ```ddpvccdbr```

    ??? success "Solution to question 10"

        !!! question "Question"

            The attacker's process attempted to access a file. Submit the full and complete file path accessed by the attacker's process.

    ??? success "Solution to question 11"

        !!! question "Question"

            Using the WindowsEvent event_source as your initial filter, how many user accounts were created?

    ??? success "Solution to question 12"

        !!! question "Question"

            Using the WindowsEvent event_source as your initial filter, how many user accounts were created?

    ??? success "Solution to question 13"

        !!! question "Question"

            Using the WindowsEvent event_source as your initial filter, how many user accounts were created?

    ??? success "Solution to question 14"

        !!! question "Question"

            Using the WindowsEvent event_source as your initial filter, how many user accounts were created?

    ??? success "Solution to question 15"

        !!! question "Question"

            Using the WindowsEvent event_source as your initial filter, how many user accounts were created?

    ??? success "Solution to question 16"

        !!! question "Question"

            Using the WindowsEvent event_source as your initial filter, how many user accounts were created?

    ??? success "Solution to question 17"

        !!! question "Question"

            Using the WindowsEvent event_source as your initial filter, how many user accounts were created?

    ??? success "Solution to question 18"

        !!! question "Question"

            Using the WindowsEvent event_source as your initial filter, how many user accounts were created?

    ??? success "Solution to question 19"

        !!! question "Question"

            Using the WindowsEvent event_source as your initial filter, how many user accounts were created?

    ??? success "Solution to question 20"

        !!! question "Question"

            Using the WindowsEvent event_source as your initial filter, how many user accounts were created?

    ??? success "Solution to question 21"

        !!! question "Question"

            Using the WindowsEvent event_source as your initial filter, how many user accounts were created?

    ??? success "Solution to question 22"

        !!! question "Question"

            Using the WindowsEvent event_source as your initial filter, how many user accounts were created?

    ??? success "Solution to question 23"

        !!! question "Question"

            Using the WindowsEvent event_source as your initial filter, how many user accounts were created?

    ??? success "Solution to question 24"

        !!! question "Question"

            Using the WindowsEvent event_source as your initial filter, how many user accounts were created?
            

## Response

??? quote "Fitzy Shortstack"

    Fantastic job! You worked through the logs using the ELK stack like a pro—efficient, quick, and spot-on. Maybe, just maybe, this will turn Santa’s frown upside down!

    Unbelievable! You dissected the attack chain using advanced analysis—impressive work! With determination like yours, we might just fix the mess and get Santa smiling again.

    Bravo! You pieced it all together, uncovering the attack path. Santa’s gonna be grateful for your quick thinking and tech savvyness. The North Pole owes you big time!