# Практическое занятие №1. Введение, основы работы в командной строке

П.Н. Советов, РТУ МИРЭА

Научиться выполнять простые действия с файлами и каталогами в Linux из командной строки. Сравнить работу в командной строке Windows и Linux.

## Задача 1

Вывести отсортированный в алфавитном порядке список имен пользователей в файле passwd (вам понадобится grep).

### Программа

```bash
#!/usr/bin/env bash
# Задача 1: отсортированный список имён пользователей из passwd
grep -oE '^[^#:][^:]*' /etc/passwd | sort
```

### Результат

```text
$ ./task1.sh | head -n 10
_apt
backup
bin
claude
daemon
games
irc
list
lp
mail
...
```

## Задача 2

Вывести данные /etc/protocols в отформатированном и отсортированном порядке для 5 наибольших портов, как показано в примере ниже:

```
[root@localhost etc]# cat /etc/protocols ...
142 rohc
141 wesp
140 shim6
139 hip
138 manet
```

### Программа

```bash
#!/usr/bin/env bash
# Задача 2: 5 наибольших номеров протоколов из /etc/protocols
grep -v '^#' /etc/protocols | awk 'NF>=2 {print $2, $1}' | sort -rn | head -5
```

### Результат

```text
$ ./task2.sh
262 mptcp
143 ethernet
142 rohc
141 wesp
140 shim6
```

## Задача 3

Написать программу banner средствами bash для вывода текстов, как в следующем примере (размер баннера должен меняться!):

```
[root@localhost ~]# ./banner "Hello from RTU MIREA!"
+-----------------------+
| Hello from RTU MIREA! |
+-----------------------+
```

Перед отправкой решения проверьте его в ShellCheck на предупреждения.

### Программа

```bash
#!/usr/bin/env bash
# Задача 3: вывод текста в рамке (ширина зависит от текста)
text="$*"
[ -z "$text" ] && { echo "Usage: $0 text" >&2; exit 1; }
border=$(printf '%*s' $(( ${#text} + 2 )) '' | tr ' ' '-')
printf '+%s+\n| %s |\n+%s+\n' "$border" "$text" "$border"
```

### Результат

```text
$ ./banner "Hello from RTU MIREA!"
+-----------------------+
| Hello from RTU MIREA! |
+-----------------------+

$ ./banner "Ok"
+----+
| Ok |
+----+
```

## Задача 4

Написать программу для вывода всех идентификаторов (по правилам C/C++ или Java) в файле (без повторений).

Пример для hello.c:

```
h hello include int main n printf return stdio void world
```

### Программа

```bash
#!/usr/bin/env bash
# Задача 4: все идентификаторы из файла без повторений
[ $# -ne 1 ] && { echo "Usage: $0 file" >&2; exit 1; }
grep -oE '[A-Za-z_][A-Za-z0-9_]*' "$1" | LC_ALL=C sort -u | paste -sd' ' -
```

### Результат

```text
$ cat testdata/hello.c
#include <stdio.h>
int main(void) {
    printf("hello world\n");
    return 0;
}

$ ./task4.sh testdata/hello.c
h hello include int main n printf return stdio void world
```

## Задача 5

Написать программу для регистрации пользовательской команды (правильные права доступа и копирование в /usr/local/bin).

Например, пусть программа называется reg:

```
./reg banner
```

В результате для banner задаются правильные права доступа и сам banner копируется в /usr/local/bin.

### Программа

```bash
#!/usr/bin/env bash
# Задача 5: регистрация команды (права 755 + копия в /usr/local/bin)
if [ $# -ne 1 ] || [ ! -f "$1" ]; then
    echo "Usage: $0 script" >&2
    exit 1
fi
chmod 755 "$1"
sudo mkdir -p /usr/local/bin
sudo cp "$1" /usr/local/bin/
echo "Registered: /usr/local/bin/$(basename "$1")"
```

### Результат

```text
$ ./reg banner
Registered: /usr/local/bin/banner

$ ls -l /usr/local/bin/banner
-rwxr-xr-x 1 root root 308 Sep 30 14:30 /usr/local/bin/banner

$ cd / && /usr/local/bin/banner "Hello from RTU MIREA!"
+-----------------------+
| Hello from RTU MIREA! |
+-----------------------+
```

