# Домашнее задание к занятию "6.2. SQL"


## Задача 1

Используя docker поднимите инстанс PostgreSQL (версию 12) c 2 volume, 
в который будут складываться данные БД и бэкапы.

Приведите получившуюся команду или docker-compose манифест.

```commandline
vagrant@server1:~$ docker pull postgres:12
12: Pulling from library/postgres
31b3f1ad4ce1: Already exists
dc97844d0cd5: Already exists
9ad9b1166fde: Already exists
286c4682b24d: Already exists
1d3679a4a1a1: Already exists
5f2e6cdc8503: Already exists
0f7dc70f54e8: Already exists
a090c7442692: Already exists
473f99b80402: Pull complete
8ca3fc2acaeb: Pull complete
f795e99c865c: Pull complete
071d381c05b0: Pull complete
04e6b9b9f224: Pull complete
Digest: sha256:c70b3c8daebd62d899881f5369d3cb57f1bf7f053b8cdcdb6dbdd967bea886c9
Status: Downloaded newer image for postgres:12
docker.io/library/postgres:12

vagrant@server1:~$ docker volume create vol1
vol1
vagrant@server1:~$ docker volume create vol2
vol2

vagrant@server1:~$ docker volume ls
DRIVER    VOLUME NAME
local     0b8955b6e0efcfbe8288205e50fc2edd9bd62f25825401e522a2b4841922c410
local     027701771c4732f8adc6ccd1b68cc0cde9c200eaddc83be09984443002f9bd34
local     c0a7f8acb433f91956bee7ebed32c7ca67ef958442edc9f129507bc271596ac1
local     datamysql
local     datapostgres
local     vol1
local     vol2

vagrant@server1:~$ docker inspect postgres
"Mounts": [
            {
                "Type": "volume",
                "Name": "vol1",
                "Source": "/var/lib/docker/volumes/vol1/_data",
                "Destination": "/data",
                "Driver": "local",
                "Mode": "z",
                "RW": true,
                "Propagation": ""
            },
            {
                "Type": "volume",
                "Name": "vol2",
                "Source": "/var/lib/docker/volumes/vol2/_data",
                "Destination": "/data/backup",
                "Driver": "local",
                "Mode": "z",
                "RW": true,
                "Propagation": ""
            }

vagrant@server1:~$ docker run --rm --name postgres -e POSTGRES_PASSWORD=postgres -dti -p 5432:5432 -v vol1:/data -v vol2:/data/backup postgres:12
13466c29a23d98f11f903c667ea867c3389379f2d5e5910f4317a79952715529
vagrant@server1:~$ docker ps
CONTAINER ID   IMAGE         COMMAND                  CREATED         STATUS         PORTS                                       NAMES
13466c29a23d   postgres:12   "docker-entrypoint.s…"   4 seconds ago   Up 3 seconds   0.0.0.0:5432->5432/tcp, :::5432->5432/tcp   postgres

vagrant@server1:~$ psql -h localhost -p 5432 -U postgres
Password for user postgres:
psql (12.12 (Ubuntu 12.12-0ubuntu0.20.04.1))
Type "help" for help.

postgres=#
```
## Задача 2

В БД из задачи 1: 
- создайте пользователя test-admin-user и БД test_db
- в БД test_db создайте таблицу orders и clients (спeцификация таблиц ниже)
- предоставьте привилегии на все операции пользователю test-admin-user на таблицы БД test_db
- создайте пользователя test-simple-user  
- предоставьте пользователю test-simple-user права на SELECT/INSERT/UPDATE/DELETE данных таблиц БД test_db

Таблица orders:
- id (serial primary key)
- наименование (string)
- цена (integer)

Таблица clients:
- id (serial primary key)
- фамилия (string)
- страна проживания (string, index)
- заказ (foreign key orders)

Приведите:
- итоговый список БД после выполнения пунктов выше,
- описание таблиц (describe)
- SQL-запрос для выдачи списка пользователей с правами над таблицами test_db
- список пользователей с правами над таблицами test_db

