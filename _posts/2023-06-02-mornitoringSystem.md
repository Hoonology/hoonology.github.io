---
date: 2023-06-02 00:00:05
layout: post
title: 모니터링 시스템 구축
subtitle: 모니터링 시스템 구축
description: rometheus에서 쿼리를 통해 주요 메트릭을 확인합니다.
image: https://res.cloudinary.com/dvqcvocet/image/upload/v1685689813/%E1%84%91%E1%85%B3%E1%84%85%E1%85%A9%E1%84%86%E1%85%A6%E1%84%90%E1%85%A6%E1%84%8B%E1%85%AE%E1%84%89%E1%85%B3_zt9ivp.png
optimized_image: https://res.cloudinary.com/dvqcvocet/image/upload/v1685689813/%E1%84%91%E1%85%B3%E1%84%85%E1%85%A9%E1%84%86%E1%85%A6%E1%84%90%E1%85%A6%E1%84%8B%E1%85%AE%E1%84%89%E1%85%B3_zt9ivp.png
category: mornitoring
tags:  
  - k8s
  - 쿠버네티스
  - kubernetes
  - prometheus
  - grafana
  - exporter
  - nginx

author: Hoonology
paginate: true
---

# 모니터링 시스템 구축
> 이정도만 보여줘도 이력서에 프로메테우스 할줄 압니다 쓸 수 있다고한다.


# 요청사항
- Prometheus Operator가 설치된 환경에서 nginx 인그레스 컨트롤러를 설치합니다.
- cozserver (v2)의 디플로이먼트 및 서비스를 배포하고, 인그레스를 만들어서 nginx를 통해 서비스에 접근하게 합니다.
- Prometheus에서 쿼리를 통해 주요 메트릭을 확인합니다.
- Grafana에 이미 존재하는 대시보드들을 살펴보고, 어떤 쿼리를 사용하는지 확인합니다.

# Getting Started
## 1. nginx 인그레스 컨트롤러 설치
Helm을 이용해서 설치합니다. **(minikube addon을 사용하는 것이 아닙니다)** 
```bash
$ brew install helm
```

이때 nginx 인그레스 컨트롤러가 프로메테우스용 메트릭을 노출해야 하므로, helm install 과정에서 반드시 설정해야 하는 옵션이 있습니다.
### 1.1 먼저 clone으로 operator를 받아옵니다.
#### [prometheus-operator Github](https://github.com/prometheus-operator/prometheus-operator)


### 1.2 프로메테우스용 메트릭 노출 
```bash
helm install ingress-nginx ingress-nginx/ingress-nginx \
--set controller.metrics.enabled=true \
--set controller.metrics.serviceMonitor.enabled=true
```

Helm이 설치되었으면, 다음은 nginx ingress controller를 설치하면서 프로메테우스용 메트릭을 노출하도록 설정하는 단계입니다. 사용자님이 참고하신 레퍼런스에 따르면, controller.metrics.enabled와 controller.metrics.serviceMonitor.enabled 옵션을 true로 설정해야 합니다.

이렇게 설정하면 프로메테우스에서 nginx ingress controller의 메트릭을 수집하고 모니터링할 수 있게 됩니다. 

### 1.3 프로메테우스 웹 UI에서 ingress-nginx-controller가 타겟으로 설정되어 있는지 확인하려면 다음 단계를 따르세요.

- 먼저 프로메테우스가 실행되고 있는지 확인합니다. 프로메테우스를 설치했다면, 대개는 `kubectl get pods -n prometheus`라는 명령어로 프로메테우스의 실행 상태를 확인할 수 있습니다.

<img width="697" alt="스크린샷 2023-06-04 15 04 10" src="https://github.com/prometheus-operator/prometheus-operator/assets/105037141/3179265c-29dd-4b60-8560-ee136dd2f54a">

- nginx ingress controller가 성공적으로 설치된 것으로 보입니다(hoonology-ingress-nginx-controller-59b97d9496-tzlnc라는 이름의 pod가 Running 상태임).

- 프로메테우스 웹 UI에 접속하기 위해 프로메테우스 서비스를 포워드합니다. 이는 `kubectl port-forward -n prometheus service/prometheus-k8s 9090`라는 명령어로 수행됩니다. 이렇게 하면 로컬 9090 포트를 통해 프로메테우스 웹 UI에 접속할 수 있게 됩니다.
  - 이전 페이지에서 minikube를 통해 배포를 다음과 같이 했었습니다. `kubectl --namespace monitoring port-forward svc/prometheus-k8s 9090` 
  - 해당 명령어로 배포를 진행합니다.
  <img width="697" alt="스크린샷 2023-06-04 15 09 52" src="https://github.com/prometheus-operator/prometheus-operator/assets/105037141/211ca6c4-5e8b-4ea6-92e5-9694925d1735">


