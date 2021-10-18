Домашнее задание к занятию "3.3. Операционные системы, лекция 1"

1. Какой системный вызов делает команда cd? В прошлом ДЗ мы выяснили, что cd не является самостоятельной программой, это shell builtin, поэтому запустить strace непосредственно на cd не получится. Тем не менее, вы можете запустить strace на /bin/bash -c 'cd /tmp'. В этом случае вы увидите полный список системных вызовов, которые делает сам bash при старте. Вам нужно найти тот единственный, который относится именно к cd. Обратите внимание, что strace выдаёт результат своей работы в поток stderr, а не в stdout.


strace /bin/bash -c 'cd /tmp'

chdir("/tmp")


2. Попробуйте использовать команду file на объекты разных типов на файловой системе. Например:
vagrant@netology1:~$ file /dev/tty
/dev/tty: character special (5/0)
vagrant@netology1:~$ file /dev/sda
/dev/sda: block special (8/0)
vagrant@netology1:~$ file /bin/bash
/bin/bash: ELF 64-bit LSB shared object, x86-64
Используя strace выясните, где находится база данных file на основании которой она делает свои догадки.

strace /bin/bash -c 'file /dev/tty'

openat(AT_FDCWD, "/usr/share/misc/magic.mgc", O_RDONLY) = 4


3. Предположим, приложение пишет лог в текстовый файл. Этот файл оказался удален (deleted в lsof), однако возможности сигналом сказать приложению переоткрыть файлы или просто перезапустить приложение – нет. Так как приложение продолжает писать в удаленный файл, место на диске постепенно заканчивается. Основываясь на знаниях о перенаправлении потоков предложите способ обнуления открытого удаленного файла (чтобы освободить место на файловой системе).

Создаю в nano файл test.test, параллельно в другой сессии ввожу ll вижу, что создался .test.test.swp. Выхожу из nano.

Далее ввожу lsof -u jnxwl | grep deleted

lsof: WARNING: can't stat() tracefs file system /sys/kernel/debug/tracing
      Output information may be incomplete.
pulseaudi  1525 jnxwl    6u      REG                0,1 67108864       1036 /memfd:pulseaudio (deleted)
Xorg       1581 jnxwl   31u      REG                0,1        4       1044 /memfd:xshmfence (deleted)
Xorg       1581 jnxwl   44u      REG                0,1        4         42 /memfd:xshmfence (deleted)
Xorg       1581 jnxwl   49u      REG                0,1        4         65 /memfd:xshmfence (deleted)
Xorg       1581 jnxwl   51u      REG                0,1        4         64 /memfd:xshmfence (deleted)
gnome-she  1807 jnxwl   23r      REG                8,1       64     790302 /home/jnxwl/.local/share/gvfs-metadata/root (deleted)
gnome-she  1807 jnxwl   26r      REG                8,1    32768     790334 /home/jnxwl/.local/share/gvfs-metadata/root-29c77929.log (deleted)
evolution  1873 jnxwl   12r      REG                8,1      108     806620 /home/jnxwl/.local/share/gvfs-metadata/home (deleted)
evolution  1873 jnxwl   13r      REG                8,1    32768     806621 /home/jnxwl/.local/share/gvfs-metadata/home-ae631645.log (deleted)

Но в уопр не вижу файла со статусом deleted.

Не могу победить ошибку >
lsof: WARNING: can't stat() tracefs file system /sys/kernel/debug/tracing
      Output information may be incomplete.

В чем может быть проблема?



4. Занимают ли зомби-процессы какие-то ресурсы в ОС (CPU, RAM, IO)?

Зомби процессы Linux не выполняются и убить их нельзя, они продолжают висеть в памяти, пока не будет завершён их родительский процесс.
Зомби занимают чуть больше одной дополнительной строки в выводе ps и всегда освобождают ресурсы, в отличие от сирот.


5. В iovisor BCC есть утилита opensnoop:
root@vagrant:~# dpkg -L bpfcc-tools | grep sbin/opensnoop
/usr/sbin/opensnoop-bpfcc
На какие файлы вы увидели вызовы группы open за первую секунду работы утилиты? Воспользуйтесь пакетом bpfcc-tools для Ubuntu 20.04. Дополнительные сведения по установке.

PID    COMM               FD ERR PATH
477    vmtoolsd            9   0 /proc/meminfo
477    vmtoolsd            9   0 /proc/vmstat
477    vmtoolsd            9   0 /proc/stat
477    vmtoolsd            9   0 /proc/zoneinfo
477    vmtoolsd            9   0 /proc/uptime
477    vmtoolsd            9   0 /proc/diskstats
1      systemd            17   0 /proc/11102/cgroup


6. Какой системный вызов использует uname -a? Приведите цитату из man по этому системному вызову, где описывается альтернативное местоположение в /proc, где можно узнать версию ядра и релиз ОС.

uname()

Part of the utsname information is also accessible  via  /proc/sys/kernel/{ostype, hostname, osrelease, version, domainname}.


7. Чем отличается последовательность команд через ; и через && в bash? Например:
root@netology1:~# test -d /tmp/some_dir; echo Hi
Hi
root@netology1:~# test -d /tmp/some_dir && echo Hi
root@netology1:~#
Есть ли смысл использовать в bash &&, если применить set -e?

Команды, разделенные ; , выполняются последовательно независимо от их статуса завершения.

С && вторая команда выполняется только в том случае , если первая завершается успешно (возвращает статус выхода 0).


8. Из каких опций состоит режим bash set -euxo pipefail и почему его хорошо было бы использовать в сценариях?

-e - оболочка завершает работу, когда простая команда в списке команд завершается c ненулевым статусом.
-u - неустановленные/не заданные параметры и переменные считаются как ошибки. Неинтерактивная оболочка завершит свое выполнение.
-x - режим отладки. Перед выполнением команды печатает её со всеми уже развернутыми подстановками и вычислениями.
-o - устаналивает или снимает опцию по её длинному имени. Если никакой опции не задано, то выводится список всех опций и их статус.
pipefall - если установлено, код выхода из конвейера отличается от нормального («последняя команда в конвейере») поведения:TRUE когда ни одна команда не завершилась неудачно, FALSE когда что-то не удалось.

9. Используя -o stat для ps, определите, какой наиболее часто встречающийся статус у процессов в системе. В man ps ознакомьтесь (/PROCESS STATE CODES) что значат дополнительные к основной заглавной буквы статуса процессов. Его можно не учитывать при расчете (считать S, Ss или Ssl равнозначными).

S*(Ssl,Ss+,S+,S,Ss) - Процессы ожидающие завершения
I*(I,I<) - фоновые(бездействующие) процессы ядра
