# Reversing and analyzing shellcode
In this repo I will be reversing and trying to understand how random shellcode payloads work at a low level. Using msfvenom and shellstorm to find fun chunks of shellcode to analyze. This will help me learn more ASM and also learn some new shellcoding skills and tool.

- Case study 1 - calling setuid(0) and spawning /bin/sh
- Case study 2 - Reads /etc/passwd

**Useful commands**

`echo -ne <shellcode> | ndisasm -u -` to convert shellcode to ASM with ndisasm

**Methodology**

1. Convert shellcode to ASM with ndisasm
2. Break up the different sections of shellcode (0x80 indicator)
3. Analyze with binary ninja and comment shellcode
4. Assemble and link the ASM version and test it
