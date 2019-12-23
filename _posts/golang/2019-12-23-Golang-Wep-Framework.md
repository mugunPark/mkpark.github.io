---
title: "Go에서 인기있는 7가지 웹 프레임워크 "
classes: wide
excerpt: "[원문] 7 popular web frameworks in Go"
header:
  overlay_image: /assets/images/develop-unsplash.jpg
  overlay_filter: rgba(255, 255, 255, 0.3)
  actions:
    - label: "Go to"
      url: "https://deepsource.io/blog/go-web-frameworks/"
categories:
  - Golang
tags:
  - web framework
  - 
last_modified_at: 2019-12-21T22:00:00
---

Golang(Google's Go programming language)이 출시 이후 Golang은 APIs 와 웹 서비스를 작성하기위한 파워풀하고 인기있는 옵션이 되었습니다. 아래 목록은 Go에서 가장 인기있는 7가지 웹 프레임워크 입니다.

# 1.Gin

Gin은 최대 40배 더빠른 성능을 제공하는 Martini 같은 API를 특징으로하는 HTTP 웹 프레임워크 입니다.
-만약 광장한 성능이 필요하다면 Gin을 선택하세요.

## 특징

* **빠른 성능** : [Radix Tree](https://en.wikipedia.org/wiki/Radix_tree) 기반의 라우팅, 작은 메모리 사용. No reflection. 에측 가능한 API 성능

* **Middleware 지원** : HTTP 요청은 middleware 체인과 최종 조치에 의해 처리할 수 있습니다. 예를 들어, Logger, Authorization, GZIP 그리고 마지막으로 DB 관련 작업들.

* **Crash-free** : Gin은 HTTP 요청 중에 발생한 panic을 catch하고 recover 할 수 있습니다. 이것은 서버가 항상 사용가능하게 되는 것입니다.

* **JSON 검사** : Gin은 요청의 JSON을 파싱 및 검사 할 수 있습니다. 예를들어 요청된 값의 존재 여부 확인

* **Route 그룹** : Route들을 더 좋게 구성하십시오. Authorization은 요구 될 수도 있고 요구되지 않을 수 도있는 서로다은 API 들이 있을 수 있습니다. 추가로 그룹은 성능 저하 없이 무제한으로 중첩 할 수 있습니다. 

* **오류 관리** : Gin은 HTTP요청 중에 발생하는 모든 오류를 수집하는 편리한 방법을 제공합니다. 결국 middleware는 로그 파일, DB에 오류를 기록하고, 네트워크를 통해 보낼 수 있습니다. 

* **빌트인 Rendering** : Gin은 JSON, XML, HTML을 위한 API를 쉽게 사용할 수 있도록 합니다.

* **확장성** : 새로운 middleware를 생성하는 것은 매우 간단합니다. 샘플 코드를 확인해보세요.

### 설치 (Go v 1.10 이상 요구됨)

```bash
  go get -u githubcom/gin-gonic/gin
```

### Hello world 예제

```go
package main

import "github.com/gin-gonic/gin"

func main() {
  r := gin.Default()
  r.GET("/ping", func(c *gin.Context) {
    c.JSON(200, gin.H{
      "message" : "pong",
    })
  })
  r.Run() // listen and serve on 0.0.0.0:8080 (for windows "localhost:8080") 
}
```

**소스** : [github.com/gin-gonic/gin](https://github.com/gin-gonic/gin) 
**문서** : [gin-gonic.com/docs/](https://gin-gonic.com/docs/)


# 2.Beego

Beego는 REST API, 웹 앱, 백엔드 서비스의 빠른 개발에 사용됩니다. Beego `Tornado`, `Sinatra`와 `Flask`에서 영감을 받았습니다. Beego는 interface와 struct 임베딩과 같은 일부 Go의 특징을 가지고 있습니다.

## 특징

* RESTful 지원, MVC 모델 그리고 코드 핫 컴파일, 테스트 자동화, 자동화된 패키징과 배포를 포함하는 앱을 빠르게 빌드하기위한 bee 툴을 사용

* 지능적인 라우팅과 모니터링으로 OPS, 메모리와 CPU 사용량 그리고 gorourin 상태를 모니터 할 수 있습니다. 온라인 앱의 전체 제어할 수 있습니다.

* 세션 제어, 캐싱, 로깅, 설정 파싱, 성능 관리, 컨택스트 핸들링, ORM 지원 그리고 요청 시뮬레이션 을 포함하는 강력한 빌트인 모듈이 있습니다. 모든 유형의 어플리케이션을 위한 강력한 기반을 얻을 수 있습니다.

* 요청과 goroutine의 효과적으로 처리를 핸들링하는 native Go http package를 사용합니다. beego 어플리케이션은 많은 트레픽을 처리할 수 있습니다.


### 설치

```bash
  go get -u github.com/astaxie/beego
```

### Hello world 예제

```go
package main

import "github.com/astaxie/beego"

func main() {
  beego.Run()
}
```

**소스** : [github.com/astaxie/beego](http://github.com/astaxie/beego)
**문서** : [beego.me/docs/intro/](https://beego.me/docs/intro/)

# 3.Echo

Echo는 높은 성능과 최소환 웹 프레임워크로 자리합니다.

## 특징

* **최적화된 Route** : route를 지능적으로 우선 순위화 하는 동적 메모리 할당이 없는 고도로 최적화된 HTTP 라우터

* **확장성** : 그룹에서 쉽게 구성되는 강력하고 확장하는한 RESTful API 빌드

* **자동 TLS** : Encrypt해서 TLS 인증서를 자동으로 설치합니다.

* **HTTP/2 지원** : HTTP2/ 지원은 속도를 향상시키고 더 나은 사용자 경험을 제공합니다.

* **Middleware** : 많은 빌트인 middleware를 사용하거나 정의할 수 있습니다. Middleware는 root, 그룹 또는 라우터 레벨에 설정할 수 있습니다.

* **데이터 바인딩** : JSON, XML 또는 from-data를 포함하는 HTTP 요청 paylod의 데이터 바인딩

* **데이터 Rendering** : JSON, XML, HTML, File, Attachment, Inline, Stream 또는 Blob를 포함하는  다양한 HTTP response를 보내는 API

* **Templating** : 모든 템플릿 엔진을 사용하는 템플릿 랜더링

* **확장성** : 개인화된 중앙 HTTP 오류 처리. 쉽게 확장가능한 API

### 설치

```bash
  go get -u github.com/labstack/echo
```

### Hello world 예제

```go
package main

import (
  "net/http"
  "github.com/labstack/echo/v4"
  "github.com/labstack/echo/v4/middleware"
)

func main() {
  // Echo instance
  e := echo.New()

  // Middleware
  e.Use(middleware.Logger())
  e.Use(middleware.Recover())

  // Routes
  e.GET("/", hello)

  // Start server
  e.Logger.Fatal(e.Start(":1323"))
}

// Handler
func hello(c echo.Context) error {
  return c.String(http.StatusOK, "Hello, World!")
}
```

**소스** : [github.com/labstack/echo](http://github.com/labstack/echo)
**문서** : [echo.labstack.com/guide](https://echo.labstack.com/guide)


# 4.GO kit

Go-kit은 마이크로 서비스를 만들기위한 프로그래밍 툴킷입니다.

## 특징

* SOA (Service Oriented Architecture) - 대부분 Go-kit이 나닌 서비스와 상호 작용 할 것입니다.

* 기본 메시징 패턴과 같은 RPC(Remote Procedure Call)

* 플러그 가능한 직렬화 및 전송 - HTTP로 주고받는 JSON 만이 아님

* 특정 기술 또는 도구를 요구하지 않고 기존 인프라 내에서 동작합니다.

### 설치

```bash
  go get -u github.com/go-kit/kit
```

**예제** : [godoc.org/github.com/go-kit/kit/examples](https://godoc.org/github.com/go-kit/kit/examples)

**소스** : [github.com/go-kit/kit](http://github.com/go-kit/kit)

**문서** : [godoc.org/github.com/go-kit/kit](https://godoc.org/github.com/go-kit/kit)

# 5.Fast HTTP

Fast HTTP 패키지는 hot path에서 메모리 할당 없이 높은 성능을 위해 조정되었습니다. net/http 보다 10배 빠릅니다.


# 특징

* **속도에 최적화** : 하드웨어에서 100k 이상의 qps(초당 쿼리 수)와  1M 이상의 동시 연결을 쉽게 처리할 수 있습니다.

* 낮은 메모리 사용량을 위한 최적화

* 서버는 클라이언트 IP당 동시 연결, 연결당 요청 수 등 많은 anti-Dos 제한을 제공합니다.

* Fast HTTP API는 이미 존재하는 클라이언트와 서버 구현을  확장하거나 커스텀 클라이언트 서버 구현을 작성할 수 있는 기능으로 설계되었습니다.

### 설치

```bash
  go get -u github.com/valyala/fasthttp
```

Helo world 예제

```go
package main

import (
	"flag"
	"fmt"
	"log"

	"github.com/valyala/fasthttp"
)

var (
	addr     = flag.String("addr", ":8080", "TCP address to listen to")
	compress = flag.Bool("compress", false, "Whether to enable transparent response compression")
)

func main() {
	flag.Parse()

	h := requestHandler
	if *compress {
		h = fasthttp.CompressHandler(h)
	}

	if err := fasthttp.ListenAndServe(*addr, h); err != nil {
		log.Fatalf("Error in ListenAndServe: %s", err)
	}
}

func requestHandler(ctx *fasthttp.RequestCtx) {
	fmt.Fprintf(ctx, "Hello, world!\n\n")
}
```

**소스** : [github.com/valyala/fasthttp](http://github.com/valyala/fasthttp)
**문서** : [godoc.org/github.com/valyala/fasthttp](https://godoc.org/github.com/valyala/fasthttp)

# 6.Mux

Mux(gorilla)는 요청을 해당 해들러에 일치 시키기위해 요청 라우터 및 디스패처를 구현합니다.

# 특징

* http.Handler 인터페이스를 구현함으로써 표순 http.ServeMux와 호환가능합니다.

* 요청은 URL host, path, path prefix, scheme, header, query 값, HTTP 메소드 또는 사용하는 커스텀 Matcher에 기반하여 일치시킬 수 있습니다.

* URL host, path 그리고 query 값은 선택적 정규 표현을 값으로 가질 수 있습니다.

* 등록된 URL들을 만들 거나, 되돌릴 수 있어 리소스에 대한 참조를 유지하는데 도움이됩니다. 

* Route는 subrouter로도 사용될 수 있습니다. 중접된 Route는 단지 부모 route가 일치하는 경우에만 테스트 됩니다. 이것은 host, path prefix 또는 다른 반복되는 속성과 같은 일반 조건을 공유하는 Route 그룹을 정의하는 것에 유용합니다. 추가로 요청 매칭을 최적화 합니다.

### 설치

```bash
  go get -u github.com/gorilla/mux
```

### Hello world 예제

```go
func main() {
    r := mux.NewRouter()
    r.HandleFunc("/articles", ArticlesHandler)
    http.Handle("/", r)
}

func ArticlesCategoryHandler(w http.ResponseWriter, r *http.Request) {
    vars := mux.Vars(r)
    w.WriteHeader(http.StatusOK)
    fmt.Fprintf(w, "Category: %v\n", vars["category"])
}
```

**소스** : [github.com/gorilla/mux](http://github.com/gorilla/mux)
**문서** : [gorillatoolkit.org/pkg/mux](https://gorillatoolkit.org/pkg/mux)

# 7.HttpRouter

HttpRouter는 경량의 높은 성능 HTTP 요청 라우터 입니다. (또는 multiplexer 또는 작은 mux로 불립니다)

Go의 net/http 패키지의 기본 mux와 달리 이 라우터는 라우팅 패턴의 변수를 지원하며 요청 메소드에 일치합니다. 또한 더 잘 확장할 수 있습니다.

# 특징

* **오직 명시적 일치**: http.ServeMux와 같은 다른 라우터에서는 요청된 URL path가 여러 패턴과 일치 할 수 있습니다. 그러므로 그것들은 "가장 긴 일치" 또는 "처음 등록된", "처음 일치된" 것과 같은 일부 어색한 패턴 우선순위 규칙이 있습니다. 이 라우터의 디자인으로 요청은 오직 완변하게 하나 일치 되거나 일치하는 라우터가 없을 수 있습니다. 그결과로 의도하지않은 일치가 없으므로 SEO에 적합하고 사용자 경험이 향샹됩니다.

* **마지막 /는 신경쓰지 마십시오** : 좋아하는 URL 스타일은 선택하십시오. 라우터는 마지막'/' 가 없거나 있을 경우 자동적으로 클라이언트를 리디렉션 합니다. 물론 새로운 경로의 핸들러가 있는 경우에만 해당됩니다. 만약 원하지 않는 다면 이 동작을 해제 할 수 있습니다.

* **Path 자동 수정** : 추가 비용없이 마지막 '/'의 누락을 확인하거나 추가하는 것 외에도, 라우터는 또한 잘못된 경우 고치고 불필요한 경로 요소(../ 또는 //)를 제거할 수 있습니다. 사용자가 CAPS LOCK를 사용한 경우에 HttpRouter는 대소문자를 구분하지 않고조회하고 올바른 URL로 리디렉션하여 도움을 줄 수 있습니다.

* **라우팅 패턴 매개 변수** : 요청된 URL path 파싱을 하지말고 path 세그먼트에 이름을 지정하면 라우터가 동적으로 값을 제공합니다. 라우터 설계로 인해 path 매개 변수는 매우 저렴합니다.

* **Zero Garbage** : 매칭과 디스패치하는 프로세스는 0 바이트의 garbage를 생성합니다. 단지 heap 할당 path 메개 변수의 key-value slice를 빌드하고, 새로운 context과 요청 객체를 빌드하는 것입니다(후자는 표준 Handler/HandlerFunc API 에서만). 3개의 인자를 갖는 API에서 요청 path에 매개 변수가 없는 경우 단일 heap 할당은 필요하지 않습니다.

* **성능** : [Benchmarks speak for themselves](https://github.com/julienschmidt/go-http-routing-benchmark).

* **더 이상 서버 충돌이 없습니다** : HTTP 요청을 처리하는 중에 발생한 panics을 처리하는 Panic 핸들러를 설정할 수 있습니다. 라우터는 이때 복구하고 PanicHandler 무슨일이 일어났는지 기록하고 오류 페이지로 이동시킬수 있습니다.

* **API에 적합** : 라우터 설계는 합리적이고 계증적인 RESTful API를 구축하도록 권장합니다. 게다가  `OPTIONS` 요청과 `405 Method Not Allowed` 응답에 대한 기본 지원 기능이 내장되어 있습니다.


### 설치

```bash
  go get -u github.com/julienschmidt/httprouter
```

### Hello world 예제

```go
package main

import (
    "fmt"
    "net/http"
    "log"

    "github.com/julienschmidt/httprouter"
)

func Index(w http.ResponseWriter, r *http.Request, _ httprouter.Params) {
    fmt.Fprint(w, "Welcome!\n")
}

func Hello(w http.ResponseWriter, r *http.Request, ps httprouter.Params) {
    fmt.Fprintf(w, "hello, %s!\n", ps.ByName("name"))
}

func main() {
    router := httprouter.New()
    router.GET("/", Index)
    router.GET("/hello/:name", Hello)

    log.Fatal(http.ListenAndServe(":8080", router))
}
```

**소스** : [github.com/julienschmidt/httprouter](https://github.com/julienschmidt/httprouter)
**문서** : [godoc.org/github.com/julienschmidt/httprouter](https://godoc.org/github.com/julienschmidt/httprouter)
