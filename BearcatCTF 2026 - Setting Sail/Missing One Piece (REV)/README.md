BearcatCTF 2026 - Setting Sail
Challenges
![Снимок](https://github.com/user-attachments/assets/892a1e1e-ca8a-4816-a3ef-fdb41e5e95f7)

Исследование файла/File Research
Первым делом проверяем тип файла и его содержимое/First, let's check the file type and its contents.

Тип/Type: ELF 64-bit LSB pie executable
![1](https://github.com/user-attachments/assets/663368b9-d641-4154-9c3f-da4148b8a7eb)

Strings: Внутри бинарника обнаружены интересные строки/Interesting strings were found inside the binary:
./devilishFruit

PWD=/tmp/gogear5

ONE_PIECE=IS_REAL

The legends have been proven true! %s
![2](https://github.com/user-attachments/assets/57220d89-59da-4436-9e1d-d8d7631a64a7)

Динамический анализ/Dynamic Analysis (ltrace)
Видно что программа активно использует strcmp для проверки параметров окружения/It is clear that the program actively uses strcmp to check environment parameters:
Сравнивает имя файла со строкой ./devilishFruit/Compares the file name to the string ./devilishFruit
Перебирает переменные окружения, ища совпадание с PWD=/tmp/gogear5/Iterates through environment variables looking for a match with PWD=/tmp/gogear5.
Ищет в окружении переменную ONE_PIECE=IS_REAL/Searches the environment for the variable ONE_PIECE=IS_REAL.
![3](https://github.com/user-attachments/assets/74040ab2-40fe-436d-8fbd-26be064eb1c0)

Запускаем и так как файл назывался missing_one_piece, а путь и переменные не совпадали, программа вывела/We run it and since the file was called missing_one_piece, and the path and variables did not match, the program output:
![4](https://github.com/user-attachments/assets/b4f12beb-f5d7-4246-9f1e-d8cb464ffe06)

