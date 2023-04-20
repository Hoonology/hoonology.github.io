---
date: 2023-04-20 03:00:30
layout: post
title: (Sprint) Github Actions를 이용한 빌드 및 테스트 자동화
subtitle: Github Actions에 대하여
description: 유닛 테스트 통과 -> Node.js CI 적용
image: https://res.cloudinary.com/dvqcvocet/image/upload/v1681954803/eoe0iiqoeiq9ghldrltc.png
optimized_image: https://res.cloudinary.com/dvqcvocet/image/upload/v1681954803/eoe0iiqoeiq9ghldrltc.png 
category: CI/CD
tags:
  - Github Action
  
author: Hoonology
comments: true

---

# 1. 유닛 테스트 통과
테스트 주도 개발(**TDD**)을 연습 - ```test/app.test.js```를 수정하여 통과하지 않는 테스트를 모두 통과

1. 애플리케이션은 node.js로 작성되어 있습니다. node.js LTS 버전을 준비합니다.
2. 먼저 애플리케이션의 의존성(dependency)을 설치해야 합니다. ```npm install``` 명령을 이용해 의존성을 설치합니다.
3. 테스트가 통과하는지 확인하려면 ```npm test``` 명령을 이용합니다. 다음과 같이 테스트가 통과하지 않는 것을 먼저 확인하세요.
4. ```test/app.test.js``` 파일을 열어 통과하지 않는 테스트를 수정하세요. ```FILL_ME_IN```이라고 적힌 곳에 기댓값을 적어주면 됩니다.



