---
title: "표준 Go 프로젝트 레이아웃"
classes: wide
excerpt: "[원문] Standard Go Project Layout"
header:
  overlay_image: /assets/images/develop-unsplash.jpg
  overlay_filter: rgba(255, 255, 255, 0.3)
  actions:
    - label: "Go to"
      url: "https://github.com/golang-standards/project-layout"
categories:
  - Golang
tags:
  - Project Layout

last_modified_at: 2020-01-07T20:00:00
---

Go 응용 프로그램 프로젝트의 기본 레이아웃입니다. 핵심 Go dev 팀이 정의한 공식 표준이 아닙니다.

# Go 디렉터리

## `/cmd`

프로젝트의 메인 응용 프로그램들.

각 응용 프로그램의 디렉터리 이름은 실행 파일 이름과 일치해야합니다. (예. /cmd/myapp)

응용 프로그램 디렉터리에 많은 코드를 넣지 마세요. 코드가 다른 프로젝트에서 사용되거나 import될 수 있다고 생각한다면, 그 코드는 `/pkg`에 추가하십시오. 코드가 재사용가능 하지 않거나 다른 곳에서 재사용 하길 원하지 않는 다면 `/internal` 디렉터리에 추가 하십시오. 코드의 목적에 대해 주석으로 남기십시오.

`/internal`, `\pkg`등으로부터 코드를 가져와서 호출하는 main 함수를 가지는 것이 일반적입니다.

