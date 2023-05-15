---
date: 2023-05-09 01:00:00
layout: post
title: (Sprint) 마이크로서비스 - API Gateway와 서버리스 애플리케이션
subtitle: 독립적인 서비스 구성
description: DynamoDB에 레코드를 추가하는 간단한 람다 함수를 하나 만들고, API Gateway를 통해 이를 호출하는 예제를 직접 실행해보자
image: https://res.cloudinary.com/dvqcvocet/image/upload/v1683088696/m8krc7ci1vzzbl7sxeac.png
optimized_image: https://res.cloudinary.com/dvqcvocet/image/upload/v1683088696/m8krc7ci1vzzbl7sxeac.png
category: msa
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
![dyna3](/assets/img/MicroService/dyna3.png)

#### 5. sam deploy --guided를 통해 배포를 시도한다.

![dyna2](/assets/img/MicroService/dyna2.png)
![dyna1](/assets/img/MicroService/dyna1.png)

![lambda](/assets/img/MicroService/lambda.png)
arn 주소 복사해서 


#### 6. 람다 및 DynamoDB에 어떤 리소스가 생성이 되었는지 직접 콘솔에 들어가서 확인해보게 한다.
![s3](/assets/img/MicroService/s3.png)

```bash
aws lambda invoke --function-name {Lambda함수의Arn를입력} --invocation-type Event  --payload '{ "Metadata": "Hello" }'  response.json --cli-binary-format raw-in-base64-out
```

202 status가 도착하면 성공!

#### 7. 잘 작동되는지 명령어를 통해 확인한다.
```bash
aws lambda invoke --function-name {Lambda함수의Arn를입력} --invocation-type Event  --payload '{ "Metadata": "Hello" }'  response.json --cli-binary-format raw-in-base64-out
```
202 status가 도착하면 성공!


### STEP 2: API 게이트웨이 - Lambda
#### 1. 트리거 추가 버튼을 누른다

![trigger](/assets/img/MicroService/trigger.png)
#### 2. 추가 트리거에서 다음 옵션을 통해 API 게이트웨이를 생성한다.
- API 게이트웨이를 선택
- 새 API를 생성
- REST API 유형
- 보안은 "열기"

#### 3. 이제 API 엔드포인트에 HTTP 요청을 보내면, 함수를 호출할 수 있다. 요청의 상세 내용이 DynamoDB에 저장된다.
![trigger](/assets/img/MicroService/apiendpoint.png)

### STEP 3: API 게이트웨이에 제한 추가하기
아래 세 개는 꼭 실습해 보세요

- POST 전용으로만 작동하게 만들기
  - [이 레퍼런스](https://docs.aws.amazon.com/ko_kr/apigateway/latest/developerguide/api-gateway-create-api-from-example.html)를 참고하세요
  ![api1](/assets/img/MicroService/api1.png)
  ![api2](/assets/img/MicroService/api2.png)
  API Gateway 콘솔에서 REST API를 선택한 후, "Actions" 메뉴에서 "Create Method"를 선택하여 POST 메소드를 추가합니다. 그리고 나서 "Integration type"을 "Lambda Function"으로 선택하고, "Use Lambda Proxy integration" 옵션을 선택합니다. 이후에 Lambda 함수와 연결합니다.



- 본문만 저장하도록 만들기  
Lambda 함수에서 본문만 추출하고 저장하도록 만듭니다.  
이를 위해 Lambda 함수 코드에서는 "event.body"를 사용하여 전송된 JSON 본문 데이터를 추출하고, 해당 데이터를 저장합니다.
![event.body](/assets/img/MicroService/eventdotbody.png)

- API 키를 이용한 인증 추가하기  
API Gateway 콘솔에서 "API Keys"를 선택한 후, "Create API Key"를 선택하여 API 키를 생성합니다. 이후에 API Gateway 리소스와 메소드에 API 키를 연결합니다. 이를 위해 메소드에서 "API Key Required" 옵션을 선택하고, "API Key"를 생성한 후 "Method Request"에 추가합니다.
![apikey](/assets/img/MicroService/apikey1.png)
![apikey](/assets/img/MicroService/apikey2.png)
![apikey](/assets/img/MicroService/apikey3.png)
[사용량 계획 추가] 클릭
![apikey](/assets/img/MicroService/usebudget.png)
우선 아무 값이나 넣어서 만들었다.
![apikey](/assets/img/MicroService/connected1.png)
![apikey](/assets/img/MicroService/connected2.png)
오른쪽 맨 끝 [추가]버튼을 누른다
![apikey](/assets/img/MicroService/connected3.png)

API Gateway - 리소스 - POST 클릭하면 아래와 같은 화면이 나온다.

![dynamo](/assets/img/MicroService/dynamo1.png)

[메소드 요청] 클릭후 False -> True로 변경 
![dynamo](/assets/img/MicroService/dynamo2.png)

[API 배포] 
![dynamo](/assets/img/MicroService/dynamo3.png)

lambda에서 API게이트웨이를 누른 뒤 아래 트리거-구성을 참고하면 API 게이트웨이 엔드포인트 주소가 나온다.  
해당 주소를 복사한 뒤, 포스트맨에 가져와본다.
![postman](/assets/img/MicroService/postman1.png)

포스트맨 실행 후 메소드를 POST로 변경한뒤 위 엔드포인트 주소 값을 입력해준다.

(Headers를 Hide한다.)

![postman](/assets/img/MicroService/postman2.png)

header에 다음 값을 넣는다.  
key : x-api-key  
value :  API 키 (위에서 생성했다.)

send를 눌렀을 때 'OK!'라고 나오면 성공
![postman](/assets/img/MicroService/postman3.png)
체크 해제를 한 뒤, send를 눌렀을 때 아래와 같이 나오면 정상 작동하는 것을 확인할 수 있음 !
![postman](/assets/img/MicroService/postman4.png)

DynamoDB - 테이블 - 항목탐색 - 문자열 편집으로 확인 했을 때, event.body로 설정한 값이 들어오면 성공 
![postman](/assets/img/MicroService/postman5.png)

그런데 나는 어째 잘 작동하는 것 같지가 않아서 더블체크 하러 가본다.
![postman](/assets/img/MicroService/postman6.png)

body에 다음과 같이 JSON 형식으로 값을 넣어본다. 그리고 send를 통해 POST 
![postman](/assets/img/MicroService/postman7.png)

무언가 잘못된 것일까? 다시 확인해본다.
![postman](/assets/img/MicroService/postman8.png)

코드 소스를 변경해보았다.
![event.body](/assets/img/MicroService/eventdotbody2.png)

다시 포스트를 해본다. 
![postman](/assets/img/MicroService/postman7.png)

성공
![postman](/assets/img/MicroService/postman9.png)


- 권한 부여자를 이용한 인증 부여하기 (optional)


### Result 
API 게이트웨이의 스테이지 - 내보내기 - OpenAPI 3, YAML을 선택하여 API Gateway 구성을 YAML로 다운로드할 수 있다.
![result](/assets/img/MicroService/result.png)
