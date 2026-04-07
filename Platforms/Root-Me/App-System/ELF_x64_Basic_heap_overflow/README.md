# ELF x64 - Basic heap overflow

![4 2](https://github.com/user-attachments/assets/d70df2b1-dc73-472a-9983-2a60211c97e0)

# Analysis



```c 
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
 
void    checkArg(const char *a)
{
  while (*a)
    {
      if (   (*a == ';')
          || (*a == '&')
          || (*a == '|')
          || (*a == ',')
          || (*a == '$')
          || (*a == '(')
          || (*a == ')')
          || (*a == '{')
          || (*a == '}')
          || (*a == '`')
          || (*a == '>')
          || (*a == '<') ) {
        puts("Forbidden !!!");
        exit(2);
      }
        a++;
    }
}
 
int     main()
{
  char  *arg = malloc(0x20); // malloc on 32 bytes
  char  *cmd = malloc(0x400); // malloc on 1024 bytes
  setreuid(geteuid(), geteuid());
 
  strcpy(cmd, "/bin/ls -l "); // Ready-made form for searching files
 
  printf("Enter directory you want to display : ");
 
  gets(arg); // Favorite gets function
  checkArg(arg); 
 
  strcat(cmd, arg); // Sticking input to a command from cmd
  system(cmd);
 
  return 0;
}
```

The vulnerability is that we can call a shell if we change the command inside cmd, from ```"/bin/ls -l "``` to ```"/bin/sh"```

