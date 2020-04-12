---
title: "Introducing Thymeleaf"
classes: wide
excerpt: "[원문] Introducing Thymeleaf"
header:
  overlay_image: /assets/images/develop-unsplash.jpg
  overlay_filter: rgba(255, 255, 255, 0.3)
  actions:
    - label: "Go to"
      url: "https://www.thymeleaf.org/doc/tutorials/2.1/usingthymeleaf.html#introducing-thymeleaf"
categories:
  - Thymeleaf
tags:
  - Thymeleaf
last_modified_at: 2020-04-12T14:14:00
---

## 1. Introducing Thymeleaf

### 1.1 What is Thymeleaf?

  Thymeleaf는 Java 라이브러리 입니다. Thymeleaf는 데이터 또는/그리고 텍스트를 표현하기위해 팀플릿 파일의 변환 설정을 적용할 수 있는 XML/XHTML/HTML5 템플릿 엔진 입니다.

  Thymeleaf는 웹 응용프로그램에서 XHTML/HTML5를 제공하는데 더 적합하지만 웹 또는 Standalone 응용 프로그램에서 모든 XML 파일을 처리할 수 있습니다.

  Thymeleaf의 주요 목표는 탬플릿을 작성하는 우아하고 올바른 방법을 제공하는 것입니다. 이를 위해서 템플릿 코드 내부에 로직을 작성하는 대신에 DOM에 미리 정의된 로직의 실행을 정의하는 XML 태그와 속성을  기반으로합니다.

  이 아키텍쳐는 템플릿을 빠르게 처리할 수 있으며, 파싱된 파일을 캐싱하여 실행 중에 가능한 최소의 I/O 작업을 사용합니다.

  마지막으로 Thymeleaf는 처음부터 XML과 Web 표준을 고려해서 설계되었기 때문에 필요한 경우 완전한 유효성 검사 템플릿을 만들 수 있습니다.

### 1.2 Thymeleaf는 어떤 종류의 템플릿을 처리 할 수 있습니까?

  기본적으로 Thymeleaf는 6가지 템플릿을 처리할 수 있고, 각 템플릿을 템플릿 모드라고 합니다.

  * XML
  * Valid XML
  * XHTML
  * Valid XHTML
  * HTML5
  * Legacy HTML5

  이러한 모든 모드는 Legacy HTML5 모드를 제외한 모든 올바른 형식의 XML 파일을 참조하며 독립형(닫히지 않는) 태그, 값이 없는 태그의 속성 또는 타옴표 사이에 쓰여지지 않는 태그 속성과 같은 기능으로 HTML5 파일을 처리할 수 있습니다. 특정 모드에서 파일을 처리하기 위해서, Thymeleaf는 먼저 여전히 유요한 HTML5(그리고 실제로는 HTML5 코드를 작성하는 권장되는 방법)인 파일을 올바른 형식의 XML 파일로 변환할 것입니다.

  또한 유효성 검사는 XML 및 XHTML 템플릿에서만 사용할 수 있습니다.

  그럼에도 불구하고 이들은 Thymeleaf가 처리 할 수있는 유일한 템플릿 유형이 아니며 사용자는 이 모드에서 템플릿을 구문 분석하는 방법과 결과를 작성하는 방법을 모두 지정하여 자신의 모드를 항상 정의 할 수 있습니다. 이런 식으로 DOM 트리 (XML인지 아닌지)로 모델링 할 수있는 모든 것은 Thymeleaf에 의해 템플릿으로 효과적으로 처리 될 수 있습니다.

