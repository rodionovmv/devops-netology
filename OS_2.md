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
EnvironmentFile=/etc/default/node_exporter

[Install]
WantedBy=default.target

Запуск и остановка сервиса

vagrant@vagrant:~$ service node-exporter start
==== AUTHENTICATING FOR org.freedesktop.systemd1.manage-units ===
Authentication is required to start 'node-exporter.service'.
Authenticating as: vagrant,,, (vagrant)
Password:
==== AUTHENTICATION COMPLETE ===

vagrant@vagrant:~$ ps -aux | grep node_exporter
root        3876  0.0  1.1 715964 11772 ?        Ssl  16:20   0:00 /home/vagrant/node_exporter/node_exporter

vagrant@vagrant:~$ systemctl stop node-exporter
==== AUTHENTICATING FOR org.freedesktop.systemd1.manage-units ===
Authentication is required to stop 'node-exporter.service'.
Authenticating as: vagrant,,, (vagrant)
Password:
==== AUTHENTICATION COMPLETE ===

vagrant@vagrant:~$ ps -aux | grep node_exporter
vagrant     3930  0.0  0.0   9032   736 pts/0    S+   16:21   0:00 grep --color=auto node_exporter

vagrant@vagrant:~$ systemctl start node-exporter
==== AUTHENTICATING FOR org.freedesktop.systemd1.manage-units ===
Authentication is required to start 'node-exporter.service'.
Authenticating as: vagrant,,, (vagrant)
Password:
==== AUTHENTICATION COMPLETE ===

vagrant@vagrant:~$ ps -aux | grep node_exporter
root        3940  0.0  1.1 715964 11468 ?        Ssl  16:21   0:00 /home/vagrant/node_exporter/node_exporter

vagrant@vagrant:~$ sudo cat /proc/3940/environ
LANG=en_US.UTF-8LANGUAGE=en_US:PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/binINVOCATION_ID=30d0dde57e8b4aa5b5a2e1f1d81d233fJOURNAL_STREAM=9:50242

```
2. 