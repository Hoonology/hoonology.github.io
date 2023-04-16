---
date: 2023-04-16 00:00:00
layout: post
title: AWS 서비스 - Storage
subtitle: AWS 서비스 Storage에 대하여
description: Storage - 클라우드 컴퓨팅 제공업체를 통해 데이터와 파일을 인터넷에 저장할 수 있는 클라우드 컴퓨팅 모델
image: https://res.cloudinary.com/dvqcvocet/image/upload/v1681432465/dev-jeans_%E1%84%87%E1%85%A9%E1%86%A8%E1%84%89%E1%85%A1%E1%84%87%E1%85%A9%E1%86%AB_y5n0eh.png
optimized_image: https://res.cloudinary.com/dvqcvocet/image/upload/v1681432465/dev-jeans_%E1%84%87%E1%85%A9%E1%86%A8%E1%84%89%E1%85%A1%E1%84%87%E1%85%A9%E1%86%AB_y5n0eh.png 
category: AWS
tags:
  - AWS
  - cloud
  - EC2
  - 인스턴스
  - AMI
author: Hoonology
comments: true

---

# AWS Storage 
## 1. 객체 스토리지 ( Object Storage )
객체 : 문서, 영상, 이미지 등 비교적 단순한 구조에 메타데이터를 포함하고 있는 데이터  
> 인터넷으로 연결된 API를 통해 데이터를 애플리케이션에 제공
> 데이터를 객체로 저장하며 메타데이터와 데이터로 구성

- 장점
  > - 대규모 데이터 저장 가능
  > - 높은 확장성, 내구성, 가용성
  > - 객체 단위로 데이터 처리 -> 객체에 대한 접근이 빠름

- 단점
  > - 파일 단위로 접근하는 것이 아니기 때문에 일부 응용 프로그램에서는 적합하지 않다.
  > - 객체 단위로 데이터를 처리하기 때문에 작은 파일에 대한 처리 비용이 높아진다.


## 2. 블록 스토리지 ( Block Storage )
**블록 스토리지에서의 데이터** : 서버 인스턴스에 디스크 볼륨의 형태로 제공되는 데이터
> EC2 인스턴스에 포함된 볼륨에 **고속으로 접근** 가능 

- 장점
  > - 블록 단위로 데이터를 처리 -? 블록 단위로 데이터를 읽고 쓰기가 가능
    - 서버 디스크로 사용할 수 있어서 높은 처리 속도를 가짐
- 단점
  > - 블록 스토리지는 서버에 직접 연결되어 있기 때문에, 서버 장애 시 데이터 손실 발생
    - 블록 스토리지는 큰 용량의 데이터를 처리하기에는 적합하지 않다.
* 대표적인 서비스
  - EBS( Elastic Block Store )
  
## 3. 파일 스토리지 ( File Storage )
**파일 스토리지에서 데이터** : 서버 인스턴스에 파일 시스템 인터페이스 방식으로 제공되는 데이터

* 대표적인 서비스( 고속으로 다수의 EC2 인스턴스를 통해 데이터에 접근 )
  - EFS( Elastic File System )
  

## 