---
title: "Go 모듈 튜토리얼"
classes: wide
excerpt: "[원문] Go Modules Tutorial"
header:
  overlay_image: /assets/images/develop-unsplash.jpg
  overlay_filter: rgba(255, 255, 255, 0.3)
  actions:
    - label: "Go to"
      url: "https://tutorialedge.net/golang/go-modules-tutorial/"
categories:
  - Golang
tags:
  - module
  - tutorial
last_modified_at: 2019-12-25T17:00:00
---

이 튜토리얼에서는 Go 모듈을 사용해서 Go 프로그램에서 의존성 작업을 단순화하는 방법을 살펴보겠습니다. Go 모듈을 사용해서 간단한 Go 프로그램을 개발하기 전에 Go 모듈이 어떻겍 동작하고 무슨 문제를 해결해주는지 살펴 볼 것입니다. 

# 목표

* Go 모듈의 완전한 이해

* Go 모듈을 사용한 Go package 빌드 가능


# 전제 조건

* Go version 1.11+

* Github 계정

# Go 모듈이 필요한 이유

몇 년 동안 Go 언어는 의존성 관리에 대한 많은 변화가 있었습니다. `dep`, `godep`, `govender` 등과 같은 이 문제를 한 번에 해결하기 위한 도구들이 등장하는 것을 보았습니다.

Go 모듈은 앞으로 Go 프로그램 내에서 의존성 관리를 위한 해결책의 공식적인 시도로 여기지게 됬습니다. 이것의 주된 이유 중 한 부분은 본질적으로 Go 개발자들이 Go 패키지의 시맨틱 버전 관리(semantic versioning)를 사용할 수 있도록 하였습니다.

시맨틱 버전 관리는 시맨틱 버전 숫자를 사용한 프로그램과 다양한 패키지 그리고 라이브러리들의 다른 버전이라는 것을 표시하는 관습이 매우 넓게 자리 잡고 있습니다. 이 숫자는  `v1.2.3` 와 같고 여기서 `1`은 Major 버전, `2`는 Minor 버전, `3`은 패치 버전을 의미 합니다.

* **Major 버전** :  특정 Major 버전의 모든 버전은 다른 Minor, 배치 버전의 하위 호환이 되어야 합니다. 버전 증가는 일반적으로 일부 주요 변경(breaking change)을 어떻게 반영해야하는지 패키지를 사용하는 다른 개발자에게 알려야합니다.

* **Minor 버전** : 개발자들은 프로그램 내에서 하위 호환성을 유지하면서 패키지에 새로운 기능 또는 Feature가 추가될 때 프로그램 또는 패키지의 Minor 버전을 증가 시키는 경향이 있습니다.

* **패치 버전** : 패치 버전은 일반적으로 버그 수정이 있을 때 사용됩니다. 개발자가 프로그램의 가벼운 이슈 또는 버그를 알린다면, 다시 하위 호환성 보장하면서 이슈를 수정할 것이고 새로운 버그 수정을 나타내는 패치 버전으로 증가할 것입니다.

> **Note** - [Proposal: Version Go Modules](https://go.googlesource.com/proposal/+/master/design/24301-versioned-go.md)


* 문제

`package A` 와같은 많은 주요 의존성을 가지는 Go 서비스를 개발하는 것을 생각해 보세요.  이제 `package A` 서비스를 작성할 때 설정된 인터페이스를 가지고 있으며 설정된 방식으로 작동합니다.

하지만, `package A`를 유지보수 담당자가 버그 수정 또는 기능 확장을 하면 무슨일이 발생합니까? 
운이 좋으면 변경사항이 프로그램에 영향을 미치지 않을 수 있습니다. 하지만 운이 좋지 않으면 그 변경이 프로그램을 종료 시킬 수 있습니다.

버전 관리를 사용함으로써 사용하길 원하고 언제든지 우리 패키지를 빌드하는 것을 보장하는 패키지 또는 라이브러리의 특정 버전을 선택할 수 있고 그것은 항상 특정한 버전을 사용할 수 있도록 합니다.

* 간단한 예제

Go 모듈로 의존성을 처리하는 간단한 Go 패키지를 만들 것입니다.


`go-modules-test`라는 프로그램을 실행할 수 있는 새 프로젝트를 만들어 보겠습니다.

```
> mkdir go-modules-test
> cd go-modules-test
```

다음으로 `go-modules-test`에서 프로젝트가 모듈을 사용하기위해서 초기화를 할 것입니다. `go mod init <모듈명>` 커맨드를 사용하면 됩니다.

```
> go mod init <모듈명>
```

실행이 종료되면 Go 프로그램의 모든 의존성을 포함하는 `go.mod` 파일이 생성될 것입니다.


이후 프로젝트 폴더에 `main.go` 파일을 새로 생성합니다.

```go
package main

import "fmt"

func main() {
  fmt.Println("Hello World")
}
```

`main.go` 파일을 실행합니다.

```
> go run main.go

> Hello World  // Output
```

# 의존성 추가

이제 기본 프로젝트가 초기화되고 go 모듈을 사용합니다. 한 걸음 더 나아가서 코드에 새로운 의존성을 추가하는 방법을 살펴 보겠습니다.

이 튜토리얼의 목적상 우리는 go 모듈을 사용하는 고급 읜존성 관리에 익숙해 질 수 있도록 많은 feature를 가지는 `"github.com/elliotforbes/test-package"` 패키지를 가져올 것입니다. 

`main.go` 파일의 위에 새로운 패키지를 import 하는 것으로 가져오기 될 것입니다. 패키지에 선언된 함수를 사용하는 것을 살펴보겠습니다.

```go
package main

import (
  "fmt"
  sample "github.com/elliotforbes/test-package"
)

func main() {
 fmt.Println("Hello World")
 sample.MySampleFunction() 
}
```

프로그램 위에 import 목록에 패키지를 추가했으므로 다시 `main.go` 파일을 실행 해보겠습니다.

```
> go run main.go

go: finding github.com/elliotforbes/test-package v2.0.0
go: downloading github.com/elliotforbes/test-package v2.0.0
go: extracting github.com/elliotforbes/test-package v2.0.0
Hello World
Version 2.0 of this Function
Hello World
```

놀랍게도, Go 모듈을 사용하고 프로그램에서 사용하는 외부 의존성을 가져오는 정말 간단한 Go 프로그램을 만들었습니다.

# Major , Minor 버전 처리

현재 상태 그대로, 프로그램에서 "github.com/elliotforbes/test-package" 패키지를 가져올 때, 가장 최신 버전을 가져왔을 것입니다.  가져올 버전을 세밀하게 제어하려면 `go.mod` 파일 내에 필요한 버전을 넣을 수 있습니다.

**go.mod**
```
module github.com/TutorialEdge/go-modules-tutorial

go 1.12

require github.com/elliotforbes/test-package v2.0.0

```

`v2.0.0` 에서 `v1.0.0`으로 버전을 수정한 후에 다시 `main.go`파일을 실행하면 다음과 같이 나타납니다.

```
> go run main.go

go: finding github.com/elliotforbes/test-package v1.0.0
go: downloading github.com/elliotforbes/test-package v1.0.0
go: extracting github.com/elliotforbes/test-package v1.0.0
Hello World
Version 1.0 of this Function
Hello World
```

제품 환경에서 사용하려면 패키지의 정확한 버전을 명시할 수 있습니다. 이를 통해 우리가 출시할 대상에 대한 확신을 갖게되고 프ㄹ그램을 제품 환경으로 최종 적용할 때 기본 패키지 변경으로 인해 치명적으로 중단되지 않습니다.















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

