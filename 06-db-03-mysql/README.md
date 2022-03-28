# Домашнее задание к занятию "6.3. MySQL"

## Введение

Перед выполнением задания вы можете ознакомиться с 
[дополнительными материалами](https://github.com/netology-code/virt-homeworks/tree/master/additional/README.md).

## Задача 1

Используя docker поднимите инстанс MySQL (версию 8). Данные БД сохраните в volume.  
Файл docker-compose.yml:
```shell
version: '3.9'

services:
  mysql:
    image: mysql:8
    ports:
      - 3306:3306
    volumes:
      - ./data/mysql:/var/lib/mysql
      - ./backup:/var/lib/backup
    environment:
      - MYSQL_ROOT_PASSWORD=root
    network_mode: bridge
```

Изучите [бэкап БД](https://github.com/netology-code/virt-homeworks/tree/master/06-db-03-mysql/test_data) и 
восстановитесь из него:

```shell
Копируем test_dump.sql в volume контейнера: 

$ docker cp test_dump.sql bd36a47c1deb:/var/lib/backup

Создаем БД и делаем импорт test_dump.sql

mysql> CREATE DATABASE testdb;
mysql> use testdb
Database changed
mysql> source /var/lib/backup/test_dump.sql 
```

Перейдите в управляющую консоль `mysql` внутри контейнера.

Используя команду `\h` получите список управляющих команд.

Найдите команду для выдачи статуса БД и **приведите в ответе** из ее вывода версию сервера БД.

```shell
mysql> \s
--------------
mysql  Ver 8.0.28 for Linux on x86_64 (MySQL Community Server - GPL)


Server version:		8.0.28 MySQL Community Server - GPL
```

Подключитесь к восстановленной БД и получите список таблиц из этой БД.

**Приведите в ответе** количество записей с `price` > 300.

```shell
mysql> SELECT count(*)  FROM orders WHERE price > 300;
+----------+
| count(*) |
+----------+
|        1 |
+----------+
1 row in set (0.00 sec)

```

В следующих заданиях мы будем продолжать работу с данным контейнером.

## Задача 2

Создайте пользователя test в БД c паролем test-pass, используя:
- плагин авторизации mysql_native_password
```shell
mysql> CREATE USER 'test_user'@'localhost' IDENTIFIED WITH mysql_native_password BY 'test-password';
```
- срок истечения пароля - 180 дней 
```shell
mysql> ALTER USER 'test'@'localhost' PASSWORD EXPIRE INTERVAL 180 DAY;
Query OK, 0 rows affected (0.01 sec)
```
- количество попыток авторизации - 3 
```shell
mysql> ALTER USER 'test'@'localhost' FAILED_LOGIN_ATTEMPTS 3;
Query OK, 0 rows affected (0.01 sec)
```
- максимальное количество запросов в час - 100
```shell
mysql> ALTER USER 'test'@'localhost' WITH MAX_QUERIES_PER_HOUR 100;
Query OK, 0 rows affected (0.01 sec)

```
- аттрибуты пользователя:
    - Фамилия "Pretty"
    - Имя "James"
```shell
mysql> ALTER USER 'test'@'localhost' ATTRIBUTE '{"lastName": "Pretty", "firstName": "James"}';
Query OK, 0 rows affected (0.00 sec)

```

Предоставьте привелегии пользователю `test` на операции SELECT базы `test_db`.

```shell
mysql> GRANT SELECT ON testdb.orders TO 'test'@'localhost';
Query OK, 0 rows affected, 1 warning (0.00 sec)

```
    
Используя таблицу INFORMATION_SCHEMA.USER_ATTRIBUTES получите данные по пользователю `test` и 
**приведите в ответе к задаче**.

```shell
mysql> SELECT * FROM INFORMATION_SCHEMA.USER_ATTRIBUTES WHERE USER='test';
+------+-----------+----------------------------------------------+
| USER | HOST      | ATTRIBUTE                                    |
+------+-----------+----------------------------------------------+
| test | localhost | {"lastName": "Pretty", "firstName": "James"} |
+------+-----------+----------------------------------------------+

```

## Задача 3

Установите профилирование `SET profiling = 1`.
Изучите вывод профилирования команд `SHOW PROFILES;`.

Исследуйте, какой `engine` используется в таблице БД `test_db` и **приведите в ответе**.

```shell
mysql> SELECT ENGINE FROM information_schema.TABLES WHERE TABLE_SCHEMA = 'testdb' AND TABLE_NAME = 'orders';
+--------+
| ENGINE |
+--------+
| InnoDB |
+--------+

```

Измените `engine` и **приведите время выполнения и запрос на изменения из профайлера в ответе**:
- на `MyISAM`
```shell
mysql> ALTER TABLE orders ENGINE = MyISAM;
Query OK, 5 rows affected (0.12 sec)

```
- на `InnoDB`
```shell
mysql> ALTER TABLE orders ENGINE = InnoDB;
Query OK, 5 rows affected (0.05 sec)

```

```shell
mysql> SHOW PROFILES;
+----------+------------+------------------------------------------------------------------------------------------------------+
| Query_ID | Duration   | Query                                                                                                |
+----------+------------+------------------------------------------------------------------------------------------------------+
|        1 | 0.00034175 | SELECT @@profiling                                                                                   |
|        2 | 0.00520675 | SELECT ENGINE FROM information_schema.TABLES WHERE TABLE_SCHEMA = 'testdb' AND TABLE_NAME = 'orders' |
|        3 | 0.00335150 | SELECT ENGINE FROM information_schema.TABLES WHERE TABLE_SCHEMA = 'testdb'                           |
|        4 | 0.00017625 | ALTER TABLE orders ENGINE = MyISAM                                                                   |
|        5 | 0.00038425 | SELECT DATABASE()                                                                                    |
|        6 | 0.00141950 | show databases                                                                                       |
|        7 | 0.00142750 | show tables                                                                                          |
|        8 | 0.00036975 | SELECT @@profiling                                                                                   |
|        9 | 0.11650900 | ALTER TABLE orders ENGINE = MyISAM                                                                   |                                                                 |
|       10 | 0.05434725 | ALTER TABLE orders ENGINE = InnoDB                                                                   |
+----------+------------+------------------------------------------------------------------------------------------------------+

```

## Задача 4 

Изучите файл `my.cnf` в директории /etc/mysql.


Измените его согласно ТЗ (движок InnoDB):
- Скорость IO важнее сохранности данных
- Нужна компрессия таблиц для экономии места на диске
- Размер буффера с незакомиченными транзакциями 1 Мб
- Буффер кеширования 30% от ОЗУ
- Размер файла логов операций 100 Мб

Приведите в ответе измененный файл `my.cnf`.


```shell
Содержание файла `my.cnf`: 

[mysqld]
pid-file        = /var/run/mysqld/mysqld.pid
socket          = /var/run/mysqld/mysqld.sock
datadir         = /var/lib/mysql
secure-file-priv= NULL

innodb_flush_log_at_trx_commit = 0 
innodb_file_format=Barracuda
innodb_log_buffer_size=1M
key_buffer_size =2458M
max_binlog_size = 100M


# Custom config should go here
!includedir /etc/mysql/conf.d/

```

---

### Как оформить ДЗ?

Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.

---
