# Домашнее задание к занятию "3.4. Операционные системы, лекция 2"

1. На лекции мы познакомились с [node_exporter](https://github.com/prometheus/node_exporter/releases). В демонстрации его исполняемый файл запускался в background. Этого достаточно для демо, но не для настоящей production-системы, где процессы должны находиться под внешним управлением. Используя знания из лекции по systemd, создайте самостоятельно простой [unit-файл](https://www.freedesktop.org/software/systemd/man/systemd.service.html) для node_exporter:

    * поместите его в автозагрузку,
    * предусмотрите возможность добавления опций к запускаемому процессу через внешний файл (посмотрите, например, на `systemctl cat cron`),
    * удостоверьтесь, что с помощью systemctl процесс корректно стартует, завершается, а после перезагрузки автоматически поднимается.


1. sudo nano /etc/systemd/system/ne-daemon.service

2. ne-daemon.service>
[Unit]
Description=Node Exporter

[Service]
ExecStart=/home/jnxwl/devops/node_exporter/node_exporter

[Install]
WantedBy=default.target

3. systemctl daemon-reload

4. systemctl start ne-daemon.service

5. systemctl status ne-daemon.service

root@jnxwl-virtual-machine:~# systemctl status ne-daemon.service
● ne-daemon.service - Node Exporter
     Loaded: loaded (/etc/systemd/system/ne-daemon.service; enabled; vendor preset: enabled)
     Active: active (running) since Wed 2021-10-20 11:16:14 MSK; 6s ago
   Main PID: 12974 (node_exporter)
      Tasks: 5 (limit: 4612)
     Memory: 2.3M
     CGroup: /system.slice/ne-daemon.service
             └─12974 /home/jnxwl/devops/node_exporter/node_exporter

окт 20 11:16:14 jnxwl-virtual-machine node_exporter[12974]: level=info ts=2021-10-20T08:16:14.091Z caller=node_exporter.go:115 collector=thermal_zone
окт 20 11:16:14 jnxwl-virtual-machine node_exporter[12974]: level=info ts=2021-10-20T08:16:14.091Z caller=node_exporter.go:115 collector=time
окт 20 11:16:14 jnxwl-virtual-machine node_exporter[12974]: level=info ts=2021-10-20T08:16:14.091Z caller=node_exporter.go:115 collector=timex
окт 20 11:16:14 jnxwl-virtual-machine node_exporter[12974]: level=info ts=2021-10-20T08:16:14.091Z caller=node_exporter.go:115 collector=udp_queues
окт 20 11:16:14 jnxwl-virtual-machine node_exporter[12974]: level=info ts=2021-10-20T08:16:14.091Z caller=node_exporter.go:115 collector=uname
окт 20 11:16:14 jnxwl-virtual-machine node_exporter[12974]: level=info ts=2021-10-20T08:16:14.091Z caller=node_exporter.go:115 collector=vmstat
окт 20 11:16:14 jnxwl-virtual-machine node_exporter[12974]: level=info ts=2021-10-20T08:16:14.091Z caller=node_exporter.go:115 collector=xfs
окт 20 11:16:14 jnxwl-virtual-machine node_exporter[12974]: level=info ts=2021-10-20T08:16:14.091Z caller=node_exporter.go:115 collector=zfs
окт 20 11:16:14 jnxwl-virtual-machine node_exporter[12974]: level=info ts=2021-10-20T08:16:14.092Z caller=node_exporter.go:199 msg="Listening on" address=:9100
окт 20 11:16:14 jnxwl-virtual-machine node_exporter[12974]: level=info ts=2021-10-20T08:16:14.095Z caller=tls_config.go:191 msg="TLS is disabled." http2=false


6. ps aux | grep node_exporter


root       12974  0.0  0.2 716440 11892 ?        Ssl  11:16   0:00 /home/jnxwl/devops/node_exporter/node_exporter
root       12987  0.0  0.0  17676   672 pts/0    S+   11:19   0:00 grep --color=auto node_exporter


7. sudo ufw allow 9100

8. sudo ufw reload

9. С хостовой машины открывается 192.168.88.6:9100

Node Exporter
Metrics



2. Ознакомьтесь с опциями node_exporter и выводом `/metrics` по-умолчанию. Приведите несколько опций, которые вы бы выбрали для базового мониторинга хоста по CPU, памяти, диску и сети.

CPU:
node_cpu_seconds_total{cpu="0",mode="idle"} 80446.5
node_cpu_seconds_total{cpu="0",mode="iowait"} 2.42
node_cpu_seconds_total{cpu="0",mode="irq"} 0
node_cpu_seconds_total{cpu="0",mode="nice"} 4.06
node_cpu_seconds_total{cpu="0",mode="softirq"} 8.52
node_cpu_seconds_total{cpu="0",mode="steal"} 0
node_cpu_seconds_total{cpu="0",mode="system"} 75.25
node_cpu_seconds_total{cpu="0",mode="user"} 30.36
node_cpu_seconds_total{cpu="1",mode="idle"} 80445.29
node_cpu_seconds_total{cpu="1",mode="iowait"} 0.52
node_cpu_seconds_total{cpu="1",mode="irq"} 0
node_cpu_seconds_total{cpu="1",mode="nice"} 4.24
node_cpu_seconds_total{cpu="1",mode="softirq"} 2.59
node_cpu_seconds_total{cpu="1",mode="steal"} 0
node_cpu_seconds_total{cpu="1",mode="system"} 76.07
node_cpu_seconds_total{cpu="1",mode="user"} 35.07

Память
node_memory_MemAvailable_bytes 3.072741376e+09
node_memory_MemFree_bytes 1.74624768e+09

Диск
node_disk_io_time_seconds_total{device="sda"}
node_disk_read_bytes_total{device="sda"}
node_disk_read_time_seconds_total{device="sda"}
node_disk_write_time_seconds_total{device="sda"}

Сеть
node_network_receive_bytes_total{device="ens33"} 1.16545948e+08
node_network_receive_drop_total{device="ens33"} 683
node_network_receive_packets_total{device="ens33"} 133243
node_network_transmit_bytes_total{device="ens33"} 1.496995e+06


3. Установите в свою виртуальную машину [Netdata](https://github.com/netdata/netdata). Воспользуйтесь [готовыми пакетами](https://packagecloud.io/netdata/netdata/install) для установки (`sudo apt install -y netdata`). После успешной установки:
    * в конфигурационном файле `/etc/netdata/netdata.conf` в секции [web] замените значение с localhost на `bind to = 0.0.0.0`,
    * добавьте в Vagrantfile проброс порта Netdata на свой локальный компьютер и сделайте `vagrant reload`:

    ```bash
    config.vm.network "forwarded_port", guest: 19999, host: 19999
    ```

>
sudo lsof -i :19999
[sudo] password for jnxwl:
COMMAND PID    USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
netdata 864 netdata    4u  IPv4  39668      0t0  TCP *:19999 (LISTEN)
netdata 864 netdata   39u  IPv4  67992      0t0  TCP 192.168.88.7:19999->192.168.88.111:55466 (ESTABLISHED)
netdata 864 netdata   57u  IPv4  67997      0t0  TCP 192.168.88.7:19999->192.168.88.111:55473 (ESTABLISHED)
netdata 864 netdata   58u  IPv4  67446      0t0  TCP 192.168.88.7:19999->192.168.88.111:55467 (ESTABLISHED)
netdata 864 netdata   59u  IPv4  67999      0t0  TCP 192.168.88.7:19999->192.168.88.111:55474 (ESTABLISHED)
netdata 864 netdata   60u  IPv4  67994      0t0  TCP 192.168.88.7:19999->192.168.88.111:55468 (ESTABLISHED)
netdata 864 netdata   61u  IPv4  67995      0t0  TCP 192.168.88.7:19999->192.168.88.111:55469 (ESTABLISHED)





4. Можно ли по выводу `dmesg` понять, осознает ли ОС, что загружена не на настоящем оборудовании, а на системе виртуализации?


[    0.268659] Booting paravirtualized kernel on VMware hypervisor
[    5.133115] systemd[1]: Detected virtualization vmware.



5. Как настроен sysctl `fs.nr_open` на системе по-умолчанию? Узнайте, что означает этот параметр. Какой другой существующий лимит не позволит достичь такого числа (`ulimit --help`)?

jnxwl@jnxwl-virtual-machine:~$ /sbin/sysctl -n fs.nr_open
1048576

Если вызвать /sbin/sysctl -n fs.nr_open, получим число 1048576, это максимальное число дескрипторов для ядра на пользователя.

jnxwl@jnxwl-virtual-machine:~$ cat /proc/sys/fs/file-max
9223372036854775807

В данном случае предел будет равен числу 9223372036854775807.

jnxwl@jnxwl-virtual-machine:~$ ulimit -Sn
1024

Мягкий лимит на пользователя. Можно увеличить.

jnxwl@jnxwl-virtual-machine:~$ ulimit -Hn
1048576

Жесткий лимит на пользователя. Увеличить нельзя, только уменьшить.

Жесткий и мягкий лимит не могут превышать системный лимит.



6. Запустите любой долгоживущий процесс (не `ls`, который отработает мгновенно, а, например, `sleep 1h`) в отдельном неймспейсе процессов; покажите, что ваш процесс работает под PID 1 через `nsenter`. Для простоты работайте в данном задании под root (`sudo -i`). Под обычным пользователем требуются дополнительные опции (`--map-root-user`) и т.д.

root@jnxwl-virtual-machine:~# sleep 1h

jnxwl@jnxwl-virtual-machine:~$ ps -e |grep sleep
  10203 pts/1    00:00:00 sleep

  root@jnxwl-virtual-machine:~# nsenter --target 10203 --pid --mount
  
  root@jnxwl-virtual-machine:/# ps
      PID TTY          TIME CMD
    10389 pts/2    00:00:00 sudo
    10390 pts/2    00:00:00 bash
    10400 pts/2    00:00:00 nsenter
    10401 pts/2    00:00:00 bash
    10410 pts/2    00:00:00 ps



7. Найдите информацию о том, что такое `:(){ :|:& };:`. Запустите эту команду в своей виртуальной машине Vagrant с Ubuntu 20.04 (**это важно, поведение в других ОС не проверялось**). Некоторое время все будет "плохо", после чего (минуты) – ОС должна стабилизироваться. Вызов `dmesg` расскажет, какой механизм помог автоматической стабилизации. Как настроен этот механизм по-умолчанию, и как изменить число процессов, которое можно создать в сессии?


:(){ :|:& };: определяет функцию с именем : ,которая порождает саму себя (дважды, один канал переходит в другой) и создает фон.

ulimit -u 50  -  ограничения количества processes-per-user.


