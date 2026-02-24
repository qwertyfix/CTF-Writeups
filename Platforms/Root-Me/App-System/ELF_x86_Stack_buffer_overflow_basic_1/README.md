# ELF x86 - Stack Buffer Overflow Basic 1
![base](https://github.com/user-attachments/assets/bf728ba8-00c5-447c-bcc0-def3ef1b8624)

## Source Analysie

We analyze the code

<img width="1366" height="768" alt="1 3" src="https://github.com/user-attachments/assets/624eea13-4273-4c5a-b238-e75513fd0df0" />

* **Buffer:** `char buf[40]` — The allocated space in the stack.
* **Vulnerability:** `fgets(buf, 45, stdin)` — It reads 45 bytes into a 40-byte buffer.
* **Win Condition:** The local variable `check` must be overwritten with `0xdeadbeef`.
