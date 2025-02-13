# Docker

---

### Задание 1 - Итогоговый проект

1. `Веб-сервер (backend): Это будет Python (Flask или FastAPI) или Node.js приложение, которое будет обрабатывать запросы от клиента.`
2. `База данных (PostgreSQL): Для хранения данных.`
3. `Очередь сообщений (Redis или RabbitMQ): Для асинхронной обработки задач.`
4. `Мониторинг (Prometheus и Grafana): Для мониторинга состояния контейнеров и сервисов.`
5. `Frontend (React или Vue.js): Для интерфейса пользователя, который взаимодействует с backend.`

``` ~Решение~
1.
mkdir backend && cd backend
nano Dockerfile
  FROM python:3.9
  WORKDIR /app
  COPY requirements.txt .
  RUN pip install -r requirements.txt
  COPY . .
  CMD ["python", "app.py"]

mkdir frontend && cd frontend
nano Dockerfile
  FROM node:18
  WORKDIR /app
  COPY package.json .
  RUN npm install
  COPY . .
  CMD ["npm", "start"]

2.
mkdir postgresql && cd postgresql
nano Dockerfile
  FROM postgres:15

  ENV POSTGRES_DB=mydatabase
  ENV POSTGRES_USER=myuser
  ENV POSTGRES_PASSWORD=mypassword

3.
mkdir redis && cd redis
nano Dockerfile
  FROM redis:latest

4.
mkdir prometheus && cd prometheus
nano prometheus.yml
  global:
    scrape_interval: 15s

  scrape_configs:
    - job_name: 'docker'
      static_configs:
        - targets: ['docker.for.mac.localhost:9090']
      metrics_path: /metrics

5.
nano docker-compose.yml

version: "3.9"

services:
  backend:
    build: ./backend
    ports:
      - "5000:5000"
    networks:
      - app-network
    depends_on:
      - postgres
      - redis

  frontend:
    build: ./frontend
    ports:
      - "3000:3000"
    networks:
      - app-network
    depends_on:
      - backend

  postgres:
    image: postgres:15
    container_name: my_postgres
    environment:
      POSTGRES_DB: mydatabase
      POSTGRES_USER: myuser
      POSTGRES_PASSWORD: mypassword
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data
    networks:
      - app-network

  redis:
    image: redis:latest
    container_name: my_redis
    ports:
      - "6379:6379"
    volumes:
      - redis_data:/data
    networks:
      - app-network

  prometheus:
    image: prom/prometheus
    container_name: prometheus
    ports:
      - "9090:9090"
    networks:
      - app-network
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml

  grafana:
    image: grafana/grafana
    container_name: grafana
    ports:
      - "3000:3000"
    networks:
      - app-network
    environment:
      GF_SECURITY_ADMIN_PASSWORD: admin

volumes:
  postgres_data:
  redis_data:

networks:
  app-network:

docker compose up -d --build
docker compose logs -f backend
docker ps
docker compose down
....
```

`Cкриншоты
![Cкриншот 1](ссылка на скриншот 1)`

---