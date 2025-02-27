# Docker_v1

---

### Задание 1 - Основы контейнеризации

1. `Запусти команду docker run hello-world и посмотри, что будет выведено.`
2. `Скачай и запусти образ nginx с помощью команды docker run nginx. Убедись, что веб-сервер работает.`

``` ~Решение~
1.
docker run hello-world
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
e6590344b1a5: Pull complete 
Digest: sha256:e0b569a5163a5e6be84e210a2587e7d447e08f87a0e90798363fa44a0464a1e8
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
2.
docker run -d --name one_nginx nginx
....
```

`Cкриншоты
![Cкриншот 1](ссылка на скриншот 1)`

---

### Задание 2 - Образы и контейнеры

1. `Запусти контейнер с образом ubuntu и зайди внутрь контейнера с помощью команды docker exec -it <container_id> bash.`
2. `Измени содержимое контейнера (например, установи curl), зафиксируй изменения в новом образе.`
3. `Останови и удали контейнер. С помощью команды docker images проверь, что образ сохранен.`

``` ~Решение~
1.
docker run -it ubuntu bash
2.
root@702b2be2e183:/# apt update
root@702b2be2e183:/# apt install curl

docker ps 
702b2be2e183   ubuntu   "bash"    9 minutes ago    Exited (0) 2 minutes ago        peaceful_williamson

docker commit peaceful_williamson my_ubuntu:curl_installed
3.
docker stop peaceful_williamson
peaceful_williamson

docker rm peaceful_williamson
peaceful_williamson

docker images |grep ubuntu 
ubuntu                         latest    a04dc4851cbc   4 weeks ago          78.1MB
....
```

`Cкриншоты
![Скриншот ](ссылка на скриншот)`

---

### Задание 3 - Работа с Docker CLI

1. `Создай контейнер из образа nginx, измени настройки порта с помощью параметра -p, запусти его.`
2. `Попробуй остановить контейнер и запустить его снова с использованием команды docker start.`
3. `Просмотри информацию о запущенных контейнерах и образах с помощью команд docker ps и docker images.`


``` ~Решение~
1.
docker run -d --name two_nginx -p 8085:80 nginx
2.
docker stop two_nginx
docker start two_nginx
3.
docker ps | grep nginx
docker images | grep nginx
....
```

`Cкриншоты
![Скриншот ](ссылка на скриншот)`

---

### Задание 4 - Создание Dockerfile

1. `Напиши Dockerfile для простого Python приложения, которое запускает сервер на порту 5000`
2. `Построь образ с этим Dockerfile с помощью команды docker build -t my-python-app ..`
3. `Запусти контейнер из построенного образа.`

``` ~Решение~
1.
FROM python:3.11
RUN pip install flask
COPY app.py /app/app.py
WORKDIR /app
EXPOSE 5000
CMD ["python", "app.py"]
2.
docker build -t my-one-python .
3.
docker run -d -p 5000:5000 --name my-one-python-container my-one-python

```

`Скриншоты
![Скриншот ](ссылка на скриншот)`

---

### Задание 5 - Мульти-стадийные сборки

1. `Напиши Dockerfile с двумя стадиями: одна для компиляции, другая для финального образа.`
2. `Построь образ с этим Dockerfile и запусти его. Проверь, что финальный образ минимален и содержит только необходимые файлы.`

``` ~Решение~
1.
FROM python:3.11-alpine AS builder
WORKDIR /app
COPY . .  
RUN pip install --no-cache-dir flask requests
FROM python:3.11-alpine
WORKDIR /app
COPY --from=builder /usr/local/lib/python3.11/site-packages /usr/local/lib/python3.11/site-packages
COPY app.py .
EXPOSE 5000
CMD ["python", "app.py"]
2.
docker build -t my-one-python .

```

`Скриншоты
![Скриншот ](ссылка на скриншот)`

---

### Задание 6 - Введение в Docker Compose

1. `Напиши файл docker-compose.yml для запуска двух контейнеров: веб-сервера и базы данных MySQL.`
2. `Запусти приложение с помощью Docker Compose, убедись, что все контейнеры связаны.`
3. `Убедись, что приложение работает, подключившись к базе данных через веб-сервер.`

``` ~Решение~
1.
services:
  web:
    image: nginx
    ports:
      - "8086:80"
    networks:
      - mynetwork

  db:
    image: mysql:5.7
    environment:
      MYSQL_ROOT_PASSWORD: root
    networks:
      - mynetwork

networks:
  mynetwork:
2.
docker compose up -d
docker exec -it docker_mysql-db-1 bash
   mysql -u root -p
   Welcome to the MySQL monitor.  Commands end with ; or \g.
   Your MySQL connection id is 2
   Server version: 5.7.44 MySQL Community Server (GPL)

   Copyright (c) 2000, 2023, Oracle and/or its affiliates.

   Oracle is a registered trademark of Oracle Corporation and/or its
   affiliates. Other names may be trademarks of their respective
   owners.

   Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

```

`Скриншоты
![Скриншот ](ссылка на скриншот)`

---

### Задание 7 - Продвинутое использование Docker Compose

1. `Добавь в docker-compose.yml настройки для использования volumes для базы данных, чтобы данные сохранялись между перезапусками контейнеров.`
2. `Добавь переменные окружения для настройки соединения с базой данных.`

``` ~Решение~
1.
services:
  web:
    image: nginx
    ports:
      - "8086:80"
    networks:
      - mynetwork

  db:
    image: mysql:5.7
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: mydb
      MYSQL_USER: user
      MYSQL_PASSWORD: password
    volumes:
      - mysql_data:/var/lib/mysql
    networks:
      - mynetwork

volumes:
  mysql_data:

networks:
  mynetwork:

2.
docker exec -it docker_mysql_db_1 mysql -uuser -ppassword

```

`Скриншоты
![Скриншот ](ссылка на скриншот)`

---

### Задание 8 - Сетевое взаимодействие в Docker

1. `Создай пользовательскую сеть в Docker с помощью команды docker network create.`
2. `Запусти два контейнера в этой сети и убедись, что они могут общаться друг с другом.`
3. `Подключи контейнер к другой сети и проверь, что общение между ними невозможно.`

``` ~Решение~
1.
docker network create test
2.
docker run -d --name tree_nginx --network test nginx
docker run -d --name my_mysql \
  -e MYSQL_ROOT_PASSWORD=root \
  -e MYSQL_DATABASE=mydb \
  -e MYSQL_USER=user \
  -e MYSQL_PASSWORD=password \
  --network test mysql:5.7
docker exec -it tree_nginx ping -c 4 my_mysql
3.
docker network create test_1
docker network connect test_1 tree_nginx
```

`Скриншоты
![Скриншот ](ссылка на скриншот)`

---

### Задание 9 - Docker и взаимодействие с внешними сервисами

1. `Создай пользовательскую сеть в Docker с помощью команды docker network create.`
2. `Запусти два контейнера в этой сети и убедись, что они могут общаться друг с другом.`
3. `Подключи контейнер к другой сети и проверь, что общение между ними невозможно.`

``` ~Решение~
1.
docker network create test
2.
docker run -d --name tree_nginx --network test nginx
docker run -d --name my_mysql \
  -e MYSQL_ROOT_PASSWORD=root \
  -e MYSQL_DATABASE=mydb \
  -e MYSQL_USER=user \
  -e MYSQL_PASSWORD=password \
  --network test mysql:5.7
docker exec -it tree_nginx ping -c 4 my_mysql
3.
docker network create test_1
docker network connect test_1 tree_nginx
```

`Скриншоты
![Скриншот ](ссылка на скриншот)`

---