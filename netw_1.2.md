1. Linux - ip, ifconfig, nmcli / Windows - ipconfig
2. LLDP. lldpctl show neighborns / lldpctl ; lldpcli - show neighborns
3. VLAN (802.1Q). apt install vlan / yum install vlan ; modprobe 8021q 
cat /etc/sysconfig/network-scripts/ifcfg-eth0.2
   TYPE=Ethernet
   PROXY_METHOD=none
   BROWSER_ONLY=no
   BOOTPROTO=none
   DEFROUTE=yes
   IPV4_FAILURE_FATAL=no
   NAME=eth0.2
   UUID=04cb4fa6-f820-45c0-b847-df94e9628bc5
   DEVICE=eth0.2
   ONBOOT=yes
   IPADDR=192.168.2.100
   NETMASK=255.255.255.0
   VLAN=yes
 
   Debian
   auto eth0.2
     iface eth0.2 inet static
     address 10.0.0.1
     netmask 255.255.255.0
     vlan-raw-device eth0

4. Какие типы агрегации интерфейсов есть в Linux? - статическая, динамическая.
     
      Опции:
      mode=0 (Balance Round Robin)
      mode=1 (Active backup)
      mode=2 (Balance XOR)
      mode=3 (Broadcast)
      mode=4 (802.3ad)
      mode=5 (Balance TLB)
      mode=6 (Balance ALB)
       
   cat /etc/network/interfaces
       # The loopback network interface
       auto lo
       iface lo inet loopback
       # The primary network interface
       auto bond0 eth0 eth1
       iface bond0 inet static
        address 10.0.0.11
        netmask 255.255.255.0
        gateway 10.0.0.254
        bond-slaves eth0 eth1
        bond-mode balance-alb
        bond-miimon 100
        bond-downdelay 200
        bond-updelay 200

5. Сколько IP адресов в сети с маской /29 ? - 8 (6 используется)
    В /24 сети - 32 сети /29
    10.10.10.0/29 (10.10.10.0-10.10.10.7)
    10.10.10.8/29 (10.10.10.8-10.10.10.15)
    10.10.10.16/29 (10.10.10.16-10.10.10.23)
    etc...
    
6. 100.64.0.0 — 100.127.255.255 - CGN (RFC 6598)
   /26 - 62 хоста

7. Windows/Linux:
   проверить ARP: arp -a
   очистить ARP: arp -d -a; 
   удалить 1 ip: arp -d 192.168.1.100
   + для Linux - ip neigh; ip neigh del 192.168.1.100 dev em1; ip neigh flush all

  
   