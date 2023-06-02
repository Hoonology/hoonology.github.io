---
date: 2023-06-02 00:00:05
layout: post
title: 모니터링 시스템 구축
subtitle: Auto Scaling + CloudWatch를 이용한 알림
description: ASG의 원리를 익히고, 메트릭에 따른 스케일링 정책을 세우고 모니터링을 통해 정책이 적용되는지 확인해 봅시다.
image: https://res.cloudinary.com/dvqcvocet/image/upload/v1685689813/%E1%84%91%E1%85%B3%E1%84%85%E1%85%A9%E1%84%86%E1%85%A6%E1%84%90%E1%85%A6%E1%84%8B%E1%85%AE%E1%84%89%E1%85%B3_zt9ivp.png
optimized_image: https://res.cloudinary.com/dvqcvocet/image/upload/v1685689813/%E1%84%91%E1%85%B3%E1%84%85%E1%85%A9%E1%84%86%E1%85%A6%E1%84%90%E1%85%A6%E1%84%8B%E1%85%AE%E1%84%89%E1%85%B3_zt9ivp.png
category: mornitoring
tags:  
  - ASG
  - AutoScailingGroup

author: Hoonology
paginate: true
---
# 모니터링 시스템 구축
# 요청사항
- Prometheus Operator가 설치된 환경에서 nginx 인그레스 컨트롤러를 설치합니다.
- cozserver (v2)의 디플로이먼트 및 서비스를 배포하고, 인그레스를 만들어서 nginx를 통해 서비스에 접근하게 합니다.
- Prometheus에서 쿼리를 통해 주요 메트릭을 확인합니다.
- Grafana에 이미 존재하는 대시보드들을 살펴보고, 어떤 쿼리를 사용하는지 확인합니다.

