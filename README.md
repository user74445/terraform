### Домашнее задание к занятию "5.3. Введение. Экосистема. Архитектура. Жизненный цикл Docker контейнера"

#### Задача 1

Сценарий выполнения задачи:

- создайте свой репозиторий на https://hub.docker.com;
- выберете любой образ, который содержит веб-сервер Nginx;
- создайте свой fork образа;
- реализуйте функциональность:
запуск веб-сервера в фоне с индекс-страницей, содержащей HTML-код ниже:
```
<html>
<head>
Hey, Netology
</head>
<body>
<h1>I’m DevOps Engineer!</h1>
</body>
</html>
```
Опубликуйте созданный форк в своем репозитории и предоставьте ответ в виде ссылки на https://hub.docker.com/username_repo.

```
vagrant@server1:~$ docker ps
CONTAINER ID   IMAGE                   COMMAND                  CREATED          STATUS          PORTS     NAMES
fabaab931a2f   uttuer2/mynginx         "/docker-entrypoint.…"   13 seconds ago   Up 12 seconds   80/tcp    musing_cannon


vagrant@server1:~$ curl 172.17.0.2
<!DOCTYPE html>
<html>
	<head>
		Hey, Netology
	</head>
	<body>
		<h1>I’m DevOps Engineer!</h1>
	</body>
</html>
```

https://hub.docker.com/repository/docker/uttuer2/mynginx


#### Задача 2

Посмотрите на сценарий ниже и ответьте на вопрос:
"Подходит ли в этом сценарии использование Docker контейнеров или лучше подойдет виртуальная машина, физическая машина? Может быть возможны разные варианты?"

Детально опишите и обоснуйте свой выбор.

--

Сценарий:

- Высоконагруженное монолитное java веб-приложение;
```
Физический сервер или виртуальная машина предпочтительнее, т.к. монолитное и на микросервисы сложно разбить. Так же для высоконагруженного
необходим прямой доступ к ресурсам.
```
- Nodejs веб-приложение;
```
Подойдет Docker, так как это веб-платформа с подключаемыми внешними библиотеками
```
- Мобильное приложение c версиями для Android и iOS;
```
Необходим GUI, подойдет виртуальная машина.
```
- Шина данных на базе Apache Kafka;
```
Если среда рабочая и полнота данных критична, то лучше использовать виртуальную машину; если среда тестовая и потеря данных некритична,
можно использовать Docker.
```
- Elasticsearch кластер для реализации логирования продуктивного веб-приложения - три ноды elasticsearch, два logstash и
две ноды kibana;
```
Elasticsearch лучше на виртуальной машине, отказоустойчивость решается на уровне кластера, kibana и logstash можно вынести в Docker.
```
- Мониторинг-стек на базе Prometheus и Grafana;
```
Подойдет Docker, так как данные не хранятся, и легко масштабировать.
```
- MongoDB, как основное хранилище данных для java-приложения;
```
Зависит от нагрузки на DB. Если нагрузка большая, то физический сервер, если нет то виртуальная машина.
```
- Gitlab сервер для реализации CI/CD процессов и приватный (закрытый) Docker Registry.
```
Подойдет виртуальная машина для DB и файлового хранилища, Docker для сервисов
```

#### Задача 3

- Запустите первый контейнер из образа ***centos*** c любым тэгом в фоновом режиме, подключив папку ```/data``` из текущей рабочей директории на хостовой машине в ```/data``` контейнера;
- Запустите второй контейнер из образа ***debian*** в фоновом режиме, подключив папку ```/data``` из текущей рабочей директории на хостовой машине в ```/data``` контейнера;
- Подключитесь к первому контейнеру с помощью ```docker exec``` и создайте текстовый файл любого содержания в ```/data```;
- Добавьте еще один файл в папку ```/data``` на хостовой машине;
- Подключитесь во второй контейнер и отобразите листинг и содержание файлов в ```/data``` контейнера.

```
vagrant@server1:~$ docker run -it --rm -d --name centos -v $(pwd)/data:/data centos:latest
Unable to find image 'centos:latest' locally
latest: Pulling from library/centos
a1d0c7532777: Pull complete
Digest: sha256:a27fd8080b517143cbbbab9dfb7c8571c40d67d534bbdee55bd6c473f432b177
Status: Downloaded newer image for centos:latest
5e19b0a2b814181063e4512234ad77a818896290647ba61f309f31c235a07d46

vagrant@server1:~$ docker run -it --rm -d --name debian -v $(pwd)/data:/data debian:latest
Unable to find image 'debian:latest' locally
latest: Pulling from library/debian
6aefca2dc61d: Pull complete
Digest: sha256:2906804d2a64e8a13a434a1a127fe3f6a28bf7cf3696be4223b06276f32f1f2d
Status: Downloaded newer image for debian:latest
0bc161cd9484d0b69a697ef648d4c1e19a5228d9fa04788e4c88c96176238229
```
```
vagrant@server1:~$ docker exec -it centos bash
[root@5e19b0a2b814 /]# echo "This file is written to docker CentOS" >> /data/centos.txt
[root@5e19b0a2b814 /]# exit
exit
```
```
vagrant@server1:~$ sudo su
root@server1:/home/vagrant# echo "This file is written to host" >> data/host.txt
```
```
vagrant@server1:~$ docker exec -it debian bash
root@0bc161cd9484:/# ls data/
centos.txt  host.txt
```
