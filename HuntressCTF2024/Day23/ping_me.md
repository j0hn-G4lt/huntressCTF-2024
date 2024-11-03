## Ping Me

After downloading the VBS script, we can open with a text editor and tell this is obfuscated. It appears to be performing some addition/subtraction of values that resolve into chars. So here is a script to deobfuscate and save you the trouble. Just place in same directory as the VBS file.
```python
import re

with open('ping_me.vbs', 'r') as file:
    vbs_code = file.read()

vbs_code = re.sub(r'CLng\((.*?)\)', r'\1', vbs_code)
vbs_code = vbs_code.replace('&H', '0x')
expressions = re.findall(r"chr\((.*?)\)", vbs_code)

def evaluate_expression(expr):
    if 'vbCrlf' in expr:
        return '\n'
    try:
        value = eval(expr)
        if isinstance(value, float):
            value = int(value)
        return chr(value)
    except Exception as e:
        print(f"Error evaluating expression '{expr}': {e}")
        return ''

decoded_message = ''.join([evaluate_expression(expr) for expr in expressions])

print("Decoded Message:")
print(decoded_message)
```
You will obtain this:
```powershell
Dim sh, ips, i:Set sh = CreateObject("WScript.Shell"):ips = Array("102.108.97.103", "123.54.100.49", "98.54.48.52", "98.98.49.98", "54.100.97.51", "50.98.56.98", "98.99.97.57", "101.50.54.100", "53.49.53.56", "57.125.35.35"):For i = 0 To UBound(ips):    sh.Run "cmd /Q /c ping " & ips(i), 0, False:Next
```
Now I originally thought we needed to interact with the servers, but then it struck me that there are 10 IP's, so they probably wouldn't have us do that. However 40 octets with last two the same -> $40 - 2 = 38 =$ flag{md5}

Convert these octets into ASCII and you will obtain:
`flag{6d1b604bb1b6da32b8bbca9e26d51589}`
