---
date: 2023-04-13 00:00:32
layout: post
title: "Docker Compose 실행 및 풀스택 앱 컨테이너화"
subtitle: Application Container & Docker Compose
description: 한 개의 Docker Image를 다루는 방식을 연습합니다. 컨테이너와 이미지에 대한 이해를 합니다.
image: https://res.cloudinary.com/dvqcvocet/image/upload/v1681198236/dev-jeans_r2fkxp.png
optimized_image: https://res.cloudinary.com/dvqcvocet/image/upload/v1681198236/dev-jeans_r2fkxp.png
category: docker
tags:  
  - Docker
  - nginx
  - postgresql
  - 컨테이너
  - DockerCLI
  - CLI
  - 도커이미지
  - 앱컨테이너화
  - Docker_Compose
author: Hoonology
paginate: true
---


# 풀스택 앱 컨테이너화와 Docker Compose를 이용한 실행
풀스택 애플리케이션은 다음 아키텍처로 구성되어 있습니다.

![80](/assets/img/Docker/80.png)
- rontend 디렉토리에 있는 파일이 이미지로 빌드되어야 합니다.
  - Apache HTTP Server (httpd:2.4)를 base image로 삼아야 합니다.
  - 컨테이너 내 80포트로 접속시, frontend/index.html이 표시되어야 합니다.
  - frontend/Dockerfile을 채워넣으세요.
- backend 디렉토리에 있는 파일이 이미지로 빌드되어야 합니다.
  - Node.js 이미지 (node:16-alpine) 를 base image로 삼아야 합니다.
  - 컨테이너 내 80포트로 접속시, hello from server가 응답으로 표시되어야 합니다.
  - backend/Dockerfile을 채워넣으세요.
- docker-compose.yml 파일을 통해 두 이미지가 동시에 실행되어야 합니다.
  - 두 이미지는 아키텍처에 표시되어 있는 포트 번호로 서로 통신할 수 있어야 합니다.
  - frontend는 backend에 의존성을 가집니다.
  - docker-compose.yml을 채워넣으세요.


## 실행

`docker compose up` 으로 실행한 후, http://localhost 로 접속하세요.
```bash
cd frontend | nano Dockerfile
# 아래 입력

# Apache HTTP server 이미지를 바탕으로 컨테이너를 실행한 후
# http://localhost로 접속했을 때 index.html이 표시되게 만드세요


FROM httpd:2.4

COPY ./ /usr/local/apache2/htdocs/
```

```bash
docker-compose up
```
```bash
cd backend | nano Dockerfile

# node.js 이미지를 바탕으로 컨테이너를 실행한 후
# http://localhost:3333으로 접속했을 때 hello from server 메시지가 표시되게 만드세요

# 아래 입력

FROM node:16-alpine

WORKDIR /usr/src/app

COPY package*.json ./

RUN npm install

COPY . .

EXPOSE 80

CMD [ "node", "app.js" ]
```

```bash
# docker compose 파일을 완성하고
# http://localhost로 접속했을 때 서버로부터 온 메시지가 표시되게 만드세요

version: "3.0"

services:
  apache2:
    image: frontend
    restart: 'always'
    ports:
      - "80:80"
    container_name: frontend_client

  node:
    image: backend
    restart: 'always'
    ports:
      - "3333:80"
    container_name: backend_server
```

## 뭔가 바꾼 것을 적용하고 싶을 때
`docker compose build` 후 `docker compose up`을 실행하세요.

<br>

# 계속 compose up이 안된 이유
1. 우선, ```docker-compose up``` 을 하려면, 루트디렉토리에서 실행한다.
2. frontend, backend 디렉토리에 있는 Dockerfile을 손본 뒤, build를 해준다.
  ```bash
  docker build -t frontend .
  docker build -t backend .
  ```
3. 최종적으로 루트 디렉토리에 가서 아래와 같이 실행
```bash
docker compose build
docker-compose up
```

![compose](/assets/img/Docker/composeup.png)

## 종료

`docker compose down` 으로 종료하세요.
