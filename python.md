# Домашнее задание к занятию "4.2. Использование Python для решения типовых DevOps задач"

## Обязательная задача 1

Есть скрипт:
```python
#!/usr/bin/env python3
a = 1
b = '2'
c = a + b
```

### Вопросы:
| Вопрос  | Ответ |
| ------------- | ------------- |
| Какое значение будет присвоено переменной `c`?  | Traceback (most recent call last): File "", line 1, in TypeError: unsupported operand type(s) for +: 'int' and 'str  |
| Как получить для переменной `c` значение 12?  | c=str(a)+b  |
| Как получить для переменной `c` значение 3?  | c=a+int(b)  |

## Обязательная задача 2
Мы устроились на работу в компанию, где раньше уже был DevOps Engineer. Он написал скрипт, позволяющий узнать, какие файлы модифицированы в репозитории, относительно локальных изменений. Этим скриптом недовольно начальство, потому что в его выводе есть не все изменённые файлы, а также непонятен полный путь к директории, где они находятся. Как можно доработать скрипт ниже, чтобы он исполнял требования вашего руководителя?

```python
#!/usr/bin/env python3

import os

bash_command = ["cd ~/devops-netology/sysadm-homeworks", "git status"]
result_os = os.popen(' && '.join(bash_command)).read()
is_change = False
for result in result_os.split('\n'):
    if result.find('modified') != -1:
        prepare_result = result.replace('\tmodified:   ', '')
        print(prepare_result)
        break
```

### Ваш скрипт:
```python
#!/usr/bin/env python3

import os

bash_command = ["cd ~/devops-netology/sysadm-homeworks", "git status"]
result_os = os.popen(' && '.join(bash_command)).read()
#is_change = False
for result in result_os.split('\n'):
    if result.find('modified') != -1:
        prepare_result = result.replace('\tmodified:   ', '')
        print(prepare_result)
#        break

```

### Вывод скрипта при запуске при тестировании:
```
redhat@ubuntu:~/devops-netology/sysadm-homeworks$ ./script_py.sh
/home/redhat/devops-netology/sysadm-homeworks/FS.md
/home/redhat/devops-netology/sysadm-homeworks/OS_1.md
/home/redhat/devops-netology/sysadm-homeworks/OS_2.md
```

## Обязательная задача 3
1. Доработать скрипт выше так, чтобы он мог проверять не только локальный репозиторий в текущей директории, а также умел воспринимать путь к репозиторию, который мы передаём как входной параметр. Мы точно знаем, что начальство коварное и будет проверять работу этого скрипта в директориях, которые не являются локальными репозиториями.

### Ваш скрипт:
```python
#!/usr/bin/env python3
print("Введите полный путь к репозиторию(со знаком '/' в конце):")
path = input()

import os

os.chdir(path)
bash_command = ["git status"]
result_os = os.popen(' && '.join(bash_command)).read()
is_change = False
for result in result_os.split('\n'):
    if result.find('modified') != -1:
        mod_result = result.replace('\tmodified:   ', '')
        print("modified:"+path+mod_result)
for result in result_os.split('\n'):
    if result.find('new file') != -1:
        new_result = result.replace('\tnew file:   ', '')
        print("new file:"+path+new_result)
for result in result_os.split('\n'):
    if result.find('deleted') != -1:
        del_result = result.replace('\tdeleted:   ', '')
        print("deleted:"+path+del_result)
```

### Вывод скрипта при запуске при тестировании:
```
redhat@ubuntu:~$ ./4script.sh
Введите полный путь к репозиторию(со знаком '/' в конце):
/home/redhat/devops-netology/sysadm-homeworks/
modified:/home/redhat/devops-netology/sysadm-homeworks/FS.md
modified:/home/redhat/devops-netology/sysadm-homeworks/OS_1.md
modified:/home/redhat/devops-netology/sysadm-homeworks/OS_2.md
new file:/home/redhat/devops-netology/sysadm-homeworks/script_py.sh
deleted:/home/redhat/devops-netology/sysadm-homeworks/has_been_moved.txt

```

## Обязательная задача 4
1. Наша команда разрабатывает несколько веб-сервисов, доступных по http. Мы точно знаем, что на их стенде нет никакой балансировки, кластеризации, за DNS прячется конкретный IP сервера, где установлен сервис. Проблема в том, что отдел, занимающийся нашей инфраструктурой очень часто меняет нам сервера, поэтому IP меняются примерно раз в неделю, при этом сервисы сохраняют за собой DNS имена. Это бы совсем никого не беспокоило, если бы несколько раз сервера не уезжали в такой сегмент сети нашей компании, который недоступен для разработчиков. Мы хотим написать скрипт, который опрашивает веб-сервисы, получает их IP, выводит информацию в стандартный вывод в виде: <URL сервиса> - <его IP>. Также, должна быть реализована возможность проверки текущего IP сервиса c его IP из предыдущей проверки. Если проверка будет провалена - оповестить об этом в стандартный вывод сообщением: [ERROR] <URL сервиса> IP mismatch: <старый IP> <Новый IP>. Будем считать, что наша разработка реализовала сервисы: `drive.google.com`, `mail.google.com`, `google.com`.

### Ваш скрипт:
```python
##!/usr/bin/env python3

import socket as s
import time as t
import datetime as dt

# set variables 
i = 1
wait = 2 # интервал проверок в секундах
srv = {'drive.google.com':'0.0.0.0', 'mail.google.com':'0.0.0.0', 'google.com':'0.0.0.0'}
init=0

print('*** start script ***')
print(srv)
print('********************')

while 1==1 : #отладочное число проверок 
  for host in srv:
    ip = s.gethostbyname(host)
    if ip != srv[host]:
      if i==1 and init !=1:
        print(str(dt.datetime.now().strftime("%Y-%m-%d %H:%M:%S")) +' [ERROR] ' + str(host) +' IP mistmatch: '+srv[host]+' '+ip)
      srv[host]=ip
# счетчик итераций для отладки, закомментировать для бесконечного цикла 3 строки
  i+=1 
  if i >= 50 : 
    break
  t.sleep(wait)

```

### Вывод скрипта при запуске при тестировании:
```
C:\Users\red_hat>C:\Users\red_hat\devops-netology\venv\Scripts\python.exe C:\Users\red_hat\devops-netology\scratch_3.py
*** start script ***
{'drive.google.com': '0.0.0.0', 'mail.google.com': '0.0.0.0', 'google.com': '0.0.0.0'}
********************
2022-05-03 16:43:00 [ERROR] drive.google.com IP mistmatch: 0.0.0.0 64.233.164.194
2022-05-03 16:43:00 [ERROR] mail.google.com IP mistmatch: 0.0.0.0 74.125.131.18
2022-05-03 16:43:00 [ERROR] google.com IP mistmatch: 0.0.0.0 142.250.74.142  

```