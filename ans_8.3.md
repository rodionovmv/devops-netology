```commandline
vagrant@server1:~/ansible/playbook/java$ molecule --version
molecule 4.0.1 using python 3.8
    ansible:2.12.7
    delegated:4.0.1 from molecule
    docker:2.0.0 from molecule_docker requiring collections: community.docker>=3.0.0-a2
    

vagrant@server1:~/ansible/playbook/java$ docker ps
CONTAINER ID   IMAGE             COMMAND       CREATED          STATUS          PORTS     NAMES
5024413c847c   ubuntu            "bash"        4 seconds ago    Up 2 seconds              ubuntu
71d6d8ec5db8   centos            "/bin/bash"   14 seconds ago   Up 12 seconds             centos
82fb17458c6c   roboxes/centos8   "/bin/bash"   23 seconds ago   Up 21 seconds             centos8
```
1.
```commandline
vagrant@server1:~/ansible/playbook/java$ cat /home/vagrant/ansible/playbook/requirements.yml

  - src: git@github.com:netology-code/mnt-homeworks-ansible.git
    scm: git
    version: "1.0.1"
    name: java
```
2. Либо я что-то не понял из задания, но скачанная роль из задания не проходит тест. 
```commandline
vagrant@server1:~/ansible/playbook/java$ molecule test
INFO     default scenario test matrix: dependency, lint, cleanup, destroy, syntax, create, prepare, converge, idempotence, side_effect, verify, cleanup, destroy
INFO     Performing prerun with role_name_check=0...
INFO     Set ANSIBLE_LIBRARY=/home/vagrant/.cache/ansible-compat/38a096/modules:/home/vagrant/.ansible/plugins/modules:/usr/share/ansible/plugins/modules
INFO     Set ANSIBLE_COLLECTIONS_PATH=/home/vagrant/.cache/ansible-compat/38a096/collections:/home/vagrant/.ansible/collections:/usr/share/ansible/collections
INFO     Set ANSIBLE_ROLES_PATH=/home/vagrant/.cache/ansible-compat/38a096/roles:/home/vagrant/.ansible/roles:/usr/share/ansible/roles:/etc/ansible/roles
ERROR    Computed fully qualified role name of java does not follow current galaxy requirements.
Please edit meta/main.yml and assure we can correctly determine full role name:

galaxy_info:
role_name: my_name  # if absent directory name hosting role is used instead
namespace: my_galaxy_namespace  # if absent, author is used instead

Namespace: https://galaxy.ansible.com/docs/contributing/namespaces.html#galaxy-namespace-limitations
Role: https://galaxy.ansible.com/docs/contributing/creating_role.html#role-names

As an alternative, you can add 'role-name' to either skip_list or warn_list.

Traceback (most recent call last):
  File "/home/vagrant/.local/bin/molecule", line 8, in <module>
    sys.exit(main())
  File "/home/vagrant/.local/lib/python3.8/site-packages/click/core.py", line 1130, in __call__
    return self.main(*args, **kwargs)
  File "/home/vagrant/.local/lib/python3.8/site-packages/click/core.py", line 1055, in main
    rv = self.invoke(ctx)
  File "/home/vagrant/.local/lib/python3.8/site-packages/click/core.py", line 1657, in invoke
    return _process_result(sub_ctx.command.invoke(sub_ctx))
  File "/home/vagrant/.local/lib/python3.8/site-packages/click/core.py", line 1404, in invoke
    return ctx.invoke(self.callback, **ctx.params)
  File "/home/vagrant/.local/lib/python3.8/site-packages/click/core.py", line 760, in invoke
    return __callback(*args, **kwargs)
  File "/home/vagrant/.local/lib/python3.8/site-packages/click/decorators.py", line 26, in new_func
    return f(get_current_context(), *args, **kwargs)
  File "/home/vagrant/.local/lib/python3.8/site-packages/molecule/command/test.py", line 176, in test
    base.execute_cmdline_scenarios(scenario_name, args, command_args, ansible_args)
  File "/home/vagrant/.local/lib/python3.8/site-packages/molecule/command/base.py", line 112, in execute_cmdline_scenarios
    scenario.config.runtime.prepare_environment(
  File "/home/vagrant/.local/lib/python3.8/site-packages/ansible_compat/runtime.py", line 362, in prepare_environment
    self._install_galaxy_role(
  File "/home/vagrant/.local/lib/python3.8/site-packages/ansible_compat/runtime.py", line 522, in _install_galaxy_role
    raise InvalidPrerequisiteError(msg)
ansible_compat.errors.InvalidPrerequisiteError: Computed fully qualified role name of java does not follow current galaxy requirements.
Please edit meta/main.yml and assure we can correctly determine full role name:

galaxy_info:
role_name: my_name  # if absent directory name hosting role is used instead
namespace: my_galaxy_namespace  # if absent, author is used instead

Namespace: https://galaxy.ansible.com/docs/contributing/namespaces.html#galaxy-namespace-limitations
Role: https://galaxy.ansible.com/docs/contributing/creating_role.html#role-names

As an alternative, you can add 'role-name' to either skip_list or warn_list.
```
После подмены файла meta/main.yml из скачнной роли molecule init role netology.java тест запускается

