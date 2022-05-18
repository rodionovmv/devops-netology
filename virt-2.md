1. 
```doctest
Опишите своими словами основные преимущества применения на практике IaaC паттернов.
```
```doctest
    Непрерывная интеграция позволяет быстро находить ошибки в коде за счет постоянного тестирования небольшими объемами. Непрерывная доставка также позволяет легко исправить или откатиться на недавнее состояние в случае ошибок.
```
```doctest
Какой из принципов IaaC является основополагающим?
```
```doctest
    Получение стабильного и предсказуемого результата каждый раз при использовании(запуске) кода.
```
2.
```doctest
Чем Ansible выгодно отличается от других систем управление конфигурациями?
```
```doctest
    Его основные преимущества это использование SSH инфраструктуры без установки дополнительного окружения, а также наличие большого количества модулей.
```

```doctest
Какой, на ваш взгляд, метод работы систем конфигурации более надёжный push или pull?
```
```doctest
    push мне кажется более надёжным. В этом случае одновременно получаем нужные конфигурации на машинах в нужный момент времени.
```

3. 
```
Virtualbox 
Графический интерфейс VirtualBox
Версия 6.1.32 r149290 (Qt5.6.2)

Vagrant
red_hat@redhat ~
$ vagrant.exe -v
Vagrant 2.2.19

Ansible
red_hat@redhat ~
$ ansible --version
ansible [core 2.12.5]
  config file = /etc/ansible/ansible.cfg
```

4. 
```doctest
Сложно и интересно, особено когда после выполнения заметил, что оно не обязательное :))
```
```doctest
Vagrant не воспринимал ansible и ругался на версию, пришлось допилить Vagrnatfile:
```
```commandline
node.vm.provision "ansible" do |ansible|
 ansible.inventory_path = INVENTORY_PATH
 ansible.playbook = "../red_hat/provion.yml"
 ansible.become = true
 ansible.verbose = 'v'
 ansible.compatibility_mode="2.0"
 ansible.extra_vars = { ansible_user: 'vagrant' }
 end
```
```doctest
Результат ansible:
```
```commandline
PLAY RECAP *******************************************************************************************************************************************************************
server1.netology           : ok=7    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```
```commandline
red_hat@redhat ~
$ vagrant ssh
Welcome to Ubuntu 20.04.3 LTS (GNU/Linux 5.4.0-91-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Wed 18 May 2022 01:41:27 PM UTC

  System load:  0.0                Users logged in:          0
  Usage of /:   13.6% of 30.88GB   IPv4 address for docker0: 172.17.0.1
  Memory usage: 22%                IPv4 address for eth0:    10.0.2.15
  Swap usage:   0%                 IPv4 address for eth1:    192.168.192.11
  Processes:    108


This system is built by the Bento project by Chef Software
More information can be found at https://github.com/chef/bento
Last login: Wed May 18 13:00:57 2022 from 10.0.2.2

vagrant@server1:~$ docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```