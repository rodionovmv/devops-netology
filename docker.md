1. https://hub.docker.com/r/rodionovmv/nginx

2.
```
Высоконагруженное монолитное java веб-приложение;
 - физический сервер, т.к. монолитное, селдовательно в микросерверах не реализуемо без изменения кода,
   и так как высоконагруженное -  то необходим физический доступ к ресурсами, без использования гипервизора виртуалки. 

Nodejs веб-приложение;
 - это веб приложение, для таких приложений достаточно докера, противопоказаний для контейнерной реализации не вижу.
   и в рамках микропроцессрной архитектуры может быть хорошим решением. 
 
Мобильное приложение c версиями для Android и iOS;
 - Виртаулка -  приложение в докере не имеет GUI, а это по описанию не вариант. 

Шина данных на базе Apache Kafka;
 - зависит от передаваемых данных или контура (тест/прод), для прода и критичности данных лучше Вируалка, для теста достаточно Контейнерной реализации, 
   если потеря данных при потере контенйера не является критичной то можно и в контейнере. 

Elastic stack для реализации логирования продуктивного веб-приложения - три ноды elasticsearch, два logstash и две ноды kibana;
 - сам Elasticsearvh лучше на виртуалку, отказоустойчивость решается на уровне кластера, 
   кибану и логсташ можно вынести в докер контейнер, или так же на виртуалках.
   использование в Докере может дать "+" для использования в тестовом периоде с максимальным функционалом, 
   в докере можно перезаливать машину каждый месяц и получать опять полную функциональность

Мониторинг-стек на базе prometheus и grafana;
 - сами системы не хранят как таковых данны, можно развернуть на Докере,
   минусов не вижу, в + скорость развертывания, возможность масштабирования для различных задачь: 
     например у графаны есть ограничение на число обрабатываемых метрик и может понадобиться разделения под разные задачи (контуры)

Mongodb, как основное хранилище данных для java-приложения;
 - можно использовать Виртуальную машину, т.к. хранилище и не сказано что высоконагруженное
   в Контейнере хранить БД с данными не подходит
   для физического сервера может быть через чур расточительно

Gitlab сервер для реализации CI/CD процессов и приватный (закрытый) Docker Registry;
 - виртуальная машина, т.к. серверу GitLab не требуется масштабирование или деплой новой версии несколько раз в день, 
   а виртуальная машина позволит очень удобно делать бекапы и при необходимости мигрировать её в кластере.
```

3. 
```commandline
vagrant@server1:~/nginx$ docker pull centos
Using default tag: latest
latest: Pulling from library/centos
a1d0c7532777: Pull complete
Digest: sha256:a27fd8080b517143cbbbab9dfb7c8571c40d67d534bbdee55bd6c473f432b177
Status: Downloaded newer image for centos:latest
docker.io/library/centos:latest

vagrant@server1:~/nginx$ docker pull debian
Using default tag: latest
latest: Pulling from library/debian
e756f3fdd6a3: Pull complete
Digest: sha256:3f1d6c17773a45c97bd8f158d665c9709d7b29ed7917ac934086ad96f92e4510
Status: Downloaded newer image for debian:latest
docker.io/library/debian:latest

vagrant@server1:~/nginx$ docker run -it --rm -d --name centos -v $(pwd)/data:/data centos
f04573d3c88e9a74cd7b358bf510cfcc021df189c91f40fb9802b815d2fe02f2
vagrant@server1:~/nginx$ docker run -it --rm -d --name debian -v $(pwd)/data:/data debian
c108ceea3c0c6565e61fe10e0c169a9bf70b272cff584112c4e5e065cdedd662

vagrant@server1:~/nginx$ docker ps
CONTAINER ID   IMAGE     COMMAND       CREATED         STATUS         PORTS     NAMES
c108ceea3c0c   debian    "bash"        7 minutes ago   Up 7 minutes             debian
f04573d3c88e   centos    "/bin/bash"   8 minutes ago   Up 8 minutes             centos

vagrant@server1:~/nginx$ docker exec -it centos bash
[root@f04573d3c88e /]# echo "Hello Netology from Centos" > /data/centos.txt
[root@f04573d3c88e /]# exit
exit

vagrant@server1:~/nginx$ echo "Hello Netology from host:server1" > data/host.txt

vagrant@server1:~/nginx$ docker exec -it debian bash
root@c108ceea3c0c:/# ls -l /data/
total 8
-rw-r--r-- 1 root root 27 May 29 20:08 centos.txt
-rw-rw-r-- 1 1000 1000 33 May 29 20:10 host.txt
```