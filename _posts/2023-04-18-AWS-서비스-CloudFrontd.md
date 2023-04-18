---
date: 2023-04-18 03:30:00
layout: post
title: (Sprint) 도메인 연결과 CDN 및 HTTPS적용
subtitle: 도메인 연결과 CDN 및 HTTPS적용
description: (Sprint) 도메인 연결과 CDN 및 HTTPS적용 
image: https://res.cloudinary.com/dvqcvocet/image/upload/v1681432465/dev-jeans_%E1%84%87%E1%85%A9%E1%86%A8%E1%84%89%E1%85%A1%E1%84%87%E1%85%A9%E1%86%AB_y5n0eh.png
optimized_image: https://res.cloudinary.com/dvqcvocet/image/upload/v1681432465/dev-jeans_%E1%84%87%E1%85%A9%E1%86%A8%E1%84%89%E1%85%A1%E1%84%87%E1%85%A9%E1%86%AB_y5n0eh.png 
category: AWS
tags:
  - AWS
  - cloud
  - CloudFront
  - Route53 
  - Sprint
author: Hoonology
comments: true

---

# 도메인 연결과 CDN 및 HTTPS 적용
## Prerequisite
- Sprint1인 HTTP 배포를 완료해야 합니다.
- HTTPS에 대한 이해와 학습이 선행되어야 합니다.
- AWS Route53을 통해 구매한 도메인이 있어야 합니다.

## Bare Minimum Requirements
- HTTPS로 웹 애플리케이션을 배포할 수 있어야 합니다.
- CloudFront, Certificate Manager, Elastic Load Balancer, Route53 등의 서비스에 대해서 이해합니다.
- 모든 테스트를 통과하고, 제출해야 합니다.

## Getting Started
- HTTPS를 적용하기 위해서는 어떤 아키텍처와 어떤 AWS 서비스가 필요한지 구상합니다.
  - 구상한 내용을 draw.io 등을 이용해 다이어그램으로 그리고, 해당 이미지파일을 S3에 httpsdiagram 이라는 이름으로 업로드합니다.
  - 이때 해당 객체에 대한 퍼블릭 액세스를 허용해야 테스트를 통과할 수 있습니다.
- .env파일에 HTTPS 적용을 위한 환경설정으로 적절하게 작성합니다.
- npm run test2 명령을 사용해 테스트가 통과하는지 확인합니다.
- 실제로 웹 애플리케이션이 브라우저 상에서 HTTPS 프로토콜로 작동하는지 확인합니다.

### 1. Certificate Manager를 통한 도메인 인증서 발급
- 구매한 도메인을 기준으로 인증서를 발급 받습니다.
  - 인증서는 프론트엔드 Cloudfront 사용 리전인 us-east-1과 백엔드 Load Balancer 사용리전인 ap-northeast-2에서 발급 받아야 합니다.
  - 발급 시, DNS 검증 가이드로 레퍼런스를 참고하세요.
  - 이후에 DNS 공급자로 Route53을 이용합니다. Route53에 레코드 생성과정을 반드시 거쳐야합니다.
- 인증까지 최소 30분의 시간이 소요될 수 있습니다.

### 2. 백엔드 HTTPS 적용
- 애플리케이션 로드밸런서(Application Load Balancer)를 생성합니다.
- ALB의 리스너, 가용영역, 인증서를 설정합니다.
- 대상 그룹(target group)을 등록합니다.
- 로드밸런서 DNS 주소로 접속해, 테스트를 진행합니다.
- 아래 레퍼런스를 참조하여, 스프린트를 진행합니다.

### 3. 프론트엔드 CDN 및 HTTPS 적용
- Origin Domain을 설정해야 합니다.
- Viewer protocol policy는 Redirect HTTP to HTTPS로 지정해야 합니다.
- Certificate Manager에서 발급받은 인증서를 사용해야 합니다.
- Default root object 부분에 index.html을 작성해야 합니다.
- 대체도메인과 인증 받은 도메인의 이름이 같아야 합니다.
- 생성된 배포의 Distribution domain name으로 접속이 되는지 확인합니다.
- 아래 레퍼런스를 참조하여, 스프린트를 진행합니다.

### 4. Route53 레코드 등록
- 백엔드와 프론트엔드의 별칭 레코드를 Route53 호스팅 영역에 생성합니다.
- 백엔드는 [https://api.yourdomain.click](https://api.yourdomain.click)으로 접속 시, 로드밸런서로 연결되어야 하며, 프론트엔드는 [https://www.yourdomain.click](https://www.yourdomain.click)으로 접속 시, Cloudfront로 연결되어야 합니다.

Reference
- [Application Load Balancer용 HTTPS 리스너 생성](https://docs.aws.amazon.com/ko_kr/ko_kr/elasticloadbalancing/latest/application/create-https-listener.html)
- [CloudFront 배포 생성](https://docs.aws.amazon.com/ko_kr/ko_kr/AmazonCloudFront/latest/DeveloperGuide/distribution-web-creating-console.html)
- [CloudFront 배포를 만들거나 업데이트할 때 지정하는 값](https://docs.aws.amazon.com/ko_kr/ko_kr/AmazonCloudFront/latest/DeveloperGuide/distribution-web-values-specify.html)