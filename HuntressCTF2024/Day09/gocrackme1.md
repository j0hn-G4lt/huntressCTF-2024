## GoCrackMe1
First installment of 3 binary reverse challenges written in Go. This one was just a simple comparison bypass. Reminds me of 2008 photoshop trials. I used **gdb** on Kali but really whatever debugger you are comfortable with will do the trick. 

Since this wasn't malware, executing is fine and prints out `Access Denied!`
Seems like we want access, and no input is asked of us. So lets see load it into gdb and see whats going on with the **info functions** command.

```sh
$ gdb ./GoCrackMe1
*snip*
(gdb) info functions
All defined functions:

File /app/src/GoCrackMe1/GoCrackMe1.go:
        void main.checkCondition(bool);
        void main.main(void);
*snip*
```
This tells us our main function is called `main.main`
So lets stop it right there to find what the program is doing before it denies our access by adding a **break** and run it.
```sh
(gdb) break main.main
Breakpoint 1 at 0x48360f: file /app/src/GoCrackMe1/GoCrackMe1.go, line 22.
(gdb) run
```

```sh
(gdb) disassemble
Dump of assembler code for function main.main:
   0x0000000000483600 <+0>:     lea    -0x8(%rsp),%r12
   0x0000000000483605 <+5>:     cmp    0x10(%r14),%r12
   0x0000000000483609 <+9>:     jbe    0x483767 <main.main+359>
=> 0x000000000048360f <+15>:    sub    $0x88,%rsp
   *snip*                                                             
End of assembler dump.
```
So we are stopped at main.main, and if you can follow the assembly code, you will see a comparison that takes you to the Access Denied message we saw earlier, specifically:
```sh
0x00000000004836c8 <+200>:   call   0x483500 <main.checkCondition>
0x00000000004836cd <+205>:   test   %al,%al
0x00000000004836cf <+207>:   je     0x483719 <main.main+281>
```
So it is running a check, testing that value, then jumping to the Access Denied! output. Let's put an end to that nonsense because we do what we want. If it never gets told to deny us, surely we must succeed right? Lets see. (we change the jump [je](https://en.wikipedia.org/wiki/X86_instruction_listings) to [NOP](https://en.wikipedia.org/wiki/NOP_(code)))
```sh
(gdb) set {char}0x4836cf = 0x90
(gdb) continue
Continuing.
flag{bb59566e21f55e5680d589f3dbbec0f8}
*snip*
(gdb)exit
```
