## Задача 1

    Используя docker поднимите инстанс MySQL (версию 8). Данные БД сохраните в volume.
    Изучите бэкап БД и восстановитесь из него.
    Перейдите в управляющую консоль mysql внутри контейнера.
    Используя команду \h получите список управляющих команд.
    Найдите команду для выдачи статуса БД и приведите в ответе из ее вывода версию сервера БД.
    Подключитесь к восстановленной БД и получите список таблиц из этой БД.
    Приведите в ответе количество записей с price > 300.
    В следующих заданиях мы будем продолжать работу с данным контейнером.


Качаем контейнер
```shell
vagrant@server1:~$ docker pull mysql:8
8: Pulling from library/mysql
051f419db9dd: Pull complete
7627573fa82a: Pull complete
a44b358d7796: Pull complete
95753aff4b95: Pull complete
a1fa3bee53f4: Pull complete
f5227e0d612c: Pull complete
b4b4368b1983: Pull complete
f26212810c32: Pull complete
d803d4215f95: Pull complete
d5358a7f7d07: Pull complete
435e8908cd69: Pull complete
Digest: sha256:b9532b1edea72b6cee12d9f5a78547bd3812ea5db842566e17f8b33291ed2921
Status: Downloaded newer image for mysql:8
docker.io/library/mysql:8
```
Создаем volume для хранения БД
```
vagrant@server1:~$ docker volume create datamysql
datamysql
vagrant@server1:~$ docker volume ls
DRIVER    VOLUME NAME
local     0b8955b6e0efcfbe8288205e50fc2edd9bd62f25825401e522a2b4841922c410
local     027701771c4732f8adc6ccd1b68cc0cde9c200eaddc83be09984443002f9bd34
local     datamysql
vagrant@server1:~$ docker ps -a
CONTAINER ID   IMAGE                     COMMAND                  CREATED         STATUS                     PORTS                                                  NAM                                               ES
c7501e93169c   mysql:8                   "docker-entrypoint.s…"   5 minutes ago   Up 5 minutes               0.0.0.0:3306->3306/tcp, :::3306->3306/tcp, 33060/tcp   my-                                               mysql

vagrant@server1:~$ docker inspect my-mysql

      
        "Mounts": [
            {
                "Type": "volume",
                "Name": "datamysql",
                "Source": "/var/lib/docker/volumes/datamysql/_data",
                "Destination": "/data",
                "Driver": "local",
                "Mode": "z",
                "RW": true,
                "Propagation": ""
```
Запуск контейнера с паролем и директорией
```
vagrant@server1:~$ docker run --rm --name my-mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -dti -p 3306:3306 -v datamysql:/data mysql:8
aae5d3689a5250097ab9472800b80f03f35c15c54f0991c17fa8821213cee8af
```
Подкючение и создание БД test_bd

```shell
vagrant@server1:~$ mysql --host=172.17.0.2 --port=3306 --user=root --password=my-secret-pw
mysql: [Warning] Using a password on the command line interface can be insecure.
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 8
Server version: 8.0.30 MySQL Community Server - GPL

Copyright (c) 2000, 2022, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> \s
--------------
mysql  Ver 8.0.30-0ubuntu0.20.04.2 for Linux on x86_64 ((Ubuntu))

Connection id:          9
Current database:
Current user:           root@172.17.0.1
SSL:                    Cipher in use is TLS_AES_256_GCM_SHA384
Current pager:          stdout
Using outfile:          ''
Using delimiter:        ;
Server version:         8.0.30 MySQL Community Server - GPL
Protocol version:       10
Connection:             172.17.0.2 via TCP/IP
Server characterset:    utf8mb4
Db     characterset:    utf8mb4
Client characterset:    utf8mb4
Conn.  characterset:    utf8mb4
TCP port:               3306
Binary data as:         Hexadecimal
Uptime:                 7 min 19 sec

Threads: 2  Questions: 10  Slow queries: 0  Opens: 137  Flush tables: 3  Open tables: 56  Queries per second avg: 0.022
--------------

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
4 rows in set (0.01 sec)

mysql> CREATE DATABASE test_db;
Query OK, 1 row affected (0.03 sec)

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
| test_db            |
+--------------------+
5 rows in set (0.00 sec)
```
Импортируем БД

