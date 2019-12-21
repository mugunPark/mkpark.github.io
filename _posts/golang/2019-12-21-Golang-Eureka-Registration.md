---
title: "Golang과 Eureka의 서비스 등록 패턴"
classes: wide
excerpt: "[원문] Service Registration Pattern With Golang and Eureka"
header:
  overlay_image: /assets/images/develop-unsplash.jpg
  overlay_filter: rgba(255, 255, 255, 0.3)
  actions:
    - label: "Go to"
      url: "https://dzone.com/articles/service-registration-with-go-lang-and-eureka"
categories:
  - Golang
tags:
  - microservice
  - eureka
last_modified_at: 2019-12-21T22:00:00
---

마이크로서비스 아키텍처에 대해서 이야기할 때, 여러 작은 서비스가 함께 작동하는 것으로 생각합니다. 각각은 독립적이고 분되어 따로따로 개발될 수 있습니다.

대부분의 경우, 많은 서비스 인스턴스와 위치를 동적으로 변경합니다. 가상 머신과 컨테이너는 일반적으로 동적 IP 주소가 할당됩니다. 또한 auto-scaling의 경우, 기본 플랫폼은 
로드 또는 다른 요인에 맞춰 인스턴스 수를  조정합니다.

이것은 문제를 일으킵니다. : 서비스 그리고/또는 라우터들의 클라이언트가 사용 가능한 서비스 인스턴스(IP와 PORT)에 대해 어떻게 알 수 있습니까?

이 문제에 대한 솔루션은 서비스 데이터베이스, 인스턴스, 서비스의 위치인 서비스 레지스트리를 구현하는 것입니다. 인스턴스들은 시작될 때 레지스트리에 등록되고, 종료될 때 등록 해제 됩니다.
클라이언트 그리고/또는 라우터들은 사용가능한 서비스의 인스턴스를 찾기 위해 레지스트리에게 확인합니다.

상업용 그리고 오픈 소스 모두에서 여러 가지 구현이 가능합니다. 몇 가지 에를 보여줍니다.

