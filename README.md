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
roles_path = /home/vagrant/ansible/
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

6. 
