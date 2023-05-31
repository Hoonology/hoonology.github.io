---
date: 2023-05-31 00:00:00
layout: post
title: 서비스 모니터링 - 계층별 메트릭과 메트릭 구분 
subtitle: 서비스 모니터링
description: 메트릭을 살펴보자
image: https://res.cloudinary.com/dvqcvocet/image/upload/v1681954803/eoe0iiqoeiq9ghldrltc.png
optimized_image: https://res.cloudinary.com/dvqcvocet/image/upload/v1681954803/eoe0iiqoeiq9ghldrltc.png
category: CICD
tags:  
  - 모니터링
  - mornitoring
  - observability

author: Hoonology
paginate: true
---
# Index
- [계층별 메트릭과 메트릭 구분](#계층별-메트릭과-메트릭-구분)
- [메트릭 한눈에 보기](#메트릭-한눈에-보기)
- [Application Load Balancer](#application-load-balancer)
- [ECS](#ecs)
- [EC2](#ec2)
- [Lambda](#lambda)
- [사이트 신뢰성 엔지니어링 (SRE) 관련 메트릭](#사이트-신뢰성-엔지니어링--sre--관련-메트릭)

# 계층별 메트릭과 메트릭 구분
## 메트릭 한눈에 보기
![메트릭](/assets/img/CICD/met.png)

## Application Load Balancer
![Alt text](https://s3.ap-northeast-2.amazonaws.com/urclass-images/N80FCe__Q8BfNCImtrDj9-1651557340916.png)

## ECS
클러스터
![Alt text](https://user-images.githubusercontent.com/702622/164989495-acb8a380-ce4b-4d71-a88b-c2e0d7cd268d.png)

서비스
![Alt text](https://user-images.githubusercontent.com/702622/164989455-67a573e2-50ea-432e-a500-42a156394cd5.png)

태스크
![Alt text](https://user-images.githubusercontent.com/702622/164989427-862e7144-5a35-4146-b370-a929745a6489.png)

## EC2
![Alt text](https://user-images.githubusercontent.com/702622/164989407-998ba1b6-5ef3-4c9d-8469-f72eb1a55774.png)

## Lambda
![Alt text](https://user-images.githubusercontent.com/702622/164989608-0c1e821a-d045-4322-8552-345ba796dbfc.png)

## 메트릭을 이용한 질문 예제
- 원하는 파드(태스크)의 개수 중 실제로 실행 중인 파드(태스크)는 몇 개인가요?
  - k8s의 "요청/응답 관련 메트릭" 중 "(원하는/실행 중인/보류 중인) 작업 개수"를 사용하여 파악할 수 있습니다.
- Pending 상태인 파드(태스크)는 몇 개인가요?
  - k8s의 "요청/응답 관련 메트릭" 중 "요청 개수"를 사용하여 파악할 수 있습니다.
- 파드 요청을 처리할 수 있을 만큼의 충분한 리소스가 있나요?
  - k8s의 "컴퓨팅 유닛 관련 메트릭" 중 "CPU 사용량 (utilization)", "메모리 사용량", "네트워크 in/out", "디스크 사용량" 등을 사용하여 파악할 수 있습니다. 이들 메트릭을 통해 현재 리소스 사용량과 파드 요청에 대한 여유 리소스를 비교하여 확인할 수 있습니다.

# 사이트 신뢰성 엔지니어링 ( SRE ) 관련 메트릭

CPU 및 메모리, 사용량 등을 파악하는 것 외에도 **네트워크 요청에 따른 응답 상태**, **요청의 횟수나 시간** 등도 중요한 지표가 될 수 있습니다.

이를 통해 어떤 서비스(웹사이트)가 온전히 사용자에게 전달될 수 있도록 가용성을 극대화하는 기술/문화를 특별히   
사이트 신뢰성 엔지니어링(***Site Reliability Engineering***, ***SRE***)라고 부릅니다.

## Google SRE "네 가지 황금 시그널"
### 1. 대기 시간 (Latency)
대기 시간은 서비스가 요청에 응답하는 데 걸리는 시간을 나타냅니다. 핵심은 지속 시간뿐만 아니라 성공적인 요청의 대기 시간과, 실패한 요청의 대기 시간을 구별하는 데에도 중점을 두어야 합니다.

### 2.트래픽 (Traffic)
트래픽은 서비스에 대한 수요 측정입니다. 대표적인 예로는, 초당 HTTP 요청 수가 있습니다.

### 3.오류 (Errors)
오류는 실패한 요청/전체 요청 의 비율로 측정됩니다. 대부분의 경우 이러한 실패는 명시적이지만(예: HTTP 500 오류) 암시적일 수도 있습니다(예: "결과 없음"이라는 메시지를 본문으로 전달하는 HTTP 200 응답).

### 4. 포화 수준 (Saturation)
포화는 서비스 또는 시스템 리소스를 “얼마나 가득 채워서 사용하는가”로 설명할 수 있습니다. 전형적인 예로는 과도한 CPU 자원 사용이 있습니다. CPU 자원이 부족하면, 스로틀링을 초래하고 결과적으로 응용 프로그램의 성능을 저하시킵니다.

## 주요 모니터링 패턴
시간이 지남에 따라 다양한 모니터링 방법이 개발되었습니다. 대표적으로 USE 패턴, RED 패턴이 있으며, 그밖에 다른 패턴 역시 유사하며 대기 시간, 트래픽, 오류 및 포화도를 측정하기 위한 SRE 요구와 크게 다르지 않습니다.

### USE 패턴
USE 패턴은 모든 리소스에 대한 사용률(Utilization), 포화도(Saturation), 오류(Errors)를 체크하는 패턴을 의미합니다.

### RED 패턴
RED 패턴은 비율(Rate), 오류(Errors) 및 기간(Duration)을 주요 메트릭으로 정의하는 패턴입니다.