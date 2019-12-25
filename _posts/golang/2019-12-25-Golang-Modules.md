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