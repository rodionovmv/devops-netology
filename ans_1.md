1.
```commandline
vagrant@server1:~/ansible/playbook$ ansible-playbook -i inventory/test.yml site.yml

PLAY [Print os facts] *******************************************************************************************************************************

TASK [Gathering Facts] ******************************************************************************************************************************
ok: [localhost]

TASK [Print OS] *************************************************************************************************************************************
ok: [localhost] => {
    "msg": "Ubuntu"
}

TASK [Print fact] ***********************************************************************************************************************************
ok: [localhost] => {
    "msg": 12
}

PLAY RECAP ******************************************************************************************************************************************
localhost                  : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```
2.
```commandline
vagrant@server1:~/ansible/playbook$ cat group_vars/all/examp.yml
---
  some_fact: all_default_facts
vagrant@server1:~/ansible/playbook$ ansible-playbook -i inventory/test.yml site.yml

PLAY [Print os facts] *******************************************************************************************************************************

TASK [Gathering Facts] ******************************************************************************************************************************
ok: [localhost]

TASK [Print OS] *************************************************************************************************************************************
ok: [localhost] => {
    "msg": "Ubuntu"
}

TASK [Print fact] ***********************************************************************************************************************************
ok: [localhost] => {
    "msg": "all_default_facts"
}

PLAY RECAP ******************************************************************************************************************************************
localhost                  : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

```
4.
```commandline
vagrant@server1:~/ansible/playbook$ ansible-playbook -i inventory/prod.yml site.yml

PLAY [Print os facts] *********************************************************************************************************************************************

TASK [Gathering Facts] ********************************************************************************************************************************************
ok: [ubuntu]
ok: [centos]

TASK [Print OS] ***************************************************************************************************************************************************
ok: [centos] => {
    "msg": "CentOS"
}
ok: [ubuntu] => {
    "msg": "Ubuntu"
}

TASK [Print fact] *************************************************************************************************************************************************
ok: [centos] => {
    "msg": "el"
}
ok: [ubuntu] => {
    "msg": "deb"
}

PLAY RECAP ********************************************************************************************************************************************************
centos                     : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
ubuntu                     : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```
5.
```commandline
vagrant@server1:~/ansible/playbook$ cat group_vars/deb/examp.yml
---
  some_fact: "deb default facts"
vagrant@server1:~/ansible/playbook$ cat group_vars/el/examp.yml
---
  some_fact: "el default facts"
```
6.
```commandline
vagrant@server1:~/ansible/playbook$ ansible-playbook -i inventory/prod.yml site.yml

PLAY [Print os facts] *********************************************************************************************************************************************

TASK [Gathering Facts] ********************************************************************************************************************************************
ok: [ubuntu]
ok: [centos]

TASK [Print OS] ***************************************************************************************************************************************************
ok: [centos] => {
    "msg": "CentOS"
}
ok: [ubuntu] => {
    "msg": "Ubuntu"
}

TASK [Print fact] *************************************************************************************************************************************************
ok: [centos] => {
    "msg": "el default facts"
}
ok: [ubuntu] => {
    "msg": "deb default facts"
}

PLAY RECAP ********************************************************************************************************************************************************
centos                     : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
ubuntu                     : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```
7.
```commandline
vagrant@server1:~/ansible/playbook$ ansible-vault encrypt group_vars/deb/examp.yml
New Vault password:
Confirm New Vault password:
Encryption successful
vagrant@server1:~/ansible/playbook$ ansible-vault encrypt group_vars/el/examp.yml
New Vault password:
Confirm New Vault password:
Encryption successful
```
8.
```commandline
vagrant@server1:~/ansible/playbook$ ansible-playbook -i inventory/prod.yml site.yml --ask-vault-pass
Vault password:

PLAY [Print os facts] *********************************************************************************************************************************************

TASK [Gathering Facts] ********************************************************************************************************************************************
ok: [ubuntu]
ok: [centos]

TASK [Print OS] ***************************************************************************************************************************************************
ok: [centos] => {
    "msg": "CentOS"
}
ok: [ubuntu] => {
    "msg": "Ubuntu"
}

TASK [Print fact] *************************************************************************************************************************************************
ok: [centos] => {
    "msg": "el default facts"
}
ok: [ubuntu] => {
    "msg": "deb default facts"
}

PLAY RECAP ********************************************************************************************************************************************************
centos                     : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
ubuntu                     : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```
9.
```commandline
local
```
10.
```commandline
vagrant@server1:~/ansible/playbook$ cat inventory/prod.yml
---
  el:
    hosts:
      centos:
        ansible_connection: docker
  deb:
    hosts:
      ubuntu:
        ansible_connection: docker
  local:
    hosts:
      localhost:
        ansible_connection: local
```
11.
```commandline
vagrant@server1:~/ansible/playbook$ ansible-playbook -i inventory/prod.yml site.yml --ask-vault-pass
Vault password:

PLAY [Print os facts] *********************************************************************************************************************************************

TASK [Gathering Facts] ********************************************************************************************************************************************
ok: [localhost]
ok: [ubuntu]
ok: [centos]

TASK [Print OS] ***************************************************************************************************************************************************
ok: [centos] => {
    "msg": "CentOS"
}
ok: [ubuntu] => {
    "msg": "Ubuntu"
}
ok: [localhost] => {
    "msg": "Ubuntu"
}

TASK [Print fact] *************************************************************************************************************************************************
ok: [centos] => {
    "msg": "el default facts"
}
ok: [ubuntu] => {
    "msg": "deb default facts"
}
ok: [localhost] => {
    "msg": "all_default_facts"
}

PLAY RECAP ********************************************************************************************************************************************************
centos                     : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
localhost                  : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
ubuntu                     : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```