```commandline
postgres=# CREATE DATABASE test_db;
CREATE DATABASE

postgres=# \c test_db
You are now connected to database "test_db" as user "postgres".

test_db=# CREATE USER test_admin_user WITH SUPERUSER NOCREATEDB NOCREATEROLE NOINHERIT LOGIN;
CREATE ROLE

test_db=# CREATE TABLE orders (
        id integer PRIMARY KEY,
        name text, price int
);
CREATE TABLE

test_db=# CREATE TABLE clients (
        id integer PRIMARY KEY,
        lastname text,
        country text,
        id_order integer,
        FOREIGN KEY(id_order) REFERENCES orders (id)
);
CREATE TABLE
test_db=# GRANT ALL ON DATABASE test_db TO test_admin_user;
GRANT
test_db=# CREATE USER test_simple_user WITH NOSUPERUSER NOCREATEDB NOCREATEROLE NOINHERIT LOGIN;
CREATE ROLE
test_db=# GRANT SELECT ON clients TO test_simple_user;
GRANT
test_db=# GRANT INSERT ON clients TO test_simple_user;
GRANT
test_db=# GRANT UPDATE ON clients TO test_simple_user;
GRANT
test_db=# GRANT DELETE ON clients TO test_simple_user;
GRANT
test_db=# GRANT SELECT ON orders TO test_simple_user;
GRANT
test_db=# GRANT INSERT ON orders TO test_simple_user;
GRANT
test_db=# GRANT UPDATE ON orders TO test_simple_user;
GRANT
test_db=# GRANT DELETE ON orders TO test_simple_user;
GRANT
```
  * Список БД
```commandline
test_db=# \l
                                    List of databases
   Name    |  Owner   | Encoding |  Collate   |   Ctype    |      Access privileges
-----------+----------+----------+------------+------------+------------------------------
 postgres  | postgres | UTF8     | en_US.utf8 | en_US.utf8 |
 template0 | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres                 +
           |          |          |            |            | postgres=CTc/postgres
 template1 | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres                 +
           |          |          |            |            | postgres=CTc/postgres
 test_db   | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =Tc/postgres                +
           |          |          |            |            | postgres=CTc/postgres       +
           |          |          |            |            | test_admin_user=CTc/postgres
(4 rows)
```
  * Describe orders
```commandline
test_db=# \d+ orders
                                   Table "public.orders"
 Column |  Type   | Collation | Nullable | Default | Storage  | Stats target | Description
--------+---------+-----------+----------+---------+----------+--------------+-------------
 id     | integer |           | not null |         | plain    |              |
 name   | text    |           |          |         | extended |              |
 price  | integer |           |          |         | plain    |              |
Indexes:
    "orders_pkey" PRIMARY KEY, btree (id)
Referenced by:
    TABLE "clients" CONSTRAINT "clients_id_order_fkey" FOREIGN KEY (id_order) REFERENCES orders(id)
Access method: heap
```
  * Describe client
```commandline
test_db=# \d+ clients
                                   Table "public.clients"
  Column  |  Type   | Collation | Nullable | Default | Storage  | Stats target | Description
----------+---------+-----------+----------+---------+----------+--------------+-------------
 id       | integer |           | not null |         | plain    |              |
 lastname | text    |           |          |         | extended |              |
 country  | text    |           |          |         | extended |              |
 id_order | integer |           |          |         | plain    |              |
Indexes:
    "clients_pkey" PRIMARY KEY, btree (id)
Foreign-key constraints:
    "clients_id_order_fkey" FOREIGN KEY (id_order) REFERENCES orders(id)
Access method: heap
```
  * SQL users test_db
```commandline
test_db=# SELECT * FROM information_schema.role_table_grants WHERE table_catalog = 'test_db' AND table_name IN ('clients', 'orders');
 grantor  |     grantee      | table_catalog | table_schema | table_name | privilege_type | is_grantable | with_hierarchy
----------+------------------+---------------+--------------+------------+----------------+--------------+----------------
 postgres | postgres         | test_db       | public       | orders     | INSERT         | YES          | NO
 postgres | postgres         | test_db       | public       | orders     | SELECT         | YES          | YES
 postgres | postgres         | test_db       | public       | orders     | UPDATE         | YES          | NO
 postgres | postgres         | test_db       | public       | orders     | DELETE         | YES          | NO
 postgres | postgres         | test_db       | public       | orders     | TRUNCATE       | YES          | NO
 postgres | postgres         | test_db       | public       | orders     | REFERENCES     | YES          | NO
 postgres | postgres         | test_db       | public       | orders     | TRIGGER        | YES          | NO
 postgres | test_simple_user | test_db       | public       | orders     | INSERT         | NO           | NO
 postgres | test_simple_user | test_db       | public       | orders     | SELECT         | NO           | YES
 postgres | test_simple_user | test_db       | public       | orders     | UPDATE         | NO           | NO
 postgres | test_simple_user | test_db       | public       | orders     | DELETE         | NO           | NO
 postgres | postgres         | test_db       | public       | clients    | INSERT         | YES          | NO
 postgres | postgres         | test_db       | public       | clients    | SELECT         | YES          | YES
 postgres | postgres         | test_db       | public       | clients    | UPDATE         | YES          | NO
 postgres | postgres         | test_db       | public       | clients    | DELETE         | YES          | NO
 postgres | postgres         | test_db       | public       | clients    | TRUNCATE       | YES          | NO
 postgres | postgres         | test_db       | public       | clients    | REFERENCES     | YES          | NO
 postgres | postgres         | test_db       | public       | clients    | TRIGGER        | YES          | NO
 postgres | test_simple_user | test_db       | public       | clients    | INSERT         | NO           | NO
 postgres | test_simple_user | test_db       | public       | clients    | SELECT         | NO           | YES
 postgres | test_simple_user | test_db       | public       | clients    | UPDATE         | NO           | NO
 postgres | test_simple_user | test_db       | public       | clients    | DELETE         | NO           | NO
(22 rows)
```
  * Users on test_tb
