## Mimi
lsass.exe dump that gave trouble when using mimikatz, using pypykatz instead we can grep flag like so:

```sh
$ pypykatz lsa minidump mimi | grep 'flag{'
INFO:pypykatz:Parsing file mimi
                password flag{7a565a86761a2b89524bf7bb0d19bcea}
                password flag{7a565a86761a2b89524bf7bb0d19bcea}
                password flag{7a565a86761a2b89524bf7bb0d19bcea}
                password flag{7a565a86761a2b89524bf7bb0d19bcea}
```
