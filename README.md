# Docker

---

### Задание 1 - Введение в Docker

1. `Запусти несколько контейнеров с разными приложениями: Например, один контейнер с Nginx, другой — с MySQL, третий — с Python.`
2. `Используй команду docker ps, чтобы проверить, что контейнеры запущены.`
3. `Попробуй остановить один контейнер с помощью docker stop и удалить его с помощью docker rm.`

``` ~Решение~
1.
docker run -d --name my-nginx -p 8085:80 nginx
docker run -d --name my-mysql -e MYSQL_ROOT_PASSWORD=root -p 3306:3306 mysql
docker run -d --name my-python -p 9090:90 python python -m http.server 90
2.
sergey@sergey-deb:~/devops/docker$ docker ps 
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS          PORTS                                                  NAMES
25b7d7afff9d   python    "python -m http.serv…"   11 seconds ago   Up 7 seconds    0.0.0.0:9090->90/tcp, [::]:9090->90/tcp                my-python
7bbe0877267f   mysql     "docker-entrypoint.s…"   22 seconds ago   Up 20 seconds   0.0.0.0:3306->3306/tcp, :::3306->3306/tcp, 33060/tcp   my-mysql
2984f4c815e2   nginx     "/docker-entrypoint.…"   17 minutes ago   Up 17 minutes   0.0.0.0:8085->80/tcp, [::]:8085->80/tcp                my-nginx
3.
docker stop my-mysql
docker stop my-python
docker stop my-python
....
```

`Cкриншоты
![Cкриншот 1](ссылка на скриншот 1)`

---

### Задание 2 - Работа с Docker-образами

1. `Создай образ для другого веб-приложения (например, на Node.js).`
2. `Запусти его локально через Docker.`

``` ~Решение~
1.
sudo apt update
sudo apt install -y nodejs npm
   node -v
   npm -v
mkdir my-node && cd my-node
npm init -y
npm install express

nano server.js
   const express = require('express');
   const app = express();

   app.get('/', (req, res) => {
      res.send('Hello, Docker with Node.js!');
   });

   const PORT = 3000;
   app.listen(PORT, () => {
      console.log(`Server is running on http://localhost:${PORT}`);
   });

nano Dockerfile
   FROM node:18
   WORKDIR /app
   COPY package*.json ./
   RUN npm install
   COPY . .
   EXPOSE 3000
   CMD ["node", "server.js"]
2.
docker build -t my-node-app .
docker run -d -p 3000:3000 my-node-app
....
```

`Cкриншоты
![docker-node.png](ссылка на скриншот 2)`

---

### Задание 3 - Сетевые возможности Docker

1. `Написать docker-compose.yml для приложения, состоящего из: Веб-сервера (Nginx или Flask),Базы данных (MySQL или PostgreSQL). Собственной сети.`


``` ~Решение~
1.
mkdir docker-network && cd docker-network
nano docker-compose.yml
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

docker-compose up -d
docker-compose ps 
docker-compose down
....
```

`Cкриншоты
![docker-network.pmg](ссылка на скриншот)`

---

### Задание 4 - Docker в реальных проектах

1. `Разработать Dockerfile и docker-compose.yml для многосервисного приложения (backend + frontend + БД).`
2. `Автоматизировать процесс CI/CD для контейнеров.`

``` ~Решение~
1.
mkdir backend && cd backend
nano Dockerfile
   FROM python:3.9
   WORKDIR /app
   COPY requirements.txt .
   RUN pip install --no-cache-dir -r requirements.txt
   COPY . .
   EXPOSE 5000
   CMD ["python", "app.py"]

mkdir fromtemd && cd frontend
nano Dockerfile
   FROM node:18
   WORKDIR /app
   COPY package.json package-lock.json ./
   RUN npm install
   COPY . .
   EXPOSE 3000
   CMD ["npm", "start"]

nano docker-compose.yml

version: '3.8'

services:
  backend:
    build: ./backend
    container_name: backend
    ports:
      - "5000:5000"
    depends_on:
      - postgres
      - redis
    networks:
      - app-network

  frontend:
    build: ./frontend
    container_name: frontend
    ports:
      - "3000:3000"
    depends_on:
      - backend
    networks:
      - app-network

  prometheus:
    image: prom/prometheus
    container_name: prometheus
    ports:
      - "9090:9090"
    networks:
      - app-network

  grafana:
    image: grafana/grafana
    container_name: grafana
    ports:
      - "3001:3000"
    networks:
      - app-network

  postgres:
    image: postgres:15
    container_name: my_postgres
    restart: always
    environment:
      POSTGRES_DB: mydatabase
      POSTGRES_USER: myuser
      POSTGRES_PASSWORD: mypassword
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data
      - ./init.sql:/docker-entrypoint-initdb.d/init.sql
    networks:
      - app-network

  redis:
    image: redis:latest  
    container_name: my_redis
    restart: unless-stopped  
    ports:
      - "6379:6379"  
    volumes:
      - redis_data:/data  
    networks:
      - app-network

volumes:
  postgres_data:
  redis_data:

networks:
  app-network:

2.
nano docker-ci.yml

name: CI Pipeline

on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest
    
    steps:
      - name: Логин в Docker Hub
        run: echo "${{ secrets.DOCKER_PASSWORD }}" | docker login -u "${{ secrets.DOCKER_USERNAME }}" --password-stdin

      - name: Проверка версии Docker и Docker Compose
        run: |
          docker --version
          docker-compose --version

      - name: Сборка образов
        run: docker-compose build

      - name: Запуск контейнеров
        run: docker-compose up -d

      - name: Проверка работы контейнера
        run: |
          docker ps
          curl http://localhost:5000  # Для проверки работы backend

      - name: Push images to Docker Hub
        run: |
          docker-compose push

      - name: Остановка контейнеров
        run: docker-compose down

```

`Скриншоты
![Скриншот ](ссылка на скриншот)`

---