- 이제 웹 브라우저에서 http://localhost:9090로 접속하면 프로메테우스의 웹 UI를 확인할 수 있습니다.

<img width="1624" alt="스크린샷 2023-06-04 15 10 41" src="https://github.com/prometheus-operator/prometheus-operator/assets/105037141/36edfec1-ac0d-4021-87ad-5c788c5ce0ed">

- 프로메테우스 웹 UI의 상단 메뉴에서 Status를 클릭한 후, 드롭다운 메뉴에서 `Targets`를 선택하면 현재 프로메테우스가 수집하는 타겟의 목록을 볼 수 있습니다.


<img width="1624" alt="스크린샷 2023-06-04 15 10 41" src="https://github.com/prometheus-operator/prometheus-operator/assets/105037141/70b2fa42-40e0-45ae-a986-0a3f2c47f76b">


- 여기서 ingress-nginx-controller가 리스트에 나타나는지 확인합니다. 만약 리스트에 나타난다면, 설치 및 설정이 성공적으로 완료된 것입니다.

<img width="1190" alt="스크린샷 2023-06-04 15 12 58" src="https://github.com/prometheus-operator/prometheus-operator/assets/105037141/85697e11-de90-4aab-8045-3df6e7ffd8a7">

## 2. 인그레스 생성
`인그레스`는 외부에서 서비스로 접속이 가능한 URL, 로드 밸런스 트래픽, SSL / TLS 종료 그리고 이름-기반의 가상 호스팅을 제공하도록 구성할 수 있습니다. 인그레스 컨트롤러는 일반적으로 로드 밸런서를 사용해서 인그레스를 수행할 책임이 있으며, 트래픽을 처리하는데 도움이 되도록 에지 라우터 또는 추가 프런트 엔드를 구성할 수도 있습니다.

![alt](https://d33wubrfki0l68.cloudfront.net/0e185f84ae43dcdc8952eb7d8f98c3eba87d2e3a/05337/ko/docs/images/ingress.svg)

![Alt text](https://s3.ap-northeast-2.amazonaws.com/urclass-images/Izq6AD1zUYg1TxerGLpo2-1652022344645.png)

- 위 아키텍처 구성을 살펴보면, http 인그레스는 localhost:80으로 설정되어 있습니다.
cozserver라는 이름의 서비스가 존재하며, 이 서비스는 8055포트를 사용합니다.
- cozserver 서비스는 pod1, pod2, pod3에 의해 백엔드로 제공되며, 이들 pod는 8080포트에서 수신 대기하고 있습니다.
- 이 정보를 바탕으로, Ingress를 설정하여 외부의 http 요청을 localhost:80으로 받고 이를 cozserver 서비스에 라우팅하는 설정을 할 수 있습니다. 이 때, cozserver 서비스는 각 pod에 요청을 전달하며, 이들 pod는 8080포트에서 요청을 처리합니다.

> 인그레스 컨트롤러가 있어야 인그레스를 충족할 수 있습니다. 인그레스 리소스만 생성한다면 효과가 없습니다.

### 2.1. 인그레스 리소스

원하는 아키텍처에 따라 Ingress를 설정해야 하므로, 각 서비스에 대해 적절한 경로 및 포트를 설정해야 합니다.

```yml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: cozserver-ingress
spec:
  rules:
  - host: "localhost"
    http:
      paths:
      - pathType: Prefix
        path: "/"
        backend:
          service:
            name: cozserver
            port:
              number: 8055
```

- 이 Ingress 설정이 작동하려면, cozserver라는 이름의 서비스가 이미 존재해야 하며, 이 서비스는 8055포트에서 수신 대기해야 합니다. 또한 이 서비스는 적절한 서버(Pod)로 요청을 전달해야 합니다. 이를 위해서는 이전에 제시한 Deployment 또는 Pod 설정을 적용해야 할 수 있습니다.

- Ingress 리소스는 요청을 서비스로 라우팅할 뿐만 아니라, 외부 트래픽을 Kubernetes 클러스터 내로 라우팅하는 역할도 합니다. 이를 위해 Ingress 컨트롤러가 필요하며, 이 예제에서는 이미 Nginx Ingress 컨트롤러가 설치되어 있다고 가정하고 있습니다.

- 이 Ingress 설정은 모든 요청을 cozserver 서비스로 라우팅합니다. 만약 다른 경로에 대해 다른 서비스로 요청을 라우팅하려면, paths 설정에 다른 항목을 추가해야 합니다.

- Ingress 규칙을 정의한 yaml 파일을 생성한 후, kubectl apply -f [파일명].yaml 명령을 사용하여 쿠버네티스에 이 규칙을 적용할 수 있습니다.

- 이렇게 설정하면, Ingress Controller는 이 규칙에 따라 외부에서 오는 트래픽을 적절한 서비스로 라우팅할 것입니다.


