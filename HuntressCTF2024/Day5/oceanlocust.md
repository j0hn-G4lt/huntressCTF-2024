## OceanLocust

```python
from pwn import log
import subprocess
import os
import binascii
import re

# Extracted flag hex from the original PNG file
target_encoded_data = (
    "0405350619040c375a55015f6d53005a0c375c06545c365d000058640307550b365157065929c2c8"
)

# Function to extract and order chunks from PNG
def extract_and_order_chunks(file_path):
    with open(file_path, "rb") as f:
        data = f.read()

    # Find all chunk names that start with 'biT'
    chunk_pattern = re.compile(b"biT[a-z]")
    chunks = {}

    # Search for the chunks in binary data
    for i in range(len(data)):
        match = chunk_pattern.match(data[i : i + 4])
        if match:
            chunk_name = match.group().decode()
            length = int.from_bytes(data[i - 4 : i], "big")
            chunk_data = data[i + 4 : i + 4 + length]
            chunks[chunk_name] = chunk_data

    concatenated_data = "".join(
        binascii.hexlify(chunks[chunk_name]).decode() for chunk_name in sorted(chunks)
    )
    return concatenated_data


def get_encoded_data(input_string):
    output_file = "encoded test.png"
    if os.path.exists(output_file):
        os.remove(output_file)

    command = f'wine png-challenge-debug.exe test.png "{input_string}"'
    result = subprocess.run(
        command, shell=True, stdout=subprocess.PIPE, stderr=subprocess.PIPE, text=True
    )

    if result.returncode != 0:
        log.error(f"Encoding failed for input: {input_string}")
        log.error(f"Error: {result.stderr.strip()}")
        return None

    if os.path.exists(output_file):
        extracted_data = extract_and_order_chunks(output_file)
        return extracted_data
    else:
        log.error(f"Failed to create encoded file for input: {input_string}")
        return None
        
# MD5 chars
charset = "0123456789abcdef"

# We know flag = flag{md5}
flag_prefix = "flag{"
flag_suffix = "}"
md5_hash_part = ""

progress_log = log.progress("Brute-forcing flag")

for position in range(5, 37):  # Range inside flag{-}
    found = False
    for char in charset:
        padding_length = 32 - len(md5_hash_part) - 1
        md5_guess = md5_hash_part + char + "a" * padding_length
        input_string = f"{flag_prefix}{md5_guess}{flag_suffix}"
        progress_log.status(f"Trying character '{char}' at position {position}")
        encoded_data = get_encoded_data(input_string)
        if encoded_data:
            target_bytes = bytes.fromhex(target_encoded_data)
            extracted_bytes = bytes.fromhex(encoded_data)

            if (
                len(extracted_bytes) > position
                and extracted_bytes[position] == target_bytes[position]
            ):
                md5_hash_part += char
                found = True
                break

    if not found:
        log.error("Fix the script")
        break

progress_log.success(f"\n{flag_prefix}{md5_hash_part}{flag_suffix}")
```
