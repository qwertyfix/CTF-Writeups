____                      _    ____ _____ _____   ____   ___  ____   __    
 | __ )  ___  __ _ _ __ ___/ |_ / ___|_   _|  ___| |___ \ / _ \|___ \ / /_   
 |  _ \ / _ \/ _` | '__/ __| __| |     | | | |_      __) | | | | __) | '_ \  
 | |_) |  __/ (_| | | | (__| |_| |___  | | |  _|    / __/| |_| / __/| (_) | 
 |____/ \___|\__,_|_|  \___|\__|\____| |_| |_|     |_____|\___/_____|\___/

# BearcatCTF 2026 - Setting Sail (Write-up)
**Category:** Reverse Engineering
## Challenge Description
![Снимок](https://github.com/user-attachments/assets/892a1e1e-ca8a-4816-a3ef-fdb41e5e95f7)

---

## Static Analysis & File Research
First, let's check the file type and its contents.

**Type:** ELF 64-bit LSB pie executable

![1](https://github.com/user-attachments/assets/663368b9-d641-4154-9c3f-da4148b8a7eb)

**Strings:** Interesting strings were found inside the binary:
- `./devilishFruit` (Expected binary name)
- `PWD=/tmp/gogear5` (Expected working directory)
- `ONE_PIECE=IS_REAL` (Required environment variable)
  
![2](https://github.com/user-attachments/assets/57220d89-59da-4436-9e1d-d8d7631a64a7)

**Dynamic Analysis (ltrace):** It is clear that the program actively uses strcmp to check environment parameters:
1. Compares the file name to the string ./devilishFruit
2. Iterates through environment variables looking for a match with PWD=/tmp/gogear5.
3. Searches the environment for the variable ONE_PIECE=IS_REAL.
![3](https://github.com/user-attachments/assets/74040ab2-40fe-436d-8fbd-26be064eb1c0)
![8](https://github.com/user-attachments/assets/b4be0741-5dbb-43d2-9b88-b59638da3514)


We run it and since the file was called missing_one_piece, and the path and variables did not match, the program output:
![4](https://github.com/user-attachments/assets/b4f12beb-f5d7-4246-9f1e-d8cb464ffe06)


## Reverse Engineering (Radare2)

![5](https://github.com/user-attachments/assets/06f27233-8b6d-4ffd-8e75-4e05e534ce1a)

![6](https://github.com/user-attachments/assets/b6489475-43aa-48bf-9762-2a06bf78160b)

Analysis of the `main` function (`pdf @ main`):
| Address | Condition | Description |
| :--- | :--- | :--- |
| `0x000013cd` | `strcmp(argv[0], "./devilishFruit")` | Ensures the file name is correct. |
| `0x1404 - 0x1474` | `envp` Loop (PWD) | Checks if current path is `/tmp/gogear5`. |
| `0x149a - 0x150a` | `envp` Loop (Variable) | Checks for `ONE_PIECE=IS_REAL` in environment. |

If all checks pass, the binary triggers `snprintf` and calls `you_dont_need_to_rev_this` to reveal the flag.

## Exploitation (Solution)
To satisfy the binary's requirements, I prepared the environment as follows:

# 1. Setup the specific directory
<pre>
mkdir -p /tmp/gogear5 && cd /tmp/gogear5
</pre>

# 2. Rename the binary to match expectations
<pre>
cp /path/to/missing_one_piece ./devilishFruit
chmod +x ./devilishFruit
</pre>

# 3. Set the environment variable and execute
<pre>
export ONE_PIECE='IS_REAL'
./devilishFruit
</pre>

## Result
Success. The binary validates the environment and prints the flag:

Flag: BCCTF{I_gU3S5_7hAt_Wh1t3BeArD_6uY_W45_TRu7h1n6!}

![7](https://github.com/user-attachments/assets/0fb5478f-0b3b-4c2e-9287-170bdb4f35ce)

