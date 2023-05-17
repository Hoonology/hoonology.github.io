---
date: 2023-05-15 00:00:00
layout: post
title: (Sprint) Full Stack 애플리케이션 구성
subtitle: Terraform
description: Terraform으로 AWS 서비스 구축하기 - EC2 RDS S3 구축 (1)
image: https://res.cloudinary.com/dvqcvocet/image/upload/v1683864943/dev-jeans_kadf7q.png
optimized_image: https://res.cloudinary.com/dvqcvocet/image/upload/v1683864943/dev-jeans_kadf7q.png
category: terraform
tags:  
  - terraform
  - 테라폼
  - IaC
author: Hoonology
paginate: true
---
# 요구사항
다음의 아키텍처를 terraform을 이용해 작성합니다.
![Alt text](https://s3.ap-northeast-2.amazonaws.com/urclass-images/W6iTHPZQ1M80m-R0dsqZS-1642861410514.png)

# Getting Started
IaC 코드를 작성하려면 먼저 AWS Management Console을 이용해 먼저 최종 인프라 상태를 만들어놓고, 잘 작동하는지 확인한 다음, 이를 해당하는 리소스를 하나씩 코드로 옮기는 방법을 사용하는 방식을 사용해 보면 좋습니다.

다음 자습서를 순서대로 따라 합니다. 먼저 AWS Management Console을 통해 최종 결과물을 따라 해보고, 예상 상태가 무엇인지 먼저 파악해야 합니다.

모든 리소스를 만들 때에는 반드시 이름을 붙여놓도록 합시다.

## STEP 1: 자습서: DB 인스턴스에 사용할 Amazon VPC 생성
[링크](https://docs.aws.amazon.com/ko_kr/AmazonRDS/latest/UserGuide/CHAP_Tutorials.WebServerDB.CreateVPC.html)

![Alt text](https://docs.aws.amazon.com/ko_kr/AmazonRDS/latest/UserGuide/images/con-VPC-sec-grp.png)
#### 1. VPC 및 서브넷 생성
- 프라이빗 서브넷과 퍼블릭 서브넷이 각각 두 개, 총 네 개가 있어야 합니다.

#### 프라이빗 서브넷과 퍼블릭 서브넷을 포함하는 VPC 생성
다음은 퍼블릭 서브넷과 프라이빗 서브넷을 모두 포함하는 VPC를 생성하는 절차입니다.

- VPC 및 서브넷을 생성하는 방법
  - Amazon VPC 콘솔을 엽니다
  - AWS Management Console의 오른쪽 상단에서 VPC를 생성할 리전을 선택합니다. 이 예에서는 미국 서부(오레곤) 리전을 사용합니다.
  - 왼쪽 상단 모서리에서 VPC 대시보드를 선택합니다. VPC 생성을 시작하려면 VPC 생성을 선택합니다.
  - VPC 설정의 생성할 리소스에서 VPC 등을 선택합니다.
  - VPC 설정을 위해 다음 값을 설정합니다.
    - 네임 태그 자동 생성 - tutorial
    - IPv4 CIDR block: - 10.0.0.0/16
      - 이 블록은 10.0.0.0에서 10.0.255.255까지의 IP 주소 범위를 제공한다.
    - IPv6 CIDR 블록 - No IPv6 CIDR Block(IPv6 CIDR 블록 없음)
    - 테넌시 - 기본값
  - 서브넷
    - 프라이빗 서브넷을 만들고 싶기 때문에 각 프라이빗 서브넷에 대해 /24 CIDR 블록을 사용하는 것이 일반적입니다.
  
  ![subnet](/assets/img/Terraform/subnet1.png)
    - 가용 영역(AZ)의 수 - 2
    - Customize AZs(AZ 사용자 지정) - 기본값을 유지합니다.
    - 퍼블릭 서브넷 수 – 2
    - 프라이빗 서브넷 수 – 2
    - Customize subnets CIDR blocks(서브넷 CIDR 블록 사용자 지정) - 기본값을 유지합니다.
  ![subnet](/assets/img/Terraform/subnet3.png)
  ![subnet](/assets/img/Terraform/subnet4.png)
  ![subnet](/assets/img/Terraform/subnet5.png)
  ![subnet](/assets/img/Terraform/subnet6.png)
  ![subnet](/assets/img/Terraform/subnet7.png)


    - NAT 게이트웨이($) – 없음
      - NAT 게이트웨이는 퍼블릭 서브넷이 아닌 프라이빗 서브넷의 리소스에 대한 인터넷 액세스를 제공하는 데 사용됩니다. 반면에 인터넷 게이트웨이는 퍼블릭 서브넷의 리소스가 인터넷에 직접 액세스할 수 있도록 합니다.
    - VPC 엔드포인트 – 없음
    - DNS options(DNS 옵션) - 기본값을 유지합니다.



#### 1-2. 위 과정이 잘 안된다면 테라폼으로 작성해본다.

`provider.tf` 파일 생성
```bash
terraform {
  required_version = ">= 0.14.8" 

  required_providers {
      aws = {
          source = "hashicorp/aws"
          version = "~> 3.0"
      }
  }
}

provider "aws" {
  region     = "ap-northeast-2"
  # access_key 와 secert_key 를 파일에 적어서 구현할 수는 있지만 권장하지 않습니다.
  # access_key = "my-access-key"
  # secret_key = "my-secret-key"
}
```


초기화
```bash
terraform init -upgrade
```
`plan`은 실제로 리소스를 작성하기 전 리소스가 어떻게 생성되는지를 확인하는 명령어입니다.

```bash
# vpc.tf
resource "aws_vpc" "my-vpc-0515" {
  cidr_block = "10.0.0.0/16"

# 네임태그
  tags = {
    Name = "my-vpc-0515"
  }
}
```

```bash
terraform plan
```
![plan](/assets/img/Terraform/terraformPlan.png)


CloudFormation의 create-change-set , execute-change-set 조합과 같습니다.

단순하게 plan 명령어만 입력하면 되기 때문에 CloudFormation을 사용하는 것보다 훨씬 빠르고 편하게 결과를 예측할 수 있습니다.

![apply](/assets/img/Terraform/terraformApply.png)

이제 AWS 콘솔로 가서 VPC에서 네임태그와 함께 올라가있는지 확인해줍니다.
![vpcCheck](/assets/img/Terraform/vpcCheck.png)

#### 1-3. 서브넷 작성
EC2에 사용할 2개의 퍼블릭 서브넷과 RDS에 사용할 2개의 프라이빗 서브넷을 작성

(파일은 사용자가 나누고 싶은대로 나눕니다.)

```bash
resource "aws_subnet" "PublicSubnet01" {
  vpc_id = aws_vpc.my-vpc-0515.id
  cidr_block = "10.0.1.0/24"
  availability_zone = "ap-northeast-2a"
  tags = {
    Name = "public-subnet01"
  }
}

resource "aws_subnet" "PublicSubnet02" {
  vpc_id = aws_vpc.my-vpc-0515.id
  cidr_block = "10.0.2.0/24"
  availability_zone = "ap-northeast-2c" # b는 t2 micro 적용이 안된다.
  tags = {
    Name = "public-subnet02"
  }
}
resource "aws_subnet" "PrivateSubnet01" {
  vpc_id = aws_vpc.my-vpc-0515.id
  cidr_block = "10.0.3.0/24"
  availability_zone = "ap-northeast-2a"
  tags = {
    Name = "private-subnet01"
  }
}
resource "aws_subnet" "PrivateSubnet02" {
  vpc_id = aws_vpc.my-vpc-0515.id
  cidr_block = "10.0.4.0/24"
  availability_zone = "ap-northeast-2c"
  tags = {
    Name = "private-subnet02"
  }
}
```




#### 1-4. 인터넷게이트웨이 (IGW) 작성 
인터넷 게이트웨이는 VPC에 연결하여 VPC가 인터넷과 통신할 수 있도록 해줍니다.

```bash
resource "aws_internet_gateway" "my-IGW-0515" {
  vpc_id = aws_vpc.my-vpc-0515.id
  tags = {
    Name = "my-IGW-0515"
  }
}
```

#### 1-5. 라우팅테이블 작성
VPC를 생성하면 기본 라우팅 테이블이 함께 만들어집니다. 하지만 VPC를 보호하기 위해 기본 라우팅 테이블은 초기 설정을 그대로 두고 **사용하지 않는 것을 권장**합니다.

라우팅 테이블을 작성하고 이를 서브넷과 연결해줍니다.
```bash
resource "aws_route_table" "my-public-route01" {
  vpc_id = aws_vpc.my-vpc-0515.id
  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.my-IGW-0515.id
  }
  tags = {
    Name = "my-public-route01"
  }
}
resource "aws_route_table" "my-public-route02" {
  vpc_id = aws_vpc.my-vpc-0515.id
  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.my-IGW-0515.id
  }
  tags = {
    Name = "my-public-route02"
  }
}

## 2. 프라이빗 라우팅 테이블 정의
resource "aws_route_table" "my-private-route01" {
  vpc_id = aws_vpc.my-vpc-0515.id

  tags = {
    Name = "my-private-route01"
  }
}
resource "aws_route_table" "my-private-route02" {
  vpc_id = aws_vpc.my-vpc-0515.id

  tags = {
    Name = "my-private-route02"
  }
}

## 퍼블릭 라우팅 테이블 연결
resource "aws_route_table_association" "my-public-RT-Assoication01" {
  subnet_id = aws_subnet.PublicSubnet01.id
  route_table_id = aws_route_table.my-public-route01.id
}
resource "aws_route_table_association" "my-public-RT-Assoication02" {
  subnet_id = aws_subnet.PublicSubnet02.id
  route_table_id = aws_route_table.my-public-route02.id
}

## 프라이빗 라우팅 테이블 연결
resource "aws_route_table_association" "my-private-RT-Assoication01" {
  subnet_id = aws_subnet.PrivateSubnet01.id
  route_table_id = aws_route_table.my-private-route01.id
}
resource "aws_route_table_association" "my-private-RT-Assoication02" {
  subnet_id = aws_subnet.PrivateSubnet02.id
  route_table_id = aws_route_table.my-private-route02.id
}
```



#### 2. VPC 보안 그룹 생성
  - 퍼블릭 웹 서버가 사용할 VPC 보안 그룹을 만들어야 합니다.
  - 프라이빗 DB 웹 서버가 사용할 VPC 보안 그룹을 만들어야 합니다.  

```bash
## 퍼블릭 보안 그룹
resource "aws_security_group" "my-public-SG" {
  vpc_id = aws_vpc.my-vpc-0515.id
  name = "my public SG"
  description = "my public SG"
  tags = {
    Name = "log public SG"
  }
}

## 프라이빗 보안 그룹
resource "aws_security_group" "my-private-SG" {
  vpc_id = aws_vpc.my-vpc-0515.id
  name = "my private SG"
  description = "private SG"
  tags = {
    Name = "log private SG"
  }
}

## 퍼블릭 보안 그룹 규칙
resource "aws_security_group_rule" "my-public-SG-Rules-HTTPingress" {
  type = "ingress"
  from_port = 80
  to_port = 80
  protocol = "TCP"
  cidr_blocks = [ "0.0.0.0/0" ]
  security_group_id = aws_security_group.my-public-SG.id
  lifecycle {
    create_before_destroy = true
  }
}
resource "aws_security_group_rule" "my-public-SG-Rules-HTTPegress" {
  type = "egress"
  from_port = 80
  to_port = 80
  protocol = "TCP"
  cidr_blocks = [ "0.0.0.0/0" ]
  security_group_id = aws_security_group.my-public-SG.id
  lifecycle {
    create_before_destroy = true
  }
}
resource "aws_security_group_rule" "my-public-SG-Rules-HTTPSingress" {
  type = "ingress"
  from_port = 443
  to_port = 443
  protocol = "TCP"
  cidr_blocks = [ "0.0.0.0/0" ]
  security_group_id = aws_security_group.my-public-SG.id
  lifecycle {
    create_before_destroy = true
  }
}
resource "aws_security_group_rule" "my-public-SG-Rules-HTTPSegress" {
  type = "egress"
  from_port = 443
  to_port = 443
  protocol = "TCP"
  cidr_blocks = [ "0.0.0.0/0" ]
  security_group_id = aws_security_group.my-public-SG.id
  lifecycle {
    create_before_destroy = true
  }
}
resource "aws_security_group_rule" "my-public-SG-Rules-SSHingress" {
  type = "ingress"
  from_port = 22
  to_port = 22
  protocol = "TCP"
  cidr_blocks = [ "---.---.---.-/32" ] # your IP
  security_group_id = aws_security_group.my-public-SG.id
  lifecycle {
    create_before_destroy = true
  }
}
resource "aws_security_group_rule" "my-public-SG-Rules-SSHegress" {
  type = "egress"
  from_port = 22
  to_port = 22
  protocol = "TCP"
  cidr_blocks = [ "---.---.---.-/32" ] # your IP
  security_group_id = aws_security_group.my-public-SG.id
  lifecycle {
    create_before_destroy = true
  }
}

### 프라이빗 보안 그룹 규칙 ( RDS )
resource "aws_security_group_rule" "my-private-SG-Rules-RDSingress" {
  type = "ingress"
  from_port = 3306
  to_port = 3306
  protocol = "TCP"
  security_group_id = aws_security_group.my-private-SG.id
  source_security_group_id = aws_security_group.my-public-SG.id
  lifecycle {
    create_before_destroy = true
  }
}
resource "aws_security_group_rule" "my-private-SG-Rules-RDSegress" {
  type = "egress"
  from_port = 3306
  to_port = 3306
  protocol = "TCP"
  security_group_id = aws_security_group.my-private-SG.id
  source_security_group_id = aws_security_group.my-public-SG.id
  lifecycle {
    create_before_destroy = true
  }
}

```

![aws](/assets/img/Terraform/awsconsole1.png)
![aws](/assets/img/Terraform/awsconsole2.png)
![aws](/assets/img/Terraform/awsconsole3.png)


#### 3. DB 서브넷 그룹 생성
  - RDS 인스턴스가 사용할 VPC 서브넷 그룹을 만들어야 합니다.

## STEP 2: EC2 인스턴스 생성
만들어야 하는 사양은 다음과 같습니다.
- 키 페어: 수동으로 만들고 EC2에 할당합니다.
- AMI: Ubuntu Server 18
- 인스턴스 타입: t2.micro
- 사용자 데이터
```bash
#!/bin/bash
echo "Hello, World" > index.html
nohup busybox httpd -f -p ${var.server_port} &
```



#### 1. 키페어 생성 

- 키 페어 할당
```bash
ssh-keygen -t rsa -b 4069 -C {자신의 메일} -f "./testPubkey" -N ""
```
디렉토리에 `testPubkey` 라고 명명한 키 페어 파일이 생성된다. 

#### 2. EC2 생성

 `ec2.tf` 작성 
  ```bash
  resource "aws_key_pair" "ec2_key" {
    key_name = "terraTest"
    public_key = file("./testPubkey.pub")
  }
  ```
퍼블릭키 지정 2가지 방법
- file로 지정
- 공개키 ssh-rsa 값 

#### 3. 인스턴스 생성

[https://cloud-images.ubuntu.com/locator/ec2/](https://cloud-images.ubuntu.com/locator/ec2/)  
내 리전에 맞는 EC2 ami id를 찾아서 값을 대입한다.

```bash
resource "aws_instance" "test_ec2" {
  ami = "ami-0da3b51b79ece2d3d"
  instance_type = "t2.micro"
  vpc_security_group_ids = [ 
    aws_security_group.my-public-SG.id,
   ]
  subnet_id = aws_subnet.PublicSubnet01.id
  key_name = aws_key_pair.ec2_key.key_name
  root_block_device {
    volume_size = 200
    volume_type = "gp3"
  }
}
```
위와 같이 작성 후 plan을 실행해본다.



![plan](/assets/img/Terraform/plan.png)



사용자 데이터를 넣어서 ec2.tf 파일을 완성시킨다.

```bash
variable "server_port" {
  description = "The port number for the HTTP server"
  type        = number
  default     = 8080
}

resource "aws_key_pair" "ec2_key" {
  key_name   = "terraTest"
  public_key = file("./testPubkey.pub")
}

resource "aws_instance" "test_ec2" {
  ami                    = "ami-0da3b51b79ece2d3d"
  instance_type          = "t2.micro"
  vpc_security_group_ids = [aws_security_group.my-public-SG.id]
  subnet_id              = aws_subnet.PublicSubnet01.id
  key_name               = aws_key_pair.ec2_key.key_name

  root_block_device {
    volume_size = 200
    volume_type = "gp3"
  }

  user_data = <<-EOF
    #!/bin/bash
    echo "Hello, World" > index.html
    nohup busybox httpd -f -p ${var.server_port} &
  EOF
}
```


이제 `terraform apply`를 실행해본다.

### 오류발생
```bash
│ Error: Error launching source instance: InvalidParameterValue: The architecture 
|'i386,x86_64' of the specified instance type does not match the architecture 'arm64' of 
|the specified AMI. Specify an instance type and an AMI that have matching architectures,
| and try again. You can use 'describe-instance-types' or 'describe-images' to discover
| the architecture of the instance type or AMI.
│       status code: 400, request id: 48898fa9-1f54-4ab4-b7b8-81b3063cfb7e
│ 
│   with aws_instance.test_ec2,
│   on ec2.tf line 12, in resource "aws_instance" "test_ec2":
│   12: resource "aws_instance" "test_ec2" {
```
### 오류 해결
- 오류 내용 : 오류 메시지는 지정된 인스턴스 유형의 아키텍처와 사용하려는 AMI의 아키텍처 간에 불일치가 있음을 나타냅니다. 인스턴스 유형은 'i386' 또는 'x86_64'의 아키텍처를 예상하지만 제공한 AMI의 아키텍처는 'arm64'입니다.

> https://cloud-images.ubuntu.com/locator/ec2/ 사이트에서 내가 찾은 ami id가 arm64 기반으로 구성된 ubuntu18.04LTS라서 발생한 문제 같다.

Ami ID를 "ami-0cb1d752d27600adb"로 바꿔줬더니 Apply가 성공했다.
![deploy](/assets/img/Terraform/deploy.png)















# Reference
https://dev.classmethod.jp/articles/build-multiple-services-with-terraform-04/


## STEP 3: RDS 생성
RDS에 서브넷을 지정하기 위해선 1개의 서브넷만 필요하더라도 서브넷 그룹으로 지정해야합니다.  
서브넷 그룹을 작성한 후 aws_db_instance리소스를 이용하여 RDS를 생성합니다.

```bash
resource "aws_db_instance" "my-rds" {
  allocated_storage     = 20
  engine                = "mysql"
  engine_version        = "8.0.23"
  instance_class        = "db.t3.micro"
  name                  = "my-db-instance"
  username              = "Iam_user"
  password              = "12345678"

# Storage ( 스토리지 섹션 기본 값으로 설정 )
  # No changes, keeping the default values

  # Connections
  # No changes, keeping the default values

  # Compute resource
  # No changes, as the connection is established using depends_on

  depends_on = [aws_instance.my-ec2]
}
```

`depends_on = [aws_instance.my-ec2]`를 추가하면 RDS 인스턴스 생성이 EC2 인스턴스 생성에 따라 달라져 EC2 인스턴스가 RDS 인스턴스보다 먼저 생성되도록 합니다.

### 오류 발생
```bash
 with aws_db_instance.my-rds,
   on rds.tf line 1, in resource "aws_db_instance" "my-rds":
    1: resource "aws_db_instance" "my-rds" {
 
```
### 오류 해결
```bash
resource "aws_db_instance" "my-rds" {
  allocated_storage     = 20
  engine                = "mysql"
  engine_version        = "8.0.32"
  instance_class        = "db.t3.micro"
  username              = "Iam_user"
  password              = "12345678"

# Storage ( 스토리지 섹션 기본 값으로 설정 )
  # No changes, keeping the default values

  # Connections
  # No changes, keeping the default values

  # Compute resource
  # No changes, as the connection is established using depends_on

  depends_on = [aws_instance.my-ec2]
    # Specify the subnet IDs for the RDS instance
  db_subnet_group_name = aws_db_subnet_group.default.name
}

resource "aws_db_subnet_group" "default" {
  name        = "default-subnet-group"
  description = "Default subnet group for RDS"
  subnet_ids  = [
    aws_subnet.PrivateSubnet01.id,
    aws_subnet.PrivateSubnet02.id,
  ]
  tags = {
    Name = "DefaultSubnetGroup"
  }
}
```

어떻게 해결했는가
```bash
resource "aws_db_instance" "my-rds" {
  allocated_storage     = 20
  engine                = "mysql"
  engine_version        = "8.0.23"
  instance_class        = "db.t3.micro"
  name                  = "my-db-instance" # 이것을 제거했다.
  username              = "Iam_user"
  password              = "12345678"
```

![rds](/assets/img/Terraform/rdsSuccess.png)

## STEP 4: 애플리케이션 로드 밸런서 및 Auto Scaling Group 적용
Auto Scaling Group은 최소 2개, 최대 10개로 설정해 놓습니다.

![Alt text](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FvcF58%2FbtrPQylUByK%2FDnXLbuvUucsF7Juyp2KraK%2Fimg.png)

작성된 주요 리소스는 다음과 같습니다.


auto scaling이 관리하는 EC2 Instance를 alb가 자동으로 인식하여 트래픽을 부하분산합니다.

![img](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcejJkj%2FbtrPQNpzO9a%2FQoy2f6N8vCkt2BtQHKq470%2Fimg.png)

ALB는 외부 요청을 받는 Listener와 Target group으로 구성됩니다. 그리고 Auto Scaling group이 Target group으로 연결하면 구성이 끝납니다.

![img](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fuj1XT%2FbtrPPwJvbTy%2FBCFRkpekd5pWB83C1X8g71%2Fimg.png)

Auto scaling group에서 Targer group연결할때는 Load balancing과 Health checks필드를 설정



AWS 콘솔 만들기를 통해 1:1로 비교해보고 코드를 작성해봅니다.

```bash
resource "aws_alb" "myALB" {
  name = "my-alb"
  internal = false
  load_balancer_type = "application"
  security_groups = [aws_security_group.my-public-SG.id]
  subnets = [aws_subnet.PublicSubnet01.id, aws_subnet.PublicSubnet02.id]
  enable_cross_zone_load_balancing = true
}
```
![lb](/assets/img/Terraform/LB.png)
![lb2](/assets/img/Terraform/LB2.png)
![lb3](/assets/img/Terraform/LB3.png)
(사진으론 프라이빗이라고 돼있지만, 퍼블릭으로 선택해준다. )


![sg1](/assets/img/Terraform/sg1.png)


```bash
# alb.tf
resource "aws_alb" "myALB" {
  name = "my-alb"
  internal = false
  load_balancer_type = "application"
  security_groups = [aws_security_group.my-public-SG.id]
  subnets = [aws_subnet.PublicSubnet01.id, aws_subnet.PublicSubnet02.id]
  enable_cross_zone_load_balancing = true
}

resource "aws_autoscaling_group" "my-ASG" {
  name                 = "my-auto-scaling-group"
  launch_configuration = aws_launch_configuration.my-lc.name
  min_size             = 2
  max_size             = 10
  desired_capacity     = 2
  target_group_arns    = [aws_lb_target_group.my-target-group.arn]
  vpc_zone_identifier  = [aws_subnet.PublicSubnet01.id, aws_subnet.PublicSubnet02.id]
}

resource "aws_lb_target_group" "my-target-group" {
  name     = "my-target-group"
  port     = 80
  protocol = "HTTP"
  vpc_id   = aws_vpc.my-vpc-0515.id
}

resource "aws_launch_configuration" "my-lc" {
  name                 = "my-launch-configuration"
  image_id             = "ami-0cb1d752d27600adb"
  instance_type        = "t2.micro"
  security_groups      = [aws_security_group.my-public-SG.id]
  key_name             = aws_key_pair.ec2-key.key_name
  associate_public_ip_address = true
  user_data = <<-EOF
    #!/bin/bash
    echo "Hello, World" > index.html
    nohup busybox httpd -f -p ${var.server_port} &
  EOF
}
```

![alb](/assets/img/Terraform/alb2.png)
![alb](/assets/img/Terraform/alb3.png)
![alb](/assets/img/Terraform/alb4.png)

### 오류 발생
이제 `terraform destroy`를 통해 싸-악 날려버리면 그만인데, 오류 발생 ( 오히려 좋아 )

```bash
╷
│ Error: final_snapshot_identifier is required when skip_final_snapshot is false
│ 
```

### 오류 해결
리소스를 제거하는 과정에서 최종 스냅샷과 관련된 문제가 있는 것으로 보입니다. 오류는 `skip_final_snapshot` 옵션이 `false`로 설정된 경우 최종 스냅샷 식별자가 필요하다고 나와 있습니다.

이 오류를 해결하기 위해서는 Terraform 구성에서 `RDS 인스턴스 리소스 (aws_db_instance.my-rds)`에 `final_snapshot_identifier` 인자를 명시해야 합니다. 이 인자는 RDS 인스턴스가 제거되기 전에 생성될 최종 스냅샷의 이름을 결정합니다. 





다음은 RDS 인스턴스 리소스를 업데이트하여 final_snapshot_identifier 인자를 포함하는 예시(**최종 스냅샷을 생성하는 방법**)입니다:



```bash
resource "aws_db_instance" "my-rds" {
  // 다른 구성 옵션

  final_snapshot_identifier = "my-rds-final-snapshot"
}
```
이후에 terraform apply를 실행하여 변경 사항을 적용하세요.


혹은 aws_db_instance.my-rds 리소스에 skip_final_snapshot 옵션을 true로 설정하여 스냅샷을 건너뛸 수 있습니다. 

```bash
resource "aws_db_instance" "my-rds" {
  // 다른 구성 옵션
  
   skip_final_snapshot = true
}
```
이후에 terraform apply를 실행하여 변경 사항을 적용하세요.
