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
 
  char buffer[64];
  int check;
  int i = 0;
  int count = 0;
 
  printf("Enter your name: ");
  fflush(stdout);
  while(1)
    {
      if(count >= 64)
        printf("Oh no...Sorry !\n");
      if(check == 0xbffffabc)
        shell();
      else
        {
            read(fileno(stdin),&i,1);
            switch(i)
            {
                case '\n':
                  printf("\a");
                  break;
                case 0x08:
                  count--;
                  printf("\b");
                  break;
                case 0x04:
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

