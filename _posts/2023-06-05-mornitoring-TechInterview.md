---
date: 2023-06-05 00:00:05
layout: post
title: 서비스 모니터링 기술면접
subtitle: 기술면접
description: 서비스를 운영하는 데 있어서, 사용자에게 필요한 적정 수준을 정의하고 제공하기 위해, 서비스 제공자와 사용자는 서로 서비스 수준 협약(Service Level Agreements, SLA)을 맺습니다.
image: https://res.cloudinary.com/dvqcvocet/image/upload/v1685689813/%E1%84%91%E1%85%B3%E1%84%85%E1%85%A9%E1%84%86%E1%85%A6%E1%84%90%E1%85%A6%E1%84%8B%E1%85%AE%E1%84%89%E1%85%B3_zt9ivp.png
optimized_image: https://res.cloudinary.com/dvqcvocet/image/upload/v1685689813/%E1%84%91%E1%85%B3%E1%84%85%E1%85%A9%E1%84%86%E1%85%A6%E1%84%90%E1%85%A6%E1%84%8B%E1%85%AE%E1%84%89%E1%85%B3_zt9ivp.png
category: mornitoring
author: Hoonology
paginate: true
---
- 모니터링 시스템에는 메트릭 수집을 위한 두 가지 방식의 메커니즘이 존재합니다. 바로 Pull 방식과 Push 방식입니다. 프로메테우스는 어떤 방식의 메커니즘을 사용하나요? 또한 Pull 방식과 Push 방식은 어떻게 다르며, 장단점은 무엇인지, 또한 해당 방식을 사용하는 모니터링 도구는 어떤 것들이 있는지 연구해 보세요.

프로메테우스는 Pull 방식의 메커니즘을 사용합니다. Pull 방식은 프로메테우스 서버가 주기적으로 모니터링 대상에게 HTTP 요청을 보내어 메트릭 데이터를 수집하는 방식입니다.

Pull 방식과 Push 방식의 주요한 차이점은 다음과 같습니다:

Pull 방식:

모니터링 시스템(예: 프로메테우스)이 데이터를 수집하기 위해 활동적으로 모니터링 대상을 조회합니다.
대상은 HTTP 엔드포인트를 노출하고, 프로메테우스는 이 엔드포인트에 쿼리를 보내어 메트릭을 가져옵니다.
데이터 수집 주기는 모니터링 시스템에서 제어됩니다.
Pull 방식을 사용하는 모니터링 도구 예시: 프로메테우스, 나기오스, 자빅스.
Push 방식:

모니터링 대상(예: 애플리케이션, 서비스)이 직접 메트릭 데이터를 모니터링 시스템으로 푸시합니다.
대상은 주기적으로 메트릭 데이터를 모니터링 시스템에 지정된 엔드포인트로 전송합니다.
데이터 전송 주기는 대상에서 제어됩니다.
Push 방식을 사용하는 모니터링 도구 예시: 그래파이트, 인플럭스디비, 데이터독.
Pull 방식과 Push 방식의 장단점은 다음과 같습니다:

Pull 방식:

장점:
데이터 수집 주기를 모니터링 시스템이 제어할 수 있습니다.
대상에 대한 네트워크 부하가 줄어듭니다. 데이터를 모니터링 시스템이 가져오기 때문입니다.
동적인 환경에서 대상이 자주 변경되는 경우에 적합합니다.
단점:
데이터를 활동적으로 조회해야 하므로 모니터링 시스템의 리소스 사용이 높아집니다.
수집 주기가 긴 경우 데이터 수집에 지연이 발생할 수 있습니다.
Push 방식:

장점:
메트릭 데이터를 모니터링 시스템으로부터 받기 때문에 모니터링 시스템의 리소스 사용이 줄어듭니다.
대상이 메트릭 데이터를 푸시하는 즉시 데이터가 사용 가능합니다.
단점:
대상이 메트릭 데이터를 푸시하도록 설정해야 하므로 추가적인 설정이 필요할 수 있습니다.


Pull 방식의 장단점:

장점:

데이터 수집 주기를 모니터링 시스템이 제어할 수 있습니다. 이는 데이터를 정확한 시간 간격으로 수집할 수 있음을 의미합니다.
모니터링 시스템이 대상에 대한 네트워크 부하를 줄일 수 있습니다. 대상은 요청이 있을 때만 응답하면 되기 때문입니다.
동적인 환경에서 대상이 자주 변경되는 경우에 유연하게 대응할 수 있습니다.
단점:

