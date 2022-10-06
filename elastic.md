# Домашнее задание к занятию "6.5. Elasticsearch"

## Задача 1

--------------------------------------------------------------------------------------------
    Используя докер образ centos:7 как базовый и документацию по установке и запуску Elastcisearch:
        -   составьте Dockerfile-манифест для elasticsearch
        -   соберите docker-образ и сделайте push в ваш docker.io репозиторий
        -   запустите контейнер из получившегося образа и выполните запрос пути / c хост-машины
    Требования к elasticsearch.yml:
        -   данные path должны сохраняться в /var/lib
        -   имя ноды должно быть netology_test
--------------------------------------------------------------------------------------------

#### Составьте Dockerfile-манифест для elasticsearch
* Dockerfile:
```shell
FROM centos:7

MAINTAINER Mikhail Rodionov <rodionovmv@gmail.com>

ENV TZ=Europe/Moscow

RUN yum update -y && \
        yum install wget -y && \
        yum install java-11-openjdk-devel -y && \
        wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.17.6-linux-x86_64.tar.gz && \
        wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.17.6-linux-x86_64.tar.gz.sha512 && \
        yum install perl-Digest-SHA -y && \
        shasum -a 512 -c elasticsearch-7.17.6-linux-x86_64.tar.gz.sha512 && \
        yum upgrade -y && \
        tar -xzf elasticsearch-7.17.6-linux-x86_64.tar.gz && \
        mkdir /var/lib/logs && mkdir /var/lib/data && \
        yum clean all && \
        rm -f elasticsearch-7.17.6-linux-x86_64.tar.gz

ADD elasticsearch.yml /elasticsearch-7.17.6/config/

ENV ES_HOME=/elasticsearch-7.17.6

RUN groupadd elsearch && useradd elsearch -g elsearch -p elasticsearch && \
        chown -R elsearch:elsearch elasticsearch-7.17.6/ && \
        chown elsearch:elsearch /var/lib/data && \
        chown elsearch:elsearch /var/lib/logs

USER elsearch

CMD ["/usr/sbin/init"]

CMD ["elasticsearch-7.17.6/bin/elasticsearch"]
```
--------------------------------------------------------------------------------------------
#### Соберите docker-образ и сделайте push в ваш docker.io репозиторий
* Образ: [elasticsearch](https://hub.docker.com/repository/docker/rodionovmv/elastic)
* [elasticsearch.yml](elasticsearch.yml)
--------------------------------------------------------------------------------------------

#### Запустите контейнер из получившегося образа и выполните запрос пути / c хост-машины

```shell
$ curl -XGET localhost:9200/
```
```json
{
  "name" : "netology_test",
  "cluster_name" : "netology-app",
  "cluster_uuid" : "ddg1-9xzQX-8TR3y7dTmZg",
  "version" : {
    "number" : "7.17.6",
    "build_flavor" : "default",
    "build_type" : "tar",
    "build_hash" : "f65e9d338dc1d07b642e14a27f338990148ee5b6",
    "build_date" : "2022-08-23T11:08:48.893373482Z",
    "build_snapshot" : false,
    "lucene_version" : "8.11.1",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}
```

## Задача 2

--------------------------------------------------------------------------------------------
    -   Ознакомьтесь с документацией и добавьте в elasticsearch 3 индекса
    -   Получите список индексов и их статусов, используя API и приведите в ответе на задание.
    -   Получите состояние кластера elasticsearch, используя API.
    -   Как вы думаете, почему часть индексов и кластер находится в состоянии yellow?
    -   Удалите все индексы.

--------------------------------------------------------------------------------------------
#### Добавьте в elasticsearch 3 индекса
* Создание индексов
```shell
$ curl -XPUT localhost:9200/ind-1 -H 'Content-Type: application/json' -d '{"settings": {"number_of_shards": 1, "number_of_replicas": 0}}'
{"acknowledged":true,"shards_acknowledged":true,"index":"ind-1"}
$ curl -XPUT localhost:9200/ind-2 -H 'Content-Type: application/json' -d '{"settings": {"number_of_shards": 2, "number_of_replicas": 1}}'
{"acknowledged":true,"shards_acknowledged":true,"index":"ind-2"}
$ curl -XPUT localhost:9200/ind-3 -H 'Content-Type: application/json' -d '{"settings": {"number_of_shards": 4, "number_of_replicas": 2}}'
{"acknowledged":true,"shards_acknowledged":true,"index":"ind-3"}
```
--------------------------------------------------------------------------------------------
#### Получите список индексов и их статусов, используя API и приведите в ответе на задание.

* Список индексов и их состояние:
```shell
$ curl -XGET localhost:9200/_cat/indices/ind*?v
health status index uuid                   pri rep docs.count docs.deleted store.size pri.store.size
green  open   ind-1 MxhysG7iRQS5BgoCRydO9w   1   0          0            0       226b           226b
yellow open   ind-3 ysL-A_bqS7SphhEH-zigDw   4   2          0            0       904b           904b
yellow open   ind-2 RogBxXxVSR2NVJ9-3NHNyw   2   1          0            0       452b           452b
```
--------------------------------------------------------------------------------------------
#### Получите состояние кластера elasticsearch, используя API.
* Состояние кластера:
```shell
$ curl -XGET localhost:9200/_cluster/health/ind*?pretty
{
  "cluster_name" : "netology-app",
  "status" : "yellow",
  "timed_out" : false,
  "number_of_nodes" : 1,
  "number_of_data_nodes" : 1,
  "active_primary_shards" : 7,
  "active_shards" : 7,
  "relocating_shards" : 0,
  "initializing_shards" : 0,
  "unassigned_shards" : 10,
  "delayed_unassigned_shards" : 0,
  "number_of_pending_tasks" : 0,
  "number_of_in_flight_fetch" : 0,
  "task_max_waiting_in_queue_millis" : 0,
  "active_shards_percent_as_number" : 50.0
}

```
--------------------------------------------------------------------------------------------
#### Как вы думаете, почему часть индексов и кластер находится в состоянии yellow?
* Статус `yellow` кластера говорит о том, что _все primary шарды в состоянии assigned. Часть secondary - шард в состоянии unassigned_. Проверим состояние индексов в кластере:
* `ind-1`:
```shell
$ curl -XGET localhost:9200/_cluster/health/ind-1?pretty
{
  "cluster_name" : "netology-app",
  "status" : "green",
  "timed_out" : false,
  "number_of_nodes" : 1,
  "number_of_data_nodes" : 1,
  "active_primary_shards" : 1,
  "active_shards" : 1,
  "relocating_shards" : 0,
  "initializing_shards" : 0,
  "unassigned_shards" : 0,        ## нет не назначенных шардов
  "delayed_unassigned_shards" : 0,
  "number_of_pending_tasks" : 0,
  "number_of_in_flight_fetch" : 0,
  "task_max_waiting_in_queue_millis" : 0,
  "active_shards_percent_as_number" : 100.0
}
```
* `ind-2`:
```shell
$ curl -XGET localhost:9200/_cluster/health/ind-2?pretty
{
  "cluster_name" : "netology-app",
  "status" : "yellow",
  "timed_out" : false,
  "number_of_nodes" : 1,
  "number_of_data_nodes" : 1,
  "active_primary_shards" : 2,
  "active_shards" : 2,
  "relocating_shards" : 0,
  "initializing_shards" : 0,
  "unassigned_shards" : 2,      ## два не назначенных шарда
  "delayed_unassigned_shards" : 0,
  "number_of_pending_tasks" : 0,
  "number_of_in_flight_fetch" : 0,
  "task_max_waiting_in_queue_millis" : 0,
  "active_shards_percent_as_number" : 50.0
}
```
* `ind-3`:
```shell
$ curl -XGET localhost:9200/_cluster/health/ind-3?pretty
{
  "cluster_name" : "netology-app",
  "status" : "yellow",
  "timed_out" : false,
  "number_of_nodes" : 1,
  "number_of_data_nodes" : 1,
  "active_primary_shards" : 4,
  "active_shards" : 4,
  "relocating_shards" : 0,
  "initializing_shards" : 0,
  "unassigned_shards" : 8,    ## восемь не назначенных шардов
  "delayed_unassigned_shards" : 0,
  "number_of_pending_tasks" : 0,
  "number_of_in_flight_fetch" : 0,
  "task_max_waiting_in_queue_millis" : 0,
  "active_shards_percent_as_number" : 50.0
}
```
* Посмотрим состояние шардов:
```shell
$ curl -XGET localhost:9200/_cat/shards/ind*?pretty
ind-3 1 p STARTED    0 226b 172.17.0.2 netology_test
ind-3 1 r UNASSIGNED
ind-3 1 r UNASSIGNED
ind-3 2 p STARTED    0 226b 172.17.0.2 netology_test
ind-3 2 r UNASSIGNED
ind-3 2 r UNASSIGNED
ind-3 3 p STARTED    0 226b 172.17.0.2 netology_test
ind-3 3 r UNASSIGNED
ind-3 3 r UNASSIGNED
ind-3 0 p STARTED    0 226b 172.17.0.2 netology_test
ind-3 0 r UNASSIGNED
ind-3 0 r UNASSIGNED
ind-2 1 p STARTED    0 226b 172.17.0.2 netology_test
ind-2 1 r UNASSIGNED
ind-2 0 p STARTED    0 226b 172.17.0.2 netology_test
ind-2 0 r UNASSIGNED
ind-1 0 p STARTED    0 226b 172.17.0.2 netology_test 
```
---------------------------------------
##### _Получается, что шарды нам доступны, а вот реплики - нет. Значит, данные могут быть потеряны. А куда реплицировать данные мы не указали._

--------------------------------------------------------------------------------------------

#### Удалите индексы

```shell
$ curl -XDELETE localhost:9200/ind*?pretty
{
  "acknowledged" : true
}
```
* Проверка:
 ```shell
$ curl -XGET localhost:9200/_cat/indices/ind*?v
health status index uuid pri rep docs.count docs.deleted store.size pri.store.size
```

## Задача 3

--------------------------------------------------------------------------------------------
    -   Создайте директорию {путь до корневой директории с elasticsearch в образе}/snapshots.
    -   Используя API зарегистрируйте данную директорию как snapshot repository c именем netology_backup.
    -   Приведите в ответе запрос API и результат вызова API для создания репозитория.
    -   Создайте индекс test с 0 реплик и 1 шардом и приведите в ответе список индексов.
    -   Создайте snapshot состояния кластера elasticsearch.
    -   Приведите в ответе список файлов в директории со snapshotами.
    -   Удалите индекс test и создайте индекс test-2. Приведите в ответе список индексов.
    -   Восстановите состояние кластера elasticsearch из snapshot, созданного ранее.
    -   Приведите в ответе запрос к API восстановления и итоговый список индексов.
--------------------------------------------------------------------------------------------
#### Создайте директорию {путь до корневой директории с elasticsearch в образе}/snapshots.

* Изменил конфигурационный файл:
```shell
path.repo: /elasticsearch-7.17.6/snapshots
```
* Создал новый образ, запустил контейнер:
```shell
....
Successfully built 1ab9721b7ce3
Successfully tagged rodionovmv/elastic:v2

$ docker image ls
REPOSITORY                        TAG        IMAGE ID       CREATED             SIZE
rodionovmv/elastic                v2         1ab9721b7ce3   About an hour ago   1.64GB
rodionovmv/elastic                v1         3ad8c7d1df14   2 hours ago         1.64GB

$ docker run --rm --name elastic_v2 -dti --privileged -p 9200:9200 -e "discovery.type=single-node" rodionovmv/elastic:v2
fcd721cdcfbba3d88c54ceeaaeb46392f338f60147fcaada018d7123b3ea1b95
$ docker ps
CONTAINER ID   IMAGE                   COMMAND                  CREATED         STATUS         PORTS                                       NAMES
fcd721cdcfbb   rodionovmv/elastic:v2   "elasticsearch-7.17.…"   5 seconds ago   Up 3 seconds   0.0.0.0:9200->9200/tcp, :::9200->9200/tcp   elastic_v2
$ docker exec -it elastic_v2 /bin/bash
[elsearch@fcd721cdcfbb /]$ pwd
/
[elsearch@fcd721cdcfbb /]$ cd elasticsearch-7.17.6
[elsearch@fcd721cdcfbb elasticsearch-7.17.6]$ ls -l
total 668
-rw-r--r-- 1 elsearch elsearch   3860 Aug 23 14:06 LICENSE.txt
-rw-r--r-- 1 elsearch elsearch 642830 Aug 23 14:10 NOTICE.txt
-rw-r--r-- 1 elsearch elsearch   2710 Aug 23 14:06 README.asciidoc
drwxr-xr-x 1 elsearch elsearch   4096 Aug 23 14:14 bin
drwxr-xr-x 1 elsearch elsearch   4096 Oct  6 13:29 config
drwxr-xr-x 1 elsearch elsearch   4096 Aug 23 14:14 jdk
drwxr-xr-x 1 elsearch elsearch   4096 Aug 23 14:14 lib
drwxr-xr-x 1 elsearch elsearch   4096 Oct  6 13:29 logs
drwxr-xr-x 1 elsearch elsearch   4096 Aug 23 14:14 modules
drwxr-xr-x 1 elsearch elsearch   4096 Aug 23 14:10 plugins
drwxr-xr-x 2 elsearch elsearch   4096 Oct  6 13:29 snapshots
[elsearch@fcd721cdcfbb elasticsearch-7.17.6]$ ls -l snapshots/
--------------------------------------------------------------------------------------------
```
#### Используя API зарегистрируйте данную директорию как snapshot repository c именем `netology_backup`. Приведите в ответе запрос API и результат вызова API для создания репозитория.
* Создание нового репозитория:

```shell
$ curl -XPUT localhost:9200/_snapshot/netology_backup -H 'Content-Type: application/json' -d '{"type": "fs", "settings" : {"location": "/elasticsearch-7.17.6/snapshots"}}'
{"acknowledged":true}
```
* Просмотр:
```shell
$ curl -XGET localhost:9200/_snapshot/repo*,*backup*?pretty
{
  "netology_backup" : {
    "type" : "fs",
    "settings" : {
      "location" : "/elasticsearch-7.17.6/snapshots"
    }
  }
}
```
--------------------------------------------------------------------------------------------

#### Создайте индекс `test` с 0 реплик и 1 шардом и приведите в ответе список индексов.
```shell
$ curl -XPUT localhost:9200/test -H 'Content-Type: application/json' -d '{"settings": {"number_of_shards": 1, "number_of_replicas": 0}}'
{"acknowledged":true,"shards_acknowledged":true,"index":"test"}

$ curl -XGET localhost:9200/_cat/indices/test?v
health status index uuid                   pri rep docs.count docs.deleted store.size pri.store.size
green  open   test  NnFJ3in4Qb6csO94kaFGFw   1   0          0            0       226b           226b


$ curl -XGET localhost:9200/_cluster/health/test?pretty
{
  "cluster_name" : "netology-app",
  "status" : "green",
  "timed_out" : false,
  "number_of_nodes" : 1,
  "number_of_data_nodes" : 1,
  "active_primary_shards" : 1,
  "active_shards" : 1,
  "relocating_shards" : 0,
  "initializing_shards" : 0,
  "unassigned_shards" : 0,
  "delayed_unassigned_shards" : 0,
  "number_of_pending_tasks" : 0,
  "number_of_in_flight_fetch" : 0,
  "task_max_waiting_in_queue_millis" : 0,
  "active_shards_percent_as_number" : 100.0
}
```
--------------------------------------------------------------------------------------------

#### Создайте snapshot состояния кластера `elasticsearch`.
* Snapshot состояния кластера:
```shell
$ curl -XPUT localhost:9200/_snapshot/netology_backup/elasticsearch?wait_for_completion=true
# ответ после выполнения команды ниже в формате json:
```
```json
{
  "snapshot": {
    "snapshot": "elasticsearch",
    "uuid": "9YmkdTKCQqa6gX2TouYhxg",
    "repository": "netology_backup",
    "version_id": 7170699,
    "version": "7.17.6",
    "indices": [
      ".geoip_databases",
      ".ds-ilm-history-5-2022.10.06-000001",
      ".ds-.logs-deprecation.elasticsearch-default-2022.10.06-000001",
      "test"
    ],
    "data_streams": [
      "ilm-history-5",
      ".logs-deprecation.elasticsearch-default"
    ],
    "include_global_state": true,
    "state": "SUCCESS",
    "start_time":"2022-10-06T10:40:21.746Z",
    "start_time_in_millis":1665052821746,
    "end_time":"2022-10-06T10:40:23.555Z",
    "end_time_in_millis":1665052823555,
    "duration_in_millis":1809,
    "failures":[],
    "shards":{
      "total":4,
      "failed":0,
      "successful":4
    },
    "feature_states": [
      {
        "feature_name": "geoip",
        "indices": [
          ".geoip_databases"
        ]
      }
    ]
  }
}
```
--------------------------------------------------------------------------------------------
#### Приведите в ответе список файлов в директории со snapshotами.

* Список файлов:
```shell
vagrant@server1:/data/docker$ docker exec -it elastic_v2 /bin/bash
[elsearch@fcd721cdcfbb /]$ ll elasticsearch-7.17.6/snapshots/
total 48
-rw-r--r-- 1 elsearch elsearch  1425 Oct  6 13:40 index-0
-rw-r--r-- 1 elsearch elsearch     8 Oct  6 13:40 index.latest
drwxr-xr-x 6 elsearch elsearch  4096 Oct  6 13:40 indices
-rw-r--r-- 1 elsearch elsearch 29243 Oct  6 13:40 meta-9YmkdTKCQqa6gX2TouYhxg.dat
-rw-r--r-- 1 elsearch elsearch   712 Oct  6 13:40 snap-9YmkdTKCQqa6gX2TouYhxg.dat
---------------------------------------------------
```
#### Удалите индекс `test` и создайте индекс `test-2`. Приведите в ответе список индексов.

* Удаление и создание:
```shell
 $ curl -XDELETE localhost:9200/test?pretty
{
  "acknowledged" : true
}
$ curl -XPUT localhost:9200/test-2 -H 'Content-Type: application/json' -d '{"settings": {"number_of_shards": 1, "number_of_replicas": 0}}'
{"acknowledged":true,"shards_acknowledged":true,"index":"test-2"}
```
* Список:
```shell
$ curl -XGET localhost:9200/_cat/indices/?pretty
green open .geoip_databases Ulw33KrMRLa2_YzAm0z0bg 1 0 40 0 38.3mb 38.3mb
green open test-2           WrX6SVbrQfm0HPnQm3a1VQ 1 0  0 0   226b   226b
```
---------------------------------------------------
#### Восстановите состояние кластера `elasticsearch` из snapshot, созданного ранее. Приведите в ответе запрос к API восстановления и итоговый список индексов.

* Восстановление:
```shell
$ curl -XPOST localhost:9200/_snapshot/netology_backup/elasticsearch/_restore?pretty -H 'Content-Type: application/json' -d'{"include_global_state":true}'
{
  "accepted" : true
}
```
* Итоговый список индексов:
```shell
$ curl -XGET localhost:9200/_cat/indices/?pretty
green open .geoip_databases Ulw33KrMRLa2_YzAm0z0bg 1 0 40 0 38.3mb 38.3mb
green open test-2           WrX6SVbrQfm0HPnQm3a1VQ 1 0  0 0   226b   226b
green open test             NnFJ3in4Qb6csO94kaFGFw 1 0  0 0   226b   226b
```
