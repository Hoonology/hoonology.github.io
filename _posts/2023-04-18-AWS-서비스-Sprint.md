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

## 0. 보안그룹(Security Group)
#### 1. 보안그룹은 AWS에서 임대한 인스턴스의 **가상 방화벽**이다.  
#### 2. 인바운드( 인스턴스로 들어가는 트래픽 )와 아웃바운드( 인스턴스에서 나가는 트래픽 )에 대한 규칙을 설정할 수 있다.

![보안그룹](/assets/img/AWS/SecurityGroup.png)
<p align = "center"> [사진] 인바운드 규칙</p>

![outbound](/assets/img/AWS/outbound.png)
<p align = "center"> [사진] 아웃바운드 규칙</p>

### 보안그룹 설정
인스턴스 탭의 우측에서 해당 인스턴스가 어떤 보안그룹에 속해있는지 확인할 수 있다.
![Which](/assets/img/AWS/WhichGroup.png)


#### 인바운드 규칙 설정
![inbound](/assets/img/AWS/InboundRule.png)
![inbound2](/assets/img/AWS/InboundRule2.png)




















## 1. Certificate Manager를 통한 도메인 인증서 발급
- 구매한 도메인을 기준으로 인증서를 발급 받습니다.
  - 도메인 구매 : ```Route 53```에서 구매를해준다.  
    - 도메인 등록 클릭  
    ( ```hoonology.click```으로 등록했다.)


![도메인구매1](/assets/img/AWS/%EB%8F%84%EB%A9%94%EC%9D%B81.png)
    ```Route53```에서 도메인 구매 후 도메인 등록 대기 
![도메인구매2](/assets/img/AWS/%EB%8F%84%EB%A9%94%EC%9D%B82.png)
    
  > S3의 이름이 ```hoonology.com```으로 되어 있어서, ```hoonology.click```으로 재설정한 뒤 인증 요청을 보냈다.


  - S3를 만들 때, 정책 아래와 같이 수정하는거 잊지 않도록 

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






