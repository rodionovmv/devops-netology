1.
```
Первый запуск

vagrant@vagrant:~/node_exporter-1.3.1.linux-amd64$ curl http://localhost:9100/metrics
# HELP go_gc_duration_seconds A summary of the pause duration of garbage collection cycles.
# TYPE go_gc_duration_seconds summary
go_gc_duration_seconds{quantile="0"} 0
go_gc_duration_seconds{quantile="0.25"} 0
go_gc_duration_seconds{quantile="0.5"} 0
go_gc_duration_seconds{quantile="0.75"} 0
go_gc_duration_seconds{quantile="1"} 0
go_gc_duration_seconds_sum 0

Пилим конфиг

cat /etc/systemd/system/node-exporter.service

[Unit]
Description=Node Exporter

[Service]
ExecStart=/home/vagrant/node_exporter/node_exporter
Environment=/etc/default/node_exporter
KillMode=process
Restart=on-failure
RestartSec=3

[Install]
WantedBy=multi-user.target

Запуск и остановка сервиса

vagrant@vagrant:~$ service node-exporter start
==== AUTHENTICATING FOR org.freedesktop.systemd1.manage-units ===
Authentication is required to start 'node-exporter.service'.
Authenticating as: vagrant,,, (vagrant)
Password:
==== AUTHENTICATION COMPLETE ===

vagrant@vagrant:~$ ps -aux | grep node_exporter
root        3876  0.0  1.1 715964 11772 ?        Ssl  16:20   0:00 /home/vagrant/node_exporter/node_exporter


vagrant@vagrant:~$ systemctl status node-exporter.service
● node-exporter.service - Node Exporter
     Loaded: loaded (/etc/systemd/system/node-exporter.service; enabled; vendor preset: enabled)
     Active: active (running) since Thu 2022-04-28 15:48:01 UTC; 4s ago
   Main PID: 4857 (node_exporter)
      Tasks: 5 (limit: 1071)
     Memory: 2.3M
     CGroup: /system.slice/node-exporter.service
             └─4857 /home/vagrant/node_exporter/node_exporter

vagrant@vagrant:~$ systemctl list-unit-files | grep node-exporter
node-exporter.service                  enabled         enabled


```
2. 
```
    node_cpu_seconds_total{cpu="0",mode="idle"} 21766.49
    node_cpu_seconds_total{cpu="0",mode="system"} 16.72
    node_cpu_seconds_total{cpu="0",mode="user"} 6.86
    process_cpu_seconds_total
    node_memory_MemAvailable_bytes 
    node_memory_MemFree_bytes
    node_memory_SwapCached_bytes  
    node_memory_SwapFree_bytes 
    node_memory_SwapTotal_bytes 
    node_disk_io_time_seconds_total{device="sda"} 
    node_disk_read_bytes_total{device="sda"} 
    node_disk_read_time_seconds_total{device="sda"} 
    node_disk_write_time_seconds_total{device="sda"}
    node_network_receive_bytes_total{device="eth0"} 
    node_network_transmit_bytes_total{device="eth0"}
    
```

3. 
```
vagrant@vagrant:~$ sudo lsof -i :19999
COMMAND  PID    USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
netdata 5330 netdata    4u  IPv4  60562      0t0  TCP localhost:19999 (LISTEN)
```

4. 
```
vagrant@vagrant:~$ dmesg > dmesg && cat dmesg | grep virtual*
[    0.002372] CPU MTRRs all blank - virtualized system.
[    0.083875] Booting paravirtualized kernel on KVM
[    3.261576] systemd[1]: Detected virtualization oracle.
```

5.
```
/sbin/sysctl -n fs.nr_open
1048576

Это максимальное число открытых дескрипторов для ядра (системы), для пользователя задать больше этого числа нельзя (если не менять). 
Число задается кратное 1024, в данном случае =1024*1024. 

Но макс.предел ОС можно посмотреть так :
vagrant@vagrant:~$ cat /proc/sys/fs/file-max
9223372036854775807

мягкий лимит 
ulimit -Sn
1024

жесткий лимит
ulimit -Hn
1048576

Оба ulimit -n НЕ могут превысить системный fs.nr_open
```

6. 
```
root@vagrant:~# unshare -f --pid --mount-proc sleep 1h
bg
^Z
[1]+  Stopped                 unshare -f --pid --mount-proc sleep 1h
root@vagrant:~# ps aux
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root        2202  0.0  0.0   8080   592 pts/0    T    18:31   0:00 unshare -f --pid --mount-proc sleep 1h
root        2203  0.0  0.0   8076   528 pts/0    S    18:31   0:00 sleep 1h
root        2204  0.0  0.3  11492  3308 pts/0    R+   18:32   0:00 ps aux
root@vagrant:~# nsenter -t 2203 -p -m
root@vagrant:/# ps
    PID TTY          TIME CMD
      1 pts/0    00:00:00 sleep
      2 pts/0    00:00:00 bash
     11 pts/0    00:00:00 ps
```

7. 
```
Команда пораждает процессы внутри себя, забивая тем самым все ресурсы

Здесь мы видим что сработал механизм сgroups
[3119.070390] cgroup: fork rejected by pids controller in /user.slice/user-1000.slice/session-6.scope

Назначить лимиты можно в /etc/security/limits.conf
```


