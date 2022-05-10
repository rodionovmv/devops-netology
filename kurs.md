Настройка ufw
```commandline
vagrant@vagrant:~$ sudo ufw allow ssh
Rules updated
Rules updated (v6)
vagrant@vagrant:~$ sudo ufw allow 443/tcp
Rules updated
Rules updated (v6)
vagrant@vagrant:~$ sudo ufw enable
Command may disrupt existing ssh connections. Proceed with operation (y|n)? y
Firewall is active and enabled on system startup
vagrant@vagrant:~$ sudo ufw status verbose
Status: active
Logging: on (low)
Default: deny (incoming), allow (outgoing), disabled (routed)
New profiles: skip

To                         Action      From
--                         ------      ----
22/tcp                     ALLOW IN    Anywhere
443/tcp                    ALLOW IN    Anywhere
22/tcp (v6)                ALLOW IN    Anywhere (v6)
443/tcp (v6)               ALLOW IN    Anywhere (v6)

```

Vault и сертификат 
```commandline
vagrant@vagrant:~$ curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add
curl: (22) The requested URL returned error: 403
gpg: no valid OpenPGP data found.
```
Без vpn сейчас никуда, строим маршрут на роутере до apt.releases.hashicorp.com через VPN :)

```commandline
vagrant@vagrant:~$ curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -
OK
vagrant@vagrant:~$ sudo apt-add-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main"
vagrant@vagrant:~$ sudo apt-get update && sudo apt-get install vault

vagrant@vagrant:~$ vault
Usage: vault <command> [args]

Common commands:
    read        Read data and retrieves secrets
    write       Write data, configuration, and secrets
    delete      Delete secrets and configuration
    list        List data or secrets
    login       Authenticate locally
    agent       Start a Vault agent
    server      Start a Vault server
    status      Print seal and HA status
    unwrap      Unwrap a wrapped secret

Other commands:
    audit                Interact with audit devices
    auth                 Interact with auth methods
    debug                Runs the debug command
    kv                   Interact with Vault's Key-Value storage
    lease                Interact with leases
    monitor              Stream log messages from a Vault server
    namespace            Interact with namespaces
    operator             Perform operator-specific tasks
    path-help            Retrieve API help for paths
    plugin               Interact with Vault plugins and catalog
    policy               Interact with policies
    print                Prints runtime configurations
    secrets              Interact with secrets engines
    ssh                  Initiate an SSH session
    token                Interact with tokens
    version-history      Prints the version history of the target Vault server
```  
Установим jq
```commandline
vagrant@vagrant:~$ sudo apt-get install jq
```
Запустим vault в dev режиме в новом терминале
```commandline
vagrant@vagrant:~$ vault server -dev -dev-root-token-id root
==> Vault server configuration:

             Api Address: http://127.0.0.1:8200
                     Cgo: disabled
         Cluster Address: https://127.0.0.1:8201
              Go Version: go1.17.9
              Listener 1: tcp (addr: "127.0.0.1:8200", cluster address: "127.0.0.1:8201", max_request_duration: "1m30s", max_request_size: "33554432", tls: "disabled")
               Log Level: info
                   Mlock: supported: true, enabled: false
           Recovery Mode: false
                 Storage: inmem
                 Version: Vault v1.10.2
             Version Sha: 94325865b12662cb72efa3003d6aaa4f5ae57f3a

==> Vault server started! Log data will stream in below:

2022-05-10T09:33:00.642Z [INFO]  proxy environment: http_proxy="" https_proxy="" no_proxy=""
2022-05-10T09:33:00.642Z [WARN]  no `api_addr` value specified in config or in VAULT_API_ADDR; falling back to detection if possible, but this value should be manually set
2022-05-10T09:33:00.642Z [INFO]  core: Initializing versionTimestamps for core
2022-05-10T09:33:00.643Z [INFO]  core: security barrier not initialized
2022-05-10T09:33:00.643Z [INFO]  core: security barrier initialized: stored=1 shares=1 threshold=1
2022-05-10T09:33:00.643Z [INFO]  core: post-unseal setup starting
2022-05-10T09:33:00.645Z [INFO]  core: loaded wrapping token key
2022-05-10T09:33:00.645Z [INFO]  core: Recorded vault version: vault version=1.10.2 upgrade time="2022-05-10 09:33:00.645140154 +0000 UTC"
2022-05-10T09:33:00.645Z [INFO]  core: successfully setup plugin catalog: plugin-directory=""
2022-05-10T09:33:00.645Z [INFO]  core: no mounts; adding default mount table
2022-05-10T09:33:00.645Z [INFO]  core: successfully mounted backend: type=cubbyhole path=cubbyhole/
2022-05-10T09:33:00.646Z [INFO]  core: successfully mounted backend: type=system path=sys/
2022-05-10T09:33:00.646Z [INFO]  core: successfully mounted backend: type=identity path=identity/
2022-05-10T09:33:00.647Z [INFO]  core: successfully enabled credential backend: type=token path=token/
2022-05-10T09:33:00.647Z [INFO]  rollback: starting rollback manager
2022-05-10T09:33:00.647Z [INFO]  core: restoring leases
2022-05-10T09:33:00.648Z [INFO]  expiration: lease restore complete
2022-05-10T09:33:00.648Z [INFO]  identity: entities restored
2022-05-10T09:33:00.648Z [INFO]  identity: groups restored
2022-05-10T09:33:00.940Z [INFO]  core: post-unseal setup complete
2022-05-10T09:33:00.940Z [INFO]  core: root token generated
2022-05-10T09:33:00.940Z [INFO]  core: pre-seal teardown starting
2022-05-10T09:33:00.940Z [INFO]  rollback: stopping rollback manager
2022-05-10T09:33:00.940Z [INFO]  core: pre-seal teardown complete
2022-05-10T09:33:00.940Z [INFO]  core.cluster-listener.tcp: starting listener: listener_address=127.0.0.1:8201
2022-05-10T09:33:00.940Z [INFO]  core.cluster-listener: serving cluster requests: cluster_listen_address=127.0.0.1:8201
2022-05-10T09:33:00.940Z [INFO]  core: post-unseal setup starting
2022-05-10T09:33:00.941Z [INFO]  core: loaded wrapping token key
2022-05-10T09:33:00.941Z [INFO]  core: successfully setup plugin catalog: plugin-directory=""
2022-05-10T09:33:00.941Z [INFO]  core: successfully mounted backend: type=system path=sys/
2022-05-10T09:33:00.941Z [INFO]  core: successfully mounted backend: type=identity path=identity/
2022-05-10T09:33:00.941Z [INFO]  core: successfully mounted backend: type=cubbyhole path=cubbyhole/
2022-05-10T09:33:00.942Z [INFO]  core: successfully enabled credential backend: type=token path=token/
2022-05-10T09:33:00.942Z [INFO]  rollback: starting rollback manager
2022-05-10T09:33:00.942Z [INFO]  core: restoring leases
2022-05-10T09:33:00.942Z [INFO]  identity: entities restored
2022-05-10T09:33:00.942Z [INFO]  identity: groups restored
2022-05-10T09:33:00.942Z [INFO]  expiration: lease restore complete
2022-05-10T09:33:00.942Z [INFO]  core: post-unseal setup complete
2022-05-10T09:33:00.942Z [INFO]  core: vault is unsealed
2022-05-10T09:33:00.943Z [INFO]  expiration: revoked lease: lease_id=auth/token/root/h299a5b66ae9aaa9d3cef53665fb319500a4a16603e8363d0fc0e9a6af61df842
2022-05-10T09:33:00.947Z [INFO]  core: successful mount: namespace="" path=secret/ type=kv
2022-05-10T09:33:00.957Z [INFO]  secrets.kv.kv_cc357bd5: collecting keys to upgrade
2022-05-10T09:33:00.957Z [INFO]  secrets.kv.kv_cc357bd5: done collecting keys: num_keys=1
2022-05-10T09:33:00.957Z [INFO]  secrets.kv.kv_cc357bd5: upgrading keys finished
WARNING! dev mode is enabled! In this mode, Vault runs entirely in-memory
and starts unsealed with a single unseal key. The root token is already
authenticated to the CLI, so you can immediately begin using Vault.

You may need to set the following environment variable:

    $ export VAULT_ADDR='http://127.0.0.1:8200'

The unseal key and root token are displayed below in case you want to
seal/unseal the Vault or re-authenticate.

Unseal Key: X7BjmGfHSLQJaaevNehSmUIYsBfaVZtPyxJgvK6/UHI=
Root Token: root

Development mode should NOT be used in production installations!


```
Создадим переменные окружения в основном терминале (You may need to set the following environment variable:)
```commandline

vagrant@vagrant:~$ export VAULT_ADDR=http://127.0.0.1:8200
vagrant@vagrant:~$ export VAULT_TOKEN=root
```

