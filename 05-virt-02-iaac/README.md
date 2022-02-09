## Домашнее задание к занятию "5.2. Применение принципов IaaC в работе с виртуальными машинами"


### Задача 1

1. IaaC ускоряет процесс развертывания инфраструктуры разработки. 
2. Iaac более стабильна, отсутствует дрейф конфигурации. 
3. Позволяет ускорить процесс разработки, развертываний тестовых зон, "песочниц", внедрение принципов CI/CD.

* Основополагающим принципом IaaC является идемпотентность - при применении одного и того же скрипта мы получим одну и ту жу конфигурацию. 

### Задача 2

* Ansible отличается простотой использования, не требует установки PKI окружения и YAML-подобный DSL, написана на Python и легко расширяется за счет модулей.

* Думаю, push метод более надежный, так как в pull мы не можем спрогнозировать применение конфигураций из-за негарантированной доставки настроек.  


### Задача 3

* VurtualBox: 
```shell
dmitry@dmitry-N56VZ:~$ vboxmanage --version
6.1.32r149290
```

* Vagrant 
```shell
dmitry@dmitry-N56VZ:~$ vagrant version
Installed Version: 2.2.19
Latest Version: 2.2.19
```

* Ansible

```shell
dmitry@dmitry-N56VZ:~$ ansible --version
ansible 2.9.6
  config file = /etc/ansible/ansible.cfg
  configured module search path = ['/home/dmitry/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/lib/python3/dist-packages/ansible
  executable location = /usr/bin/ansible
  python version = 3.8.10 (default, Nov 26 2021, 20:14:08) [GCC 9.3.0]

```

### Задача 4

```shell

vagrant@server1:~$ docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES


```
