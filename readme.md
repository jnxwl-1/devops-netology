8. HISTFILESIZE (480).
   Позволяет спользовать обе опции ignoredups и ignorespace.
9. Автоматизируют создание, к примеру, 100 каталогов. (683).
10. touch file-{1..100000}, Argument list too long (: победил командой echo file-{1..300000} | xargs touch.
11. [[]] - завуалированная команда test, которая возвращает статус 0 (успешно) или другой статус (неуспешно) в зависимости от аргументов. [[ -d /tmp ]] - проверяет является ли /tmp директорией.
12. Не получается решить. Прошу дать наводящий комментарий. (export PATH="${PATH}:/tmp/new_path_directory/bash")
13. Команда at выполняет задачи в назначенное время. Команда batch выполняет задачи во время периодов низкой загрузки системы.
14. vagrant halt


UPD (03.10.2021)
12. 
mkdir /tmp/new_bash_dir/
cp /bin/bash /tmp/new_bash_dir/
PATH=/tmp/new_bash_dir/:$PATH