모니터링 시스템의 리소스 사용이 높아집니다. 데이터를 수집하기 위해 주기적으로 대상에게 요청해야 하기 때문입니다.
수집 주기가 긴 경우 데이터 수집에 지연이 발생할 수 있습니다.
Push 방식의 장단점:

장점:

대상이 메트릭 데이터를 푸시하면 즉시 데이터가 사용 가능합니다. 실시간 모니터링이 가능하며, 데이터의 신속한 반응이 필요한 경우에 유리합니다.
모니터링 시스템의 리소스 사용이 줄어듭니다. 데이터를 수집하기 위해 주기적인 요청을 보내지 않아도 되기 때문입니다.
단점:

대상이 메트릭 데이터를 푸시하도록 설정해야 합니다. 대상에 대한 추가적인 설정이 필요할 수 있습니다.
대상이 메트릭 데이터를 푸시하는 주기를 관리해야 합니다. 과도한 푸시로 인해 대상에게 부하가 발생할 수 있습니다.
각 방식의 장단점을 고려하여 적절한 모니터링 시나리오에 적용해야 합니다.



Pull 방식과 Push 방식을 사용하는 대표적인 모니터링 도구는 다음과 같습니다:

Pull 방식을 사용하는 모니터링 도구:

Prometheus: 오픈소스 기반의 모니터링 및 경고 도구로, Pull 방식을 사용하여 데이터를 수집합니다.
Nagios: 인프라 및 서비스 상태를 모니터링하기 위한 널리 사용되는 Pull 방식의 도구입니다.
Zabbix: 서버, 네트워크, 애플리케이션 등의 모니터링을 위한 Pull 방식의 도구로, 다양한 기능을 제공합니다.
Push 방식을 사용하는 모니터링 도구:

Graphite: 시계열 데이터를 수집, 저장 및 시각화하기 위한 Push 방식의 모니터링 도구입니다.
InfluxDB: 실시간 데이터 처리를 위한 오픈소스 시계열 데이터베이스로, Push 방식을 사용하여 데이터를 수집합니다.
Datadog: 클라우드 인프라, 애플리케이션 및 서비스를 모니터링하기 위한 Push 방식의 모니터링 및 분석 도구입니다.
이외에도 많은 모니터링 도구들이 Pull 방식이나 Push 방식 중 하나를 사용하거나, 둘을 모두 지원하는 경우도 있습니다. 선택할 모니터링 도구는 요구 사항과 환경에 따라 달라질 수 있으며, 각 도구의 기능, 성능, 커뮤니티 지원 등을 고려하여 결정해야 합니다.



- 어떤 조직의 SLO가 다음과 같습니다. "GET 호출의 99%는 10ms 이내에 수행되어야 한다" 그렇다면, 이러한 SLO를 달성하려면 어떤 메트릭을 수집하고 어떻게 계산해야 할까요? (척도는 표준화된 범용 지표를 사용합니다)


해당 SLO를 달성하기 위해 수집해야 할 메트릭과 계산 방법은 다음과 같습니다:

응답 시간 메트릭 수집:

GET 호출의 응답 시간을 측정하는 메트릭을 수집해야 합니다.
각 GET 호출에 대한 응답 시간을 측정하여 기록합니다.
백분위수(Percentile) 계산:

수집한 응답 시간 메트릭을 사용하여 백분위수를 계산합니다.
99번째 백분위수(99th percentile)를 계산하는 것이 목표입니다.
백분위수는 주어진 데이터 집합에서 특정 백분율 이하의 값을 가지는 데이터의 위치를 나타냅니다.
99번째 백분위수의 값 확인:

계산한 99번째 백분위수의 값이 10ms 이내에 있는지 확인합니다.
만약 99번째 백분위수의 값이 10ms 이내에 있다면, SLO를 달성한 것입니다.
그렇지 않은 경우, 조치를 취하여 응답 시간을 개선해야 합니다.
표준화된 범용 지표를 사용하는 경우, 대부분의 모니터링 도구에서는 응답 시간 데이터의 평균, 중앙값, 백분위수 등을 계산할 수 있습니다. 이러한 메트릭과 계산 기능을 활용하여 SLO를 달성하기 위한 응답 시간을 모니터링하고 평가할 수 있습니다.