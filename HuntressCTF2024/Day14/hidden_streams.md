## Hidden Streams
Another .evtx challenge, this one involving Sysmon logs. Can sort and search for suspicious Event ID's associated with malicious activity. 
Good list [here](https://medium.com/@rebaleos0/sysmon-event-id-potential-uses-in-detecting-malicious-activity-bf8e48b50780)
Full list [here](https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon)
Eventually you will find Event ID 15 — **File Create Stream Hash**, containing a powershell.exe execution with contents `ZmxhZ3tiZmVmYjg5MTE4MzAzMmY0NGZhOTNkMGM3YmQ0MGRhOX0=`. Decode this as base64 and obtain the flag.
`flag{bfefb891183032f44fa93d0c7bd40da9}`
