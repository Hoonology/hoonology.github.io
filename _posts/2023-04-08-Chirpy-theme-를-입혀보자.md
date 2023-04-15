---
date: 2023-04-08 12:00:00
layout: post
title: 깃허브 블로그 만들기 3 - Chirpy 테마 입혀보기
subtitle: 깃허브 블로그를 만들어보자
description: 실패를 하지 않는 두려움과 시도의 즐거움 - 트러블슈팅
image: https://res.cloudinary.com/dvqcvocet/image/upload/v1681139412/dev-jeans_v2eutk.png
optimized_image: https://res.cloudinary.com/dvqcvocet/image/upload/v1681139412/dev-jeans_v2eutk.png
category: blog
tags:
  - github
  - blog
author: Hoonology
comments: true
---

# Chirpy Theme
아래 블로그에서 chirpy 테마에 대한 모든 설치 방법이 잘 나와있다.  
https://www.irgroup.org/posts/jekyll-chirpy/

먼저 https://github.com/cotes2020/jekyll-theme-chirpy/fork 에서  포크 한 뒤,   

로컬 환경에 클론하고 CUI 환경에서 클론한 폴더로 이동한 뒤 아래 명령어 실행한다.

```bash
sudo tools/init
[INFO] Initialization successful!  <-- 이런 메세지가 나오면 성공입니다.
```
아래 파일들이 삭제 된다.
- .travis.yml
- _posts 폴더 하위의 파일들
- docs 폴더

## 의존성이 있는 모듈을 모두 설치를 위해 로컬에서 실행해본다.
```bash
bundle # 번들 설치
jekyll serve # 실행
```
위 명령어를 모두 실행했을 때 이상이 없음을 확인한다.

##  ```_configyml``` 커스터마이징  
https://www.irgroup.org/posts/jekyll-chirpy/  
이 페이지에 잘 나와있다.

### conf 파일 상세내용

| 항목              | 값                                                      | 설명                                                                                           |
| ----------------- | -------------------------------------------------------- | ---------------------------------------------------------------------------------------------- |
| lang              | ko                                                       | 언어를 한글로 설정합니다. 기본값은 en입니다.                                                 |
| timezone          | Asia/Seoul                                               | 서울 표준시로 설정합니다.                                                                     |
| title             | 아무거나~                                               | 블로그 제목을 넣어 줍니다.                                                                 |
| tagline           | 아무거나~                                               | title 아래에 작은 글씨로 부연설명을 넣을 수 있습니다.                                         |
| description       | 아무거나~                                               | SEO를 위한 키워드들을 입력합니다.                                                             |
| url               | [https://focuschange-test.github.io](https://focuschange-test.github.io)                       | 내 블로그로 실제 접속할 URL을 입력합니다.                                                     |
| github            | github id                                                | 본인의 GitHub 아이디를 입력합니다.                                                            |
| twitter.username | twitter id                                               | 트위터를 사용한다면 아이디를 입력합니다.                                                       |
| social.name       | 이름                                                     | 포스트 등에 표시할 나의 이름을 입력합니다.                                                     |
| social.email      | 이메일                                                   | 나의 이메일 계정을 입력합니다.                                                                 |
| social.links      | 소셜 링크들                                             | 트위터, 페이스북 등 내가 사용하고 있는 소셜 서비스의 나의 홈 URL을 입력합니다.                 |
| theme_mode        | light or dark                                            | 원하는 테마 스킨을 선택합니다. 기본은 light입니다.                                            |
| avatar            | 이미지 경로                                             | 블로그 왼쪽 상단에 표시될 나의 아바타 이미지를 설정합니다.                                    |
| toc               | true                                                     | 포스팅된 글의 오른쪽에 목차를 표시합니다.                                                     |
| paginate          | 10                                                       | 한 목록에 몇 개의 글을 표시해 줄 것인지 선택합니다.                                          |


### Gemfile.lock을 ignore하기
``` bash
nano .ignore
# 아래 내용 추가 -> ? 빌드/배포 에러 방지하기 위해 올리지 않도록 하는 것
Gemfile.lock
```



```bash
git push -u origin master
```

