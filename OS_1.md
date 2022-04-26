1. 
```
системный вызов команды CD -> chdir("/tmp")
```
2. 
```
Файл базы типов - /usr/share/misc/magic.mgc
в тексте это:

openat(AT_FDCWD, "/usr/share/misc/magic.mgc", O_RDONLY) = 3
```
3. 
```
vagrant@vagrant:~$ lsof -p 2070
...
nano      2070 vagrant    4u   REG  253,0    12288  526898 /home/vagrant/test_lsof.swp (deleted)

vagrant@vagrant:~$ echo '' >/proc/2070/fd/4


где 2070 - PID процесса nano
4 - дескриптор файла , который предварительно удалил. 
```
4. 
```
Зомби не занимают ресурсов, но блокируют записи в таблице процессов. При заполнении лимита таблицы процессов возможно "зависание" сервисов. Убрать их может либо родительский процесс, либо его завершение
```
5. 
```
vagrant@vagrant:~$ sudo /usr/sbin/opensnoop-bpfcc
PID    COMM               FD ERR PATH
905    vminfo              5   0 /var/run/utmp
657    dbus-daemon        -1   2 /usr/local/share/dbus-1/system-services
657    dbus-daemon        17   0 /usr/share/dbus-1/system-services
657    dbus-daemon        -1   2 /lib/dbus-1/system-services
657    dbus-daemon        17   0 /var/lib/snapd/dbus-1/system-services/
```

6.  
```
uname() returns system information in the structure pointed to by
       buf.  The utsname struct is defined in <sys/utsname.h>:

           struct utsname {
               char sysname[];    /* Operating system name (e.g., "Linux") */
               char nodename[];   /* Name within "some implementation-defined
                                     network" */
               char release[];    /* Operating system release
                                     (e.g., "2.6.28") */
               char version[];    /* Operating system version */
               char machine[];    /* Hardware identifier */
           #ifdef _GNU_SOURCE
               char domainname[]; /* NIS or YP domain name */
           #endif
           };
```
7. 
```
&& -  условный оператор, 
;  - разделитель последовательных команд

test -d /tmp/some_dir && echo Hi - в данном случае echo отработает только при успешном заверщении команды test

set -e - прерывает сессию при любом ненулевом значении исполняемых команд в конвеере кроме последней.
в случае &&  вместе с set -e не имеет смысла, так как при ошибке выполнение команд прекратится. 
```

8. 
```
-e прерывает выполнение исполнения при ошибке любой команды кроме последней в последовательности 
-x вывод трейса простых команд 
-u неустановленные/не заданные параметры и переменные считаются как ошибки, с выводом в stderr текста ошибки и выполнит завершение неинтерактивного вызова
-o pipefail возвращает код возврата набора/последовательности команд, ненулевой при последней команды или 0 для успешного выполнения команд.

Повышает деталезацию вывода ошибок (логирования) и завершит сценарий при наличии ошибок на любом этапе выполнения сценария, кроме последней завершающей команды.
```
9. 
```
S*(S,S+,Ss,Ssl,Ss+) - Процессы ожидающие завершения (спящие с прерыванием "сна")
I*(I,I<) - фоновые (бездействующие) процессы ядра

доп символы это доп характеристики (< , s, +)
```