---
date: 2023-06-07 00:00:14
layout: post
title: 성능 및 부하 테스트 기술면접
subtitle: 
description: AWS에서는 인스턴스나 볼륨에 대해서 버스트 기능을 제공합니다. 이는 평소에 사용하지 않을 때의 성능을 모아두고, 부하가 발생할 경우 일시적으로 성능을 올리는 기능입니다. 이것이 어떤 메커니즘으로 작동하는지 연구하세요.
image: https://s3.ap-northeast-2.amazonaws.com/urclass-images/vAZ96HI84yghJWvZ4weBg-1638317071798.png
optimized_image: https://s3.ap-northeast-2.amazonaws.com/urclass-images/vAZ96HI84yghJWvZ4weBg-1638317071798.png
category: mornitoring
tags:  
  - 가용성
  - 확장성
  - Latency
  - Throughput
  - 병목

author: Hoonology
paginate: true
---
#### AWS에서는 인스턴스나 볼륨에 대해서 버스트 기능을 제공합니다. 이는 평소에 사용하지 않을 때의 성능을 모아두고, 부하가 발생할 경우 일시적으로 성능을 올리는 기능입니다. 이것이 어떤 메커니즘으로 작동하는지 연구하세요.

Amazon Web Services(AWS)는 필요할 때 고성능 버스트를 허용하는 특정 서비스를 제공합니다. 이를 종종 "버스트 성능"이라고 합니다.


이러한 두 가지 서비스는 다음과 같습니다.


- Amazon EC2 T 시리즈 인스턴스: 클라우드의 가상 서버입니다. 이러한 서버는 많이 사용되지 않을 때 "CPU 크레딧"을 축적합니다. 많은 처리 능력이 갑자기 필요할 때(예: 웹 사이트에서 사용자 활동이 급증하는 경우) 이러한 크레딧을 사용하여 서버의 성능을 일시적으로 높일 수 있습니다.
- Amazon EBS(Elastic Block Store) gp2 볼륨: 스토리지 시스템입니다. EC2 인스턴스와 유사하게 gp2 볼륨은 많이 사용되지 않을 때 I/O(입력/출력) 크레딧을 얻습니다. 이러한 크레딧은 필요할 때 데이터를 읽고 쓰는 시스템의 기능을 향상시키는 데 사용할 수 있습니다.

두 경우 모두 획득한 크레딧이 모두 소진되면 시스템은 더 많은 크레딧을 획득할 때까지 정상적인 기본 수준으로 작동합니다.


이 "버스트 성능"은 시스템이 고성능 수준에서 지속적으로 실행(및 고객이 비용을 지불)하지 않고도 예기치 않은 수요 증가를 처리할 수 있도록 해주기 때문에 유용합니다.