---
date: 2023-04-12 01:48:32
layout: post
title: "Docker CLI"
subtitle: Docker Image 및 Container를 다루기 위한 Docker Command Line 명령어를 알아본다.
description: Docker Image 및 Container를 다루기 위한 Docker Command Line 명령어를 알아본다.
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

# Docker CLI
- 필독 : [Docker docs](https://docs.docker.com/engine/reference/commandline/container_run/)
  - 사용법 : Docker CLI, Docker-Compose CLI, API Reference
  - 환경 및 빌드 파일 구성 : DockerFile, Docker-Compose File

## 사용법
```bash
docker container run [OPTIONS] IMAGE [COMMAND] [ARG...]
```
### Docker docs
#### [OPTIONS](https://docs.docker.com/engine/reference/commandline/container_run/#options)
#### [COMMAND](https://docs.docker.com/engine/reference/commandline/container_run/#parent-command)
#### [Related COMMAND](https://docs.docker.com/engine/reference/commandline/container_run/#related-commands)

## Docker 이용하기
![이미지구분](/assets/img/Docker/%EC%9D%B4%EB%AF%B8%EC%A7%80%EA%B5%AC%EB%B6%84.png)
<p align = "center"> [그림] 이미지 구분 </p>

- Registry
  - 도커 이미지 관리 공간
  - 기본 레지스트리 : [Docker Hub](https://hub.docker.com/)
  - 레지스트리 구분 : Docker Hub, Private Docker Hub, 회사 내부용 레지스트리
- Repository ( Github Rep와 비슷하다. )
  - 레지스트리 내 도커 이미지가 저장되는 공간
  - 이미지 이름으로 사용도 함
- Tag
  - 이미지를 설명하는 버전 정보
  - 기본 태그 : ```latest```

#### 위의 설명을 토대로 ```docker/whalesay:latest```를 읽어보자
- Registry Account : ```docker```
- Repository Name : ```whalesay```
- Tag : ```latest```

### Docker Example 수행 ( docker/whalesay )
```bash
docker image pull docker/whalesay:latest
docker image ls
```
![pull](/assets/img/Docker/dockerpull.png)

#### 받아온 이미지 실행
```bash
docker container run --name whaleasy docker/whalesay:latest cowsay boo
```
![boo](/assets/img/Docker/boo.png)

#### 컨테이너 리스트 출력
```bash
docker container ps -a
```
![psa](/assets/img/Docker/psa.png)

#### 컨테이너 삭제
```bash
docker container rm 컨테이너_이름
```

내 환경에서 ```docker image rm docker/whalesay``` 실행 오류가 나왔다.  
> Error response from daemon: conflict: unable to remove repository reference "docker/whalesay" (must force) - container 44103ad9eed0 is using its referenced image 6b362a9f73eb

```bash
docker stop 44103ad9eed0 # 정지
docker rm 44103ad9eed0 # 삭제
```

터널에서 수족관의 물고기를 볼 수 있는 커맨드 
```bash
docker container run -it --rm danielkraic/asciiquarium:latest
```
- it : -i, -t 를 동시에 사용한 옵션입니다. 사용자와 컨테이너 간에 인터렉션(interaction)이 필요하다면 이 옵션을 사용합니다. 이 예제에서는 출력되는 화면을 사용자가 지속적으로 보기 위해서 사용하였습니다. 예를 들어 Python 명령이 필요하거나 추가로 다른 입력을 받는다면, 이 옵션을 지정한 뒤 사용합니다.

# Docker CLI(2) - Copy, Dockerfile
## Docker 컨테이너에 파일을 복사하기
1. 웹서버는 도커 컨테이너로 실행
2. 웹 서버를 구성하는 파일은 직접 만들거나 가져온 파일 구성
- Pros
  - 서버에 문제가 생기는 것을 호스트와 별개로 파악 가능
  - 문제가 생긴 서버를 끄고, 마치 공장 초기화를 하듯 도커 이미지로 서버를 재구동할 수 있음

로컬에 있는 파일과 도커 이미지를 연결하는 방법
- CP : 호스트와 컨테이너 사이에 파일을 복사
- Volume : 호스트와 컨테이너 사이에 공간을 마운트

## httpd 웹 서버
1. clone 
```bash
git clone https://github.com/codestates/pacman-canvas
```


2. ```docker container run``` 명령어로 httpd 를 실행

```bash
docker container run --name 컨테이너_이름 -p 818:80 httpd
```
- -p 옵션은 로컬호스트의 포트와 컨테이너의 포트를 연결합니다. 2의 명령어에서 818포트가 로컬호스트의 포트이고, 80번은 컨테이너의 포트입니다.
- httpd 는 일정 시간 연결 기록이 없으면, 서버 가동이 중지됩니다. 실행 중이던 도커 컨테이너가 중지되었다면, 다시 실행하세요.
- 터미널에서 1, 2의 명령어를 입력했을 때, 다음 화면처럼 터미널의 작동이 중단된 것처럼 보여도 걱정할 필요 없습니다. 정상적인 상태이므로 터미널을 종료하지 말고, 다른 터미널 창을 열어 계속해서 실습을 진행합니다.

- 컨테이너를 백그라운드에서 실행하게 해주는 -d 옵션에 대해 알아봅니다. 해당 옵션을 사용했을 때와 사용하지 않았을 때 차이점을 확인해보세요.


3. ```127.0.0.1:818``` 혹은 ```localhost:818``` 을 통해 웹 서버가 작동하고 있는지 확인합니다.
> It Works! 나오면 성공

4. 서버가 정상적으로 열린 것을 확인한 후, 새로운 터미널을 열어 docker container cp 명령어를 입력해 로컬호스트에 있는 파일을 컨테이너에 전달합니다.

``` bash
docker container cp ./ 컨테이너_이름:/usr/local/apache2/htdocs/
```
[주의] 위의 명령어를 로컬 터미널에서 실행할 때, 위치는 pacman-canvas 디렉토리여야 합니다.


## Docker 이미지 만들기
이번에는 앞서 만들어 본 Docker Container를 이미지 파일로 변환합니다. **이미지로 만들어 놓을 때의 장점**은 다음과 같습니다.
- 이전에 작업했던 내용을 다시 한 번 수행하지 않아도 됨
- 배포 및 관리가 유용
### 1. 구동한 Docker Container 를 **이미지로 만드는 법**
  - ```docker container commit```
    ```bash
    docker container commit 컨테이너_이름 my_pacman:1.0
    ╰─$ docker container commit 220f6bd95e4e pacman:1.0
    ```
  <p align = "center">[커맨드] 구동한 Docker Container를 commit </p>    

- 
  ```bash
  docker images
  ```

<p align = "center">[커맨드] pacman 1.0 확인 </p>

- 생성된 이미지를 900 포트에서 웹 서버로 구동

  ```bash
  docker run --name my_web2 -p 900:80 my_pacman:1.0
  ```


<p align = "center">[커맨드] 900 포트에서 웹 서버로 이미지를 구동</p>

```127.0.0.1:900``` 또는 ```localhost:900``` 접속 !
![pac](/assets/img/Docker/pacman.png)


### 2. Docker Image 빌드를 위한 파일 : Dockerfile로 만들기
[https://docs.docker.com/engine/reference/builder/](https://docs.docker.com/engine/reference/builder/)
Dockerfile는 이미지를 어셈블하기 위해 사용자가 명령줄에서 호출할 수 있는 모든 명령을 포함하는 텍스트 문서입니다. 이 페이지에서는 에서 사용할 수 있는 명령에 대해 설명합니다.
- Dockerfile 을 만들고, Dockerfile 대로 이미지를 build 하는 방법입니다.


#### 기본 형식

  ```bash
  # Comment
  INSTRUCTION arguments
  ```

- ```FROM ARG FROM Dockerfile```

  ```bash
  RUN echo hello \
  world
  ```

- Dockerfile로 pacman 이미지를 생성해 보세요.
  - COPY 구문을 잘 살펴보세요. Dockerfile은 어디에 생성되어야 할까요?  
  
  ```bash
  FROM httpd:2.4 # 베이스 이미지를 httpd:2.4 로 사용합니다.
  COPY ./ /usr/local/apache2/htdocs/ 
  # 호스트의 현재 경로에 있는 파일을 생성할 이미지 /usr/local/apache2/htdocs/ 에 복사합니다.
  ```
  (현재 경로에 있는 파일을 생성할 이미지 경로에 복사하는 명령의 Dockerfile 소스 코드)

- ```docker build``` 명령은, Dockerfile로 도커 이미지 파일을 생성합니다.
  ```bash
  # --tag 는 name:tag 형식으로 이미지를 생성할 수 있습니다.
  # 지정한 경로에 있는 Dockerfile을 찾아서 빌드합니다.
  docker build --tag my_pacman:2.0 . # "."을 명령어에 꼭 포함해야 합니다! 
  ```
![docker build](/assets/img/Docker/dockerbuild.png)

- 생성된 이미지를 이용해 901 포트에 웹 서버 구동
  ```bash
  docker run --name my_web3 -p 901:80 pacman:2.0
  ```
```127.0.0.1:901``` 혹은 ```localhost:901``` 을 통해 웹 서버가 작동하고 있는지 확인합니다.

![pacman2](/assets/img/Docker/pacman.png)
