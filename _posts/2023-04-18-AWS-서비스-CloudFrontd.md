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
- ```.env```파일에 HTTPS 적용을 위한 환경설정으로 적절하게 작성합니다.
- ```npm run test2``` 명령을 사용해 테스트가 통과하는지 확인합니다.
- 실제로 웹 애플리케이션이 브라우저 상에서 HTTPS 프로토콜로 작동하는지 확인합니다.

### 1. Certificate Manager를 통한 도메인 인증서 발급
- 구매한 도메인을 기준으로 인증서를 발급 받습니다.
  - 도메인 구매 : ```Route 53```에서 구매를해준다.  
    - 도메인 등록 클릭  
    ( ```hoonology.click```으로 등록했다.)
    ![도메인구매1](/assets/img/AWS/%EB%8F%84%EB%A9%94%EC%9D%B81.png)
    ```Route53```에서 도메인 구매 후 도메인 등록 대기 
    ![도메인구매2](/assets/img/AWS/%EB%8F%84%EB%A9%94%EC%9D%B82.png)
    
    > S3의 이름이 ```hoonology.com```으로 되어 있어서, ```hoonology.click```으로 재설정한 뒤 인증 요청을 보냈다.


      S3를 만들 때, 정책 아래와 같이 수정하는거 잊지 않도록 
      ![bucket](/assets/img/AWS/%EB%B2%84%EC%BA%A3%EC%A0%95%EC%B1%85.png)

    - 도메인 등록 성공(시간이 좀 걸린다.)
    ![success](/assets/img/AWS/success.png)

    - Route53 등록 성공 확인 후  
    ```CertificateManager```에 Route53에 등록한 도메인을 등록해준다.
      - https는 빼줘야함
    ![certificateManager](/assets/img/AWS/certificateManager.png)

      - 30분 ~ 1시간 정도 기다린다.
      ![wait](/assets/img/AWS/Waitting.png)

    [로드밸런서 Reference](https://docs.aws.amazon.com/ko_kr/ko_kr/elasticloadbalancing/latest/application/create-https-listener.html)
    - HTTPS 수신기는 클라이언트와 서버 간의 보안 HTTPS 트래픽을 처리하는 데 사용됩니다. AWS Management Console을 사용하여 HTTPS 리스너를 생성하려면 사용자의 AWS 계정에 이미 생성된 애플리케이션 로드 밸런서가 있어야 합니다.


사용자에게 애플리케이션 로드 밸런서가 있으면 콘솔에서 "리스너" 탭으로 이동하여 "만들기"를 클릭할 수 있습니다. 그런 다음 HTTPS 프로토콜을 선택하고 SSL 인증서를 제공하라는 메시지가 표시됩니다.


프로토콜 및 인증서를 선택한 후 사용자는 포트 및 대상 그룹과 같은 수신기 설정을 구성할 수 있습니다. 설정이 구성되면 HTTPS 리스너가 생성되고 사용자 애플리케이션의 보안 트래픽을 처리할 준비가 됩니다.






  - 인증서는 ```프론트엔드 Cloudfront 사용 리전```인 ```us-east-1```과 ```백엔드 Load Balancer 사용리전```인 ```ap-northeast-2```에서 발급 받아야 합니다.
  - 발급 시, DNS 검증 가이드로 [레퍼런스](https://docs.aws.amazon.com/ko_kr/acm/latest/userguide/dns-validation.html)를 참고하세요.
  > 도메인 이름 시스템(DNS)은 네트워크에 연결되는 리소스를 위한 디렉터리 서비스입니다. DNS 공급자는 도메인을 정의하는 레코드가 포함된 데이터베이스를 유지 관리합니다. DNS 검증을 선택하면 ACM은 이 데이터베이스에 추가해야 하는 하나 이상의 CNAME 레코드를 제공합니다. 이 레코드에는 사용자가 도메인을 통제함을 증명하는 역할을 하는 고유한 키-값 페어가 포함되어 있습니다.

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



</br>
</br>
</br>

# 회고
## EC2, S3 
Route53에서 도메인 주소 등록해줘야하는데, 내가 S3의 이름을 '.com'으로 등록했던것을 발견한 뒤 수정을 '.click'으로 하면서 정리해야겠다고 생각해서 정리한다.
> S3는 정적 웹사이트를 위해 빌드를 버켓에 담기 위해 쓴다.  