```shell
vagrant@server1:~$ docker exec -i my-mysql sh -c 'exec mysql -uroot -p"$MYSQL_ROOT_PASSWORD" --database=test_db' -d test_db < test_dump.sql
mysql: [Warning] Using a password on the command line interface can be insecure.

vagrant@server1:~$ tree /data/
/data/
├── auto.cnf
├── binlog.000001
├── binlog.000002
├── binlog.index
├── c7501e93169c.err
├── ca-key.pem
├── ca.pem
├── client-cert.pem
├── client-key.pem
├── #ib_16384_0.dblwr
├── #ib_16384_1.dblwr
├── ib_buffer_pool
├── ibdata1
├── #innodb_redo [error opening dir]
├── #innodb_temp [error opening dir]
├── mysql [error opening dir]
├── mysql.ibd
├── mysql.sock -> /var/run/mysqld/mysqld.sock
├── performance_schema [error opening dir]
├── private_key.pem
├── public_key.pem
├── server-cert.pem
├── server-key.pem
├── sys [error opening dir]
├── test_db [error opening dir]
├── undo_001
└── undo_002

vagrant@server1:~$ mysql --host=172.17.0.2 --port=3306 --user=root --password=my-secret-pw
mysql: [Warning] Using a password on the command line interface can be insecure.
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 11
Server version: 8.0.30 MySQL Community Server - GPL

Copyright (c) 2000, 2022, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
| test_db            |
+--------------------+
5 rows in set (0.00 sec)

mysql> use test_db;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> show tables;
+-------------------+
| Tables_in_test_db |
+-------------------+
| orders            |
+-------------------+
1 row in set (0.01 sec)
```
Приведите в ответе количество записей с price > 300
```
mysql> select * from orders;
+----+-----------------------+-------+
| id | title                 | price |
+----+-----------------------+-------+
|  1 | War and Peace         |   100 |
|  2 | My little pony        |   500 |
|  3 | Adventure mysql times |   300 |
|  4 | Server gravity falls  |   300 |
|  5 | Log gossips           |   123 |
+----+-----------------------+-------+
5 rows in set (0.00 sec)

mysql> select * from orders where price > 300;
+----+----------------+-------+
| id | title          | price |
+----+----------------+-------+
|  2 | My little pony |   500 |
+----+----------------+-------+
1 row in set (0.00 sec)
```
## Задача 2

    Создайте пользователя test в БД c паролем test-pass, используя:
        -   плагин авторизации mysql_native_password
        -   срок истечения пароля - 180 дней
        -   количество попыток авторизации - 3
        -   максимальное количество запросов в час - 100
        -   аттрибуты пользователя:
            -   Фамилия "Pretty"
            -   Имя "James"
        -   Предоставьте привелегии пользователю test на операции SELECT базы test_db.
    Используя таблицу INFORMATION_SCHEMA.USER_ATTRIBUTES получите данные по пользователю test и приведите в ответе к задаче.

```shell
mysql> CREATE USER 'test'@'localhost'
    -> IDENTIFIED WITH mysql_native_password
    -> BY 'test-pass';
Query OK, 0 rows affected (0.02 sec)

mysql> ALTER USER 'test'@'localhost' ATTRIBUTE '{"fname":"James", "lname":"Pretty"}';
Query OK, 0 rows affected (0.01 sec)

mysql> ALTER USER 'test'@'localhost'
    -> IDENTIFIED BY 'test-pass'
    -> WITH MAX_QUERIES_PER_HOUR 100
    -> PASSWORD EXPIRE INTERVAL 180 DAY;
Query OK, 0 rows affected (0.01 sec)

mysql> ALTER USER 'test'@'localhost'
    -> IDENTIFIED BY 'test-pass'
    -> FAILED_LOGIN_ATTEMPTS 3 PASSWORD_LOCK_TIME 2;
Query OK, 0 rows affected (0.01 sec)

mysql> GRANT SELECT ON test_db.* TO 'test'@'localhost';
Query OK, 0 rows affected, 1 warning (0.01 sec)

mysql> select * from INFORMATION_SCHEMA.USER_ATTRIBUTES where user = 'test';
+------+-----------+---------------------------------------+
| USER | HOST      | ATTRIBUTE                             |
+------+-----------+---------------------------------------+
| test | localhost | {"fname": "James", "lname": "Pretty"} |
+------+-----------+---------------------------------------+
1 row in set (0.01 sec)
```
## Задача 3

    Установите профилирование SET profiling = 1. Изучите вывод профилирования команд SHOW PROFILES;.    
    Исследуйте, какой engine используется в таблице БД test_db и приведите в ответе.    
    Измените engine и приведите время выполнения и запрос на изменения из профайлера в ответе:    
        -   на MyISAM
        -   на InnoDB
 