```commandline
test_db=# \du
                                       List of roles
    Role name     |                         Attributes                         | Member of
------------------+------------------------------------------------------------+-----------
 postgres         | Superuser, Create role, Create DB, Replication, Bypass RLS | {}
 test_admin_user  | Superuser, No inheritance                                  | {}
 test_simple_user | No inheritance                                             | {}

```


## Задача 3

Используя SQL синтаксис - наполните таблицы следующими тестовыми данными:

Таблица orders

|Наименование|цена|
|------------|----|
|Шоколад| 10 |
|Принтер| 3000 |
|Книга| 500 |
|Монитор| 7000|
|Гитара| 4000|

Таблица clients

|ФИО|Страна проживания|
|------------|----|
|Иванов Иван Иванович| USA |
|Петров Петр Петрович| Canada |
|Иоганн Себастьян Бах| Japan |
|Ронни Джеймс Дио| Russia|
|Ritchie Blackmore| Russia|

Используя SQL синтаксис:
- вычислите количество записей для каждой таблицы 
- приведите в ответе:
    - запросы 
    - результаты их выполнения.

```commandline
test_db=# insert into orders(id, name, price) values(1, 'Шоколад', 10);
INSERT 0 1
test_db=# insert into orders(id, name, price) values(2, 'Принтер', 3000);
INSERT 0 1
test_db=# insert into orders(id, name, price) values(3, 'Книга', 500);
INSERT 0 1
test_db=# insert into orders(id, name, price) values(4, 'Монитор', 7000);
INSERT 0 1
test_db=# insert into orders(id, name, price) values(5, 'Гитара', 4000);
INSERT 0 1

test_db=# insert into clients(id, lastname, country) values(1, 'Иванов Иван Иванович', 'USA');
INSERT 0 1
test_db=# insert into clients(id, lastname, country) values(2, 'Петров Петр Петрович', 'Canada');
INSERT 0 1
test_db=# insert into clients(id, lastname, country) values(3, 'Иоганн Себастьян Бах', 'Japan');
INSERT 0 1
test_db=# insert into clients(id, lastname, country) values(4, 'Ронни Джеймс Дио', 'Russia');
INSERT 0 1
test_db=# insert into clients(id, lastname, country) values(5, 'Ritchie Blackmore', 'Russia');
INSERT 0 1

test_db=# select count(*) from orders o ;
 count
-------
     5
(1 row)

test_db=# select count(*) from clients;
 count
-------
     5
(1 row)

test_db=# select (select count(*) from orders) as orders_count, (select count(*) from clients) as clients_count;
 orders_count | clients_count
--------------+---------------
            5 |             5
(1 row)
```

## Задача 4

Часть пользователей из таблицы clients решили оформить заказы из таблицы orders.

Используя foreign keys свяжите записи из таблиц, согласно таблице:

|ФИО|Заказ|
|------------|----|
|Иванов Иван Иванович| Книга |
|Петров Петр Петрович| Монитор |
|Иоганн Себастьян Бах| Гитара |

Приведите SQL-запросы для выполнения данных операций.

Приведите SQL-запрос для выдачи всех пользователей, которые совершили заказ, а также вывод данного запроса.
 
Подсказк - используйте директиву `UPDATE`.

