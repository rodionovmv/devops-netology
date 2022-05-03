1. 
```
 - Будет ошибка, т.к. типы не соответсвуют для операции , int и str
 - привести a к строке:       c=str(a)+b
 - привести b к целому числу: c=a+int(b)
```

2. 
```
Лишняя переменная is_change и команда breake, которая прерывает обработку при первом же найденом вхождении


#!/usr/bin/env python3

import os

bash_command = ["cd ~/devops-netology/sysadmin-homeworks", "git status"]
result_os = os.popen(' && '.join(bash_command)).read()
#is_change = False
for result in result_os.split('\n'):
    if result.find('modified') != -1:
        prepare_result = result.replace('\tmodified:   ', '')
        print(prepare_result)
#        break

redhat@ubuntu:~/netology/sysadm-homeworks$ ./script_py.sh
/home/redhat/devops-netology/sysadm-homeworks/FS.md
/home/redhat/devops-netology/sysadm-homeworks/OS_1.md
/home/redhat/devops-netology/sysadm-homeworks/OS_2.md

```
3. 
```
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
        
        
redhat@ubuntu:~$ ./4script.sh
Введите полный путь к репозиторию(со знаком '/' в конце):
/home/redhat/devops-netology/sysadm-homeworks/
modified:/home/redhat/devops-netology/sysadm-homeworks/FS.md
modified:/home/redhat/devops-netology/sysadm-homeworks/OS_1.md
modified:/home/redhat/devops-netology/sysadm-homeworks/OS_2.md
new file:/home/redhat/devops-netology/sysadm-homeworks/script_py.sh
deleted:/home/redhat/devops-netology/sysadm-homeworks/has_been_moved.txt
```

4. 
```
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
 
 
 
C:\Users\red_hat>C:\Users\red_hat\devops-netology\venv\Scripts\python.exe C:\Users\red_hat\devops-netology\scratch_3.py
*** start script ***
{'drive.google.com': '0.0.0.0', 'mail.google.com': '0.0.0.0', 'google.com': '0.0.0.0'}
********************
2022-05-03 16:43:00 [ERROR] drive.google.com IP mistmatch: 0.0.0.0 64.233.164.194
2022-05-03 16:43:00 [ERROR] mail.google.com IP mistmatch: 0.0.0.0 74.125.131.18
2022-05-03 16:43:00 [ERROR] google.com IP mistmatch: 0.0.0.0 142.250.74.142  
 
```