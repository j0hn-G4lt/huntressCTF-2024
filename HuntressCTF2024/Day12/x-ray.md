## X-Ray
Download the [DeXRAY](https://hexacorn.com/d/DeXRAY.pl) perl script, which is a tool specifically for quarantined files, and capable of identifying which AV did so. 
`wget https://hexacorn.com/d/DeXRAY.pl -O DeXRAY.pl`.

Execute the script like so: `perl DeXRAY.pl /path/to/x-ray` which will create a new file with the `.out` extension we can use for further analysis, since it is now unpacked.
```sh
Processing file: 'x-ray'
 -> 'x-ray.00000184_Defender.out' - Defender File
 -> ofs='184' (000000B8)
```
We can also now determine the file type via:
```bash
$ file x-ray.00000184_Defender.out 
x-ray.00000184_Defender.out: PE32 executable (DLL) (console) Intel 80386 Mono/.Net assembly, for MS Windows, 3 sections
```
Since we identified the file as a MS WIN DLL, it might be easier to get running on a Windows machine. I was able to get dotnet and pwsh working on Kali to use the [Ilspy](https://github.com/icsharpcode/ILSpy) tool, which is a decompiling tool for DLL's, exe's, etc. It is recommended you use a VM or turn off AV in order to avoid deletion of your files as this is a malware challenge.
Execute Ilspy and load your file, for me it was as simple as using:
```c
$ pwsh
PS> dotnet /path/to/ilspycmd.dll /path/to/x-ray.00000184_Defender.out >> xray_dump.txt
PS> cat xray_dump.txt
```
Reading through the dump, we notice the program called `stagetwo`. Accessing its main class `stagetwo` we see the source code which, in general, is receiving commands from the user then subsequently executing operations and commands. But the interesting elements are **otp**, **load** and **static void Main**.
```csharp
private static byte[] otp(byte[] data, byte[] key)
{
    byte[] array = new byte[data.Length];
    for (int i = 0; i < data.Length; i++)
    {
        array[i] = (byte)(data[i] ^ key[i % key.Length]);
    }
    return array;
}

private static byte[] load(string hex)
{
    int length = hex.Length;
    byte[] array = new byte[length / 2];
    for (int i = 0; i < length; i += 2)
    {
        array[i / 2] = Convert.ToByte(hex.Substring(i, 2), 16);
    }
    return array;
}

public static void Main(string[] args)
{
    byte[] data = load("15b279d8c0fdbd7d4a8eea255876a0fd189f4fafd4f4124dafae47cb20a447308e3f77995d3c");
    byte[] key = load("73de18bfbb99db4f7cbed3156d40959e7aac7d96b29071759c9b70fb18947000be5d41ab6c41");
    byte[] bytes = otp(data, key);
    string result = Encoding.UTF8.GetString(bytes);
    Console.WriteLine(result);
}
```
**OTP**
- Uses a key to encrypt the data using XOR
**LOAD**
- Converts a hexadecimal string to a byte array.
**MAIN**
- An instance of StageTwo is created by calling the main method passing an empty string, then both hex strings are passed. **data** and **key** are for XOR, OTP is used to encrypt this data into bytes. Then finally it converts the encoded bytes into a UTF-8 string.

Since we have both **data** and **key**, we can reverse the strings as such.
HEX -> BYTES -> XOR w/ data and key -> ASCII(UTF-8).
```python
def load(hex_str):
    return bytes.fromhex(hex_str)

def otp(data, key):
    return bytes(d ^ key[i % len(key)] for i, d in enumerate(data))

data_hex = "15b279d8c0fdbd7d4a8eea255876a0fd189f4fafd4f4124dafae47cb20a447308e3f77995d3c"
key_hex = "73de18bfbb99db4f7cbed3156d40959e7aac7d96b29071759c9b70fb18947000be5d41ab6c41"

data = load(data_hex)
key = load(key_hex)


decoded_bytes = otp(data, key)

decoded_message = decoded_bytes.decode('utf-8')
print(decoded_message)
```
Output:
`flag{df26090565cb329fdc8357080700b621}`
