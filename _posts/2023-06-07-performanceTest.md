---
date: 2023-06-07 00:00:05
layout: post
title: 성능 및 부하 테스트 I - 복습
subtitle: 
description: SLO를 설정하고, 이를 기반으로 부하 테스트를 진행하고, 달성 여부를 파악할 수 있다.
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
# 가용성과 확장성 복습
다수의 노드를 가진 분산 시스템, 또한 서버리스 아키텍처 등을 통해 가용성과 확장성을 확보할 수 있습니다.

- 가용성(Availability) : 시스템이 정상적으로 사용 가능한 정도
    - Uptime / Uptime + Downtime 
        - 99.95%는 좋은 가용성이 아니다 ! (99.9...도 있다 !)
        - Uptime (정상 사용 시간), Downtime(사용 불가 시간)
    - 서비스 사용 불가능 시간을 최소로 만들어야 가용성이 올라간다.
    - 단일 장애점을 없애는 것이 핵심
    - 노드 장애 발생 시, 동일한 노드로 대체 될 수 있어야한다. ( **시스템 확장**의 필요성 )
- 확장성
    - 수직 확장 : 하나의 머신에서 메모리나 CPU를 늘리는 방식 (Scale Up)
        - 수직 확장을 고려할 경우 다운타임이 발생하여 가용성이 떨어지며, 성능 제한이 있으므로 반드시 한계를 이해한다.
    - 수평 확장 : 머신의 인스턴스 수를 늘리는 방식 (Scale Out)

