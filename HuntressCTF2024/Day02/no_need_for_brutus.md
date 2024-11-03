## No Need for Brutus

Ok so no surprise the name is a hint. Brutus. Brute. Brute Force.
So we don't need to brute force? Ok so must be a simple cipher or something easily manipulated. 
Given the string `squiqhyiiycfbudeduutvehrhkjki`, plaintext ciphers without capitals or numbers limits the cipher further. The most basic is a Caesar Cipher, but there is a bit of guess work in that. ROT13 and Vigenère are based on the Caesar, but Vigenère requires a keyword, and Cyberchef has ROT13 decoder. So let's roll with ROT13.

Using the cipher (there is also a brute force for ROT13), which starts at 13, I went up and down 3 clicks. At 10 the string became the readable plaintext `caesarissimplenoneedforbrutus`. Seems to line up. 

The instructions said the MD5 hash of this text, IS the flag, so we generate one using this command:
```sh
echo -n "caesarisimplenoneedforbrutus" | md5sum
1f39cbe843a37561d22f318d6dd5fc21
```

So our flag = `flag{1f39cbe843a37561d22f318d6dd5fc21}`