```commandline
test_db=# update clients set id_order = 3 where id = 1; -- Иванов Иван Иванович - Книга
UPDATE 1
test_db=# update clients set id_order = 4 where id = 2; -- Петров Петр Петрович - Монитор
UPDATE 1
test_db=# update clients set id_order = 5 where id = 3; -- Иоганн Себастьян Бах - Гитара
UPDATE 1

test_db=# select * from clients c where id_order is not null;
 id |       lastname       | country | id_order
----+----------------------+---------+----------
  1 | Иванов Иван Иванович | USA     |        3
  2 | Петров Петр Петрович | Canada  |        4
  3 | Иоганн Себастьян Бах | Japan   |        5
(3 rows)

test_db=# select c.*, o.name, o.price from clients c join orders o on c.id_order = o.id where c.id_order  is not null;
 id |       lastname       | country | id_order |  name   | price
----+----------------------+---------+----------+---------+-------
  1 | Иванов Иван Иванович | USA     |        3 | Книга   |   500
  2 | Петров Петр Петрович | Canada  |        4 | Монитор |  7000
  3 | Иоганн Себастьян Бах | Japan   |        5 | Гитара  |  4000
(3 rows)

```

## Задача 5

Получите полную информацию по выполнению запроса выдачи всех пользователей из задачи 4 
(используя директиву EXPLAIN).

Приведите получившийся результат и объясните что значат полученные значения.

```commandline
test_db=# EXPLAIN select c.*, o.name, o.price from clients c join orders o on c.id_order = o.id where c.id_order is not null;
                               QUERY PLAN
-------------------------------------------------------------------------
 Hash Join  (cost=37.00..57.23 rows=806 width=108)
   Hash Cond: (c.id_order = o.id)
   ->  Seq Scan on clients c  (cost=0.00..18.10 rows=806 width=72)
         Filter: (id_order IS NOT NULL)
   ->  Hash  (cost=22.00..22.00 rows=1200 width=40)
         ->  Seq Scan on orders o  (cost=0.00..22.00 rows=1200 width=40)
(6 rows)
```
```commandline
Планировщик выбрал соединение по хешу, при котором строки таблицы clients записываются в хэш-таблицу в памяти, после чего сканируется таблица orders и для каждой её строки проверяется соответствие по хеш-таблице.
Узел Hash строит хеш-таблицу, передает план Hash Join, который читает строки из узла внешнего потомка и проверяет их по этой хеш-таблице.
```

## Задача 6

Создайте бэкап БД test_db и поместите его в volume, предназначенный для бэкапов (см. Задачу 1).

Остановите контейнер с PostgreSQL (но не удаляйте volumes).

Поднимите новый пустой контейнер с PostgreSQL.

Восстановите БД test_db в новом контейнере.

Приведите список операций, который вы применяли для бэкапа данных и восстановления. 

```commandline
vagrant@server1:~$ docker exec -i postgres pg_dump -c -U postgres -d test_db > /data/backup/dump_test.sql
vagrant@server1:~$ docker ps
CONTAINER ID   IMAGE         COMMAND                  CREATED          STATUS          PORTS                                       NAMES
8334b4ca7ca1   postgres:12   "docker-entrypoint.s…"   16 minutes ago   Up 16 minutes   0.0.0.0:5432->5432/tcp, :::5432->5432/tcp   postgres
vagrant@server1:~$ docker stop 8334b4ca7ca1
8334b4ca7ca1

vagrant@server1:~$ docker run --rm --name postgres-back -e POSTGRES_PASSWORD=postgres -dti -p 5432:5432 -v vol2:/data/backup postgres:12
365e756a9a378977800c0db83c25ad16cfb9699992d0f49b21915cade2db18b9
vagrant@server1:~$ docker ps
CONTAINER ID   IMAGE         COMMAND                  CREATED         STATUS         PORTS                                       NAMES
365e756a9a37   postgres:12   "docker-entrypoint.s…"   6 seconds ago   Up 5 seconds   0.0.0.0:5432->5432/tcp, :::5432->5432/tcp   postgres-back

vagrant@server1:~$ psql -h localhost -p 5432 -U postgres
Password for user postgres:
psql (12.12 (Ubuntu 12.12-0ubuntu0.20.04.1))
Type "help" for help.

postgres=# CREATE DATABASE test_db;
CREATE DATABASE
postgres=# \q

vagrant@server1:~$ docker exec -i postgres-back psql -U postgres -d test_db < dump_test.sql

vagrant@server1:~$ psql -h localhost -p 5432 -U postgres
Password for user postgres:
psql (12.12 (Ubuntu 12.12-0ubuntu0.20.04.1))
Type "help" for help.

postgres=# \c test_db
You are now connected to database "test_db" as user "postgres".
postgres=# select * from orders;
 id |  name   | price
----+---------+-------
  1 | Шоколад |    10
  2 | Принтер |  3000
  3 | Книга   |   500
  4 | Монитор |  7000
  5 | Гитара  |  4000
(5 rows)

```

