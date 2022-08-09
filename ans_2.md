1.
```commandline
vagrant@server1:~/ansible/playbook$ cat inventory/prod.yml
---
elasticsearch:
  hosts:
    ubuntu:
      ansible_connection: docker
kibana:
  hosts:
    centos:
      ansible_connection: docker
```
2-4.
```commandline
- name: Install Kibana
  hosts: kibana
  tasks:
    - name: Upload tar.gz Kibana from remote URL
      get_url:
        url: "https://artifacts.elastic.co/downloads/kibana/kibana-{{ kibana_version }}-linux-x86_64.tar.gz"
        dest: "/tmp/kibana-{{ kibana_version }}-linux-x86_64.tar.gz"
        mode: 0755
        timeout: 60
        force: true
        validate_certs: false
      register: get_kibana
      until: get_kibana is succeeded
      tags: kibana
    - name: Create directrory for Kibana ({{ kibana_home }})
      file:
        path: "{{ kibana_home }}"
        state: directory
      tags: kibana
    - name: Extract Kibana in the installation directory
      #become: yes
      unarchive:
        copy: false
        src: "/tmp/kibana-{{ kibana_version }}-linux-x86_64.tar.gz"
        dest: "{{ kibana_home }}"
        extra_opts: [--strip-components=1]
        creates: "{{ kibana_home }}/bin/kibana"
      tags:
        - skip_ansible_lint
        - kibana
    - name: Set environment Kibana
      #become: yes
      template:
        src: templates/kib.sh.j2
        dest: /etc/profile.d/kib.sh
      tags: kibana
```
5.
```doctest
vagrant@server1:~/ansible/playbook$ ansible-lint site.yml -vvv
Examining site.yml of type playbook
vagrant@server1:~/ansible/playbook$

Ошибок небыло
```
6.
```commandline
vagrant@server1:~/ansible/playbook$ ansible-playbook -i inventory/prod.yml site.yml --check

PLAY [Install Java] *********************************************************************************************************************************************

TASK [Gathering Facts] ******************************************************************************************************************************************
ok: [centos]
ok: [ubuntu]

TASK [Set facts for Java 11 vars] *******************************************************************************************************************************
ok: [ubuntu]
ok: [centos]

TASK [Upload .tar.gz file containing binaries from local storage] ***********************************************************************************************
changed: [centos]
changed: [ubuntu]

TASK [Ensure installation dir exists] ***************************************************************************************************************************
changed: [ubuntu]
changed: [centos]

TASK [Extract java in the installation directory] ***************************************************************************************************************
An exception occurred during task execution. To see the full traceback, use -vvv. The error was: NoneType: None
fatal: [ubuntu]: FAILED! => {"changed": false, "msg": "dest '/opt/jdk/11.0.16' must be an existing dir"}
An exception occurred during task execution. To see the full traceback, use -vvv. The error was: NoneType: None
fatal: [centos]: FAILED! => {"changed": false, "msg": "dest '/opt/jdk/11.0.16' must be an existing dir"}

PLAY RECAP ******************************************************************************************************************************************************
centos                     : ok=4    changed=2    unreachable=0    failed=1    skipped=0    rescued=0    ignored=0
ubuntu                     : ok=4    changed=2    unreachable=0    failed=1    skipped=0    rescued=0    ignored=0

```
7.
```commandline
vagrant@server1:~/ansible/playbook$ ansible-playbook -i inventory/prod.yml site.yml --diff

PLAY [Install Java] *********************************************************************************************************************************************

TASK [Gathering Facts] ******************************************************************************************************************************************
ok: [centos]
ok: [ubuntu]

TASK [Set facts for Java 11 vars] *******************************************************************************************************************************
ok: [ubuntu]
ok: [centos]

TASK [Upload .tar.gz file containing binaries from local storage] ***********************************************************************************************
diff skipped: source file size is greater than 104448
changed: [centos]
diff skipped: source file size is greater than 104448
changed: [ubuntu]

TASK [Ensure installation dir exists] ***************************************************************************************************************************
--- before
+++ after
@@ -1,4 +1,4 @@
 {
     "path": "/opt/jdk/11.0.16",
-    "state": "absent"
+    "state": "directory"
 }

changed: [ubuntu]
--- before
+++ after
@@ -1,4 +1,4 @@
 {
     "path": "/opt/jdk/11.0.16",
-    "state": "absent"
+    "state": "directory"
 }

changed: [centos]

TASK [Extract java in the installation directory] ***************************************************************************************************************
changed: [ubuntu]
changed: [centos]

TASK [Export environment variables] *****************************************************************************************************************************
--- before
+++ after: /home/vagrant/.ansible/tmp/ansible-local-8978v04zdq9t/tmpksblkdz3/jdk.sh.j2
@@ -0,0 +1,5 @@
+# Warning: This file is Ansible Managed, manual changes will be overwritten on next playbook run.
+#!/usr/bin/env bash
+
+export JAVA_HOME=/opt/jdk/11.0.16
+export PATH=$PATH:$JAVA_HOME/bin
\ No newline at end of file

changed: [ubuntu]
--- before
+++ after: /home/vagrant/.ansible/tmp/ansible-local-8978v04zdq9t/tmp2a1xlnyk/jdk.sh.j2
@@ -0,0 +1,5 @@
+# Warning: This file is Ansible Managed, manual changes will be overwritten on next playbook run.
+#!/usr/bin/env bash
+
+export JAVA_HOME=/opt/jdk/11.0.16
+export PATH=$PATH:$JAVA_HOME/bin
\ No newline at end of file

changed: [centos]

PLAY [Install Elasticsearch] ************************************************************************************************************************************

TASK [Gathering Facts] ******************************************************************************************************************************************
ok: [ubuntu]

TASK [Upload tar.gz Elasticsearch from remote URL] **************************************************************************************************************
changed: [ubuntu]

TASK [Create directrory for Elasticsearch] **********************************************************************************************************************
--- before
+++ after
@@ -1,4 +1,4 @@
 {
     "path": "/opt/elastic/8.3.3",
-    "state": "absent"
+    "state": "directory"
 }

changed: [ubuntu]

TASK [Extract Elasticsearch in the installation directory] ******************************************************************************************************
changed: [ubuntu]

TASK [Set environment Elastic] **********************************************************************************************************************************
--- before
+++ after: /home/vagrant/.ansible/tmp/ansible-local-8978v04zdq9t/tmpfdkbxbye/elk.sh.j2
@@ -0,0 +1,5 @@
+# Warning: This file is Ansible Managed, manual changes will be overwritten on next playbook run.
+#!/usr/bin/env bash
+
+export ES_HOME=/opt/elastic/8.3.3
+export PATH=$PATH:$ES_HOME/bin
\ No newline at end of file

changed: [ubuntu]

PLAY [Install Kibana] *******************************************************************************************************************************************

TASK [Gathering Facts] ******************************************************************************************************************************************
ok: [centos]

TASK [Upload tar.gz Kibana from remote URL] *********************************************************************************************************************
changed: [centos]

TASK [Create directrory for Kibana (/opt/kibana/8.3.3)] *********************************************************************************************************
--- before
+++ after
@@ -1,4 +1,4 @@
 {
     "path": "/opt/kibana/8.3.3",
-    "state": "absent"
+    "state": "directory"
 }

changed: [centos]

TASK [Extract Kibana in the installation directory] *************************************************************************************************************
changed: [centos]

TASK [Set environment Kibana] ***********************************************************************************************************************************
--- before
+++ after: /home/vagrant/.ansible/tmp/ansible-local-8978v04zdq9t/tmpz9yn8458/kib.sh.j2
@@ -0,0 +1,5 @@
+# Warning: This file is Ansible Managed, manual changes will be overwritten on next playbook run.
+#!/usr/bin/env bash
+
+export KIBANA_HOME=/opt/kibana/8.3.3
+export PATH=$PATH:$KIBANA_HOME/bin

changed: [centos]

PLAY RECAP ******************************************************************************************************************************************************
centos                     : ok=11   changed=8    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
ubuntu                     : ok=11   changed=8    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```
8.
```commandline
vagrant@server1:~/ansible/playbook$ ansible-playbook -i inventory/prod.yml site.yml --diff

PLAY [Install Java] *********************************************************************************************************************************************

TASK [Gathering Facts] ******************************************************************************************************************************************
ok: [centos]
ok: [ubuntu]

TASK [Set facts for Java 11 vars] *******************************************************************************************************************************
ok: [ubuntu]
ok: [centos]

TASK [Upload .tar.gz file containing binaries from local storage] ***********************************************************************************************
ok: [centos]
ok: [ubuntu]

TASK [Ensure installation dir exists] ***************************************************************************************************************************
ok: [ubuntu]
ok: [centos]

TASK [Extract java in the installation directory] ***************************************************************************************************************
skipping: [ubuntu]
skipping: [centos]

TASK [Export environment variables] *****************************************************************************************************************************
ok: [ubuntu]
ok: [centos]

PLAY [Install Elasticsearch] ************************************************************************************************************************************

TASK [Gathering Facts] ******************************************************************************************************************************************
ok: [ubuntu]

TASK [Upload tar.gz Elasticsearch from remote URL] **************************************************************************************************************
ok: [ubuntu]

TASK [Create directrory for Elasticsearch] **********************************************************************************************************************
ok: [ubuntu]

TASK [Extract Elasticsearch in the installation directory] ******************************************************************************************************
skipping: [ubuntu]

TASK [Set environment Elastic] **********************************************************************************************************************************
ok: [ubuntu]

PLAY [Install Kibana] *******************************************************************************************************************************************

TASK [Gathering Facts] ******************************************************************************************************************************************
ok: [centos]

TASK [Upload tar.gz Kibana from remote URL] *********************************************************************************************************************
ok: [centos]

TASK [Create directrory for Kibana (/opt/kibana/8.3.3)] *********************************************************************************************************
ok: [centos]

TASK [Extract Kibana in the installation directory] *************************************************************************************************************
skipping: [centos]

TASK [Set environment Kibana] ***********************************************************************************************************************************
ok: [centos]

PLAY RECAP ******************************************************************************************************************************************************
centos                     : ok=9    changed=0    unreachable=0    failed=0    skipped=2    rescued=0    ignored=0
ubuntu                     : ok=9    changed=0    unreachable=0    failed=0    skipped=2    rescued=0    ignored=0
```