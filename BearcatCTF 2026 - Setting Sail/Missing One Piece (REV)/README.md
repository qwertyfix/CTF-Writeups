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


Реверс-инжиниринг кода/Reverse engineering of code (Radare2)

Вводим дальше в консоль: r2 -A missing_one_piece
![5](https://github.com/user-attachments/assets/06f27233-8b6d-4ffd-8e75-4e05e534ce1a)

Анализ функции main: pdf @ main
![6](https://github.com/user-attachments/assets/b6489475-43aa-48bf-9762-2a06bf78160b)


По функции имеются три условия:
Условие 1 (0x000013cd): strcmp(argv[0], "./devilishFruit"). Программа должна быть запущена через файл с конкретным именем.
Условие 2 (0x1404 - 0x1474): Цикл по envp, ищущий PWD=/tmp/gogear5. Это значит, текущая рабочая директория должна быть именно такой.
Условие 3 (0x149a - 0x150a): Еще один цикл по envp, ищущий ONE_PIECE=IS_REAL.
Если все cmp проходят успешно, вызывается snprintf для сборки флага и секретная функция you_dont_need_to_rev_this.

Эксплуатация (Solution)
Для выполнения условий необходимо подготовить окружение:

Создаем нужную директорию и переходим в нее:
mkdir -p /tmp/gogear5 && cd /tmp/gogear5

Переименовываем бинарник (создаем копию):
cp /path/to/missing_one_piece ./devilishFruit
chmod 775 ./devilishFruit

Устанавливаем переменную окружения и запускаем:
export ONE_PIECE='IS_REAL'
./devilishFruit

Результат
После выполнения всех условий программа выдает флаг:
![7](https://github.com/user-attachments/assets/0fb5478f-0b3b-4c2e-9287-170bdb4f35ce)

Flag: BCCTF{I_gU3S5_7hAt_Wh1t3BeArD_6uY_W45_TRu7h1n6!}
