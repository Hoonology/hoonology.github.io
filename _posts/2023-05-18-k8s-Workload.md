---
date: 2023-05-18 00:00:00
layout: post
title: Kubernetes Workload
subtitle: Kubernetes, k8s
description: 워크로드란, '쿠버네티스 상에서 작동되는 애플리케이션'을 의미하며, 클라우드 분야에서는 "어떤 애플리케이션을 실행할 때 필요한 IT 리소스의 집합”이라는 의미로 통용된다.
image: https://res.cloudinary.com/dvqcvocet/image/upload/v1684335849/%E1%84%8F%E1%85%AE%E1%84%87%E1%85%A5%E1%84%8C%E1%85%B5%E1%86%AB%E1%84%89%E1%85%B3_yoqeyy.png
optimized_image: https://res.cloudinary.com/dvqcvocet/image/upload/v1684335849/%E1%84%8F%E1%85%AE%E1%84%87%E1%85%A5%E1%84%8C%E1%85%B5%E1%86%AB%E1%84%89%E1%85%B3_yoqeyy.png
category: k8s
tags:  
  - kubernetes
  - k8s
  - pod
  - workload
  - 워크로드

author: Hoonology
paginate: true
---
# INDEX
- [파드(Pods)](#쿠버네티스--kubernetes-k8s)
- [디플로이먼트(Deployment)](#디플로이먼트-deployment)
- [서비스](#서비스)


# 파드(Pods)
쿠버네티스의 배포 가능한 가장 작은 컴퓨팅 유닛, '논리적인 호스트'

- 1개 이상의 애플리케이션 컨테이너
- IP 주소
- 스토리지 

> 파드는 일시적이고, 언제나 삭제될 수 있음을 감안하고 만듭니다.


![pod](/assets/img/kubernetes/k8s_pod1.png)

> 쿠버네티스에서는 '**워크로드 리소스**'를 만들기 위해 **YAML** 파일과 같은 리소스 정의 파일을 사용합니다.

#### 워크로드란?

- 쿠버네티스 : "쿠버네티스 상에서 작동되는 애플리케이션"
- 클라우드 : "애플리케이션을 실행할 때 필요한 IT 리소스의 집합"

파드를 생성하기 위해서도 YAML을 사용합니다.
```bash
# practice_pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: nginx
    image: nginx:1.14.2
    ports:
    - containerPort: 80
```

```bash
kubectl apply -f practice_pod.yaml

> pod/nginx created
```

```bash
kubectl get pods

NAME    READY   STATUS              RESTARTS   AGE
nginx   0/1     ContainerCreating   0          8s
```

## Action Items
Q. kubectl describe pod/nginx 명령을 이용해서 파드에 대한 자세한 정보를 볼 수 있습니다. 이 정보를 통해서 알 수 있는 내용은 무엇인가요?
- Pod 메타데이터: 여기에는 Pod와 관련된 이름, 네임스페이스, 레이블 및 주석이 포함됩니다.
- 포드 상태: 실행 중인지, 대기 중인지, 종료되었는지 등 포드의 현재 상태를 제공합니다. 포드 컨테이너의 시작 및 종료 타임스탬프도 볼 수 있습니다.
- 포드 조건: 준비 상태, 예약, 초기화 또는 준비 여부와 같은 포드의 조건을 보여줍니다.
- 포드 이벤트: 이 섹션에는 수명 주기 동안 발생한 오류 또는 경고와 같은 포드와 관련된 최근 이벤트가 표시됩니다.
- 컨테이너: Pod 내에서 실행 중인 컨테이너에 대한 정보를 제공합니다. 여기에는 컨테이너 이름, 사용된 이미지 및 컨테이너의 현재 상태(실행 중, 대기 중, 종료됨)가 포함됩니다.
- 환경 변수: 포드 또는 컨테이너에 대해 환경 변수가 정의된 경우 여기에 나열됩니다.
- 볼륨 탑재: 포드에 탑재된 볼륨이 있는 경우 이 섹션에는 탑재 지점의 세부 정보가 표시됩니다.
- 네트워크: IP 주소, 포트, 네트워크 정책 등 포드의 네트워킹과 관련된 정보입니다.
- 리소스 사용량: Pod의 CPU 및 메모리 리소스 제한과 사용량에 대한 세부 정보를 제공합니다.
- 이벤트: 이 섹션에는 일정 결정, 컨테이너 충돌 또는 기타 중요한 알림과 같이 포드와 관련된 모든 관련 이벤트가 표시됩니다.

Q. 방금 만든 파드를 지우기 위해서는 어떤 명령어를 사용하면 좋을까요? 공식 문서의 [치트 시트](https://kubernetes.io/ko/docs/reference/kubectl/cheatsheet/#%EB%A6%AC%EC%86%8C%EC%8A%A4-%EC%82%AD%EC%A0%9C)를 참고하여 직접 지워봅시다.

```bash
kubectl delete -f ./pod.json          # pod.json에 지정된 유형 및 이름을 사용하여 파드 삭제
kubectl delete pod unwanted --now     # 유예 시간 없이 즉시 파드 삭제
kubectl delete pod,service baz foo    # "baz", "foo"와 동일한 이름을 가진 파드와 서비스 삭제
kubectl delete pods,services -l name=myLabel   # name=myLabel 라벨을 가진 파드와 서비스 삭제
kubectl -n my-ns delete pod,svc --all    # my-ns 네임스페이스 내 모든 파드와 서비스 삭제
```

```bash
kubectl delete pod <pod-name>
```

<br>
<br>
<br>


# 디플로이먼트( Deployment )

<u>'배포'의 의미가 아닙니다.</u>  

**파드의 교체 / 배치 (placement)와 관련된 명세**입니다.

위 파드 설명에서 언급했지만, **파드는 일시적이고, 언제나 삭제될 수 있음을 감안**하고 만들기 때문에 사용자가 직접 개별 파드를 만들 일이 많지 않습니다. 파드가 실행되는 공간인 노드가 만일 실패하는 경우, 그 안에서 실행되는 파드 역시 사용할 수 없게 됩니다.

![Alt text](https://d33wubrfki0l68.cloudfront.net/2475489eaf20163ec0f54ddc1d92aa8d4c87c96b/e7c81/images/docs/components-of-kubernetes.svg)


컨테이너를 수동으로 만들고 관리하는 것은 도커로 충분히 할 수 있습니다. 그렇다면 쿠버네티스를 쓰는 이유는 무엇일까요?  
정답은 **컨테이너를 오케스트레이션**하는데 의의가 있습니다. 즉, 파드 장애 시 자동 복구 및 복제하는 일을 '자동'으로 처리하는 것입니다. AWS의 ECS(Elastic Container Service)가 있겠죠, 컨테이너의 로드 밸런싱과 오토 스케일링과 같은 일을 맡는다는 공통점이 있습니다.

- 디플로이먼트
- 스테이트풀셋
- 데몬셋 

위 세가지로 파드를 관리하는 것을 지향합니다.

```yaml
# Deployment

apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    # ==========여기서부터 파드 템플릿 ============
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
    # ===========여기까지 파드 템플릿 =============
```
  





## 그래서 디플로이먼트란 ?

*" 파드를 업데이트하기 위한 **선언적 명세** "*

- 파드를 원하는 개수만큼 실행
- 파드 업데이트
- 파드 롤백

## Review : 다양한 배포 전략 
애플리케이션의 여러 복제본을 새 버전으로 업데이트 하는 방법
- **재생성 : 이전 버전을 삭제하고 새 버전 생성** 
- 블루/그린 배포 : 한 번에 이전 버전에서 새 버전으로 연결 전환
- **롤링 배포 : 이전 버전을 Scale Down, 새 버전을 Scale Up = 단계별 교체 = 롤아웃(Rollout)**
- 카나리 배포 : 새 버전이 잘 작동한다고 판단되면 이전 버전을 교체

## 디플로이먼트는 어떤 전략 ?
파드의 복제본을 자동으로 업데이트하게 해주는 명세이므로, 쿠버네티스에서 지원하는 배포 전략은 
> 재생성(Recreate)과 롤링 배포(RollingUpdate)



## 디플로이먼트 실습

```bash
kubectl apply -f <디플로이먼트_파일>
```

```bash
kubectl create deployment kubernetes-bootcamp --image=gcr.io/google-samples/kubernetes-bootcamp:v1
```

```bash
kubectl get deployments

NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
hello-minikube        1/1     1            1           3h16m
kubernetes-bootcamp   1/1     1            1           57s
nginx-deployment      3/3     3            3           2m55s
```




#### Q. 디플로이먼트가 지원하는 배포 전략에서 블루/그린이나 카나리는 찾아볼 수 없습니다. 어떻게 블루/그린이나 카나리 배포를 할 수 있을까요?

#### 블루/그린 배포:

![Alt text](https://velog.velcdn.com/images/xgro/post/2ebfa180-c17c-4b65-85aa-9878a95a6be5/image.png)
- 애플리케이션의 "파란색" 버전용과 "녹색" 버전용으로 두 개의 개별 배포를 만듭니다.
- 서비스를 사용하여 활성 버전을 노출합니다(처음에는 "파란색" 배포를 가리킴).
- "그린" 배포에서 필요한 테스트 및 검증을 수행합니다.
- "그린" 배포가 확인되고 준비되면 "그린" 배포를 가리키도록 서비스를 업데이트하여 트래픽을 새 버전으로 보냅니다.


**Deployment Blue**  
블루에 해당하는 ReplicaSet 생성
```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: replica1
spec:
  replicas: 2
  selector:
    matchLabels:
      ver: v1
  template:
    metadata:
      name: pod1
      labels:
        ver: v1
    spec:
      containers:
      - name: container
        image: kubetm/app:v1
      terminationGracePeriodSeconds: 0
  ```

**Service**  
selector에 ver:v1을 지정하여 ReplicaSet의 Label과 동일하게 구성(ReplicaSet의 Pod와 연결)
```yaml
apiVersion: v1
kind: Service
metadata:
  name: svc-3
spec:
  selector:
    ver: v1
  ports:
  - port: 8080
    protocol: TCP
    targetPort: 8080
```
**Deployment Green**
```yaml
v2의 ReplicaSet을 생성(green)

apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: replica2
spec:
  replicas: 2
  selector:
    matchLabels:
      ver: v2
  template:
    metadata:
      name: pod1
      labels:
        ver: v2
    spec:
      containers:
      - name: container
        image: kubetm/app:v2
      terminationGracePeriodSeconds: 0
```
**Service의 selector을 v2로 변경한다.**
```yaml
....
kind: Service
metadata:
  name: svc-3
spec:
  selector:
    ver: v2
  ports:
  - port: 8080
    protocol: TCP
    targetPort: 8080
    ....
```


#### 카나리아 배포:


- 더 작고 제어된 릴리스인 애플리케이션의 카나리아 버전에 대한 새 배포를 만듭니다.
- 트래픽의 일부를 카나리아 배포로 점진적으로 전환하고 대부분은 여전히 ​​안정적인 버전으로 이동합니다.
- 카나리아 버전의 동작과 성능을 모니터링하고 분석합니다.
- 카나리아 버전의 성능이 좋으면 조금씩 트래픽을 늘려가세요.
- 문제가 발생하면 카나리아 배포를 신속하게 롤백하고 모든 트래픽을 안정적인 버전으로 되돌립니다.



여러 레이블이 필요한 또 다른 시나리오는 **동일한 컴포넌트의 다른 릴리스 또는 구성의 디플로이먼트를 구별하는 것**입니다. 새 릴리스가 완전히 롤아웃되기 전에 실제 운영 트래픽을 수신할 수 있도록 새로운 애플리케이션 릴리스(파드 템플리트의 이미지 태그를 통해 지정됨)의 카나리 를 이전 릴리스와 나란히 배포하는 것이 일반적입니다.

- track 레이블을 사용하여 다른 릴리스를 구별할 수 있습니다.

  - 기본(primary), 안정(stable) 릴리스에는 값이 stable 인 track 레이블이 있습니다.

```yaml
     name: frontend
     replicas: 3
     ...
     labels:
        app: guestbook
        tier: frontend
        track: stable
     ...
     image: gb-frontend:v3
```

그런 다음 **서로 다른 값**(예: canary)으로 **track 레이블을 전달하는 방명록 프론트엔드의 새 릴리스를 생성**하여, 두 세트의 파드가 **겹치지 않도록** 할 수 있습니다.
```yaml
     name: frontend-canary
     replicas: 1
     ...
     labels:
        app: guestbook
        tier: frontend
        track: canary
     ...
     image: gb-frontend:v4
```
프론트엔드 서비스는 레이블의 공통 서브셋을 선택하여(즉, track 레이블 생략) 두 레플리카 세트에 걸쳐 있으므로, **트래픽이 두 애플리케이션으로 리디렉션됩니다**.


```yaml
  selector:
     app: guestbook
     tier: frontend
```
안정 및 카나리 릴리스의 레플리카 수를 조정하여 실제 운영 트래픽을 수신할 각 릴리스의 비율을 결정합니다. 확신이 들면, 안정 릴리스의 track을 새로운 애플리케이션 릴리스로 업데이트하고 카나리를 제거할 수 있습니다.




블루/그린 및 카나리아 배포 모두 트래픽 라우팅 및 모니터링을 신중하게 관리해야 합니다. Kubernetes 수신 컨트롤러, 서비스 메시(예: Istio) 또는 API 게이트웨이와 같은 도구를 활용하여 서로 다른 배포 또는 버전 간의 트래픽 라우팅을 제어할 수 있습니다.


**블루/그린 및 카나리아 배포 전략을 기본적으로 지원하는 Jenkins, Spinnaker 또는 Argo CD와 같은 CI/CD(지속적인 통합 및 배포) 도구를 활용**할 수 있습니다.


이러한 접근 방식은 원활한 전환을 보장하고 배포 프로세스 중에 사용자에게 미치는 잠재적인 영향을 최소화하기 위해 신중한 계획, 테스트 및 모니터링이 필요하다는 점을 기억하십시오.










# 서비스

## 파드를 외부로 노출시키기
클러스터 안에 파드는 각각 고유의 IP를 가지고 있지만, 직접 우리가 내부망에 접속할 수 있는 것은 아닙니다. 그럼 어떻게 파드 안에 서비스가 외부로 노출될 수 있을까요?

앞서 디플로이먼트를 통해 파드의 복제본을 원하는 개수만큼 실행시킬 수 있다고 언급했습니다. 서비스 리소스는 이러한 파드 집합에 접근할 수 있게 하며, 파드가 교체되거나, 어떤 특정 파드에 문제가 생긴 경우에도 사용 가능한 파드를 찾아 알아서 접속할 수 있게 돕습니다.


### 쿠버네티스 네트워크 모델

클러스터의 **모든 파드는 고유한 IP 주소를 갖는다**. 이는 즉 파드간 연결을 명시적으로 만들 필요가 없으며 또한 컨테이너 포트를 호스트 포트에 매핑할 필요가 거의 없음을 의미한다. 이로 인해 포트 할당, 네이밍, 서비스 디스커버리, 로드 밸런싱, 애플리케이션 구성, 마이그레이션 관점에서 파드를 VM 또는 물리 호스트처럼 다룰 수 있는 깔끔하고 하위 호환성을 갖는 모델이 제시된다.

쿠버네티스 IP 주소는 파드 범주에 존재하며, 파드 내의 컨테이너들은 IP 주소, MAC 주소를 포함하는 네트워크 네임스페이스를 공유한다. 이는 곧 파드 내의 컨테이너들이 각자의 포트에 localhost로 접근할 수 있음을 의미한다. 또한 파드 내의 컨테이너들이 포트 사용에 있어 서로 협조해야 하는데, 이는 VM 내 프로세스 간에도 마찬가지이다. 이러한 모델은 "파드 별 IP" 모델로 불린다.



- 파드는 **NAT 없이 노드 상의 모든 파드와 통신**할 수 있다.
- 노드 상의 에이전트(예: 시스템 데몬, kubelet)는 해당 노드의 모든 파드와 통신할 수 있다.

## 서비스 만들어보기
```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx
  namespace: default
spec:
  selector:
    app: nginx # 배포하려는 파드를 지정합니다. 당연히 파드가 이미 실행중이어야 합니다.
  type: LoadBalancer
  ports:
  - name: nginx
    protocol: TCP
    port: 80
    targetPort: 80
```
 LoadBalancer로 서비스를 만들고, 백엔드에 cozserver라는 이름을 가진 파드 집합에 연결되도록 지정했습니다. 적용된 결과를 확인해 봅시다.
```bash
$ minikube tunnel

✅  Tunnel successfully started

📌  NOTE: Please do not close this terminal as this process must stay alive for the tunnel to be accessible ...
```
```bash
$  kubectl get svc 

NAME             TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
hello-minikube   NodePort    10.100.175.122   <none>        8080:32654/TCP   3h25m
kubernetes       ClusterIP   10.96.0.1        <none>        443/TCP          3h31m
```





#### Q. 서비스의 타입은 ClusterIP, NodePort, LoadBalancer, ExternalName 네 가지가 있습니다. 이들은 어떻게 다른가요?


Kubernetes의 네 가지 서비스 유형인 `ClusterIP`, `NodePort`, `LoadBalancer` 및 `ExternalName`은 서로 다른 용도로 사용되며 고유한 특성이 있습니다.


#### 1. Cluster IP :


- **ClusterIP 서비스 유형은 Kubernetes의 기본 서비스 유형**입니다.
- **클러스터 내에서만 액세스할 수 있는 클러스터 내부 IP 주소에 서비스를 노출**합니다.
- 클러스터 내 다른 내부 서비스에 대한 서비스 검색 및 로드 밸런싱을 제공합니다.
- 이 서비스 유형은 클러스터 내의 **서로 다른 구성 요소 또는 마이크로 서비스 간의 통신에 적합**합니다.

#### 2. NodePort :


- NodePort 서비스 유형은 클러스터에서 선택한 **각 노드의 정적 포트에 서비스를 노출**합니다.
- 클러스터 노드의 IP 주소와 지정된 정적 포트에 접근하여 **외부 트래픽이 서비스에 도달할 수 있도록** 합니다.
- 이 서비스 유형은 일반적으로 **개발 또는 테스트 목적**으로 **서비스를 외부에 노출**해야 하는 시나리오에 유용합니다.
- **외부 로드 밸런서 없이도** **클러스터 외부에서 서비스에 접근할 수 있는 방법**을 제공합니다.

#### 3. LoadBalancer :


- LoadBalancer 서비스 유형은 외부 로드 밸런서(예: 클라우드 공급자의 로드 밸런서)를 프로비저닝하여 서비스에 트래픽을 분산합니다.
- 자동으로 공인 IP 주소를 서비스에 할당하고 트래픽을 서비스로 라우팅하도록 외부 로드 밸런서를 구성합니다.
- 주로 인터넷이나 외부 클라이언트에 서비스를 노출해야 하고 부하 분산 기능이 필요할 때 사용하는 서비스 유형입니다.
- 일반적으로 트래픽을 여러 포드 또는 노드에 분산해야 하는 프로덕션 환경에서 사용됩니다.

#### 4. ExternalName :

쿠버네티스를 사용하지 않겠다 : headless를 쓰면 됨

- ExternalName 서비스 유형을 사용하면 **외부 서비스를 DNS 이름에 매핑**하여 별칭을 제공할 수 있습니다.
- 클러스터 내에서 서비스를 생성하는 대신 단순히 외부 서비스의 DNS 이름을 가리키는 **DNS CNAME** 레코드 역할을 합니다.
- 이 서비스 유형은 **부하 분산 또는 프록시 기능을 제공하지 않으며 주로 외부 서비스와의 통합에 사용**됩니다.
- 클러스터 내에서 DNS 이름으로 외부 서비스를 참조할 수 있어 **외부 시스템과 원활한 통신**이 가능합니다.


각 서비스 유형은 특정 용도에 사용되며 애플리케이션 및 인프라 설정의 요구 사항에 따라 다양한 기능을 제공합니다. 적절한 서비스 유형을 선택하여 클러스터 내외에서 서비스의 접근성과 가용성을 제어할 수 있습니다.