---
date: 2023-06-01 00:00:02
layout: post
title: (Sprint) 실습 진행
subtitle: Auto Scaling + CloudWatch를 이용한 알림
description: ASG의 원리를 익히고, 메트릭에 따른 스케일링 정책을 세우고 모니터링을 통해 정책이 적용되는지 확인해 봅시다.
image: https://res.cloudinary.com/dvqcvocet/image/upload/v1681432465/dev-jeans_%E1%84%87%E1%85%A9%E1%86%A8%E1%84%89%E1%85%A1%E1%84%87%E1%85%A9%E1%86%AB_y5n0eh.png 
optimized_image: https://res.cloudinary.com/dvqcvocet/image/upload/v1681432465/dev-jeans_%E1%84%87%E1%85%A9%E1%86%A8%E1%84%89%E1%85%A1%E1%84%87%E1%85%A9%E1%86%AB_y5n0eh.png 
category: mornitoring
tags:  
  - ASG
  - AutoScailingGroup

author: Hoonology
paginate: true
---
![Alt text](https://s3.ap-northeast-2.amazonaws.com/urclass-images/9Yt5UtU7AgThaz3RlNpjY-1651497045773.png)

# Step I
## 시작 템플릿, ASG 생성
#### 1. EC2 > 시작 템플릿 > 시작 템플릿 생성
![start1](/assets/img/CICD/start1.png)
#### 1.1. Quick Start > 원하는 아키텍처 선택
#### 1.2. 원하는 인스턴스 유형 선택
![start3](/assets/img/CICD/start3.png)
( 우분투 LTS, t2.nano 선택 )

#### 1.3. 네트워크 설정 > 서브넷 ( 시작 템플릿에 포함하지 않음 선택 - 2.2에서 설명한다. )
#### 1.4. 기존 보안그룹 선택 > launch-wizard-1 ( SSH 22번 포트, HTTP 80번 포트로 구성 )
![start4](/assets/img/CICD/start4.png)

#### 1.5. 고급 세부정보 > 사용자 데이터 추가
![start5](/assets/img/CICD/start5.png)
아래 스니펫을 입력해준다.
```bash
#!/bin/bash
echo "Hello, World" > index.html
sudo apt update
sudo apt install stress
nohup busybox httpd -f -p 80 &
```
#### 2. Auto Scailing 그룹 > Auto Scailing 그룹 생성
#### 2.1. 시작 템플릿을 연결해준다. 
![start6](/assets/img/CICD/start6.png)

#### 2.2. [다음]을 누른 뒤, 서브넷을 이 때 등록해준다.
a, c, d 를 등록한다. 간혹 b는 작동하지 않는다고 한다.

![start7](/assets/img/CICD/start7.png)

#### 2.3. 추가설정 > 모니터링 > CloudWatch 내에서 그룹 지표 수집 활성화 체크
![start8](/assets/img/CICD/start8.png)
#### 2.4. 그룹 크기 > 원하는 용량 : 1, 최소용량 : 1, 최대 용량 : 3
#### 2.5. 크기 조정 정책 > 대상 추적 크기 조정 정책 > 크기 조정 정책 이름 설정
![start9](/assets/img/CICD/start9.png)
#### 2.6. ASG가 시작 템플릿 구성과 연동 됐는지 확인
![start10](/assets/img/CICD/start10.png)


# Step II
## Lambda 함수 생성
#### 3. 람다 함수 생성을 python으로 한다.
#### 3.1. 트리거 추가로 SNS를 연결한다.
![lambda](/assets/img/CICD/lambda2.png)

![lambda](/assets/img/CICD/lambda1.png)
#### 3.2. 제공된 스니펫을 입력해주고 [Deploy] 실행
![lambda](/assets/img/CICD/lambda3.png)
#### 3.3. 구성 > 권한 > 실행 역할 > 역할 이름 선택
#### 3.4. 들어가서 [SNSFullAccess] 권한을 추가해준다. ( Full Access는 권장하지 않음 )
![lambda](/assets/img/CICD/lambda4.png)
#### 3.5. 환경변수에 HOOK_URL 추가
![lambda](/assets/img/CICD/lambda5.png)


# Step III
이제 부터 정말 중요하다. 
#### 4. CloudWatch > 경보 > 경보 생성 > 지표 선택
![alert](/assets/img/CICD/alert1.png)
#### 4.1. EC2 > Auto Scaling 그룹별 > 아까 만든 ASG - CPUUtilization 선택 
![alert](/assets/img/CICD/alert2.png)
#### 4.2. scale-out / in의 이름으로 정해준다.
![alert](/assets/img/CICD/alert4.png)
![alert](/assets/img/CICD/alert3.png)
#### 4.3. 임계값 조건을 50 보다 크거나 같음, 40 보다 작거나 같음으로 설정한다.

![alert](/assets/img/CICD/alert5.png)
![alert](/assets/img/CICD/alert6.png)

> 뒤에 설명하겠지만, ASG로 생성한 EC2 인스턴스를 실행하고 stress 명령어를 입력하여 CPU 사용량을 99%로 증가시킬 것이다.(scale-out의 경보를 만들기 위해)   
임계값 50 이상에서는 scale-out, 40 이하에서는 scale-in의 경보가 SNS에 전달되고 디스코드에 전송된다.

#### 4.4 람다 함수에 연결된 SNS에 경보상태 알림을 전송하는 기능을 넣어준다.
![alert](/assets/img/CICD/alert7.png)
#### 4.5. 각각 scale-out과 scal-in에 설정해준다.
![alert](/assets/img/CICD/alert8.png)

#### 5. EC2 > Auto Scaling 그룹 > 내가 만든 ASG 선택 > 자동 크기 조절
![alert](/assets/img/CICD/alert9.png)
#### 동적 크기 조정 정책을 아래와 같이 설정한다. 
- 단순 크기 조정을 선택 
![alert](/assets/img/CICD/size1.png)
![alert](/assets/img/CICD/size2.png)

# Step IV
## 작동 확인 

#### 6. pem 키에 chmod400 권한을 넣어준다.
#### 6.1. ASG > 인스턴스 관리 > 인스턴스 선택 > 연결 > SSH 클라이언트 
![alert](/assets/img/CICD/connect1.png)

#### 6.2. 터미널에 위 명령어 입력 후 EC2 환경에서 아래 코드 실행
```bash
$ stress -c 1
```

#### 6.3. 새로운 터미널을 열어 top을 입력하면 CPU 사용량이 99%임을 확인할 수 있다.
- CPU 사용량 50% 이상 : scale-out 
  - 경보가 SNS를 통해 전달되고, 람다 함수로 discord로 전송되게 해놓았으니 결과를 지켜본다.
  - 5분 정도 걸린다.

- stress 실행 터미널을 `cmd + C`로 종료하면 CPU가 40 이하로 내려간다.
  - scale-in 
  - 마찬가지로 5분 정도 소요된다.

# 결과
![alert](/assets/img/CICD/scale-out.png)
![alert](/assets/img/CICD/scale-in.png)