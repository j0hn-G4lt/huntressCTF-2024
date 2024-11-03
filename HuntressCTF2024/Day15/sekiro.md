## Sekiro
We are given a TTY game which is based on parry mechanics in video games. So the counter attack to each opponent move is how we avoid dying. The time to respond for each answer gets shorter and shorter, so why not script it? After determining the correct move/countermove we can map these into a python script which auto sends the counter moves.
```python
from pwn import *

move_map = {
    'block': 'advance',
    'strike': 'block',
    'advance': 'retreat',
    'retreat': 'strike',
}

# CHANGE PORT
conn = remote('challenge.ctf.games', PORT)

try:
    while True:
        output = conn.recvuntil(b'Opponent move:').decode(errors='ignore')
        print(output, end='')

        opponent_move_line = conn.recvline().decode(errors='ignore').strip()
        print(opponent_move_line)
        opponent_move = opponent_move_line.lower()

        your_move = move_map.get(opponent_move)
        if not your_move:
            print(f"Unknown opponent move: {opponent_move}")
            break

        output = conn.recvuntil(b'Your move:').decode(errors='ignore')
        print(output, end='')

        conn.sendline(your_move.encode())
        print(your_move)

except EOFError:
    print("Connection closed by the server.")

try:
    remaining_data = conn.recvall(timeout=1).decode(errors='ignore')
    print(remaining_data)
except Exception:
    pass
finally:
    conn.close()
```This will go through the challenge and eventually obtain the flag.
```sh
~~~~~~~~~~~~~~~~~~ 忍殺! ~~~~~~~~~~~~~~~~~~~~
flag{a1ae4e5604576818132ce3bfebe95de5}
	```
