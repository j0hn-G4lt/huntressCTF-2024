## Stack It
Disassemble the binary and we can read the function which appears to do more than printing "Hello World. 

```python
$ objdump -d stack_it.bin

stack_it.bin:     file format elf32-i386

Disassembly of section .text:

08049000 <.text>:
 8049000:       c6 05 50 a0 04 08 66    movb   $0x66,0x804a050
 8049007:       c6 05 51 a0 04 08 6c    movb   $0x6c,0x804a051
 804900e:       c6 05 52 a0 04 08 61    movb   $0x61,0x804a052
 8049015:       c6 05 53 a0 04 08 67    movb   $0x67,0x804a053
 804901c:       c6 05 54 a0 04 08 7b    movb   $0x7b,0x804a054
 8049023:       8d 35 0e a0 04 08       lea    0x804a00e,%esi
 8049029:       8d 3d 2e a0 04 08       lea    0x804a02e,%edi
 804902f:       8d 15 55 a0 04 08       lea    0x804a055,%edx
 8049035:       b9 20 00 00 00          mov    $0x20,%ecx
 804903a:       8a 06                   mov    (%esi),%al
 804903c:       32 07                   xor    (%edi),%al
 804903e:       88 02                   mov    %al,(%edx)
 8049040:       46                      inc    %esi
 8049041:       47                      inc    %edi
 8049042:       42                      inc    %edx
 8049043:       e2 f5                   loop   0x804903a
 8049045:       c6 02 7d                movb   $0x7d,(%edx)
 8049048:       42                      inc    %edx
 8049049:       c6 02 00                movb   $0x0,(%edx)
 804904c:       8d 15 50 a0 04 08       lea    0x804a050,%edx
 8049052:       b9 24 00 00 00          mov    $0x24,%ecx
 8049057:       ff 32                   push   (%edx)
 8049059:       83 c2 04                add    $0x4,%edx
 804905c:       e2 f9                   loop   0x8049057
 804905e:       ba 0d 00 00 00          mov    $0xd,%edx
 8049063:       b9 00 a0 04 08          mov    $0x804a000,%ecx
 8049068:       bb 01 00 00 00          mov    $0x1,%ebx
 804906d:       b8 04 00 00 00          mov    $0x4,%eax
 8049072:       cd 80                   int    $0x80
 8049074:       b8 01 00 00 00          mov    $0x1,%eax
 8049079:       31 db                   xor    %ebx,%ebx
 804907b:       cd 80                   int    $0x80
```

Looking closer we can start to understand what is happening a little better:
```
8049000: c6 05 50 a0 04 08 66    movb   $0x66,0x804a050  ; 'f'
8049007: c6 05 51 a0 04 08 6c    movb   $0x6c,0x804a051  ; 'l'
804900e: c6 05 52 a0 04 08 61    movb   $0x61,0x804a052  ; 'a'
8049015: c6 05 53 a0 04 08 67    movb   $0x67,0x804a053  ; 'g'
804901c: c6 05 54 a0 04 08 7b    movb   $0x7b,0x804a054  ; '{'

8049035: b9 20 00 00 00          mov    $0x20,%ecx       ; loop counter = 32
804903a: 8a 06                   mov    (%esi),%al       ; Load  from esi
804903c: 32 07                   xor    (%edi),%al       ; XOR with edi
804903e: 88 02                   mov    %al,(%edx)       ; Store result in edx
8049040: 46                      inc    %esi             ; Increment esi
8049041: 47                      inc    %edi             ; Increment edi
8049042: 42                      inc    %edx             ; Increment edx
8049043: e2 f5                   loop   0x804903a        ; Loop until ecx = zero

8049045: c6 02 7d                movb   $0x7d,(%edx)     ; Append '}'
8049048: 42                      inc    %edx             ; Increment `edx`
8049049: c6 02 00                movb   $0x0,(%edx)      ; terminate the string
```
- Flag is stored in 0x804a050 
- Final '}' is added at 0x8049045

So let us set a breakpoint after final append to flag at 0x8049048, and then try to read the location where flag is stored
```bash
gef➤  b *0x8049048
Breakpoint 1 at 0x8049048
gef➤  run
*HIT BREAKPOINT*
gef➤  x/s 0x0804a050
0x804a050:      "flag{b4234f4bba4685dc84d6ee9a48e9c106}"
```
