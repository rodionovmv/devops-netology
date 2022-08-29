1-3. 
```doctest
Создан, отредактирован, перемещен в lib/ansible/modules
```

4.
```commandline
(venv) vagrant@server1:~/ansible/my_own_collection/ansible$ python -m ansible.modules.my_own_module args.json

{"changed": true, "original_message": "Hello, netology!", "message": "file was written", "invocation": {"module_args": {"path": "/tmp/test.txt", "content": "Hello, netology!"}}}

(venv) vagrant@server1:~/ansible/my_own_collection/ansible$ cat /tmp/test.txt
Hello, netology!
```

5-6. 
Пришлось немного помучаться с версией python (ERROR: Ansible requires Python 3.9 or newer on the controller. Current version: 3.8.10). Пересоздал окружение с  python3.9 -m venv ven
```commandline

 cat site.yml
---
- name: Test my_own_module
  hosts: localhost
  gather_facts: false
  tasks:
    - name: Execute module
      my_own_module:
        path: "/tmp/test.txt"
        content: "Test module in task\nHello, netology!\n"

(venv) vagrant@server1:~/ansible/my_own_collection/ansible$ ansible-playbook site.yml
[WARNING]: You are running the development version of Ansible. You should only run Ansible from "devel" if you are modifying the Ansible engine, or trying out
features under development. This is a rapidly changing source of code and can become unstable at any point.
[WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost does not match 'all'

PLAY [Test my_own_module] *********************************************************************************************************************************************

TASK [Execute module] *************************************************************************************************************************************************
ok: [localhost]

PLAY RECAP ************************************************************************************************************************************************************
localhost                  : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

Проверка:

(venv) vagrant@server1:~/ansible/my_own_collection/ansible$ ansible-playbook site.yml
[WARNING]: You are running the development version of Ansible. You should only run Ansible from "devel" if you are modifying the Ansible engine, or trying out
features under development. This is a rapidly changing source of code and can become unstable at any point.
[WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost does not match 'all'

PLAY [Test my_own_module] *********************************************************************************************************************************************

TASK [Execute module] *************************************************************************************************************************************************
ok: [localhost]

PLAY RECAP ************************************************************************************************************************************************************
localhost                  : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

7.
```commandline
(venv) vagrant@server1:~/ansible/my_own_collection/ansible$ deactivate
vagrant@server1:~/ansible/my_own_collection/ansible$
```

8-11.
```commandline
vagrant@server1:~/ansible/my_own_collection$ ansible-galaxy collection init my_netology.my_collection
- Collection my_netology.my_collection was created successfully
```
12-13.
```commandline
vagrant@server1:~/ansible/my_own_collection/my_netology/my_collection$ ansible-playbook site2.yml
[WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost does not match 'all'

PLAY [Test my_own_module] *********************************************************************************************************************************************

TASK [Gathering Facts] ************************************************************************************************************************************************
ok: [localhost]

TASK [my_role : Execute module] ***************************************************************************************************************************************
ok: [localhost]

PLAY RECAP ************************************************************************************************************************************************************
localhost                  : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

vagrant@server1:~/ansible/my_own_collection/my_netology/my_collection$ ansible-playbook site2.yml
[WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost does not match 'all'

PLAY [Test my_own_module] *********************************************************************************************************************************************

TASK [Gathering Facts] ************************************************************************************************************************************************
ok: [localhost]

TASK [my_role : Execute module] ***************************************************************************************************************************************
ok: [localhost]

PLAY RECAP ************************************************************************************************************************************************************
localhost                  : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

```commandline
vagrant@server1:~/ansible/my_own_collection/my_netology/my_collection$ ansible-galaxy collection build
Created collection for my_netology.my_collection at /home/vagrant/ansible/my_own_collection/my_netology/my_collection/my_netology-my_collection-1.0.0.tar.gz
```
14-15.

```commandline
vagrant@server1:~/ansible/test$ ansible-galaxy collection install my_netology-my_collection-1.0.0.tar.gz -p collection
Starting galaxy collection install process
[WARNING]: The specified collections path '/home/vagrant/ansible/test/collection' is not part of the configured Ansible collections paths
'/home/vagrant/.ansible/collections:/usr/share/ansible/collections'. The installed collection won't be picked up in an Ansible run.
Process install dependency map
Starting collection install process
Installing 'my_netology.my_collection:1.0.0' to '/home/vagrant/ansible/test/collection/ansible_collections/my_netology/my_collection'
my_netology.my_collection:1.0.0 was installed successfully
vagrant@server1:~/ansible/test$ tree
.
├── collection
│   └── ansible_collections
│       └── my_netology
│           └── my_collection
│               ├── docs
│               ├── FILES.json
│               ├── inventory
│               │   └── prod.yml
│               ├── MANIFEST.json
│               ├── plugins
│               │   ├── modules
│               │   │   └── my_own_module.py
│               │   └── README.md
│               ├── README.md
│               ├── roles
│               │   └── my_role
│               │       ├── defaults
│               │       │   └── main.yml
│               │       ├── files
│               │       ├── handlers
│               │       │   └── main.yml
│               │       ├── meta
│               │       │   └── main.yml
│               │       ├── README.md
│               │       ├── tasks
│               │       │   └── main.yml
│               │       ├── templates
│               │       ├── tests
│               │       │   ├── inventory
│               │       │   └── test.yml
│               │       └── vars
│               │           └── main.yml
│               ├── site2.yml
│               └── site.yml
└── my_netology-my_collection-1.0.0.tar.gz

18 directories, 17 files

```

16.
```commandline

vagrant@server1:~/ansible/test/collection/ansible_collections/my_netology/my_collection$ ansible-playbook site2.yml
[WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost does not match 'all'

PLAY [Test my_own_module] *********************************************************************************************************************************************

TASK [Gathering Facts] ************************************************************************************************************************************************
ok: [localhost]

TASK [my_role : Execute module] ***************************************************************************************************************************************
ok: [localhost]

PLAY RECAP ************************************************************************************************************************************************************
localhost                  : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

vagrant@server1:~/ansible/test/collection/ansible_collections/my_netology/my_collection$ ll /tmp/test*
-rw-rw-r-- 1 vagrant vagrant 37 Aug 26 13:54 /tmp/test_file.txt
vagrant@server1:~/ansible/test/collection/ansible_collections/my_netology/my_collection$

vagrant@server1:~/ansible/test/collection/ansible_collections/my_netology/my_collection$ cat /tmp/test_file.txt
Test module in task
Hello, netology!
```

17.
```commandline
https://github.com/rodionovmv/collection.git
```