* [Nexflix Eureka](https://github.com/Netflix/eureka)

* [etcd](https://github.com/etcd-io/etcd)

* [consul](https://www.consul.io/)

* [Apache Zookeeper](http://zookeeper.apache.org/)

대부분 사용가능한 서비스 레지스트리 구현은 등록하고 인스턴스를 확인하는 인터페이스(일반적으로 REST API)를 제공합니다. 인스턴스의 라이프사이클은 다음 세 가지 단계로 묘사 될 수 있습니다.

1. **레지스터(Register)**: 첫 단계에서, 마이크로서비스 인스턴스는 서비스 레지스트리에 스스로 등록하는 것이 필요합니다.

2. **Heartbeat**: 인스턴스는 미리 정의된 간격을 두고 heartbeat를 전송합니다. 레지스트리가 특정 시간동안 인스턴스의 heartbeat를 받지 못하면, 그때 인스턴스가 종료된 것으로 표시하고, 설정에 따라 인스턴스를 제거합니다.

3.**Deregister/Down**: 인스턴스가 종료되거나 강제 종료되면, 인스턴스는 서비스 레지스트리로부터 스스로 등록 해제해야 합니다. 이후 레지스트리가 인스턴스가 종료한것으로 표시하고, 인스턴스로의 모든 요청은 중지됩니다.

![image-center]({{ site.url }}{{ site.baseurl }}/assets/images/Register Heatbeat Down.png){: .align-center}

대부분의 레지스트리들은 plug-and-play 클라이언트 라이브러리를 제공하여, 전체 프로세스 구현에 요구되는 노력을 줄입니다. **Netflix Eureka**는 Java 클라이언트 라이브러리를 제공하는 오픈 소스 서비스 레지스트리 입니다. Spring과 완벽하게 동작하고, 넓게 사용됩니다.

마이크로서비스의 주요한 이점중 하나는 특정한 프로그래밍 언어 또는 기술에 묶이지 않는다는 것입니다. Eureka는 또한 통신을 위한 REST 인터페이스를 제공합니다. 

이제 Netflx Eureka 레지스트리에 등록하는 Golang으로 서비스 구현을 시작할 것입니다. 이것을 이루기 위해서는 아래 흐름으로 구현해야합니다.

![image-center]({{ site.url }}{{ site.baseurl }}/assets/images/Go Eureka Implement Flow.png){: .align-center}

*"MY_AWSOME_GO_MS "* 이름의 어플리케이셔을 동록을 해보겠습니다.  로컬 머신에서 실행하는 Eureka 레지스트리가 있습니다.

1. 입력: 레지스트리의 세부 사항을 입력으로 제공해야합니다. 환경 변수로 부터 읽을 것입니다.
  
  * **REGISTRY_TYPE**: "NETFLIX_EUREKA" 값을 포함해야합니다.

  * **REGISTRY_URL**: 8761 포트로 로컬에서 Netflix Eureka 레지스트리는 실행되고 있을 것입니다. 그렇기 때문에 값은 *"http://localhost:8761/eureka/apps/"* 일 것입니다.

  * **REGISTRY_USER**: 레지스트리가 확보되었으면 사용자 이름은 인증에 사용됩니다.

  * **REGISTRY_PASSWORD**: 사용자 비밀번호

2. 인스턴스가 실행중인 동안 인스턴스는 URL *"http://localhost:8761/eureka/apps/{SERVICE_NAME}"*를 호출하는 것으로 등록됩니다. 이 경우 URL은 *"http://localhost:8761/eureka/apps/MY_AWSOME_GO_MS"* 일 것입니다. 이것은 다음과 같이 본문에 대한 POST 요청 입니다.

```json
{
    "instance": {
        "hostName": "MY_HOSTNAME", #hostname of my machine
        "app": "org.github.hellosatish.microservicepattern.awesomeproject",
        "vipAddress": "org.github.hellosatish.microservicepattern.awesomeproject",
        "secureVipAddress": "org.github.hellosatish.microservicepattern.awesomeproject"
        "ipAddr": "10.0.0.10",
        "status": "STARTING",
        "port": {"$": "8080", "@enabled": "true"},
        "securePort": {"$": "8443", "@enabled": "true"},
        "healthCheckUrl": "http://MY_HOSTNAME:8080/healthcheck",
        "statusPageUrl": "http://MY_HOSTNAME:8080/status",
        "homePageUrl": "http://MY_HOSTNAME:8080",
        "dataCenterInfo": {
            "@class": "com.netflix.appinfo.InstanceInfo$DefaultDataCenterInfo", 
            "name": "MyOwn"
        },
    }
}
```

3. 프로젝트는 요청을 처리할 준비가되면, 인스턴스 상태를 UP으로 업데이트해야 합니다. 이는 레지스트리에 HTTP POST 요청을 다시 보내면 됩니다. 이 요청 본문은 status를 **UP**으로 하는 것을 제외하면 동일합니다.

4. Eureka는 30초 마다 인스턴스로 부터 heartbeat를 받습니다. heartbeat는 *"http://localhost:8761/eureka/apps/{APP_NAME}/{INSTANCE_ID}"* URL을 호출하여 HTTP PUT을 통해 보내집니다. 기본적으로 인스턴스 ID는 어플리케이션의 *hostname*이고, 그렇기 때문에 URL은 *"http://localhost:8761/eureka/apps/MY_AWSOME_GO_MS/MY_HOSTNAME"* 일 것 이빈다.

5. 인스턴스를 종료해야한다면, status를 DOWN으로 업데이트를 해야합니다. 그 요청 본문은 status를 **STARTING** 또는 **UP**으로 동록에 사용한것과 동일하지만 status에 **DOWN**을 사용합니다.


Eureka가 90초 동안 heartbeat를 받지 않는다면 자동적으로 레지스트리에서 인스턴스를 제거합니다. 어플리케이션을 종료하고 90초 이후가 되면 레지스트리에서 인스턴스가 제거된것을 볼 수 있습니다. 

**CODE**
[GitHub repository](https://github.com/hellosatish/microservice-patterns/tree/master/awesomeProject).

