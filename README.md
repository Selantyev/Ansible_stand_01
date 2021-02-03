# Ansible_stand_01

Развёрнуты две виртуалки: nginx и ansible. Доступ к ним по паролю (SSH). Так как запускаем vagrant из под windows 10, провижининг хоста nginx будем делать с помощью виртуалки ansible.

На виртуалке ansible:

1. Установить Ansible (ansible 2.9.16)

```
yum install epel-release -y
yum update -y
yum install ansible -y
```

2. Отредактировать ansible.cfg

`vi /etc/ansible/ansible.cfg`

```
[defaults]
forks = 25
transport = ssh
host_key_checking = False
roles_path = /home/vagrant/ansible/roles.galaxy/nginx
retry_files_enabled = False
ansible_user = vagrant
inventory = /home/vagrant/ansible/hosts

[diff]
always = True
context = 3
```

3. Создать директорию с ролью

`ansible-galaxy init --init-path /home/vagrant/ansible/roles.galaxy/ nginx`

`tree /home/vagrant/ansible/roles.galaxy/`

```
/home/vagrant/ansible/roles.galaxy/
└── nginx
    ├── defaults
    │   └── main.yml
    ├── files
    ├── handlers
    │   └── main.yml
    ├── meta
    │   └── main.yml
    ├── README.md
    ├── tasks
    │   └── main.yml
    ├── templates
    ├── tests
    │   ├── inventory
    │   └── test.yml
    └── vars
        └── main.yml

9 directories, 8 files
```

4. Отредактировать инвентарь hosts

`vi /home/vagrant/ansible/hosts`

```
[nginx_lab]
#192.168.50.15
nginx

[nginx_lab:vars]
ansible_connection=ssh
ansible_ssh_user=vagrant
ansible_ssh_pass=vagrant
```

`ansible-inventory --graph --vars`

```
@all:
  |--@nginx_lab:
  |  |--nginx
  |  |  |--{ansible_connection = ssh}
  |  |  |--{ansible_ssh_pass = vagrant}
  |  |  |--{ansible_ssh_user = vagrant}
  |  |--{ansible_connection = ssh}
  |  |--{ansible_ssh_pass = vagrant}
  |  |--{ansible_ssh_user = vagrant}
  |--@ungrouped:
```

5. Отредактировать /etc/hosts и проверить доступность хоста nginx

`vi /etc/hosts`

```
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
127.0.1.1 ansible ansible
192.168.50.15 nginx nginx
```

`ansible -m ping all`

```
nginx | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": false,
    "ping": "pong"
}
```

6. Приступить к написанию роли nginx

7. Прверить nginx после выполенния роли

```
systemctl status nginx
● nginx.service - nginx - high performance web server
   Loaded: loaded (/usr/lib/systemd/system/nginx.service; disabled; vendor preset: disabled)
   Active: active (running) since Tue 2021-02-02 11:40:50 UTC; 1min 16s ago
     Docs: http://nginx.org/en/docs/
  Process: 1728 ExecStart=/usr/sbin/nginx -c /etc/nginx/nginx.conf (code=exited, status=0/SUCCESS)
 Main PID: 1729 (nginx)
   CGroup: /system.slice/nginx.service
           ├─1729 nginx: master process /usr/sbin/nginx -c /etc/nginx/nginx.conf
           └─1730 nginx: worker process

Feb 02 11:40:50 nginx systemd[1]: Starting nginx - high performance web server...
Feb 02 11:40:50 nginx systemd[1]: Can't open PID file /var/run/nginx.pid (yet?) after start: No such file or directory
Feb 02 11:40:50 nginx systemd[1]: Started nginx - high performance web server.
```

```
ss -tulpn |grep 8080
tcp    LISTEN     0      128       *:8080                  *:*                   users:(("nginx",pid=1730,fd=6),("nginx",pid=1729,fd=6))
```

```
curl localhost:8080
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```
