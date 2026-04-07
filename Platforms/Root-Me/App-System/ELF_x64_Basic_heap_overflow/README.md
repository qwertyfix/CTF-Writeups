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

This can be done using the gets function. We enter a value in arg and can go beyond the size of arg, that is, more than 32 bytes + metadata, to reach cmd.

Also, don't forget about ```strcat(cmd, arg)```. We just need the system to run ```"/bin/sh"```, and strcat appends the values ​​to the end. I was confused while trying to figure out how to bypass this so that only the shell command would run, but I figured it out later.

# Preparing & Solution

+-----------+----------------+------------------------------------------------+
| Смещение  | Поле           | Описание                                       |
+-----------+----------------+------------------------------------------------+
| -0x10     | prev_size      | Метаданные чанка arg                           |
+-----------+----------------+------------------------------------------------+
| -0x08     | size (0x31)    | Размер чанка arg (48 байт всего + флаг P)      |
+-----------+----------------+------------------------------------------------+
|  0x00     | arg (user data)| Начало твоего буфера                           |
+-----------+----------------+------------------------------------------------+
|  0x20     | prev_size      | Метаданные чанка cmd (target for overflow)     |
+-----------+----------------+------------------------------------------------+
|  0x28     | size (0x411)   | Размер чанка cmd                               |
+-----------+----------------+------------------------------------------------+
|  0x30     | cmd (user data)| Куда пишем /bin/sh                             |
+-----------+----------------+------------------------------------------------+