### 1.3 Dialects: The Standard Dialect

  Thymeleaf는 템플릿에서 처리 될 DOM 노드와 처리 방법을 완전히 정의 할 수있는 확장 성이 뛰어난 템플릿 엔진입니다 (실제로는 템플릿 엔진 프레임 워크라고 부릅니다).

  DOM 노드에 일부 로직을 적용하는 객체를 프로세서라고 하며, 이러한 프로세서의 세트와 추가 아티펙트를 Dialect라고 하며, Thymeleaf의 핵심 라이브러리는 Standard Dialect라는 표준을 제공합니다. 

  물론, 라이브러리의 고급 기능을 활용하면서 자체 처리 로직을 정의하려는 경우 사용자는 표준 언어를 확장하더라도 고유 한 Dialect을 작성할 수 있습니다. 템플릿 엔진은 한 번에 여러 방언을 구성 할 수 있습니다.

  Thymeleaf Standard Dialect는 모든 모드에서 템플릿을 처리할 수 있지만, 특히 웹 지향 템플릿 모드(XHTML 및 HTML5)에 적합합니다. HTML5 외에도 XHTML 1.0 Transitional, XHTML 1.0 Strict, XHTML 1.0 Frameset 및 XHTML 1.1과 같은 XHTML 사양을 지원하고 유효성을 검사합니다.

  Standard Dialect의 프로세서는 대부분 속성 프로세서입니다. 따라서 브라우저는 추가 속성을 무시하기 때문에 처리하기 전에도 XHTML / HTML5 템플릿 파일을 올바르게 표시 할 수 있습니다. 예를 들어, 태그 라이브러리를 사용하는 JSP는 다음과 같은 브라우저에서 직접 표시 할 수없는 코드 조각을 포함 할 수 있습니다.

  ```html
    <form:inputText name="userName" value="${user.name}" />
  ```

  ```html
    <input type="text" name="userName" value="James Carrot" th:value="${user.name}" />
  ```

  브라우저에서 올바르게 표시 될뿐만 아니라 프로토 타입이 브라우저에서 정적으로 열릴 때 표시되는 값 속성 (이 경우 "James Carrot")을 (선택적으로) 지정할 수도 있습니다. 템플릿의 Thymeleaf 처리 중에 $ {user.name}의 평가 결과 값으로 대체됩니다.

  필요한 경우 디자이너와 개발자가 동일한 템플릿 파일을 작업하고 정적 프로토 타입을 작업 템플릿 파일로 변환하는 데 필요한 노력을 줄일 수 있습니다. 이 기능은 일반적으로 Natural Templating이라고하는 기능입니다.

### 1.4 Overall Architecture

  Thymeleaf 핵심은 DOM을 처리하는 엔진입니다. 특히, 템플릿 메모리 내 트리 표현을 구축하기 위해 표준 DOM API가 아닌 자체 고성능 DOM 구현을 사용합니다. 나중에 노드를 탐새가고 현재 구성 및 표시를 위해 템플릿으로 전달되는 데이터들에 따라 DOM을 수정하는 프로세서를 실행하여 작동합니다.

  DOM 템플릿 표현을 사용하면 웹 문서가 종종 객체 트리로 표현되기 때문에 웹 응용프로그램에 매우 적합합니다(실제로 DOM 트리는 브라우저가 메모리의 웹 페이지를 나타내는 방식입니다.) 또한 대부분의 웹 응용프로그램은 수십개의 템플릿 만 사용한다는 아이디어를 기반으로하여, 이러한 파일은 큰 파일이 아니며 응용프로그램 실행 중에 보통 변경되지 않는다는 것입니다. Thymeleaf의 파싱된 템플릿 DOM 트리의 메모리 내 캐시를 사용하여 대부분의 템플릿 처리 작업에 I/O가 거의 필요하지 않기 때문에 프로덕션 환경에서 속도가 빠릅니다.

  그럼에도 불구하고,이 아키텍처에는 다른 템플릿 파싱 / 프로세싱 접근법보다 각 템플릿 실행에 더 많은 양의 메모리 공간을 사용해야하므로 빅 데이터 XML 문서를 만드는 데 라이브러리를 사용해서는 안됩니다 ( 웹 문서). 일반적으로 JVM의 메모리 크기에 따라 항상 단일 템플릿 실행으로 수십 MB 크기의 XML 파일을 생성하는 경우 Thymeleaf를 사용하지 않아야합니다.