[/cmd](https://github.com/golang-standards/project-layout/blob/master/cmd/README.md) 디렉토리를 참조하십시오.

## `/internal`

private 응용 프로그램과 라이브러리 코드 입니다. 다른 응용 프로그램 또는 라이브러리에서 import하는 것을 원하지 않는 코드 입니다. 레이아웃 패턴은 Go 컴파일러 자체에 의해 시행됩니다. 자세한 내용은 Go 1.4 [릴리즈 노트](https://golang.org/doc/go1.4#internalpackages)를 확인하세요. 최상위 internal 디렉터리로 제한되지 않습니다. 프로젝트 트리 중 어떠한 레벨에서 하나 이상의 internal 디렉터리를 가질 수 있습니다.

선택적으로 internal 패키지에 약간의 구조를 추가하여 공유 및 비공유 internal 코드를 분리할 수 있습니다. 그것은 작은 프로젝트에서는 특별히 요구되지 않지만 의도된 패키지 사용을 보여주는 시각적 단서가 있느느 것이 좋습니다. 실제 응용 프로그램 코드는 `/internal/app` 디렉터리에 있을 수 있고(예. /internal/app/myapp) `/internal/pkg` 디렉터리 안에서 코드가 공유 될 수 있습니다(예. /internal/pkg/myprivlib).

{% capture notice-text %}
  Go의 패키지 시스템은 경계가 명확한 컴포넌트로 프로그램 구조를 쉽게 만들지만 로컬(export X) 및 글로벌(export O)의 두가지 접근 방식만 있습니다. 예를 들어 public 저장소의 일부이지만 자신이 속한 프로그램 외부에서 사용하는것을 의도하지 않는 코드에 대한 인터페이스를 사용할 수 없도록 내보내지 않는 컴포넌트를 만들고 싶은 경우가 있습니다.

  Go 언어는 이 구분을 강제하는 기능은 없지만  Go 1.4의 go 커맨드는 패키지가 있는 소스 트리의 외부의 패키지에서 import할 수 없는 internel 패키지를 정의에 매커니즘이에 대해서 설명합니다. 

  이러한 패키지를 만들기 위해서 internal 디렉터리 또는 internal 디렉터리의 서브 디렉터리 내에 위치해야 합니다. go 커맨드는 경로에 internal이 포함된 패키지를 import하는 것을 보면 그것을 import하는 패키지가 internal 패키지의 부모 디렉터리의 서브 디렉터리인지 확인합니다. 예를 들어 *.../a/b/c/internal/d/e/f* 패키지는 *.../a/b/c*의 서브 디렉터리에 포함된 패키지에서만 import할 수 있습니다.

  Go 1.4의 경우 기본 Go 저장소에 internal 패키지 매커니즘이 적용됩니다. 1.5이후 부터는 어떠한 저장소에도 적용될 것입니다.

  메커니즘에 대한 자세한 내용은 [디자인 문서](https://docs.google.com/document/d/1e8kOo3r51b2BWtTs_1uADIA5djfXhPT36s6eHVRIvaU/edit)에 있습니다.
{% endcapture %}

<div class="notice--info">
  <h4>Go 1.4 릴리즈 노트 - internal package</h4>
  {{ notice-text | markdownify }}
</div>

## `/pkg`

외부 응용 프로그램에서 사용할 수 있는 라이브러리 코드(예. /pkg/mypubliclib). 다른 프로젝트이 이 라이브러리들을 import해서 사용할 것으로 예상하는 패키지 입니다. 참고로 internal 디렉터리에 있는 것은 private 패키지가 Go에 의해서 가져올 수 없도록 하는 더 좋은 방법입니다.  /pkg 디렉터리는 디렉터리 내부의 패키지가 다른 패키지에서 사용하기 위해 안전하다는 것을 명시적으로 알려주는 좋은 방법입니다. 
[I'll take pkg over internal](https://travisjeffery.com/b/2019/11/i-ll-take-pkg-over-internal/) 블로그 글은 pkg와 internal 디렉터리의 좋은 개요와 언제 사용하는 것이 좋은지에 대해 설명합니다.

루트 디렉터리에 Go가 아닌 컴포넌트 및 디렉터리가 포함되어 어떠한 Go 도구들 보다 쉽게 실행할 수 있는 경우 Go 코드를 한 곳에 그룹화하는 방법이기도 합니다.

인기있는 Go 저장소가 프로젝트 레이아웃을 사용하는 패턴을 보기를 원한다면 [/pkg](https://github.com/golang-standards/project-layout/blob/master/pkg/README.md) 디렉터리를 보십시오. 이는 일반적인 레이아웃 패턴이지만 Go 커뮤니티의 일부는 권장하지 않기도 합니다.

## `/vendor`

응용 프로그램 종속성 (수동으로 또는 새로운 내장 기능이지만 여전히 실험적인 모듈 기능과 같은 자주 사용하는 종속성 관리 도구로 관리)

라이브러리를 구축하는 경우 응용 프로그램 종속성을 커밋하지 마십시오.

Go는 1.13부터 모듈 프록시 기능을 활성화했습니다 (기본적으로 [https://proxy.golang.org](https://proxy.golang.org)를 모듈 프록시 서버로 사용). 모든 요구 사항과 제약 조건에 맞는지 확인하려면 여기를 참조하십시오. 그렇다면 vender 디렉토리가 전혀 필요하지 않습니다.

<br>

# 서비스 응용 프로그램 디렉터리

## `/api`

OpenAPI/Swagger 사양, JSON 스키마 파일, 프로토콜 정의 파일 예제는 [/​​api](https://github.com/golang-standards/project-layout/blob/master/api/README.md) 디렉토리를 참조하십시오.

<br>

# Web Application Directories

## `/web`

웹 응용 프로그램 특정 구성 요소 : static assets, 서버 쪽 템플릿 및 SPA.

<br>

# 일반 응용 프로그램 디렉터리

## `/configs`

설정 파일 템플릿 또는 기본 설정

confd 또는 consul-template 템플릿 파일

## `/init`

시스템 초기화(systemd, upstart, sysv) 및 프로세스 manager/supervisor(runit, supervisord) 설정

## `/scripts`

다양한 build, install, analysis 등을 수행하는 스크립트.

이 스크립트는 루트 수준 Makefile을 작고 단순하게 유지합니다 (예 : https://github.com/hashicorp/terraform/blob/master/Makefile).

예제는 [/scripts](https://github.com/golang-standards/project-layout/blob/master/scripts/README.md) 디렉토리를 참조하십시오

## `/build`

Packaging and Continuous Integration.

클라우드 (AMI), 컨테이너 (Docker), OS (deb, rpm, pkg) 패키지 구성 및 스크립트를 /build/package 디렉토리에 넣습니다.

CI (travis, circle, drone) 구성 및 스크립트를 /build/ci 디렉토리에 넣습니다. 일부 CI 도구 (예 : Travis CI)는 구성 파일의 위치에 매우 까다 롭습니다. 구성 파일을 /build\ci 디렉토리에 넣어 CI 도구가 예상하는 위치에 연결하십시오 (가능한 경우).

## `deployments`

IaaS, Paas, 시스템 및 컨테이너 오케스트레이션 배포 구성 및 템플릿(docker-compose, kubernetes / helm, mesos, terraform, bosh).

## `/test`

추가적인 외부 테스트 앱 및 테스트 데이터. 원하는대로 /test 디렉터리를 자유롭게 구성하십시오. 더큰 프로젝트의 경우 데이터를 서브 디렉터리로 만드는 것이 좋습니다. 예를 들어 Go가 그 디렉터리에 있는 것을 무시할 필요가 있는 경우 /test/data 또는 /test/testdata 일 수 있습니다. Go는 "." 또는 "_"로 시작하는 디렉터리나 파일을 무시하므로 테스트 데이터 디렉터리의 이름을 지정하는 방법에 유연성이 높아집니다.

[/test](https://github.com/golang-standards/project-layout/blob/master/test/README.md) 디렉터리를 참조하십시오.

<br>

# 기타 디렉터리

## `/docs`

디자인 및 사용자 문서 (godoc 생성 문서 외에도)

[/docs](https://github.com/golang-standards/project-layout/blob/master/docs/README.md) 디렉토리를 참조하십시오.

## `/tools`

이 프로젝트를 지원하는 도구.이 도구는 /pkg 및 /internal 디렉토리에서 코드를 가져올 수 있습니다.

[/tools](https://github.com/golang-standards/project-layout/blob/master/tools/README.md) 디렉토리를 참조하십시오.

## `/examples`

응용 프로그램 또는 라이브러리 예제

[/examples](https://github.com/golang-standards/project-layout/blob/master/examples/README.md) 디렉토리를 참조하십시오.

## `/third_party`

External helper tools, forked code and other 3rd party utilities (e.g., Swagger UI).

## `/githooks`

Git hooks.

## `/assets`

저장소와 함께 사용할 기타 assets (이미지, 로고 등)

## `/website`

Github 페이지를 사용하지 않는 경우 프로젝트 웹 사이트 데이터를 저장하는 곳입니다.

[/website](https://github.com/golang-standards/project-layout/blob/master/website/README.md) 디렉토리를 참조하십시오.

<br>

# 만들지 말아야할 디렉터리

## `/src`

일부 Go 프로젝트는 src 디렉터리를 사용하지만 일반적으로 Java로 개발을 했던 사람이 많이 사용합니다. Go 코드 또는 프로젝트가 Java 처럼 보이기를 원하지 않는 다면 사용하지 마세요.

Go 코드 작성 방법에 설명 된대로 프로젝트 레벨 /src 디렉토리를 Go가 작업 공간에 사용하는 /src 디렉토리와 혼동하지 마십시오. $GOPATH 환경 변수 현재 작업 공간 위치입니다. 이 작업 공간은 루트 레벨에 /pkg, /bin, /src 디렉터리를 포함합니다. 실제 프로젝트 는 /src 하위 디렉터리가 되므로 프로젝트에 /src 디렉터리가 있으면 프로젝트 경로는 /some/path/to/workspace/src/your_project/src/your_code.go와 같습니다. Go 1.11을 사용하면 프로젝트를 GOPATH 외부에 둘 수 있지만 여전히 이 레이아웃 패턴을 사용하는 것이 좋습니다.