Сгенерируем корневой сертификат
```commandline
vagrant@vagrant:~$ vault secrets enable pki
Success! Enabled the pki secrets engine at: pki/
vagrant@vagrant:~$ vault secrets tune -max-lease-ttl=87600h pki
Success! Tuned the secrets engine at: pki/
vagrant@vagrant:~$ vault write -field=certificate pki/root/generate/internal \
> common_name="netology.rh" \
> ttl=87600h > CA_cert.crt
vagrant@vagrant:~$ vault write pki/config/urls \
> issuing_certificates="$VAULT_ADDR/v1/pki/ca" \
> crl_distribution_points="$VAULT_ADDR/v1/pki/crl"
Success! Data written to: pki/config/urls
```
Сгенерируем промежуточный сертификат
```commandline
vagrant@vagrant:~$ vault secrets enable -path=pki_int pki
Success! Enabled the pki secrets engine at: pki_int/
vagrant@vagrant:~$ vault secrets tune -max-lease-ttl=720h pki_int
Success! Tuned the secrets engine at: pki_int/
vagrant@vagrant:~$ vault write -format=json pki_int/intermediate/generate/internal \
> common_name="netology.rh Intermediate Authority" \
> | jq -r '.data.csr' > pki_intermediate.csr
vagrant@vagrant:~$ vault write -format=json pki/root/sign-intermediate csr=@pki_intermediate.csr \
>  format=pem_bundle ttl="43800h" \
> | jq -r '.data.certificate' > intermediate.cert.pem
vagrant@vagrant:~$ vault write pki_int/intermediate/set-signed certificate=@intermediate.cert.pem
Success! Data written to: pki_int/intermediate/set-signed
```
Создадим роль
```commandline
vagrant@vagrant:~$ vault write pki_int/roles/netology-dot-rh \
> allowed_domains="test.netology.rh" \
> allow_bare_domains=true \
> alt_names="test.netology.rh" \
> allow_subdomains=true \
> max_ttl="720h"
Success! Data written to: pki_int/roles/netology-dot-rh
```
Создадим сертификат для роли и поддомена
```commandline
vagrant@vagrant:~$ vault write -format=json pki_int/issue/netology-dot-rh common_name="test.netology.rh" ttl="720h" > all.crt
```
Выгружаем его в файл all.crt а из него уже раскидывать по файлам ключей и сертификатов
```commandline
vagrant@vagrant:~$ cat all.crt | jq -r .data.certificate > test.netology.rh.crt
vagrant@vagrant:~$ cat all.crt | jq -r .data.issuing_ca >> test.netology.rh.crt
vagrant@vagrant:~$ cat all.crt | jq -r .data.private_key > test.netology.rh.key
```
Установим nginx
```commandline
vagrant@vagrant:~$ sudo apt install nginx
vagrant@vagrant:~$ sudo systemctl status nginx.service
● nginx.service - A high performance web server and a reverse proxy server
     Loaded: loaded (/lib/systemd/system/nginx.service; enabled; vendor preset: enabled)
     Active: active (running) since Tue 2022-05-10 12:21:13 UTC; 3s ago
       Docs: man:nginx(8)
    Process: 4040 ExecStartPre=/usr/sbin/nginx -t -q -g daemon on; master_process on; (code=exited, status=0/SUCCESS)
    Process: 4041 ExecStart=/usr/sbin/nginx -g daemon on; master_process on; (code=exited, status=0/SUCCESS)
   Main PID: 4048 (nginx)
      Tasks: 3 (limit: 1071)
     Memory: 3.5M
     CGroup: /system.slice/nginx.service
             ├─4048 nginx: master process /usr/sbin/nginx -g daemon on; master_process on;
             ├─4053 nginx: worker process
             └─4054 nginx: worker process
```

