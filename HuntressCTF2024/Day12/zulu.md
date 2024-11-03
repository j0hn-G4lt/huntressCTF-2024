## Zulu

The file we download is not directly readable, but upon inspection contains some compressed contents. Based on the hint, Zulu = Z in the [phonetic alphabet](https://en.wikipedia.org/wiki/NATO_phonetic_alphabet). Z -> zip. Adding the .zip extension to the file allows us to easily extract the file which contains the flag.
```bash
7z x zulu.zip
cat zulu
flag{74235a9216ee609538022e6689b4de5c}
```
