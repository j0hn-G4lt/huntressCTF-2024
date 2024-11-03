## I can't SSH
Download the **id_rsa** file. Common issue is not formatting the key correctly, or not adding correct permissions to the file. So lets put it through a formatter like this to be sure.
Copy this back into the file, and make sure it is private via:
```bash
$ chmod 600 /path/to/id_rsa
```
Now try again using ssh but utilizing the id_rsa file like so:
```bash
$ ssh -i id_rsa -p 30442 user@challenge.ctf.games
user@i-cant-ssh-fa043244a39c509c-f7d49469-6ljcs:~$ ls
flag.txt
user@i-cant-ssh-fa043244a39c509c-f7d49469-6ljcs:~$ cat flag.txt
flag{ee1f28722ec1ce1542aa1b486dbb1361}
```
