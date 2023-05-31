---
date: 2023-05-31 00:00:01
layout: post
title: 서비스 모니터링 - 기술면접 발표
subtitle: 서비스 모니터링
description: 메트릭을 살펴보자
image: https://res.cloudinary.com/dvqcvocet/image/upload/v1681954803/eoe0iiqoeiq9ghldrltc.png
optimized_image: https://res.cloudinary.com/dvqcvocet/image/upload/v1681954803/eoe0iiqoeiq9ghldrltc.png
category: CICD
tags:  
  - 모니터링
  - mornitoring
  - observability

author: Hoonology
paginate: true
---

# 기술면접 
#### Q. 람다를 모니터링하려는 경우, 메트릭을 활용해 어떤 질문이 나올 수 있을까요? 레퍼런스(Lambda 키 메트릭)를 읽고, 어떤 질문을 해결할 수 있는지 알아봅시다.

- 몇 개의 람다 함수가 실행되었나요? (Invocation count)   
Lambda는 들어오는 요청 수에 따라 기능을 자동으로 확장합니다.  
Lambda는 함수의 사용률 및 성능 지표를 자동으로 추적합니다. 이 데이터를 모니터링하면 기능을 최적화하고 비용을 관리하는 데 도움이 됩니다.  
![Alt text](https://imgix.datadoghq.com/img/blog/key-metrics-for-monitoring-aws-lambda/lambda_iterator_age.png?auto%3Dformat%26fit%3Dmax%26w%3D847%26dpr%3D2)

- 얼마나 많은 람다 함수가 오류를 발생시켰나요? (Error count)
![Alt text](https://imgix.datadoghq.com/img/blog/key-metrics-for-monitoring-aws-lambda/lambda_execution_errors_by_function.png?auto%3Dformat%26fit%3Dmax%26w%3D847%26dpr%3D2)

- 람다 함수의 실행에 얼마나 오랜 시간이 걸렸나요? (Duration)
  - 각 람다 함수에 얼마나 많은 메모리가 할당되었나요? (Memory size)
  - 람다 함수가 사용한 메모리는 얼마나 됩니까? (Memory usage)

  ```bash
  REPORT RequestId: f1d3fc9a-4875-4c34-b280-a5fae40abcf9	Duration: 102.25 ms	Billed Duration: 200 ms	Memory Size: 128 MB	Max Memory Used: 58 MB	Init Duration: 2.04 ms	
  ```

![Alt text](https://imgix.datadoghq.com/img/blog/key-metrics-for-monitoring-aws-lambda/lambda_duration_overview.png?auto%3Dformat%26fit%3Dmax%26w%3D847%26dpr%3D2)
- 람다 함수가 트리거된 후 얼마나 오랜 시간 동안 대기하였나요? (Throttle count)
![Alt text](https://imgix.datadoghq.com/img/blog/key-metrics-for-monitoring-aws-lambda/unreserved_concurrency_diagram.png?auto%3Dformat%26fit%3Dmax%26w%3D847%26dpr%3D2)

- 특정 람다 함수의 병렬 실행 수는 얼마나 됩니까? (Concurrent executions)
![Alt text](https://imgix.datadoghq.com/img/blog/key-metrics-for-monitoring-aws-lambda/reserved_concurrency_diagram.png?auto%3Dformat%26fit%3Dmax%26w%3D847%26dpr%3D2)

#### Q. 쿠버네티스에 어떤 파드가 Pending 상태에 머물러있다면, 어떤 계층부터 살펴보아야 할까요? 이 경우는 파드가 Running 상태인데 잘 작동하지 않는 경우랑은 어떻게 다른가요? (서비스는 연결되어 있다고 가정합니다)
먼저 **Node 및 파드 스케줄러의 상태**를 살펴보아야 합니다. 파드가 `Pending` 상태에 있으면, 그것은 필요한**리소스(예: CPU, 메모리)가 부족하거나 노드에 문제가 있어서 스케줄링 되지 않은 상태를 의미**합니다.

- **스케줄러**: 파드가 Pending 상태에 머물러 있다면, 쿠버네티스 스케줄러가 파드를 적합한 노드에 배치하지 못했을 가능성이 있습니다. 스케줄러의 로그를 확인해보세요.

- **노드의 리소스**: 파드가 필요로 하는 리소스(CPU, 메모리, 디스크 등)가 충분한지 확인해야 합니다. 만약 필요 리소스가 부족하다면, 파드는 스케줄링 되지 않습니다.

- **Taints and Tolerations**: 노드에 설정된 Taints가 파드의 Tolerations와 맞지 않는 경우, 해당 노드에 스케줄링되지 않을 수 있습니다.

- **Affinity and Anti-Affinity** 설정: 이 설정들은 특정 **노드에 파드를 유지하거나 특정 노드에서 파드를 배제**하는데 사용됩니다. 이러한 설정이 파드 스케줄링에 영향을 줄 수 있습니다.

- **Security Policies**: 파드가 실행되는 데 필요한 권한이나 보안 정책이 제대로 설정되어 있는지 확인해야 합니다.

파드가 Running 상태이지만 잘 작동하지 않는 경우와는 다릅니다. `Running` 상태의 파드는 **이미 스케줄링이 완료**되어 노드에 할당되었으나, **파드 내의 컨테이너 또는 응용 프로그램에 문제가 있을 수 있습니다**. 이 경우에는 **파드 로그, 컨테이너 로그, 그리고 쿠버네티스 이벤트**, **애플리케이션 오류 메시지**를 살펴보는 것이 좋습니다. 또한, 서비스가 연결되어 있다고 하더라도, 네트워크 문제 또는 서비스 설정 문제 등으로 인해 응용 프로그램이 제대로 작동하지 않을 수 있습니다. 이런 경우에는 네트워크 설정과 서비스 설정을 점검해야 합니다.