Создадим страницу по адресу test.netology.rh
```commandline
vagrant@vagrant:~$ sudo mkdir -p /var/www/test.netology.rh/html
vagrant@vagrant:~$ sudo chown -R vagrant:vagrant /var/www/test.netology.rh/html
vagrant@vagrant:~$ sudo chmod -R 755 /var/www/test.netology.rh/html
vagrant@vagrant:~$ nano /var/www/test.netology.rh/html/index.html
vagrant@vagrant:~$ sudo ln -s /etc/nginx/sites-available/test.netology.rh /etc/nginx/sites-enabled/
vagrant@vagrant:~$ sudo systemctl restart nginx

echo '<h1>Hello test.netology.rh </h1>' > /var/www/test.netology.rh/html/index.html
```

Скрипт для генерации сертификата
```commandline
vagrant@vagrant:~$ cat ~/crt.sh
#!/usr/bin/env bash
vault write -format=json pki_int/issue/netology-dot-rh common_name="test.netology.rh" ttl="720h" > /home/vagrant/all.crt
cat all.crt | jq -r .data.certificate > /home/vagrant/test.netology.rh.crt
cat all.crt | jq -r .data.issuing_ca >> /home/vagrant/test.netology.rh.crt
cat all.crt | jq -r .data.private_key > /home/vagrant/test.netology.rh.key
```

Скрпит для перезапуска nginx

```commandline
vagrant@vagrant:~$ sudo cat /root/nginx.sh
#!/usr/bin/env bash
systemctl restart nginx.service
```

Crontab для перевыпуска сертификатов

```commandline
vagrant@vagrant:~$ crontab -l

# m h  dom mon dow   command

1 0 1 * * /home/vagrant/crt.sh
```
Crontab root для перезапуска nginx
```commandline
vagrant@vagrant:~$ sudo crontab -l

# m h  dom mon dow   command
2 0 1 * * /root/nginx.sh

```