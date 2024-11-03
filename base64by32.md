## Base64by32
The name is a hint, base64 is obviously an encoding. At first I thought it was a simple base64 decode, then base32 decode, but quickly realized the given string was very long. Given the length, it makes more sense that the string was encoded 32 times with base64, so decrypting can be done a number of ways. Here is a python script to do it for you.
```python
import base64

def decoder(data, iterations=32):
    for _ in range(iterations):
        data = base64.b64decode(data).decode('utf-8', errors='ignore')
    return data

def read_file(path):
    with open(path, 'r') as file:
        return file.read().replace('\n', '')

file_path = 'base64by32'
encoded_data = read_file(file_path)
decoded_message = decoder(encoded_data)
print(decoded_message)
```
This prints out the flag: **flag{8b3980f3d33f2ad2f531f5365d0e3970}**
