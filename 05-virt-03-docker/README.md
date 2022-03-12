## Домашнее задание к занятию "5.3. Введение. Экосистема. Архитектура. Жизненный цикл Docker контейнера"  

### Задача 1

Ссылка на репозиторий: https://hub.docker.com/r/dmitrykuchukov/nginx-netology/tags.  

### Задача 2

Сценарий:

* Высоконагруженное монолитное java веб-приложение - физический сервер, так как нужны большие аппартные ресурсы, потому что высоконагреженное. Монолитное - не подходят принципы докера.


*  Nodejs веб-приложение - подходит docker контейнеры, так как для работы NodeJS приложения необходима среда - NodeJS сервер, определенной версии. Также приложение можно собрать из микросервисов.       


* Мобильное приложение c версиями для Android и iOS - одновременно использовать контейнеры для мобильных приложения навряд ли получится, потому что docker использует ядро Linux обычно на x86/AMD64 чипсетах и iOS работать не будет, так как iOS не на всем железе может запускаться. Отдельно для android можно использовать контейнеры, для iOS либо физическая машина, либо виртуальная, если позволит чипсет. 


* Шина данных на базе Apache Kafka - не вижу препятствий для использования контейнеров, с помощью контейнеров можно создавать кластеризацию.


* Elasticsearch кластер для реализации логирования продуктивного веб-приложения - три ноды elasticsearch, два logstash и две ноды kibana - используем докер для удобства сбора стека и взаимодействия elasticsearch, logstash и kibana.

* Мониторинг-стек на базе Prometheus и Grafana -используем докер, так как позволит масштабировать один образ на многих серверах (виртуальных, либо физических) 


* MongoDB, как основное хранилище данных для java-приложения - можно использовать как и докер, так и виртуальную машину. С помощью докера ускорит процесс масштабирования.  


* Gitlab сервер для реализации CI/CD процессов и приватный (закрытый) Docker Registry - используем докер, чтобы быть уверенным, что на любой машине получим рабочую, идемпотентную среду для CI/CD процессов. 




### Задача 3

1) Запускаем контейнер из образа centos

```shell
dmitry@dmitry-N56VZ:~/Netology/data$ docker run -d -v ${PWD}:/data -it centos bash
```

2) Запускаем контейнер из образа debian
```shell
dmitry@dmitry-N56VZ:~/Netology/data$ docker run -d -v ${PWD}:/data -it debian bash
```

3) Подключаемся к первому контейнеру и создаем файл text1.txt

```shell
dmitry@dmitry-N56VZ:~/Netology/data$ docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED          STATUS          PORTS     NAMES
692e8a5990d8   debian    "bash"    3 seconds ago    Up 1 second               elegant_wu
1183234cc585   centos    "bash"    18 seconds ago   Up 17 seconds             silly_jang

dmitry@dmitry-N56VZ:~/Netology/data$ docker exec -it 1183234cc585 bash
[root@1183234cc585 data]# echo Hello > text1.txt 

```

4) Добавляем файл text2.txt на хостовой машине

```shell
dmitry@dmitry-N56VZ:~/Netology/data$ echo Привет > text2.txt
```

5) Вывод файлов во втором контейнере

```shell

dmitry@dmitry-N56VZ:~/Netology/data$  docker exec -it 692e8a5990d8 bash

root@692e8a5990d8:/# cd data
root@692e8a5990d8:/data# ls -l
total 8
-rw-r--r-- 1 root root  6 Feb 12 18:02 text1.txt
-rw-rw-r-- 1 1000 1000 13 Feb 12 18:06 text2.txt
root@692e8a5990d8:/data# cat text1.txt 
Hello
root@692e8a5990d8:/data# cat text2.txt 
Привет
```