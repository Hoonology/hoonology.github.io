---
date: 2023-04-12 11:48:32
layout: post
title: "Docker Image 다루기 2 "
subtitle: Sprint - Docker Image - II
description: 두 개의 Docker Image를 다루는 방식을 연습합니다. - docker-compose CLI
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
  - Docker_Compose
author: Hoonology
paginate: true
---
<!--more-->

# Getting Started
- nginx 이미지를 기반으로 한 sebcontents/client 이미지를 이용하여 client 컨테이너를 생성합니다.
- node 이미지를 기반으로 한 sebcontents/server 이미지를 이용하여 server 컨테이너를 생성합니다.
![docker](/assets/img/Docker/docker.png)
- 이번 실습에서는 localhost 의 8080 번 포트로 접속하면, sebcontents/client 이미지를 이용해 배포한 client 컨테이너의 80 번 포트로 연결됩니다.
- 위 그림은 매우 간략하게 표현되어 있습니다. 도커 네트워크에 대해서 좀 더 궁금하시다면 docker network, docker bridge 를 검색해 더 많은 정보를 찾을 수 있습니다

## docker-compose CLI
[docker-compose CLI](https://docs.docker.com/compose/reference/)  
아래 명령어로 Docker 컨테이너에서 여러 서비스를 빌드하고 관리
```bash
docker compose [-f <arg>...] [options] [COMMAND] [ARGS...]
```

| Command | Description |
| ------- | ----------- |
| build   | Build or rebuild services |
| bundle  | Generate a Docker bundle from the Compose file |
| config  | Validate and view the Compose file |
| create  | Create services |
| down    | Stop and remove containers, networks, images, and volumes |
| events  | Receive real time events from containers |
| exec    | Execute a command in a running container |
| help    | Get help on a command |
| images  | List images |
| kill    | Kill containers |
| logs    | View output from containers |
| pause   | Pause services |
| port    | Print the public port for a port binding |
| ps      | List containers |
| pull    | Pull service images |
| push    | Push service images |
| restart | Restart services |
| rm      | Remove stopped containers |
| run     | Run a one-off command |
| scale   | Set number of containers for a service |
| start   | Start services |
| stop    | Stop services |
| top     | Display the running processes |
| unpause | Unpause services |
| up      | Create and start containers |
| version | Show the Docker Compose version information |

### ```-f``` 하나 이상의 Compose 파일의 이름과 경로 지정
#### 여러 Compose 파일 지정 
```bash
docker compose -f docker-compose.yml -f docker-compose.admin.yml run backup_db
```
- ```docker-compose.yml```로 지정할 수 있다.
  ```yaml
  webapp:
    image: examples/web
    ports:
      - "8000:8000"
    volumes:
      - "/data"
  ```
  여러 Compose 파일을 사용하는 경우 파일의 모든 경로는 로 지정된 첫 번째 구성 파일에 상대적입니다 ```-f. --project-directory```옵션을 사용하여 이 기본 경로를 재정의 할 수 있습니다 .

#### 단일 Compose 파일에 대한 경로 지정 
플래그를 사용하여 명령줄에서 또는 셸 또는 환경 파일에서 COMPOSE_FILE 환경 변수를 ```-f``` 설정하여   
현재 디렉터리에 없는 Compose 파일의 경로를 지정할 수 있습니다.  

- 어디서나 서비스 에 대한 postgres 이미지를 가져오기 위해 ```docker compose pull``` 과 같은 명령을 사용할 수 있습니다 .
  ```bash
  docker compose -f ~/sandbox/rails/docker-compose.yml pull db
  ```


### docker-compose CLI 사용
1. ```docker-compse.yaml```에 정의된 이미지를 컨테이너로 **실행**
    
    ```bash
    docker-compose up # -d 옵션을 함께 사용하면, 컨테이너를 백그라운드로 실행할 수 있습니다.
    ```

2. ```docker-compose.yaml```에 정의된 이미지를 이용해 실행된 컨테이너를 **종료**
    ```bash
    docker-compose down
    ```
3. 특정 이미지만 컨테이너로 실행
    ```bash
    docker-compose up {특정 이미지}
    ```

# 요구사항
두 개 이상의 도커 컨테이너를 연결하는 docker-compose를 사용해 보겠습니다.

- 다음의 설명에 따라 ```docker-compose.yaml``` 파일을 생성하세요.
  - 소스 코드는 이미 작성되어 있습니다.
  - ```docker-compose up -d```을 통해 컨테이너를 구동하세요.
  - 결과를 ```localhost:8080``` 혹은 ```127.0.0.1:8080``` 에서 확인하세요.


# Quiz 3
client와 server를 동시에 구동해 로그인 후 나타나는 단어를 확인하세요.
- ```docker-compse.yaml```
  - ***하나의 docker-compose에서 관리되는 컨테이너끼리는 동일한 docker network에서 구동됩니다.***  
    docker run 명령과 다르게 docker-compose 파일 안에서 기본 network가 사용됩니다.

```yml
version: '3.8'

services:
  nginx:
    image: sebcontents/client
    restart: 'always'
    ports:
      - "8080:80"
    container_name: client

  node:
    image: sebcontents/server
    restart: 'always'
    ports:
      - "4999:80"
    container_name: server
```
1. docker-compose.yaml 혹은 docker-compose.yml 파일을 위의 소스를 그대로 붙여넣어 생성합니다. 파일을 생성할 때 터미널의 위치는 무관합니다.
2. yaml 파일이 있는 위치에서 ```docker-compose up -d``` 명령어를 통해 yaml 파일을 실행합니다.
![docker1](/assets/img/Docker/dockerTerminal.png)
![docker2](/assets/img/Docker/dockerDesktop.png)
3. 브라우저에서 ```localhost:8080``` 혹은 ```127.0.0.1:8080``` 에서 실행된 **client 화면을 확인**한 후, 로그인 합니다. 로그인 정보는 브라우저 화면을 확인하세요.
![docker3](/assets/img/Docker/login.png)
4. 로그인 후 나오는 단어를 확인하세요. Quiz 3의 정답입니다.

