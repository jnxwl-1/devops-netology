Домашнее задание к занятию "3.2. Работа в терминале, лекция 2"

1. Какого типа команда cd? Попробуйте объяснить, почему она именно такого типа; опишите ход своих мыслей,
если считаете что она могла бы быть другого типа.

CD - встроенная команда. Ничего не приходит на ум (:

2. Rакая альтернатива без pipe команде grep <some_string> <some_file> | wc -l? man grep поможет в ответе на этот вопрос.
Ознакомьтесь с документом о других подобных некорректных вариантах использования pipe.

test.log contents:
this line is the 1st lower case line in this file.
Two lines above this line is empty.
And this is the last line.

wc -l < <(grep "this" test.log)

result: 3

3. Какой процесс с PID 1 является родителем для всех процессов в вашей виртуальной машине Ubuntu 20.04?

ps aux

result:
root           1  0.0  0.3 168708 12656 ?        Ss   15:09   0:05 /sbin/init splash

4. Как будет выглядеть команда, которая перенаправит вывод stderr ls на другую сессию терминала?

stderr ls 2>/dev/pts/2

5. Получится ли одновременно передать команде файл на stdin и вывести ее stdout в другой файл? Приведите работающий пример.

sort <test.log >sort_output.log

6. Получится ли вывести находясь в графическом режиме данные из PTY в какой-либо из эмуляторов TTY? Сможете ли вы наблюдать выводимые данные?

echo forward to tty from pts1 to tty1 >/dev/tty1

7. Выполните команду bash 5>&1. К чему она приведет? Что будет, если вы выполните echo netology > /proc/$$/fd/5? Почему так происходит?

bash 5>&1 - Создает дескриптор со значением 5. Затем идет перенаправление в STDOUT.

echo netology > /proc/$$/fd/5 - Выводит в дескриптор значение 5, который был пернеаправлен в STDOUT.

8. Получится ли в качестве входного потока для pipe использовать только stderr команды, не потеряв при этом отображение stdout на pty?
Напоминаем: по умолчанию через pipe передается только stdout команды слева от | на stdin команды справа.
Это можно сделать, поменяв стандартные потоки местами через промежуточный новый дескриптор, который вы научились создавать в предыдущем вопросе.

ll. ls /home 3>&2 2>&1 1>&3

9. Что выведет команда c at /proc/$$/environ? Как еще можно получить аналогичный по содержанию вывод?

Вывод переменных окружения.

env или printenv.

10. Используя man, опишите что доступно по адресам /proc/<PID>/cmdline, /proc/<PID>/exe.

/proc/<PID>/cmdline - путь до исполняемого файла процесса
/proc/<PID>/exe - сслыка на запущенный файл для процесса

11. Узнайте, какую наиболее старшую версию набора инструкций SSE поддерживает ваш процессор с помощью /proc/cpuinfo.

grep sse /proc/cpuinfo

sse4_2

12. При открытии нового окна терминала и vagrant ssh создается новая сессия и выделяется pty.
Это можно подтвердить командой tty, которая упоминалась в лекции 3.2. Однако:

vagrant@netology1:~$ ssh localhost 'tty'
not a tty
Почитайте, почему так происходит, и как изменить поведение.

Возможно следует добавить команду -t, для принудительного создания PTY.

13. Бывает, что есть необходимость переместить запущенный процесс из одной сессии в другую.
Попробуйте сделать это, воспользовавшись reptyr.
Например, так можно перенести в screen процесс, который вы запустили по ошибке в обычной SSH-сессии.

1. tty 2
/dev/pts/2

wget http://mirror.ufs.ac.za/linuxmint/stable/14/linuxmint-14-kde-dvd-64bit.iso

2. tty 5
/dev/pts/5

ps -a

PID TTY          TIME CMD
1581 tty2     00:00:04 Xorg
1637 tty2     00:00:00 gnome-session-b
4409 pts/1    00:00:00 sudo
4410 pts/1    00:00:00 bash
4505 pts/1    00:00:00 su
4506 pts/1    00:00:00 bash
4664 pts/1    00:00:00 sudo
4665 pts/1    00:00:00 bash
5369 pts/1    00:00:00 bash
5404 pts/1    00:00:00 su
5405 pts/1    00:00:00 bash
5436 pts/1    00:00:00 sudo
5437 pts/1    00:00:00 bash
5463 pts/1    00:00:00 man
5469 pts/1    00:00:00 pager
7172 pts/1    00:00:00 screen
8031 pts/3    00:00:00 screen
8042 pts/4    00:00:00 tmux: client
8066 pts/2    00:00:00 wget
8093 pts/5    00:00:00 ps

sudo reptyr 8066



14. sudo echo string > /root/new_file не даст выполнить перенаправление под обычным пользователем, так как перенаправлением занимается процесс shell'а,
который запущен без sudo под вашим пользователем.
Для решения данной проблемы можно использовать конструкцию echo string | sudo tee /root/new_file.
Узнайте что делает команда tee и почему в отличие от sudo echo команда с sudo tee будет работать.


Tee одновременно выводит в файл (параметр)и в STDOUT.

echo string | sudo tee /root/new_file - вывод из STDIN, перенаправленный через PIPE от STDOUT команды ECHO.
Команда запущена от sudo и имеет права на запись в файл.