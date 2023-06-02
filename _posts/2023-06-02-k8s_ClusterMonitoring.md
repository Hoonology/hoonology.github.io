---
date: 2023-06-02 00:00:00
layout: post
title: 쿠버네티스 클러스터 모니터링, 프로메테우스, Grafana 설치
subtitle: 클러스터 환경에서의 문제 해결의 어려움
description: 쿠버네티스의 경우 클러스터 안에 다수의 노드, 그리고 그 안에 파드를 비롯한 다양한 워크로드가 많게는 수백 개가 실행되는 형태로 구성되어 있습니다. 
image: https://res.cloudinary.com/dvqcvocet/image/upload/v1685689813/%E1%84%91%E1%85%B3%E1%84%85%E1%85%A9%E1%84%86%E1%85%A6%E1%84%90%E1%85%A6%E1%84%8B%E1%85%AE%E1%84%89%E1%85%B3_zt9ivp.png
optimized_image: https://res.cloudinary.com/dvqcvocet/image/upload/v1685689813/%E1%84%91%E1%85%B3%E1%84%85%E1%85%A9%E1%84%86%E1%85%A6%E1%84%90%E1%85%A6%E1%84%8B%E1%85%AE%E1%84%89%E1%85%B3_zt9ivp.png
category: mornitoring
tags:  
  - k8s
  - 쿠버네티스
  - kubernetes

author: Hoonology
paginate: true
---


# 프로메테우스 모니터링 시스템
**오픈소스 모니터링 및 알림 시스템**
- 프로메테우스 : 쿠버네티스, 노드, 프로메테우스 자체를 모니터링
- CNCF : 쿠버네티스를 지원하고 관리하는재단  
- Grafana : 시각화 담당

## 프로메테우스 구성 요소
- 시계열 데이터 저장
- 프로메테우스 서버는 다양한 exporter로 부터 각 대상의 메트릭을 pull하여 주기적으로 가져오는 모니터링 시스템
  - k8s 관련 메트릭 : k8s exporter
  - mongoDB 관련 메트릭 : mongodb 메트릭
- Alert manager : 경고 및 알림 담당
- 사용자가 데이터를 질의할 수 있는 Web UI 존재
  - PromgQL(Prometheus Query Language) 사용
- Grafana : 프로메테우스에서 권장하는 시각화 도구

## 쿠버네티스 exporter
![Alt text](https://user-images.githubusercontent.com/702622/158340501-2cda5ff5-0dd0-4241-8ffe-353c94688c31.png)

쿠버네티스 관련 메트릭을 가져올 수 있는 쿠버네티스 exporter
- Kube API 사용

# Prometheus, Grafana 설치


## Prometheus Operator를 이용하여 설치
```bash
minikube start --nodes=3
```
위 처럼 여러개의 노드를 만들면 첫 번째는 `master`, 나머지 노드는 worker 노드로 구성됩니다. 
> 여러 개의 노드를 구성할 경우 **워커 노드에는 실제로 실행되는 애플리케이션을 배치**하며, **마스터 노드는 클러스터 관리에 집중**하게 됩니다.

- 워커 노드에 애플리케이션을 별도로 배치하는 것은 모니터링 관점에서 몇 가지 이점을 제공할 수 있습니다:

  - 성능 모니터링: 워커 노드에 애플리케이션을 배치하면 해당 노드에서 실행되는 애플리케이션의 성능을 쉽게 모니터링할 수 있습니다. 애플리케이션의 CPU 사용률, 메모리 사용량, 디스크 I/O, 네트워크 트래픽 등을 실시간으로 확인하고 성능 문제를 식별할 수 있습니다.
  - 리소스 관리: 워커 노드에 애플리케이션을 별도로 배치하면 해당 노드의 리소스 사용을 더욱 효율적으로 관리할 수 있습니다. 애플리케이션이 독립적인 환경에서 실행되므로 다른 애플리케이션의 리소스 요구에 영향을 받지 않고 필요한 만큼의 CPU, 메모리, 디스크 공간을 할당할 수 있습니다.

  - 이상 감지: 애플리케이션을 워커 노드에 별도로 배치하면 해당 노드에서 발생하는 이상 현상을 신속하게 감지할 수 있습니다. 예를 들어, 워커 노드의 네트워크 트래픽이 평소보다 급격하게 증가한다면 애플리케이션에 문제가 발생할 수 있음을 알 수 있습니다. 이러한 이상 현상을 신속하게 감지하고 조치를 취함으로써 잠재적인 장애를 방지할 수 있습니다.

  - 로깅 및 추적: 워커 노드에 애플리케이션을 배치하면 해당 노드에서 발생하는 로그 및 추적 정보를 중앙 집중식으로 수집할 수 있습니다. 이는 애플리케이션의 동작과 성능을 분석하고 디버깅할 때 유용합니다. 중앙 집중식 로깅 및 추적 시스템을 사용하면 여러 워커 노드에서 실행되는 애플리케이션의 로그 및 추적을 통합적으로 관리할 수 있습니다.


```bash
$ minikube status
```
<img width="816" alt="스크린샷 2023-06-02 14 22 55" src="https://github.com/Hoonology/hoonology.github.io/assets/105037141/1f8d0f20-49dc-4e93-814a-01a9d994705f">

## Prometheus Operator Quick Start
#### 1. kube-prometheus 클론
```bash
git clone https://github.com/prometheus-operator/kube-prometheus.git
```
#### 2. kube-prometheus 배포
```bash
# STEP 1
kubectl create -f manifests/setup
# manifests/setup 안의 워크로드를 전부 실행합니다.

# STEP 2
until kubectl get servicemonitors --all-namespaces ; do date; sleep 1; echo ""; done
# 일단 모든 모든 서비스 모니터(servicemonitor)가 구동될 때까지 기다립니다.

# STEP 3
kubectl create -f manifests/
# manifest/ 안의 워크로드를 전부 실행합니다.
```

#### 4. Prometheus Operator 관련 서비스 및 워크로드가 실행되기 전까지 기다립니다.
a. 프로메테우스 노출
![프로메테우스](https://www.theteams.kr/includes/uploads/feed/20201006181121_1601975480562_1280.png)
```bash
kubectl --namespace monitoring port-forward svc/prometheus-k8s 9090
```
[http://localhost:9090](http://localhost:9090)로 접속해서 프로메테우스에 접속할 수 있습니다.

<img width="1246" alt="스크린샷 2023-06-02 15 20 43" src="https://github.com/Hoonology/hoonology.github.io/assets/105037141/e37f7954-9c0d-4531-847b-4586afcff657">

b. 그라파나 노출
![그라파나](https://www.theteams.kr/includes/uploads/feed/20201006181120_1601975480151_1280.png)
```bash
kubectl --namespace monitoring port-forward svc/grafana 3000
```
[http://localhost:3000](http://localhost:3000)으로 접속해서 그라파나에 접속할 수 있습니다. (초기 사용자 이름과 비밀번호는 admin/admin입니다)

<img width="1736" alt="스크린샷 2023-06-02 15 32 57" src="https://github.com/Hoonology/hoonology.github.io/assets/105037141/38dcb014-0a51-4489-b773-9d17f387f9d5">
