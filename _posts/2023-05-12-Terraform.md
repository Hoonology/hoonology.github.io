---
date: 2023-05-12 01:00:00
layout: post
title: Terraform
subtitle: Terraform
description: 왜 선언형 IaC 중, Terraform을 선택했는지 알아보자
image: https://res.cloudinary.com/dvqcvocet/image/upload/v1683864943/dev-jeans_kadf7q.png
optimized_image: https://res.cloudinary.com/dvqcvocet/image/upload/v1683864943/dev-jeans_kadf7q.png
category: docker
tags:  
  - terraform
  - 테라폼
  - IaC
author: Hoonology
paginate: true
---

## Terraform
**Infra structure as Code**를 구현하는 도구 ( HashiCorp의 오픈소스 )
- '인프라스트럭쳐 = 코드' 
  - 클라우드 위에서 '서버'는 '언제든' 필요할만큼 사용할 수 있는 가상의 컴퓨팅 리소스  

![ec2](/assets/img/Terraform/ec2Instance.png)
<p align = "center">[사진] AWS EC2의 인스턴스 선언 - Apply - 인스턴스 생성완료 ( 여러번 실행한다고해서 여러 서버가 생기지 않는다. )</p>


- 테라폼은 단순히 리소스를 생성하는 역할만 하지 않는다. (tfstate라는 파일로 관리)
  - 코드에 선언적으로 정의된 리소스들의 상태를 유지시켜준다.
  - 서버가 이미 존재한다면 테라폼은 아무런 작업을 하지 않음
  - 아마존 웹서비스 웹콘솔에서 해당 인스턴스를 종료하게 되면 테라폼을 적용하면 '이제 서버가 없다'고 판단하여 다시 서버를 생성한다.
  - 테라폼은 항상 코드의 상태를 기준으로 클라우드의 실제 상태를 유지하고자 한다.

## Terraform을 이용해서 여러 대의 서버를 만들고 싶으면 ?
- 테라폼에서 여러대의 서버를 정의해준다
  - 리소스를 여러번 선언
  ![terraformServer](/assets/img/Terraform/terraformResource.png)
  - count 속성 사용
  ![terraformServer](/assets/img/Terraform/terraformResource2.png)
  - Auto Scailing Group 과 같은 상위 개념의 리소스(인스턴스 관리해주는 것) 사용
  ![terraformServer](/assets/img/Terraform/terraformResource3.png)
  
## 리소스들 간의 의존 관계 이해
  - 인스턴스를 만들면, 보안 그룹이 필요하다. 
    - 테라폼은 보안 그룹을 먼저 만들고 인스턴스를 생성해준다.
      - 이것이 가능하도록 하는 것 : **참조**를 통해 가능 ! 
      - 테라폼에 정의된 보안그룹을 참조하도록 작성을 하는데, 보안그룹이 생성되기 전이라서 인스턴스 생성 단계에서 보안그룹의 ID를 조회할 수 없게 된다. 이 때, 테라폼은 인스턴스가 보안그룹에 의존성이 있다고 판단하여 **보안그룹을 먼저 만들고 인스턴스를 생성하게 된다**.
  ![terraformServer](/assets/img/Terraform/secu_ins.png)


## 범용성의 대가 
- 메이저 급 클라우드 지원

![clouds](/assets/img/Terraform/aws_google_mi.png)

- 네이버클라우드, DigitalOcean, Alibaba Cloud, openstack, ORACLE cloud, HEROKU 등 클라우드 서비스 지원
- 데이터베이스, 쿠버네티스 클러스터, 프로바이더(모니터링 서비스) 제공 
![db](/assets/img/Terraform/db.png)

