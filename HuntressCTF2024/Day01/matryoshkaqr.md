## MatryoshkaQR
Given a *very* large png QR code, the name suggests much like the Russian dolls that there is likely *another* QR code. Parsing in Cyberchef yields the following:
`\x89PNG\r\n\x1a\n\x00\x00\*snip*`

Indeed it looks like nested PNG files, so lets make a script to loop through the original QR and generate the proceeding QR, repeating until we no longer have PNG headers. At that point, the data left over would likely be our flag. Let's see.
```python
from PIL import Image
from pyzbar.pyzbar import decode
import os

def process(original):
    current = original
    count = 0

    while True:
        try:
            decoded = decode(Image.open(current))
            if decoded:
                qr_data = decoded[0].data.decode('utf-8')
                parse_data = bytes(qr_data, 'utf-8').decode('unicode_escape').encode('latin1')

                if parse_data.startswith(b'\x89PNG\r\n\x1a\n'):
                    with open(f"tmpimg{count}.png", 'wb') as f:
                        f.write(parse_data)
                    current = f"tmpimg{count}.png"
                    count += 1
                else:
                    print(f"{qr_data}")
                    break
            else:
                break
        except Exception as e:
            print(f"Error: {e}")
            break

    for i in range(count):
        os.remove(f"tmpimg{i}.png")

process('qrcode.png') # Path to qrcode
```
Running this script prints out: `flag{01c6e24c48f48856ee3adcca00f86e9b}`
