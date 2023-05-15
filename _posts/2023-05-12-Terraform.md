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

# Terraform
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


## Terraform 배포 워크플로우 표준화
- Terraform의 구성 언어 : 선언적 = 원하는 최종의 상태를 설명한다.
- 리소스 간의 종속성을 자동으로 계산하여 올바른 순서로 리소스를 생성하거나 삭제한다.
![img](https://content.hashicorp.com/api/assets?product=tutorials&version=main&asset=public%2Fimg%2Fterraform%2Fterraform-iac.png)


- Terraform으로 인프라를 배포하려면
  - 범위 - 프로젝트의 인프라를 식별합니다.
  - 작성자 - 인프라에 대한 구성을 작성합니다.
  - 초기화 - Terraform이 인프라를 관리하는 데 필요한 플러그인을 설치합니다.
  - 계획 - 구성과 일치하도록 Terraform이 수행할 변경 사항을 미리 봅니다.
  - 적용 - 계획된 변경 사항을 적용합니다.

## 인프라 추적
Terraform은 환경에 대한 정보 소스 역할을 하는 상태 파일에서 실제 인프라를 추적합니다. Terraform은 상태 파일을 사용하여 구성과 일치하도록 인프라에 대한 변경 사항을 결정합니다.

## 협업
Terraform을 사용하면 원격 상태 백엔드로 인프라에서 협업할 수 있습니다. Terraform Cloud(최대 5명의 사용자에게 무료)를 사용하면 팀원과 상태를 안전하게 공유하고 Terraform이 실행할 수 있는 안정적인 환경을 제공하며 여러 사람이 한 번에 구성을 변경할 때 경합 상태를 방지할 수 있습니다.

또한 Terraform Cloud를 GitHub, GitLab 등과 같은 버전 제어 시스템(VCS)에 연결하여 VCS에 대한 구성 변경 사항을 커밋할 때 인프라 변경 사항을 자동으로 제안할 수 있습니다. 이를 통해 애플리케이션 코드와 마찬가지로 버전 제어를 통해 인프라 변경 사항을 관리할 수 있습니다.


# Terraform 설치

최신 버전의 Terraform으로 업데이트하려면 먼저 Homebrew를 업데이트합니다.
```bash
brew update
```

먼저 모든 Homebrew 패키지의 저장소인 HashiCorp 탭을 설치합니다.
```bash
brew tap hashicorp/tap
```
이제 hashicorp/tap/terraform을 설치합니다.
```bash
# 이렇게 하면 서명된 바이너리가 설치되고 새로운 공식 릴리스가 나올 때마다 자동으로 업데이트됩니다.
brew install hashicorp/tap/terraform
```

## M1 아키텍처 이슈
```bash
$ brew install hashicorp/tap/terraform                                  130 ↵

Error: Cannot install in Homebrew on ARM processor in Intel default prefix (/usr/local)!
Please create a new installation in /opt/homebrew using one of the
"Alternative Installs" from:
  https://docs.brew.sh/Installation
You can migrate your previously installed formula list with:
  brew bundle dump
```
## 해결

Homebrew를 설치하기 전에 새 ARM 실리콘(M1 칩)용 `Rosetta2` 에뮬레이터를 설치해야 합니다. 방금 다음을 사용하여 터미널을 통해 `Rosetta2`를 설치했습니다.


이렇게 하면 추가 버튼 클릭 없이 `rosetta2`가 설치됩니다.

위의 Rosetta2를 설치한 후 `Homebrew cmd`를 사용하여 **ARM M1 칩용 Homebrew**를 설치할 수 있습니다. 
```bash
arch -x86_64 /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"
```
M1 ARM용 Homebrew가 설치되면 이 Homebrew 명령을 사용하여 패키지를 설치합니다.arch -x86_64 brew install <package>

## 다시 설치로 돌아와서 

```bash
 arch -x86_64 brew install hashicorp/tap/terraform
 ```

 ## 설치 확인
 ```bash
 terraform -help
 ```


![terraform](/assets/img/Terraform/terraform1.png)

### 탭 완성 활성화
Bash 또는 Zsh를 사용하는 경우 Terraform 명령에 대해 탭 완성을 활성화할 수 있습니다. 자동 완성을 활성화하려면 먼저 선택한 셸에 대한 구성 파일이 있는지 확인하십시오.
```bash
touch ~/.zshrc
terraform -install-autocomplete
```
자동 완성 지원이 설치되면 셸을 다시 시작해야 합니다.


## 빠른 시작
#### 1. 도커 열기
```bash
open -a Docker
```
#### 2. 디렉토리 생성
```bash
mkdir learn-terraform-docker-container
```

#### 3. 작업 디렉터리에서 `main.tf`라는 파일을 만들고 다음 Terraform 구성을 붙여넣습니다.

```bash
terraform {
  required_providers {
    docker = {
      source  = "kreuzwerker/docker"
      version = "~> 3.0.1"
    }
  }
}

provider "docker" {}

resource "docker_image" "nginx" {
  name         = "nginx"
  keep_locally = false
}

resource "docker_container" "nginx" {
  image = docker_image.nginx.image_id
  name  = "tutorial"

  ports {
    internal = 80
    external = 8000
  }
}

```

#### 4. Terraform이 Docker와 상호 작용할 수 있도록 하는 공급자라는 플러그인을 다운로드하는 프로젝트를 초기화합니다.
```bash
terraform init
```
#### 5. .NET으로 NGINX 서버 컨테이너를 프로비저닝합니다.  
Terraform이 apply 유형 확인을 요청하면 yes를 입력후 ENTER.

![terraform](/assets/img/Terraform/terraform2.png)

웹 브라우저에서 localhost:8000을 방문

#### 6. 컨테이너 중지
```bash
terraform destroy
```