## Задача 6

Написать программу для проверки наличия комментария в первой строке файлов с расширением c, js и py.

### Программа

```bash
#!/usr/bin/env bash
# Задача 6: есть ли комментарий в первой строке файлов .c, .js, .py
dir="${1:-.}"
find "$dir" -type f \( -name '*.c' -o -name '*.js' -o -name '*.py' \) | sort |
while IFS= read -r f; do
    first=$(head -n 1 "$f")
    case "$f" in
        *.py) re='^[[:space:]]*#' ;;
        *)    re='^[[:space:]]*(//|/\*)' ;;
    esac
    if printf '%s\n' "$first" | grep -Eq "$re"; then
        echo "комментарий есть: $f"
    else
        echo "комментария нет:  $f"
    fi
done
```

### Результат

```text
$ ./task6.sh testdata/t6
комментарий есть: testdata/t6/a.c
комментария нет:  testdata/t6/b.c
комментарий есть: testdata/t6/c.py
комментария нет:  testdata/t6/d.py
комментарий есть: testdata/t6/e.js
```

## Задача 7

Написать программу для нахождения файлов-дубликатов (имеющих 1 или более копий содержимого) по заданному пути (и подкаталогам).

### Программа

```bash
#!/usr/bin/env bash
# Задача 7: поиск файлов-дубликатов (по SHA-256 содержимого)
[ $# -ne 1 ] && { echo "Usage: $0 dir" >&2; exit 1; }
find "$1" -type f -exec shasum -a 256 {} + | sort | awk '
{
    hash = $1
    file = substr($0, 67)
    if (hash == prev) {
        if (!printed) { print "--- дубликаты:"; print prevfile; printed = 1 }
        print file
    } else {
        printed = 0
    }
    prev = hash
    prevfile = file
}'
```

### Результат

```text
$ ./task7.sh testdata/t7
--- дубликаты:
testdata/t7/a.txt
testdata/t7/c.txt
testdata/t7/sub/b.txt
```

## Задача 8

Написать программу, которая находит все файлы в данном каталоге с расширением, указанным в качестве аргумента и архивирует все эти файлы в архив tar.

### Программа

```bash
#!/usr/bin/env bash
# Задача 8: архивировать файлы с заданным расширением в tar
if [ $# -lt 1 ]; then
    echo "Usage: $0 extension [dir]" >&2
    exit 1
fi
ext="${1#.}"
dir="${2:-.}"
out="archive_${ext}.tar"

files=()
while IFS= read -r -d '' f; do
    files+=("$f")
done < <(find "$dir" -maxdepth 1 -type f -name "*.$ext" -print0)

if [ ${#files[@]} -eq 0 ]; then
    echo "No .$ext files in $dir" >&2
    exit 1
fi
tar -cf "$out" "${files[@]}"
echo "Создан архив $out:"
tar -tf "$out"
```

### Результат

```text
$ cd testdata && ../task8.sh txt t8
Создан архив archive_txt.tar:
t8/b.txt
t8/a.txt
```

## Задача 9

Написать программу, которая заменяет в файле последовательности из 4 пробелов на символ табуляции. Входной и выходной файлы задаются аргументами.

### Программа

```bash
#!/usr/bin/env bash
# Задача 9: замена 4 пробелов на табуляцию
if [ $# -ne 2 ]; then
    echo "Usage: $0 input output" >&2
    exit 1
fi
tab=$(printf '\t')
sed -E "s/ {4}/$tab/g" "$1" > "$2"
```

### Результат

```text
$ ./task9.sh testdata/in.txt testdata/out.txt && cat -et testdata/out.txt
aaaa^Ibbbb^I^Icccc$
```

## Задача 10

Написать программу, которая выводит названия всех пустых текстовых файлов в указанной директории. Директория передается в программу параметром.

### Программа

```bash
#!/usr/bin/env bash
# Задача 10: пустые текстовые файлы в каталоге
if [ $# -ne 1 ] || [ ! -d "$1" ]; then
    echo "Usage: $0 directory" >&2
    exit 1
fi
find "$1" -type f -name '*.txt' -empty
```

### Результат

```text
$ ./task10.sh testdata/t10
testdata/t10/empty2.txt
testdata/t10/empty1.txt
```
