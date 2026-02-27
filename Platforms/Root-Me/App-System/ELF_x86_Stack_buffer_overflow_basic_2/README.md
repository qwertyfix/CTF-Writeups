# ELF x86 - Stack buffer overflow basic 2

![2 3](https://github.com/user-attachments/assets/8d484789-a862-4afc-b5eb-fb13fc8cdb25)

![2 2](https://github.com/user-attachments/assets/b8120546-44b8-44f6-addd-bfe4d4aa4f96)

# Preparing

<img width="1366" height="768" alt="2 4" src="https://github.com/user-attachments/assets/3cfb4027-e310-4932-b3fb-0d4785ce2bae" />

First, we need to compile the code using this command in terminal. Here, we save the Non-Executable Stack and Non-Executable Head by condition.

```bash
gcc basic.c -o basic -m32 -fno-stack-protector -z noexecstack -no-pie -Wno-stringop-overflow
```
- -m32: Compilation for 32-bit architecture (4-byte pointers)
- -fno-stack-protector: Allows us to overwrite variables outside of buf.
- -z noexecstack: Includes NX
- -no-pie: Makes the address of the shell() function static.
- -Wno-stringop-overflow: Avoiding the problem

# Vulnerability Research

|0 - 127 |	buf[128] |	Local char array (Target for overflow)|
|128 - 131	| void (*func)() | Function Pointer (The Exploit Primitive)|
|132 | null-byte	| Terminator byte appended by fgets|