```commandline
~
~
~

PLAY RECAP *********************************************************************
centos                     : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
centos8                    : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
ubuntu                     : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

WARNING  Retrying execution failure 2 of: ansible-playbook --inventory /home/vagrant/.cache/molecule/java/default/inventory --skip-tags molecule-notest,notest /home/vagrant/ansible/playbook/java/molecule/default/converge.yml
CRITICAL Ansible return code was 2, command was: ['ansible-playbook', '--inventory', '/home/vagrant/.cache/molecule/java/default/inventory', '--skip-tags', 'molecule-notest,notest', '/home/vagrant/ansible/playbook/java/molecule/default/converge.yml']
WARNING  An error occurred during the test sequence action: 'converge'. Cleaning up.
INFO     Running default > cleanup
WARNING  Skipping, cleanup playbook not configured.
INFO     Running default > destroy

PLAY [Destroy] *****************************************************************

TASK [Destroy molecule instance(s)] ********************************************
changed: [localhost] => (item=centos8)
changed: [localhost] => (item=centos)
changed: [localhost] => (item=ubuntu)

TASK [Wait for instance(s) deletion to complete] *******************************
changed: [localhost] => (item=centos8)
changed: [localhost] => (item=centos)
changed: [localhost] => (item=ubuntu)

TASK [Delete docker networks(s)] ***********************************************

PLAY RECAP *********************************************************************
localhost                  : ok=2    changed=2    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0

INFO     Pruning extra files from scenario ephemeral directory

```
3.
```commandline
/elastic_role$ molecule test
INFO     default scenario test matrix: dependency, lint, cleanup, destroy, syntax, create, prepare, converge, idempotence, side_effect, verify, cleanup, destroy
INFO     Performing prerun with role_name_check=0...
INFO     Set ANSIBLE_LIBRARY=/home/vagrant/.cache/ansible-compat/93818e/modules:/home/vagrant/.ansible/plugins/modules:/usr/share/ansible/plugins/modules
INFO     Set ANSIBLE_COLLECTIONS_PATH=/home/vagrant/.cache/ansible-compat/93818e/collections:/home/vagrant/.ansible/collections:/usr/share/ansible/collections
INFO     Set ANSIBLE_ROLES_PATH=/home/vagrant/.cache/ansible-compat/93818e/roles:/home/vagrant/.ansible/roles:/usr/share/ansible/roles:/etc/ansible/roles
INFO     Using /home/vagrant/.cache/ansible-compat/93818e/roles/netology.elastic_role symlink to current repository in order to enable Ansible to find the role using its expected full name.

~
~
~

PLAY [Verify] ******************************************************************

TASK [Example assertion] *******************************************************
ok: [instance] => {
    "changed": false,
    "msg": "All assertions passed"
}

PLAY RECAP *********************************************************************
instance                   : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

INFO     Verifier completed successfully.
INFO     Running default > cleanup
WARNING  Skipping, cleanup playbook not configured.
INFO     Running default > destroy

PLAY [Destroy] *****************************************************************

TASK [Populate instance config] ************************************************
ok: [localhost]

TASK [Dump instance config] ****************************************************
skipping: [localhost]

PLAY RECAP *********************************************************************
localhost                  : ok=1    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0

INFO     Pruning extra files from scenario ephemeral directory
```
4. 

```commandline
vagrant@server1:~/ansible/elastic_role$ molecule test
INFO     default scenario test matrix: dependency, lint, cleanup, destroy, syntax, create, prepare, converge, idempotence, side_effect, verify, cleanup, destroy
INFO     Performing prerun with role_name_check=0...
~
~
~
PLAY RECAP *********************************************************************
centos                     : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
centos8                    : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
ubuntu                     : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

INFO     Verifier completed successfully.
INFO     Running default > cleanup
WARNING  Skipping, cleanup playbook not configured.
INFO     Running default > destroy

PLAY [Destroy] *****************************************************************

TASK [Populate instance config] ************************************************
ok: [localhost]

TASK [Dump instance config] ****************************************************
skipping: [localhost]

PLAY RECAP *********************************************************************
localhost                  : ok=1    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
```

5.
```commandline
vagrant@server1:~/ansible$ molecule init role --driver-name docker netology.kibana_role
INFO     Initializing new role kibana_role...
Using /etc/ansible/ansible.cfg as config file
- Role kibana_role was created successfully
Invalid -W option ignored: unknown warning category: 'CryptographyDeprecationWarning'
Invalid -W option ignored: unknown warning category: 'CryptographyDeprecationWarning'
localhost | CHANGED => {"backup": "","changed": true,"msg": "line added"}
INFO     Initialized role in /home/vagrant/ansible/kibana_role successfully.
```

