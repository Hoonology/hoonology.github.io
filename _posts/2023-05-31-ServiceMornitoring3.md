---
date: 2023-06-01 00:00:01
layout: post
title: (Sprint) Auto Scaling + CloudWatch를 이용한 알림
subtitle: Auto Scaling + CloudWatch를 이용한 알림
description: ASG의 원리를 익히고, 메트릭에 따른 스케일링 정책을 세우고 모니터링을 통해 정책이 적용되는지 확인해 봅시다.
image: https://res.cloudinary.com/dvqcvocet/image/upload/v1681954803/eoe0iiqoeiq9ghldrltc.png
optimized_image: https://res.cloudinary.com/dvqcvocet/image/upload/v1681954803/eoe0iiqoeiq9ghldrltc.png
category: CICD
tags:  
  - ASG
  - AutoScailingGroup

author: Hoonology
paginate: true
---
갑작스러운 트래픽 증가에 대응하기 위해서는, 서버의 주요 메트릭을 모니터링하고, 특정 메트릭이 임계치를 넘을 때, 수평 확장이 자동으로 진행되게 하는 것이 바람직합니다. 우리는 이미 앞서 AWS를 통해 이러한 서비스를 제공하는 Auto Scaling Group (이하 ASG)에 대해 배웠습니다. 스프린트를 통해 ASG의 원리를 익히고, 메트릭에 따른 스케일링 정책을 세우고 모니터링을 통해 정책이 적용되는지 확인해 봅시다.

추가적으로 모니터링을 통해 모든 지표를 항상 관찰할 수 없으므로, 주요 메트릭의 임계치, 또는 장애 발생 예상 시점(예를 들어, CPU 사용량이 80%에 도달할 경우)을 경보의 형태로 제공해야 합니다. 이를 기존에 익혔던 SNS 및 람다를 통해 구현해 봅시다.

## Bare Minimum Requirement

- EC2 서버를 ASG를 통해 구성합니다. 구성은 다음을 따릅니다.
- CloudWatch 알람을 통해 ASG의 스케일 인/아웃을 진행합니다.
- 스케일 인/아웃이 진행될 때 디스코드에 알림을 보냅니다.
- 메트릭을 바탕으로 장애 발생 예상 시점에 디스코드에 알림을 보냅니다.
  - CPU 사용률(CPUUtilization) 값이 특정 값 이상일 때 경보가 발생하게 하세요

## Getting Started
### 시작 템플릿 구성
ASG를 위한 시작 템플릿 구성은 다음을 따릅니다.

- 그룹 정보
  - 원하는 용량: 1
  - 최소 용량: 1
  - 최대 용량: 3
- 시작 템플릿은 다음 구성을 따릅니다.
  - Ubuntu Server (LTS)
  - t2.nano
  - 기존 혹은 신규 키 페어를 사용합니다
  - 보안 그룹: 인바운드 HTTP 및 SSH 허용
  - 사용자 데이터
    ```bash
    #!/bin/bash
    echo "Hello, World" > index.html
    sudo apt update
    sudo apt install stress
    nohup busybox httpd -f -p 80 &
    ```

### CloudWatch와 조정 정책
- CloudWatch를 통한 Auto Scaling 그룹 지표 수집 활성화 필요
- Scale-in 조건: CPU 40% 이하
- Scale-out 조건: CPU 50% 이상

기타
- 로드 밸런서는 설정하지 않아도 좋습니다.


## 지표 수집 과정
EC2 수집 주기의 기본값은 5분입니다. ASG의 경우 [지표 수집 활성화]를 통해 지표를 CloudWatch에 기록합니다.

이러한 기록을 이용해 시간에 따른 메트릭 추이를 확인할 수 있습니다. 이때 경보를 통해 임계치에 따른 메시지가 SNS로 전달됩니다. 또한 ASG에서 발생하는 스케일링 이벤트를 트리거하기 위해 경보 두 개(스케일 인/아웃)를 자동으로 생성합니다.

참고로, CloudWatch의 메트릭 보존 기간은 수집 주기에 따라 다릅니다. 세부 모니터링 옵션을 활성화하여 수집 주기를 조절할 수 있습니다. 자세한 내용은 CloudWatch FAQ를 통해 확인하세요.

### 알림 및 경보 아키텍처
알림을 위해서 다음의 아키텍처를 사용합니다.


![Alt text](https://s3.ap-northeast-2.amazonaws.com/urclass-images/9Yt5UtU7AgThaz3RlNpjY-1651497045773.png)

추가적으로, 자동으로 생성되는 경보 외에도, 메트릭에 따른 경보를 만들어야 합니다. 레퍼런스를 참고하여 CPU 사용률(CPUUtilization) 값이 특정 값 이상일 때 경보가 발생하게 하세요.

### Lambda 코드
[https://gist.github.com/gotoweb/0f993bdc19833e76f7860608181bedac](https://gist.github.com/gotoweb/0f993bdc19833e76f7860608181bedac)

- 디스코드 웹훅 URL은 HOOK_URL 환경변수를 이용해 추가하며, 다음 URL을 입력해 넣습니다.
```bash
https://discord.com/api/webhooks/1102396954543128618/785PFD5H5ORooihWDdipYdVsFQdSwETMypye-X9EavBTrhXGg8DyBLRJW66EGad6XIpD
```
- 코드의 username 부분에 CloudWatch Monitoring 대신, 여러분의 이름을 적어 넣으세요.

## 부하 테스트
부하 테스트를 위해서 stress 명령어를 사용합니다. 다음 명령어를 이용하면, CPU를 100%를 사용하도록 만들 수 있습니다. top명령어를 통해서 정말 100%를 사용하는지 확인해 보세요.

```
$ stress -c 1
```