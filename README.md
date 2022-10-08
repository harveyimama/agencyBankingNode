Boat Backend App For AGRO MALL

<h1>Getting Started</h1>

## API Documentation

<p>View API documentation 
<a href="https://documenter.getpostman.com/view/8699665/UVsHU8FL" target="_blank">Here</a>
</p>

## Pre-installation

Make sure your Docker Engine is running, start your Docker Engine if it isn't running

<h2>Installation & Setup</h2>

## Agriculture Service

```bash
$ cd agriculture_service && yarn install

# Build image
$ docker build .
```

## Community Service

```bash
$ cd community_service && yarn install

# Build image
$ docker build .
```

## Information Service

```bash
$ cd information_service && yarn install

# Build image
$ docker build .
```

## Main Service

```bash
$ cd main_service && yarn install

# Build image
$ docker build .
```

## Transaction Service

```bash
$ cd transaction_service && yarn install

# Build image
$ docker build .
```

## User Service

```bash
$ cd user_service && yarn install

# Build image
$ docker build .
```

## Wallet Service

```bash
$ cd wallet_service && yarn install
# Build image
$ docker build .
```

## Websocket Service

```bash
$ cd web_socket_service && yarn install

# Build image
$ docker build .
```

## nginx

```bash
$ cd nginx && mkdir data
$ touch .gitkeep
$ touch default.conf
$ touch Dockerfile
```

```conf
# Paste the below code block inside default.conf file
server {
    listen 80;
    large_client_header_buffers 4 16k;
    location / {
     root /usr/share/nginx/html;
     try_files $uri /index.html;
   }

   location /user_service {
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-NginX-Proxy true;
        proxy_pass http://user_service:4000;
        proxy_ssl_session_reuse off;
        proxy_set_header Host $http_host;
        proxy_cache_bypass $http_upgrade;
        proxy_redirect off;
    }

    location /wallet_service {
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-NginX-Proxy true;
        proxy_pass http://wallet_service:4100;
        proxy_ssl_session_reuse off;
        proxy_set_header Host $http_host;
        proxy_cache_bypass $http_upgrade;
        proxy_redirect off;
    }

    location /transaction_service {
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-NginX-Proxy true;
        proxy_pass http://transaction_service:4200;
        proxy_ssl_session_reuse off;
        proxy_set_header Host $http_host;
        proxy_cache_bypass $http_upgrade;
        proxy_redirect off;
    }

   location /main_service {
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-NginX-Proxy true;
        proxy_pass http://main_service:4400;
        proxy_ssl_session_reuse off;
        proxy_set_header Host $http_host;
        proxy_cache_bypass $http_upgrade;
        proxy_redirect off;
    }

    error_page 504 /504.html;
        location /504.html{
         return 502 '{"error": {"status_code": 502,"status": "Bad Gateway"}}';
    }
}
```

```Dockerfile
#Paste the below code inside Dockerfile created under nginx directory
FROM nginx:mainline-alpine
COPY default.conf /etc/nginx/conf.d/default.conf
```

## rabbitmq

```bash
#From root directory create a rabbitmq folder
$ mkdir rabbitmq
$ cd rabbitmq

# Create data and log folders
$ mkdir data
$ mkdir log

# Create rabbitm1.env at the root directory
$ touch rabbitmq.env
```

```env
# Paste the below configs inside rabbitmq.env file
RABBITMQ_DEFAULT_USER=admin
RABBITMQ_DEFAULT_PASS=admin
```

## Running the app with npm

```bash
# development
$ npm run start

# watch mode
$ npm run start:dev

# production mode
$ npm run start:prod
```

## Running the app with docker

```bash
#Create Docker-compose file at the root directory
$ touch docker-compose.yml
```

```yml
#Paste the below codes inside docker-compose.yml
version: "3.8"
services:
  web1:
    build:
      context: ./nginx
      dockerfile: Dockerfile
    image: boat_web
    container_name: boat_web
    restart: unless-stopped
    volumes:
      - ./nginx/data:/usr/share/nginx/html
      - ./nginx/default.conf:/etc/nginx/conf.d/default.conf
    networks:
      - boat-network
    ports:
      - "8000:80"

  user_service:
    build:
      context: ./user_service
      dockerfile: Dockerfile
      target: dev
    image: boat_user_service
    container_name: boat_user_service
    restart: unless-stopped
    env_file:
      - ./user_service/.env
    ports:
      - "4000:4000"
    volumes:
      - ./user_service/src:/usr/user_service/src
    command: npm run start:dev
    networks:
      - boat-network

  wallet_service:
    build:
      context: ./wallet_service
      dockerfile: Dockerfile
      target: dev
    image: boat_wallet_service
    container_name: boat_wallet_service
    restart: unless-stopped
    env_file:
      - ./wallet_service/.env
    ports:
      - "4100:4100"
    volumes:
      - ./wallet_service/src:/usr/wallet_service/src
    command: npm run start:dev
    networks:
      - boat-network

  transaction_service:
    build:
      context: ./transaction_service
      dockerfile: Dockerfile
      target: dev
    image: boat_transaction_service
    container_name: boat_transaction_service
    restart: unless-stopped
    env_file:
      - ./transaction_service/.env
    ports:
      - "4200:4200"
    volumes:
      - ./transaction_service/src:/usr/transaction_service/src
    command: npm run start:dev
    networks:
      - boat-network

  main_service:
    build:
      context: ./main_service
      dockerfile: Dockerfile
      target: dev
    image: main_service
    container_name: main_service
    restart: unless-stopped
    env_file:
      - ./main_service/.env
    ports:
      - "4400:4400"
    volumes:
      - ./main_service/src:/usr/main_service/src
    command: npm run start:dev
    networks:
      - boat-network

  agriculture_service:
    build:
      context: ./agriculture_service
      dockerfile: Dockerfile
      target: dev
    image: agriculture_service
    container_name: agriculture_service
    restart: unless-stopped
    env_file: ./agriculture_service/.env
    ports:
      - "4600:4600"
    volumes:
      - ./agriculture_service/src:/usr/agriculture_service/src
    command: npm run start:dev
    networks:
      - boat-network

  community_service:
    build:
      context: ./community_service
      dockerfile: Dockerfile
      target: dev
    image: community_service
    container_name: community_service
    restart: unless-stopped
    env_file: ./community_service/.env
    ports:
      - "4300:4300"
    volumes:
      - ./community_service/src:/usr/community_service/src
    command: npm run start:dev
    networks:
      - boat-network

  rabbitmq:
    image: rabbitmq:3-management-alpine
    container_name: "rabbitmq"
    env_file:
      - ./rabbitmq.env
    ports:
      - 5672:5672
      - 15672:15672
    volumes:
      - ./rabbitmq/data/:/var/lib/rabbitmq/
      - ./rabbitmq/log/:/var/log/rabbitmq
    networks:
      - boat-network

  postgres:
    container_name: postgres_container
    image: postgres
    environment:
      POSTGRES_USER: ${POSTGRES_USER:-postgres}
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD:-secret}
      PGDATA: /data/postgres
    volumes:
      - ./db:/data/postgres
    ports:
      - "54321:5432"
    networks:
      - boat-network
    restart: unless-stopped

networks:
  boat-network:
    driver: bridge
```

```bash
# Start Docker Container
$ docker-compose up
```

## Test

```bash
# unit tests
$ npm run test

# e2e tests
$ npm run test:e2e

# test coverage
$ npm run test:cov
```
