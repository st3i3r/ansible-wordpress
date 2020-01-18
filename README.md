# Ansible-Wordpress

Автоматизированная установка WordPress на CentOS 7.x с помощью Ansible

## O Ansible-Wordpress

Ansible Playbook для установки Wordpress. Данный ansible-плейбук выполняет:
    
* Установку EPEL репозитория и Wordpress зависимостей (mysql, php-7.0, mariadb, httpd)
* Создание пользователя MySQL и базы данных
* Установку Wordpress
        
### Wordpress-cервер

* Операционная система: CentOS Linux 7 (Core)
* Ядро: Linux 3.10.0-1062.9.1.el7.x86_64
* Архитектура: x86-64

### Установка зависимостей 

* [geerlingguy.pip](https://galaxy.ansible.com/geerlingguy/pip) - Роль для установки pip из galaxy ansible
* [robertdepock.httpd](https://galaxy.ansible.com/robertdebock/httpd) - Роль для установки httpd из galaxy ansible

```
$ ansible-galaxy install --roles-path . -r requirements.yml
```

### Структура Ansible-Playbook

```
├── group_vars
│   └── vars.yml
├── hosts
├── README.md
├── requirements.yml
├── wordpress.yml
├── wp-dependencies
│   ├── defaults
│   │   └── main.yml
│   ├── files
│   ├── handlers
│   │   └── main.yml
│   ├── meta
│   │   └── main.yml
│   ├── tasks
│   │   ├── database.yml
│   │   ├── main.yml
│   │   └── php-7.yml
│   ├── templates
│   └── vars
│       └── main.yml
└── wp-install
    ├── defaults
    │   └── main.yml
    ├── files
    │   └── wordpress.tar.gz
    ├── handlers
    │   └── main.yml
    ├── meta
    │   └── main.yml
    ├── tasks
    │   └── main.yml
    ├── templates
    │   └── wp-config.php
    └── vars
        └── main.yml
```

### Настройка хостов

```
centos-7 ansible_host=192.168.0.50 

[wordpress-server:children]
moscow-server

[moscow-server]
centos-7

[wordpress-server:vars]
ansible_user=root
ansible_port=22
```

### Подготовка ключей SSH к удаленным хостам

Генерация ключа для доступа по SSH

```
$ ssh-keygen -t rsa -b 4096
```

Копирование публичного ключа на Wordpress-сервер

```
$ ssh-copy-id [user]@[ip-address-wp-server]
```

### Развертывание

Можно запускать ansible-playbook двумя спосбами:

#### 1. Расшифровать зашифрованные файлы и запустить ansible-playbook

```
ansible-vault decrypt [зашифрованный файл]
Vault password:

$ ansible-playbook -i hosts wordpress.yml
```

#### 2. Запустить ansible-playbook с опцией --ask-vault-pass / --vault-password-file (файл расшифруется только для выполнения)

```
$ ansible-playbook -i hosts --vault-password-file=.vault_pass wordpress.yml
```
```
$ ansible-playbook -i hosts --ask-vault-pass wordpress.yml
Vault password:
```
Запускать ansible-playbook с тегами

```
$ ansible-playbook -i hosts --ask-vault-pass wordpree.yml --tags system,database
```
```
$ ansible-playbook -i hosts --ask-vault-pass wordpree.yml --skip-tags system,database
```

После успешного выполнения всех ansible-тасков можно переходить по адресу Wordpress-сервера для настройки Wordpress.
