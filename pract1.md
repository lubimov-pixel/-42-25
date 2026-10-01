# Практическое занятие №1. Введение, основы работы в командной строке

П.Н. Советов, РТУ МИРЭА

Научиться выполнять простые действия с файлами и каталогами в Linux из командной строки. Сравнить работу в командной строке Windows и Linux.

## Задача 1

Вывести отсортированный в алфавитном порядке список имен пользователей в файле passwd (вам понадобится grep).

### Программа

```bash
grep -v '^#' /etc/passwd | cut -d: -f1 | sort
```

### Результат

```text
_accessoryupdater
_amavisd
_analyticsd
_aonsensed
_appinstalld
_appleevents
_applepay
_appowner
_appserver
_appstore
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
grep -v '^#' /etc/protocols | awk 'NF>=2 {print $2, $1}' | sort -rn | head -5
```

### Результат

```text
258 divert
240 pfsync
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
text="$*"
[ -z "$text" ] && { echo "Usage: $0 text" >&2; exit 1; }
border=$(printf '%*s' $(( ${#text} + 2 )) '' | tr ' ' '-')
printf '+%s+\n| %s |\n+%s+\n' "$border" "$text" "$border"
```

### Результат

```text
+-----------------------+
| Hello from RTU MIREA! |
+-----------------------+

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

