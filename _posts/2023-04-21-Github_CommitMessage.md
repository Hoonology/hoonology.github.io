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
  - 커밋
  - 커밋메시지
  - commit
  - 컨벤션
  - Commit Message Convention



  
author: Hoonology
comments: true

---

# Git Commit Convention
Commit Convention은 쉽게 commit할 때 commit message에 대한 약속

```bash
type: Subject -> 제목  # 50 글자 제한
(한칸 띄우기)  
body(생략 가능) -> 본문  # 72 글자 제한
(한칸 띄우기)  
footer(생략 가능) -> 꼬리말 
``` 

|Type	| 설명|
|---|---|
|Feat |	새로운 기능 추가|
|Fix|	버그 수정 또는 typo
|Refactor|	리팩토링
|Design|	CSS 등 사용자 UI 디자인 변경
|Comment|	필요한 주석 추가 및 변경
|Style|	코드 포맷팅, 세미콜론 누락, 코드 변경이 없는 경우
|Test|	테스트(테스트 코드 추가, 수정, 삭제, 비즈니스 로직에 변경이 없는 경우)
|Chore|	위에 걸리지 않는 기타 변경사항(빌드 스크립트 수정, assets image, 패키지 매니저 등)
|Init|	프로젝트 초기 생성
|Rename|	파일 혹은 폴더명 수정하거나 옮기는 경우
|Remove|	파일을 삭제하는 작업만 수행하는 경우