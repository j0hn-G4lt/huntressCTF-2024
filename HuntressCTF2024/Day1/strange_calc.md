## strange_calc

calc.exe is given to us for analysis, with a hint it requires administrator privileges to run. Red flag. Without executing this file, lets investigate its contents if possible. You can use whatever hex editor, disassembler, or console commands you are familiar with, personally I used 'file' and 'strings' on kali linux initially.

Regardless of how you approach it, ***most*** of the immediate data is gibberish. However, there is a section visible which hints to the next step. On Kali I started with this:
```sh
$ file calc.exe 
calc.exe: PE32 executable (GUI) Intel 80386, for MS Windows, UPX compressed, 3 sections

$ strings calc.exe
*snip*
AU3!EA05
```
AU3!EA05 is maybe not well known, but I recognized this as a compiled AutoIt script. Also revealed is XML data that confirms the suspicion, and why it wants to be launched as admin (runs with perms of user):
```xml
version="3.0.0.0"
name="AutoIt3"
<description>AutoIt v3</description>
*snip*
```
AutoIt is a macro software pretty widely used. But as far as a calculator using it... Probably not for good reasons. So lets investigate this further. I had trouble finding the right tool to work on linux, but a teammate suggested https://github.com/digitalsleuth/autoit-extractor. We want to try and decompile/analyze the macro for anything useful, as it realistically should not be part of any calculator, so probably a good path to investigate.

After getting it to run in wine (had to install wine mono), loading the calc.exe and saving the script as a text file we can view the code for the macro:
```xml
<AUT2EXE INCLUDE-START:
*snip*
Local $a = "I0B+XmNRTUFBQT09VyF4XkRrS3hQbWAoYgk3bC5QMSdFRUJOJyggL2FWa0RjRS0JSippV1cuYzdsLlB/"
*snip*
```
Looks like $a is some sort of base64 encoded string so lets decode that:
```js
function a(b){var c="",d=b.split("\n");for(var e=0;e<d.length;e++){var f=d[e].replace(/^\s+|\s+$/g,'');
*snip*
```
Looks like obfuscated js code that creates an Admin user account. It sets a password, so lets try to deobfuscate and reverse this to generate the password it would have done.
```js
var m="begin 644 -\nG9FQA9WLY.3(R9F(R,6%A9C$W-3=E,V9D8C(X9#<X.3!A-60Y,WT*\n`\nend"; // The cmd generating password
```
In python I did this:
```python
import binascii

def decode_string(encoded_string):
    decoded_result = ""
    for line in encoded_string.strip().split("\n"):
        line = line.strip()
        if (
            line.startswith("begin")
            or line.startswith("end")
            or line == ""
            or line == "`"
        ):
            continue
        decoded_result += binascii.a2b_uu(line).decode("utf-8", errors="ignore")
    print("Decoded:", decoded_result)
    return decoded_result

encoded_string = """begin 644 -
G9FQA9WLY.3(R9F(R,6%A9C$W-3=E,V9D8C(X9#<X.3!A-60Y,WT*
`
end"""

decoded_password = decode_string(encoded_string)
```
Outputs:
`Decoded: flag{9922fb21aaf1757e3fdb28d7890a5d93}`
