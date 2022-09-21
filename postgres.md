##Задача 1

Используя docker поднимите инстанс PostgreSQL (версию 13). Данные БД сохраните в volume.        
    Подключитесь к БД PostgreSQL используя psql.        
    Воспользуйтесь командой \? для вывода подсказки по имеющимся в psql управляющим командам.        
    Найдите и приведите управляющие команды для:        
        -   вывода списка БД
        -   подключения к БД
        -   вывода списка таблиц
        -   вывода описания содержимого таблиц
        -   выхода из psql
```shell
vagrant@server1:~$ docker pull postgres:13
13: Pulling from library/postgres
latest: Pulling from library/postgres
31b3f1ad4ce1: Pull complete
dc97844d0cd5: Pull complete
9ad9b1166fde: Pull complete
286c4682b24d: Pull complete
1d3679a4a1a1: Pull complete
5f2e6cdc8503: Pull complete
0f7dc70f54e8: Pull complete
a090c7442692: Pull complete
81bfe40fd0f6: Pull complete
8ac8c22bbb38: Pull complete
96e51d1d3c6e: Pull complete
667bd4154fa2: Pull complete
87267fb600a9: Pull complete
Digest: sha256:a088584fed1f94c742c07ba160aee8f59419979a76b134c4b1366c69c56ae13c
Status: Downloaded newer image for postgres:13

vagrant@server1:~$ docker volume create datapostgres
datapostgres

vagrant@server1:~$ docker run --rm --name my-postgres -e POSTGRES_USER=postgres -e POSTGRES_PASSWORD=my-secret-pw -dti -p 5432:5432 -v datapostgres:/data postgres:13
9ee05de808665d717d7202a125c9f9b2bc37a9889659411f59d5b88e1fe5273a
vagrant@server1:~$ docker ps
CONTAINER ID   IMAGE         COMMAND                  CREATED         STATUS         PORTS                                       NAMES
9ee05de80866   postgres:13   "docker-entrypoint.s…"   6 seconds ago   Up 5 seconds   0.0.0.0:5432->5432/tcp, :::5432->5432/tcp   my-postgres

vagrant@server1:~$ docker volume ls
DRIVER    VOLUME NAME
local     0b8955b6e0efcfbe8288205e50fc2edd9bd62f25825401e522a2b4841922c410
local     027701771c4732f8adc6ccd1b68cc0cde9c200eaddc83be09984443002f9bd34
local     ab6aee029f0d763005ec3ee1462e800efdb7d294d499569b5ac9021065b1fb4f
local     datamysql
local     datapostgres

vagrant@server1:~$ psql -h localhost -p 5432 -U postgres
Password for user postgres:
psql (12.12 (Ubuntu 12.12-0ubuntu0.20.04.1), server 13.8 (Debian 13.8-1.pgdg110+1))
WARNING: psql major version 12, server major version 13.
         Some psql features might not work.
Type "help" for help.

postgres=# \l
                                 List of databases
   Name    |  Owner   | Encoding |  Collate   |   Ctype    |   Access privileges
-----------+----------+----------+------------+------------+-----------------------
 postgres  | postgres | UTF8     | en_US.utf8 | en_US.utf8 |
 template0 | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres          +
           |          |          |            |            | postgres=CTc/postgres
 template1 | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres          +
           |          |          |            |            | postgres=CTc/postgres
(3 rows)

postgres=# \c template1
psql (12.12 (Ubuntu 12.12-0ubuntu0.20.04.1), server 13.8 (Debian 13.8-1.pgdg110+1))
WARNING: psql major version 12, server major version 13.
         Some psql features might not work.
You are now connected to database "template1" as user "postgres".

template1=# \dt[S+] pg_am
                      List of relations
   Schema   | Name  | Type  |  Owner   | Size  | Description
------------+-------+-------+----------+-------+-------------
 pg_catalog | pg_am | table | postgres | 40 kB |
(1 row)

template1=# \q
```
## Задача 2

    Используя psql создайте БД test_database.
    Изучите бэкап БД.
    Восстановите бэкап БД в test_database.
    Перейдите в управляющую консоль psql внутри контейнера.
    Подключитесь к восстановленной БД и проведите операцию ANALYZE для сбора статистики по таблице.
    Используя таблицу pg_stats, найдите столбец таблицы orders с наибольшим средним значением размера элементов в байтах.
    Приведите в ответе команду, которую вы использовали для вычисления и полученный результат.

