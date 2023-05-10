---
date: 2023-05-09 00:00:00
layout: post
title: (Sprint) 마이크로서비스 - API Gateway와 서버리스 애플리케이션
subtitle: 독립적인 서비스 구성
description: DynamoDB에 레코드를 추가하는 간단한 람다 함수를 하나 만들고, API Gateway를 통해 이를 호출하는 예제를 직접 실행해보자
image: https://res.cloudinary.com/dvqcvocet/image/upload/v1683088696/m8krc7ci1vzzbl7sxeac.png
optimized_image: https://res.cloudinary.com/dvqcvocet/image/upload/v1683088696/m8krc7ci1vzzbl7sxeac.png
category: docker
tags:  
  - FaaS
  - Lambda
  - CloudWatch
  - API Gateway
  - SAM
  - DynamoDB
author: Hoonology
paginate: true
---
# API Gateway와 서버리스 애플리케이션
DynamoDB에 레코드를 추가하는 간단한 람다 함수를 하나 만들고, API Gateway를 통해 이를 호출하는 예제를 직접 실행해 본다.

### 람다 함수의 역할
함수에 JSON 형식의 payload를 싣고 실행하면 DynamoDB에 해당 payload가 저장

## 요구사항
- 다음 아키텍처로 구성된 서버리스 애플리케이션을 배포합니다.
  - API Gateway - Lambda - DynamoDB
- 직접 API Gateway로 실행해 봅니다.
- API Gateway의 인증 기능을 이용해서, HTTP 요청에 특정 API Key를 사용하는 예제를 다음 두 가지 방법으로 구현합니다.
  - API Key
  - 권한 부여자
- CloudWatch Logs를 통해서 API 호출을 모니터링할 수 있어야 합니다.

## Getting Started
### Step 1: API Gateway - Lambda 배포 Instruction
#### 1. 먼저 lambda 함수와 API Gateway 세팅을 한꺼번에 할 수 있게 SAM을 이용한다
![dynamo](/assets/img/MicroService/dynamo.png)
```bash
git clone https://github.com/aws-samples/serverless-patterns/ 
cd serverless-patterns/lambda-dynamodb
```

#### 2. 현재 람다가 런타임 nodejs 14.x을 지원하므로 template.yaml 파일에서 Runtime 부분을 찾아 다음과 같이 바꾼다.
```bash
- Runtime: nodejs12.x
+ Runtime: nodejs14.x
```
#### 3. 사용자 컴퓨터에 node.js 런타임을 설치한다.
#### 4. sam build를 통해 빌드가 되는지 확인해 본다.
#### 5. sam deploy --guided를 통해 배포를 시도한다.
#### 6. 람다 및 DynamoDB에 어떤 리소스가 생성이 되었는지 직접 콘솔에 들어가서 확인해보게 한다.

#### 7. 잘 작동되는지 명령어를 통해 확인한다.
```bash
aws lambda invoke --function-name {Lambda함수의Arn를입력} --invocation-type Event  --payload '{ "Metadata": "Hello" }'  response.json --cli-binary-format raw-in-base64-out
```
202 status가 도착하면 성공!
