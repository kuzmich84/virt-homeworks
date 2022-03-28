# Домашнее задание к занятию "6.4. PostgreSQL"

## Задача 1

Используя docker поднимите инстанс PostgreSQL (версию 13). Данные БД сохраните в volume.

Подключитесь к БД PostgreSQL используя `psql`.

Воспользуйтесь командой `\?` для вывода подсказки по имеющимся в `psql` управляющим командам.

**Найдите и приведите** управляющие команды для:

```shell
1. Вывод списка БД

postgres=# \l или
postgres=# \list

2. Подключения к БД

postgres-# \c

3. Вывод списка таблиц

postgres-# \dt

4. Вывод описания содержимого таблиц

postgres-# \dt+

5. Выход из psql

postgres-# \q


```

## Задача 2

Используя `psql` создайте БД `test_database`.

```shell
postgres=# CREATE DATABASE test_database;
CREATE DATABASE
```

Изучите [бэкап БД](https://github.com/netology-code/virt-homeworks/tree/master/06-db-04-postgresql/test_data).

Восстановите бэкап БД в `test_database`.

```shell
$ docker cp test_dump.sql 3c7018fdca21:/var/lib/postgresql/data_backup

bash-5.1# psql -U postgres -d test_database < test_dump.sql
```

Перейдите в управляющую консоль `psql` внутри контейнера.

Подключитесь к восстановленной БД и проведите операцию ANALYZE для сбора статистики по таблице.

Используя таблицу [pg_stats](https://postgrespro.ru/docs/postgresql/12/view-pg-stats), найдите столбец таблицы `orders` 
с наибольшим средним значением размера элементов в байтах.

**Приведите в ответе** команду, которую вы использовали для вычисления и полученный результат.

```shell
test_database=# SELECT attname, avg_width FROM pg_stats WHERE tablename='orders' ORDER BY avg_width DESC;
 attname | avg_width 
---------+-----------
 title   |        16
 id      |         4
 price   |         4
(3 rows)

столбец title
```

## Задача 3

Архитектор и администратор БД выяснили, что ваша таблица orders разрослась до невиданных размеров и
поиск по ней занимает долгое время. Вам, как успешному выпускнику курсов DevOps в нетологии предложили
провести разбиение таблицы на 2 (шардировать на orders_1 - price>499 и orders_2 - price<=499).

Предложите SQL-транзакцию для проведения данной операции.
```shell
CREATE TABLE orders_1 ( CHECK ( price > 499 )) INHERITS (orders);
CREATE TABLE orders_2 ( CHECK ( price <= 499 )) INHERITS (orders);
```

Можно ли было изначально исключить "ручное" разбиение при проектировании таблицы orders?

`Да, можно было.`

## Задача 4

Используя утилиту `pg_dump` создайте бекап БД `test_database`.
```shell
bash-5.1# pg_dump -U postgres test_database -f /var/lib/postgresql/data_backup/test_database.sql
```

Как бы вы доработали бэкап-файл, чтобы добавить уникальность значения столбца `title` для таблиц `test_database`?

```shell
Изменил файл в месте создании таблицы orders.

CREATE TABLE public.orders (
    id integer NOT NULL,
    title character varying(80) NOT NULL,
    price integer DEFAULT 0,
    CONSTRAINT title_unique UNIQUE (title)
);

```
---

### Как cдавать задание

Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.

---
