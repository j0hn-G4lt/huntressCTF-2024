## Nightmare On Hunt Street

Nightmare on Hunt Street Opening the .evtx files proved difficult. Only the System.evtx actually opened events in Event Viewer, but no useful information was shown. On my Kali Machine, a simple cat command on the Security.evtx file yielded mostly unreadable data; but I was able to determine that I needed another tool to open these. I came across Gigasheet (app.gigasheet.com) and was able to upload all three files. This allowed for easily reading each log in a spreadsheet-like format. 
1. **What is the IP address of the host that the attacker used?**
	- Within Security.evtx, able to use filters to easily determine that `10.1.1.42` (Filter = EventData/IpAddress) 
2. **How many times was the compromised account brute-forced? Answer just the integer value.** 
	- Logs (EventIDs 213215-234339) showed EventData/AuthenticationPackageNames with the value NTLM multiple times, I was able to determine through EventData/FailureReason that these were multiple authentication attempts. Counting the amount of rows and we were able to determine `32` brute force attempts. 
3. **What is the name of the offensive security tool that was used to gain initial access? Answer in all lowercase.** 
	- This question was by far the most ridiculous question, nowhere in the logs was it explicitly shown, and nothing eluded to the tool in question. Various hints in the Huntress Discord + ChatGPT + about 30 incorrect guesses and I was able to get `psexec` to finally work. I'm under the assumption there were multiple correct answers though based on talking to other participants.
4. **How many unique enumeration commands were run with net.exe? Answer just the integer value.** 
	- Under EventData/ParentProcessName, we were able to simply count the amount of unique commands containing net.exe, this came to `5`. 
5. **What password was successfully given to the user created?**
	- EventID 234534 showed the creation of user susan_admin created with the password "Susan123!" using \"C:\Windows\system32\net.exe\" user susan_admin `Susan123!` /ADD