```shell
postgres=# CREATE DATABASE test_database;
CREATE DATABASE
postgres=# \l
                                   List of databases
     Name      |  Owner   | Encoding |  Collate   |   Ctype    |   Access privileges
---------------+----------+----------+------------+------------+-----------------------
 postgres      | postgres | UTF8     | en_US.utf8 | en_US.utf8 |
 template0     | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres          +
               |          |          |            |            | postgres=CTc/postgres
 template1     | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres          +
               |          |          |            |            | postgres=CTc/postgres
 test_database | postgres | UTF8     | en_US.utf8 | en_US.utf8 |
(4 rows)

postgres=# \c test_database
psql (12.12 (Ubuntu 12.12-0ubuntu0.20.04.1), server 13.8 (Debian 13.8-1.pgdg110+1))
WARNING: psql major version 12, server major version 13.
         Some psql features might not work.
You are now connected to database "test_database" as user "postgres".

test_database=# \q
```
* Импорт 
```shell
vagrant@server1:/data$ docker exec -i my-postgres psql -U postgres -d test_database < test_dump.sql
SET
SET
SET
SET
SET
 set_config
------------

(1 row)

SET
SET
SET
SET
SET
SET
CREATE TABLE
ALTER TABLE
CREATE SEQUENCE
ALTER TABLE
ALTER SEQUENCE
ALTER TABLE
COPY 8
 setval
--------
      8
(1 row)

ALTER TABLE
```
* Проверка данных после импорта
```shell
vagrant@server1:/data$ psql -h localhost -p 5432 -U postgres
Password for user postgres:
psql (12.12 (Ubuntu 12.12-0ubuntu0.20.04.1), server 13.8 (Debian 13.8-1.pgdg110+1))
WARNING: psql major version 12, server major version 13.
         Some psql features might not work.
Type "help" for help.

postgres=# \c test_database

test_database=# \dt
         List of relations
 Schema |  Name  | Type  |  Owner
--------+--------+-------+----------
 public | orders | table | postgres
(1 row)

test_database=# select * from public.orders;
 id |        title         | price
----+----------------------+-------
  1 | War and peace        |   100
  2 | My little database   |   500
  3 | Adventure psql time  |   300
  4 | Server gravity falls |   300
  5 | Log gossips          |   123
  6 | WAL never lies       |   900
  7 | Me and my bash-pet   |   499
  8 | Dbiezdmin            |   501
(8 rows)
```
* ANALYZE
```shell
test_database=# ANALYZE VERBOSE public.orders;
INFO:  analyzing "public.orders"
INFO:  "orders": scanned 1 of 1 pages, containing 8 live rows and 0 dead rows; 8 rows in sample, 8 estimated total rows
ANALYZE
```
* Наибольшее среднее значение 
```shell
test_database=# select attname, avg_width from pg_stats where schemaname = 'public' and tablename = 'orders' and avg_width in (select max(avg_width) from pg_stats where schemaname = 'public' and tablename = 'orders');
 attname | avg_width
---------+-----------
 title   |        16
(1 row)
```

## Задача 3

    Архитектор и администратор БД выяснили, что ваша таблица orders разрослась до невиданных размеров и поиск по ней занимает 
    долгое время. Вам, как успешному выпускнику курсов DevOps в нетологии предложили провести разбиение таблицы на 2 (шардировать 
    на orders_1 - price>499 и orders_2 - price<=499).    
    Предложите SQL-транзакцию для проведения данной операции.    
    Можно ли было изначально исключить "ручное" разбиение при проектировании таблицы orders?

```shell
test_database=# alter table "orders" rename to orders_simple;
ALTER TABLE
test_database=# create table orders (id integer, title varchar(80), price integer) partition by range(price);
CREATE TABLE
test_database=# create table orders_2 partition of orders for values from (0) to (499);
CREATE TABLE
test_database=# create table orders_1 partition of orders for values from (499) to (999999999);
CREATE TABLE
test_database=# insert into orders (id, title, price) select * from orders_simple;
INSERT 0 8
test_database=# \dt
                   List of relations
 Schema |     Name      |       Type        |  Owner
--------+---------------+-------------------+----------
 public | orders        | partitioned table | postgres
 public | orders_1      | table             | postgres
 public | orders_2      | table             | postgres
 public | orders_simple | table             | postgres
(4 rows)
```
* Проверим данные

```shell
test_database=# select * from orders;
 id |        title         | price
----+----------------------+-------
  1 | War and peace        |   100
  3 | Adventure psql time  |   300
  4 | Server gravity falls |   300
  5 | Log gossips          |   123
  2 | My little database   |   500
  6 | WAL never lies       |   900
  7 | Me and my bash-pet   |   499
  8 | Dbiezdmin            |   501
(8 rows)

test_database=# select * from orders where price < 499;
 id |        title         | price
----+----------------------+-------
  1 | War and peace        |   100
  3 | Adventure psql time  |   300
  4 | Server gravity falls |   300
  5 | Log gossips          |   123
(4 rows)

test_database=# select * from orders where price >= 499;
 id |       title        | price
----+--------------------+-------
  2 | My little database |   500
  6 | WAL never lies     |   900
  7 | Me and my bash-pet |   499
  8 | Dbiezdmin          |   501
(4 rows)
```
* При изначальном проектировании таблиц необходимо было сделать ее секционированной.

## Задача 4

    Используя утилиту pg_dump создайте бекап БД test_database.
    Как бы вы доработали бэкап-файл, чтобы добавить уникальность значения столбца title для таблиц test_database?

```shell
vagrant@server1:/data$ docker exec -i my-postgres pg_dump -c -U postgres -d test_database > my_test_dump.sql
vagrant@server1:/data$ ls -l
total 8
-rw-rw-r-- 1 vagrant vagrant 3778 Sep 21 10:58 my_test_dump.sql
-rwxrwxrwx 1 root    root    2081 Sep 21 09:19 test_dump.sql
```
* Для уникальности можно добавить индекс или первичный ключ.
```commandline
    CREATE INDEX ON orders ((lower(title)));
    
    test_database=# \d orders
                Partitioned table "public.orders"
 Column |         Type          | Collation | Nullable | Default
--------+-----------------------+-----------+----------+---------
 id     | integer               |           |          |
 title  | character varying(80) |           |          |
 price  | integer               |           |          |
Partition key: RANGE (price)
Indexes:
    "orders_lower_idx" btree (lower(title::text))
Number of partitions: 2 (Use \d+ to list them.)
```