```commandline
vagrant@server1:~/ansible/kibana_role$ molecule test
INFO     default scenario test matrix: dependency, lint, cleanup, destroy, syntax, create, prepare, converge, idempotence, side_effect, verify, cleanup, destroy
INFO     Performing prerun with role_name_check=0...

~
~
~

TASK [Example assertion] *******************************************************
ok: [centos] => {
    "changed": false,
    "msg": "All assertions passed"
}
ok: [centos8] => {
    "changed": false,
    "msg": "All assertions passed"
}
ok: [ubuntu] => {
    "changed": false,
    "msg": "All assertions passed"
}

PLAY RECAP *********************************************************************
centos                     : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
centos8                    : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
ubuntu                     : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

INFO     Verifier completed successfully.
INFO     Running default > cleanup
WARNING  Skipping, cleanup playbook not configured.
INFO     Running default > destroy

PLAY [Destroy] *****************************************************************

TASK [Destroy molecule instance(s)] ********************************************
changed: [localhost] => (item=centos8)
changed: [localhost] => (item=centos)
changed: [localhost] => (item=ubuntu)

TASK [Wait for instance(s) deletion to complete] *******************************
FAILED - RETRYING: [localhost]: Wait for instance(s) deletion to complete (300 retries left).
changed: [localhost] => (item=centos8)
FAILED - RETRYING: [localhost]: Wait for instance(s) deletion to complete (300 retries left).
changed: [localhost] => (item=centos)
changed: [localhost] => (item=ubuntu)

TASK [Delete docker networks(s)] ***********************************************

PLAY RECAP *********************************************************************
localhost                  : ok=2    changed=2    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0

INFO     Pruning extra files from scenario ephemeral directory
```
7.
```commandline
https://github.com/rodionovmv/kibana-role.git
https://github.com/rodionovmv/elastic-role.git
https://github.com/rodionovmv/java-role.git
```

8.

```commandline
cat requirements.yml
---
  - src: git@github.com:rodionovmv/kibana-role.git
    scm: git
    version: "1.0"
    name: kibana_role
  - src: git@github.com:rodionovmv/elastic-role.git
    scm: git
    version: "1.0"
    name: elastic_role
  - src: git@github.com:rodionovmv/java-role.git
    scm: git
    version: "1.0"
    name: java_role
```

9.
```commandline
vagrant@server1:~/ansible/playbook$ ansible-playbook -i inventory/prod.yml site.yml

PLAY [all] ********************************************************************************************************************************************************

TASK [Gathering Facts] ********************************************************************************************************************************************
ok: [ubuntu]
ok: [centos]

TASK [java_role : Upload .tar.gz file containing binaries from local storage] *************************************************************************************
skipping: [ubuntu]
skipping: [centos]

TASK [java_role : Upload .tar.gz file conaining binaries from remote storage] *************************************************************************************
ok: [centos]
ok: [ubuntu]

TASK [java_role : Ensure installation dir exists] *****************************************************************************************************************
ok: [centos]
ok: [ubuntu]

TASK [java_role : Extract java in the installation directory] *****************************************************************************************************
skipping: [ubuntu]
skipping: [centos]

TASK [java_role : Export environment variables] *******************************************************************************************************************
ok: [ubuntu]
ok: [centos]

PLAY [elasticsearch] **********************************************************************************************************************************************

TASK [Gathering Facts] ********************************************************************************************************************************************
ok: [ubuntu]

TASK [elastic_role : Upload tar.gz Elasticsearch from remote URL] *************************************************************************************************
changed: [ubuntu]

TASK [elastic_role : Create directrory for Elasticsearch] *********************************************************************************************************
changed: [ubuntu]

TASK [elastic_role : Extract Elasticsearch in the installation directory] *****************************************************************************************
changed: [ubuntu]

TASK [elastic_role : Set environment Elastic] *********************************************************************************************************************
changed: [ubuntu]

PLAY [kibana] *****************************************************************************************************************************************************

TASK [Gathering Facts] ********************************************************************************************************************************************
ok: [centos]

TASK [kibana_role : Upload tar.gz Kibana from remote URL] *********************************************************************************************************
changed: [centos]

TASK [kibana_role : Create directrory for Kibana (/opt/kibana/8.3.3)] *********************************************************************************************
changed: [centos]

TASK [kibana_role : Extract Kibana in the installation directory] *************************************************************************************************
changed: [centos]

TASK [kibana_role : Set environment Kibana] ***********************************************************************************************************************
changed: [centos]

PLAY RECAP ********************************************************************************************************************************************************
centos                     : ok=9    changed=4    unreachable=0    failed=0    skipped=2    rescued=0    ignored=0
ubuntu                     : ok=9    changed=4    unreachable=0    failed=0    skipped=2    rescued=0    ignored=0
```
10-11.
```commandline
https://github.com/rodionovmv/kibana-role
https://github.com/rodionovmv/elastic-role
https://github.com/rodionovmv/java-role
https://github.com/rodionovmv/playbook
```