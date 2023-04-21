---
date: 2023-04-21 00:02:30
layout: post
title: 깃허브 커밋 메시지 컨벤션 - CI를 위하여 
subtitle: Github Commit Message
description: Github Commit Mesaage 규칙을 알아보자
image: https://res.cloudinary.com/dvqcvocet/image/upload/v1681139412/dev-jeans_v2eutk.png
optimized_image: https://res.cloudinary.com/dvqcvocet/image/upload/v1681139412/dev-jeans_v2eutk.png
category: Github
tags:
  - 환경변수


  
author: Hoonology
comments: true

---

# 환경변수
운영체제 혹은 소프트웨어가, 각기 다른 컴퓨터 또는 사용자마다 별도로 가질 수 있는 고유한 정보를 담는 데 사용하는 변수

# 환경변수가 필요한 이유
- 배포되는 기능 마다 설정이 달라질 수 있기 때문이다.
  - 새로운 배포마다 새로운 기능들을 배포하게 됨으로써 설정값이 바귄다.
  - 코드의 버전을 관리하는 것처럼 설정값의 버전을 관리할 필요가 있다.
- 환경변수를 통해 설정을 분리하는 이유
  - 환경 변수는 코드 변경 없이 배포 때마다 쉽게 변경할 수 있다.
  - 설정 파일과 달리 잘못해서 코드 저장소에 올라갈 가능성이 적다.
  - 다른 설정 매커니즘과 달리 언어나 OS에 의존하지 않는다.
  - 설정을 환경 변수를 통해 분리해야 하는 이유에 대한 레퍼런스 : [https://12factor.net/config](https://12factor.net/config)

## 환경변수 예시
- Linux 운영체제: export 명령어
- 코드를 통해 환경 변수를 설정하는 법
  - node.js → process.env
- 서비스 내에서 환경 변수를 설정하는 법
  - GitHub Action

---

# Sprint - 코드로부터 환경 변수 분리

## Bare minimum requirement 
- GitHub Action을 이용하여 CI 상에서 Mini node server를 Docker 이미지로 만든 후, 여러분의 Docker Hub에 push하세요.

## Get Started
본 스프린트는 앞서 GitHub Action으로 진행한 빌드/테스트 자동화가 끝나고 진행되는 스프린트입니다.

### 1. CI 상에서 주어진 Dockerfile을 이용해 Docker 이미지를 빌드할 수 있도록, workflow를 새로 만드세요.
- 다음 [레퍼런스](https://docs.github.com/en/actions/publishing-packages/publishing-docker-images#publishing-images-to-docker-hub)를 참고해서 Docker 빌드용 GitHub Action workflow를 만드세요.
- workflow를 추가한다고 해서 GitHub Action이 즉시 작동하지는 않을 것입니다.
- repository에서 오른쪽 사이드바를 살펴보면, Release -> Create a new release 링크가 존재합니다. 이 링크를 누르고 새로운 릴리스를 발행합니다. 설정은 다음과 같이 진행합니다.
  - Choose a tag: v1.0.0
  - Release title 및 Release notes는 여러분이 자유롭게 입력하세요.
- Publish release 후에 GitHub Action이 작동하나요?
  - Q. 왜 작동이 되는 걸까요? 아까는 왜 안 됐을까요?

### 2. 인증 정보에 대한 환경 변수를 만드세요.
1번 과정을 통해 GitHub Action을 실행하면 그 결과는 실패로 나타날 것입니다. 무엇이 잘못되었는지 먼저 로그를 살펴보세요.

- Q. 왜 실패했을까요? 로그에서 그 이유를 찾아보세요.


Workflow YAML 파일을 자세히 살펴보면, DOCKER_USERNAME 및 DOCKER_PASSWORD라는 환경 변수가 존재합니다. 말 그대로 아이디와 비밀번호와 같은 민감정보를 YAML 코드에 입력해서 git commit 기록으로 남겨둔다면, 더 이상은 그 비밀번호를 사용할 수 없게 될 것입니다. (돌이킬 수 없어요!)

GitHub에서는 이러한 환경 변수를 안전하게 보관할 수 있는 기능을 제공합니다. Settings -> Secrets에서 환경 변수를 설정하세요.


### 3. Dockerfile 빌드 결과를 확인하고, Docker Hub에 이미지가 제대로 push 되었는지 확인하세요.
앞서 이 과정을 다 잘 따라왔다면, 여러분 각 개인의 Docker Hub에는 mini-node-server 이미지가 성공적으로 push되어 있을 겁니다. ```https://hub.docker.com/u/<여러분_아이디>```에 들어가서 결과를 확인해 보세요.