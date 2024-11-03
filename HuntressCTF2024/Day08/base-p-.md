## Base-p-
After completing, it makes sense. But after trying every. single. Text and Base X encoding, found [this](https://www.better-converter.com/Encoders-Decoders/Base65536-Decode). Base **65536**.
Pasting the text and decoding reveals another string, which is compressed Base64. Decoding this now reveals PNG data that renders an image of several differently colored squares. Eyedropper the RGB hex value of the squares and concatenate them for a hex string, that then decodes into the flag: `flag{586cf8c849c9730ea7b2112fff39ff6ff6a}`

-p- is all ports for nmap. WOW what a *hint*.
