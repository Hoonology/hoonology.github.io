---
date: 2023-04-12 01:48:32
layout: post
title: "Docker Image 다루기"
subtitle: Sprint - Docker Image - I
description: 한 개의 Docker Image를 다루는 방식을 연습합니다.
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
author: Hoonology
paginate: true
---
<!--more-->

# 요구사항
요구 사항에 따라 docker-cli 명령어를 이용해 실습을 진행하여, Quiz를 풀 수 있어야 합니다.

--page-break--  

# Quiz 1: 브라우저 속 게임 화면의 특정 단어를 확인하세요.
- 이미지는 ```sebcontents/part1```을 이용해야 합니다. 태그는 latest 입니다.
  ```bash
  docker pull sebcontents/part1:latest
  ```
  ![pull](/assets/img/Docker/pull.png)

- ```sebcontents/part1```은 **아파치 웹 서버 이미지(httpd)**를 기반으로 commit된 새로운 이미지 입니다.
- 아래 명령어를 터미널에 입력하여 컨테이너를 생성합니다
    ```bash
    docker run --name 컨테이너_이름 -d -p 3000:80 sebcontents/part1
    ```
> 먼저 다음 명령을 사용하여 Docker 레지스트리에서 sebcontents/part1 이미지의 최신 버전을 가져옵니다.  
```docker pull sebcontents/part1:latest```  
이 명령은 이미지를 로컬 컴퓨터에 다운로드하여 컨테이너를 만드는 데 사용할 수 있도록 합니다.  
그런 다음,  ```docker run``` 명령을 사용하여 이름이 game_container인 sebcontents/part1 이미지를 기반으로  
새 컨테이너를 만들고 호스트 시스템의 포트 3000을 컨테이너의 포트 80에 매핑하고 컨테이너를 실행합니다.  
분리 모드:  
```docker run --name game_container -d -p 3000:80 sebcontents/part1```  
이 명령은 이전에 다운로드한 sebcontents/part1 이미지를 컨테이너의 기본 이미지로 사용하여 지정된 구성으로 새 컨테이너를 만듭니다.


- Docker Hub에 저장되어 있는 이미지를 사용하는 경우, repository에 작성된 안내 사항을 확인하는 습관을 들이는 것이 중요합니다.
- ```sebcontents/part1``` 이미지의 repository로 이동하여 안내 사항을 참고하고, 컨테이너를 생성합니다.
- 컨테이너를 통해 space 게임을 실행해보세요. 소스 코드는 [https://github.com/codestates/SpaceInvaders](https://github.com/codestates/SpaceInvaders)에 있습니다.  

먼저, 
```bash
# Dockerfile 추가 후 수정 진행
nano Dockerfile
# 아래 내용 추가
FROM sebcontents/part1:latest

WORKDIR /app

COPY package*.json ./

RUN apt-get update && apt-get install -y npm

COPY . .

EXPOSE 3000

CMD ["npm", "start"]
```

  ```bash
  docker build -t game_container .
  ```

> ```127.0.0.1:3000``` 혹은 ```localhost:3000``` 실행 
- 게임 실행 후 다음 그림의 빨간 박스에 들어갈 단어가 무엇인지 확인하세요. Quiz 1의 정답입니다.


# 오류 발생
일단 모든 이미지와 컨테이너를 정지한 뒤 삭제한다.
```bash
# 컨테이너 정지 후 삭제
docker stop $(docker ps -a -q)
docker rm $(docker ps -a -q)
# 이미지 삭제
docker rmi $(docker images -q) --force
```
## prune으로 사용하지 않는 오브젝트만 삭제하기
```bash
 docker container prune
 docker image prune
```

# 다시 실행해보자