## 2. Route53 레코드 등록
- 백엔드와 프론트엔드의 별칭 레코드를 Route53 호스팅 영역에 생성합니다.
- 백엔드는 [https://api.yourdomain.click](https://api.yourdomain.click)으로 접속 시, 로드밸런서로 연결되어야 하며, 프론트엔드는 [https://www.yourdomain.click](https://www.yourdomain.click)으로 접속 시, ```Cloudfront```로 연결되어야 합니다.

- 인증서는 ```프론트엔드 Cloudfront 사용 리전```인 ```us-east-1```과   
```백엔드 Load Balancer 사용리전```인 ```ap-northeast-2```에서 발급 받아야 합니다.
![리전별](/assets/img/AWS/%EB%A6%AC%EC%A0%84%EB%B3%84.png)  
  <p align = "center">[사진] 리전 두개를 선택한 뒤 인증서 요청을 해야한다. </p>

![리전별2](/assets/img/AWS/%EB%A6%AC%EC%A0%84%EB%B3%842.png)
  <p align = "center">[사진] 정규화된 도메인 이름에 와일드카드 붙이기 </p>

![리전별3](/assets/img/AWS/%EB%A6%AC%EC%A0%84%EB%B3%843.png)
  <p align = "center">[사진] 리전 버지니아 북부에서 인증서 발급 완료(프론트엔드 Cloudfront 사용) </p>

![리전별4](/assets/img/AWS/%EB%A6%AC%EC%A0%84%EB%B3%844.png)

  <p align = "center">[사진] 리전 서울에서 인증서 발급 완료(Load Balancer 사용) </p>
  
  - 발급 시, DNS 검증 가이드로 [레퍼런스](https://docs.aws.amazon.com/ko_kr/acm/latest/userguide/dns-validation.html)를 참고하세요.
    > 도메인 이름 시스템(DNS)은 네트워크에 연결되는 리소스를 위한 디렉터리 서비스입니다. DNS 공급자는 도메인을 정의하는 레코드가 포함된 데이터베이스를 유지 관리합니다. DNS 검증을 선택하면 ACM은 이 데이터베이스에 추가해야 하는 하나 이상의 CNAME 레코드를 제공합니다. 이 레코드에는 사용자가 도메인을 통제함을 증명하는 역할을 하는 고유한 키-값 페어가 포함되어 있습니다.

- 이후에 DNS 공급자로 Route53을 이용합니다. Route53에 레코드 생성과정을 반드시 거쳐야합니다.
- 인증까지 최소 30분의 시간이 소요될 수 있습니다.




## 3. 백엔드 HTTPS 적용
- 애플리케이션 로드밸런서(Application Load Balancer)를 생성합니다.

![로드밸런서](/assets/img/aws/%EB%A1%9C%EB%93%9C%EB%B0%B8%EB%9F%B0%EC%84%9C.png)

<p align = "center">[사진] Create load balancer 클릭</p>

- 우리는 HTTP와 HTTPS를 다룰 것이기 때문에, 첫 번째를 누른다.
![create](/assets/img/AWS/ApplicationLoadBalancer.png)
<p align = "center">[사진] 로드밸런서 타입 - Application Load Balancer 선택</p>

- 로드밸런서 만들기
  - 로드밸런서 이름
  - Internet-facing
![create](/assets/img/AWS/%08Create.png)
<p align = "center">[사진] Create Application Load Balancer</p>

- Network mapping
  - VPC : default
![mapping](/assets/img/AWS/%08mapping.png)
<p align = "center">[사진] Create Application Load Balancer</p>

- 보안그룹과 Listeners and routing
  - 로드밸런서는 하나 이상의 Listener가 있어야한다.
  - 로드밸런서가 받아서 분산을 시키든 무언가를 할텐데, 받는 친구도 있어야한다. 그게 리스너다.
  - **'나에게 80으로 들어오는 것을 타겟그룹으로 보내라'** 라는 로직하에, 타겟 그룹이 필요하다.
![보안그룹](/assets/img/AWS/security%20groups.png)
<p align = "center">[사진] Security groups & Listeners and routing</p>

- 난 타겟 그룹 설정이 안돼있어서 새로 만들어줬다.
  - Instances 를 선택한 이유 : 타겟으로 하고 싶은 것이 EC2 인스턴스 안에 돌아가는 서버이기 때문
![타겟그룹2](/assets/img/AWS/%ED%83%80%EA%B2%9F%EA%B7%B8%EB%A3%B92.png)
<p align = "center">[사진] Create taget group</p>

- 타겟그룹을 만든 뒤 토글을 눌러 선택 
![타겟그룹](/assets/img/aws/%ED%83%80%EC%BC%93%EA%B7%B8%EB%A3%B9.png)
<p align = "center">[사진] Listeners and routing </p>

- 헬스체크

![헬스체크](/assets/img/aws/%ED%97%AC%EC%8A%A4%EC%B2%B4%ED%81%AC.png)

<p align = "center">[사진] Edit health check settings </p>

- sample 로 지정된 repository에서는 200번이 아닌, 201번으로 설정해줘서 바꿔야한다.
  - Advanced health check settings 토글 클릭
![토글](/assets/img/AWS/advanced.png)
<p align = "center">[사진] Advanced health check settings 토글 클릭 후 나오는 화면 </p>

  - success code : 201로 수정(샘플 참조)
  ![advanced](/assets/img/AWS/advanced_201.png)
  <p align = "center">[사진] success code를 201로 수정 </p>

- 로드밸런서 상태 확인
  - 사진에는 hoonology라고 나오는데, 나중에 hoonology-rb로 변경했다.
![load](/assets/img/AWS/%EB%A1%9C%EB%93%9C%EB%B0%B8%EB%9F%B0%EC%84%9C%EC%8A%A4.png)

- Unhealthy 해결하기
  - EC2 재 연결 -> 새로운 IP가 부여 -> ssh 연결 시 해당 IP를 입력
![EC2재연결](/assets/img/AWS/%EC%83%88%EB%A1%9C%EC%9A%B4IP.png)  
<p align = "center">[사진] 새로 부여 받은 IP 주소 - 퍼블릭 IPv4주소 </p>

![ssh연결](/assets/img/AWS/ssh%EC%97%B0%EA%B2%B0.png)
<p align = "center">[사진] 새로 부여 받은 IP주소로 할당 </p>

![ssh연결완료](/assets/img/AWS/%EC%97%B0%EA%B2%B0%EC%99%84%EB%A3%8C.png)
<p align = "center">[사진] EC2 연결완료 </p>

- sample 디렉토리에 이동 후 아래 코드 실행

```bash
sudo npm run start
```
- 서버 구동 확인
![sudo](/assets/img/AWS/sudo.png)
<p align = "center">[사진] 서버 구동 확인 </p>

- 인터넷을 통해 연결해본다.

![연결확인](/assets/img/AWS/%EB%93%A4%EC%96%B4%EA%B0%80%EB%B3%B4%EA%B8%B0.png)
<p align = "center">[사진] IP주소로 접속 시 Hello World 문구 확인 가능 </p>

- healthy or unhealthy ?
![healthy](/assets/img/AWS/healthy.png)

> 서버가 죽어있으면 unhealthy로 나온다. 서버를 종료하지 않고 80번 포트에서 실행이 되도록 한다.



- ALB의 리스너, 가용영역, 인증서를 설정합니다.
- 대상 그룹(target group)을 등록합니다.
- 로드밸런서 DNS 주소로 접속해, 테스트를 진행합니다.
- 아래 레퍼런스를 참조하여, 스프린트를 진행합니다.

### [인스턴스] / [시작템플릿]
![startT](/assets/img/AWS/startTemplate.png)
EC2 만드는 것 처럼 하면 된다.
- 키페어는 무엇이며 .. 등등

### 3. Auto Scaling
![auto](/assets/img/AWS/auto.png)
![auto2](/assets/img/AWS/auto2.png)
가용 영역은 여러개 분산 시키는 것이 좋다. (4개 정도)  
한 곳에 몰려있는 것은 비추천
![auto3](/assets/img/AWS/auto3.png)
로드밸런서와 오토스켈링은 따로 쓸 수 있지만, 같이 쓰면 효과가 강력하기에 같이 쓰도록 한다.
![auto4](/assets/img/AWS/auto4.png)
최대 : 사용자가 늘어난다고 정의했지만 무한히 늘어나면 안되니깐, 최대 어디 까지 늘것인지 설정  
최소 : 트래픽이 줄어들 때 몇까지 줄어들 것인지 (최소한 몇개 까진 돌아가야한다는 것을 설정)
![auto5](/assets/img/AWS/auto5.png)
대상 추적 크기 조정 정책 : 언제 스케일링을 통해서 변할(늘고 줄) 것인지 설정하는 것
![auto6](/assets/img/AWS/auto6.png)




### 4. 프론트엔드 CDN 및 HTTPS 적용 - CloudFront
캐싱을 해가지고 클라이언트에게 배포를 전달해주는 것 !

![cloudFront](/assets/img/AWS/cloudFront2.png)

- Origin Domain을 설정해야 합니다.
  - CloudFront 배포 생성
![cloudfront](/assets/img/AWS/cloudfront.png)
  - 원본 생성
![cloudfront](/assets/img/AWS/origin.png)

- Redirect HTTP to HTTPS
![cloudfront](/assets/img/AWS/origin_cache.png)

- Certificate Manager에서 발급받은 인증서를 사용해야 합니다.

- Default root object 부분에 index.html을 작성해야 합니다.
- 대체도메인과 인증 받은 도메인의 이름이 같아야 합니다.
- 생성된 배포의 Distribution domain name으로 접속이 되는지 확인합니다.
- 아래 레퍼런스를 참조하여, 스프린트를 진행합니다.


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

와일드카드로 도메인을 인증 -> 대체도메인도 마찬가지

- HTTPS로 보안이 된 것을 통신하고 싶다. 
  - 이것이 로직의 핵심

- '클라 - 서버' : EC2로 만드는 것(VPC 안에서)