```shell
mysql> SET SESSION profiling = 1;
Query OK, 0 rows affected, 1 warning (0.00 sec)

mysql> SELECT table_schema, table_name, engine FROM INFORMATION_SCHEMA.TABLES WHERE table_schema = 'test_db' AND table_name = 'orders';
+--------------+------------+--------+
| TABLE_SCHEMA | TABLE_NAME | ENGINE |
+--------------+------------+--------+
| test_db      | orders     | InnoDB |
+--------------+------------+--------+
1 row in set (0.01 sec)

mysql> ALTER TABLE orders ENGINE = MyISAM;
Query OK, 5 rows affected (0.07 sec)
Records: 5  Duplicates: 0  Warnings: 0

mysql> ALTER TABLE orders ENGINE = InnoDB;
Query OK, 5 rows affected (0.10 sec)
Records: 5  Duplicates: 0  Warnings: 0

mysql> SHOW PROFILES;
+----------+------------+----------------------------------------------------------------------------------------------------------------------------------------+
| Query_ID | Duration   | Query                                                                                                                                  |
+----------+------------+----------------------------------------------------------------------------------------------------------------------------------------+
|        1 | 0.00292550 | SELECT table_schema, table_name, engine FROM INFORMATION_SCHEMA.TABLES WHERE table_schema = 'test_db' AND table_name = 'orders'        |
|        2 | 0.07315025 | ALTER TABLE orders ENGINE = MyISAM                                                                                                     |
|        3 | 0.09996000 | ALTER TABLE orders ENGINE = InnoDB                                                                                                     |
+----------+------------+----------------------------------------------------------------------------------------------------------------------------------------+
3 rows in set, 1 warning (0.00 sec)

* *Переключение на MyISAM заняло 0,07 сек*
* *Переключение на InnoDB заняло 0,10 сек*
```

## Задача 4

    Изучите файл my.cnf в директории /etc/mysql.    
    Измените его согласно ТЗ (движок InnoDB):    
        -   Скорость IO важнее сохранности данных
        -   Нужна компрессия таблиц для экономии места на диске
        -   Размер буффера с незакомиченными транзакциями 1 Мб
        -   Буффер кеширования 30% от ОЗУ
        -   Размер файла логов операций 100 Мб
    Приведите в ответе измененный файл my.cnf.

```shell
vagrant@server1:/data$ docker exec -it my-mysql bash
bash-4.4# cat /proc/meminfo
MemTotal:        1000324 kB
```

<details><summary>my.cnf</summary>

```
# For advice on how to change settings please see
# http://dev.mysql.com/doc/refman/8.0/en/server-configuration-defaults.html

[mysqld]
#
# Remove leading # and set to the amount of RAM for the most important data
# cache in MySQL. Start at 70% of total RAM for dedicated server, else 10%.
innodb_buffer_pool_size = 300M
innodb_flush_log_at_trx_commit	=	0
innodb_file_per_table	=	ON
innodb_log_buffer_size	=	1M
innodb_log_file_size = 100M
# Remove leading # to turn on a very important data integrity option: logging
# changes to the binary log between backups.
# log_bin
#
# Remove leading # to set options mainly useful for reporting servers.
# The server defaults are faster for transactions and fast SELECTs.
# Adjust sizes as needed, experiment to find the optimal values.
# join_buffer_size = 128M
# sort_buffer_size = 2M
# read_rnd_buffer_size = 2M

# Remove leading # to revert to previous value for default_authentication_plugin,
# this will increase compatibility with older clients. For background, see:
# https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_default_authentication_plugin
# default-authentication-plugin=mysql_native_password
skip-host-cache
skip-name-resolve
datadir=/var/lib/mysql
socket=/var/run/mysqld/mysqld.sock
secure-file-priv=/var/lib/mysql-files
user=mysql

pid-file=/var/run/mysqld/mysqld.pid
[client]
socket=/var/run/mysqld/mysqld.sock

!includedir /etc/mysql/conf.d/
```
</details>

Подкинем отредактированный конфиг на сервер и проверим параметры mysql
```shell
vagrant@server1:/data$ docker run --rm --name my-mysql -v /data:/etc/mysql/conf.d -e MYSQL_ROOT_PASSWORD=my-secret-pw -dti -p 3306:3306 mysql:8

vagrant@server1:/data$ docker exec -it my-mysql bash

bash-4.4# /usr/sbin/mysqld --verbose --help
~~~
innodb-flush-log-at-trx-commit                               0
innodb-file-per-table                                        TRUE
innodb-log-buffer-size                                       1048576
innodb-buffer-pool-size                                      314572800
innodb-log-file-size                                         104857600
~~~
```

<details><summary>Docker+MySQL info</summary>
https://hub.docker.com/_/mysql
</details>
