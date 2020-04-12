---
title: "Standard Expression syntax"
classes: wide
excerpt: "[원문] Standard Expression syntax"
header:
  overlay_image: /assets/images/develop-unsplash.jpg
  overlay_filter: rgba(255, 255, 255, 0.3)
  actions:
    - label: "Go to"
      url: "https://www.thymeleaf.org/doc/articles/standarddialect5minutes.html"
categories:
  - Thymeleaf
tags:
  - syntax
last_modified_at: 2020-04-12T14:14:00
---

## 1. Standard Expression syntax

  대부분의 Thymeleaf 속성은 값을 표현식으로 설정하거나 포함하는 표현식을 허용합니다.이 표현식은 사용 된 Dialect으로 인해 표준 표현식을 호출합니다.

  * ${...} : Variable expressions.
  * *{...} : Selection expressions.
  * #{...} : Message (i18n) expressions.
  * @{...} : Link (URL) expressions.
  * ~{...} : Fragment expressions.

### 1.1 Variable expressions

  변수 표현식은 OGNL 표현식 (또는 스프링 전문 용어로 모델 속성이라고도하는 컨텍스트 변수에서 실행되는 Thymeleaf를 Spring과 통합하는 경우 Spring EL)입니다. 

  ```html
    ${session.user.name}
  ```
  그리고 속성에 따라 속성 값 또는 그 일부로 찾을 수 있습니다.

  ```html
    <span th:text="${book.author.name}">
  ```

  위의 표현식은 다음과 같습니다 (OGNL 및 SpringEL 모두).

  ```java
    ((Book)context.getVariable("book")).getAuthor().getName()
  ```

  그러나 출력뿐만 아니라 조건부, 반복과 같은 더 복잡한 처리와 관련된 시나리오에서 변수 표현식을 찾을 수 있습니다.

  ```html
    <li th:each="book : ${books}">
  ```

  여기서 $ {books}는 컨텍스트에서 books라는 변수를 선택하고 th : each 루프에서 사용할 수있는 iterable로 평가합니다.

### 1.2 Selection expressions

  선택 표현식은 전체 컨텍스트 변수 맵 대신 이전에 선택한 객체에서 실행된다는 점을 제외하고 변수 표현식과 같습니다. 그들은 다음과 같이 보입니다 :

  ```html
    *{customer.name}
  ```
  그들이 작용하는 객체는 th:object 속성으로 지정됩니다 :

  ```html
    <div th:object="${book}">
      ...
      <span th:text="*{title}">...</span>
      ...
  </div>
  ```

  따라서 다음과 같습니다.

  ```java
    {
      // th:object="${book}"
      final Book selection = (Book) context.getVariable("book");
      // th:text="*{title}"
     output(selection.getTitle());
   }
  ```

### 1.3 Message (i18n) expressions

  메시지 표현 (텍스트 외부화, 국제화 또는 i18n이라고도 함)을 사용하면 외부 소스 (.properties 파일)에서 로케일 별 메시지를 검색하여 키로 참조하고 선택적으로 매개 변수 세트를 적용 할 수 있습니다.

  Spring 애플리케이션에서는 Spring의 MessageSource 메커니즘과 자동으로 통합됩니다.

  ```html
    #{main.title}
  ```
   ```html
    #{message.entrycreated(${entryId})}
  ```

  다음과 같은 템플릿에서 찾을 수 있습니다.

  ```html
    <table>
      ...
      <th th:text="#{header.address.city}">...</th>
      <th th:text="#{header.address.country}">...</th>
      ...
    </table>  
  ```

  컨텍스트 키 값으로 메시지 키를 결정하거나 변수를 매개 변수로 지정하려는 경우 메시지 표현식 내에 변수 표현식을 사용할 수 있습니다..

  ```html
    #{${config.adminWelcomeKey}(${session.user.name})}
  ```

  ### 1.4 Link (URL) expressions

  링크 표현식은 URL을 작성하고 유용한 컨텍스트 및 세션 정보를 추가합니다 (일반적으로 URL 재 작성이라고하는 프로세스).

  따라서 웹 서버의 /myapp 컨텍스트에 배포 된 웹 응용 프로그램의 경우 다음과 같은 표현입니다.

  ```html
    <a th:href="@{/order/list}">...</a>
  ```

  다음과 같이 변환 될 수 있습니다.

  ```html
    <a href="/myapp/order/list">...</a>
  ```

  또는 세션을 유지해야하고 쿠키가 활성화되지 않은 경우 (또는 서버가 아직 모르는 경우) :

  ```html
    <a href="/myapp/order/list;jsessionid=23fa31abd41ea093">...</a>
  ```

  URL은 매개 변수를 사용할 수도 있습니다.

  ```html
    <a th:href="@{/order/details(id=${orderId},type=${orderType})}">...</a>
  ```

  다음과 같은 결과가 발생합니다.

  ```html
    <!-- & 는 태그 속성에서 HTML 이스케이프되어야합니다.... -->
    <a href="/myapp/order/details?id=23&amp;type=online">...</a>
  ```

   링크 표현식은 상대적 일 수 있으며, 이 경우 URL에 애플리케이션 컨텍스트가 접두어로 표시되지 않습니다.

  ```html
    <a th:href="@{../documents/report}">...</a>
  ```

   또한 서버 기준 (접두어를 지정할 애플리케이션 컨텍스트가 없음) :

  ```html
   <a th:href="@{~/contents/main}">...</a>
  ```

   프로토콜 기준 (절대 URL과 동일하지만 브라우저는 표시되는 페이지에 사용 된 것과 동일한 HTTP 또는 HTTPS 프로토콜을 사용함) :

  ```html
    <a th:href="@{//static.mycompany.com/res/initial}">...</a>
  ```

   물론 링크 표현식은 절대적 일 수 있습니다.

  ```html
    <a th:href="@{http://www.mycompany.com/main}">...</a>
  ```
  
  ### 1.5 Fragment expressions

  Fragment 표현식은 마크 업 조각을 표현하고 템플릿 주위로 쉽게 이동할 수있는 방법입니다. 이러한 표현 덕분에 조각을 복제하고 다른 템플릿에 인수로 전달할 수 있습니다.

  가장 일반적으로 사용되는 것은 th:insert 또는 th:replace를 사용한 조각 삽입입니다.

    ```html
      <div th:insert="~{commons :: main}">...</div>
    ```

  그러나 다른 변수와 마찬가지로 어디서나 사용할 수 있습니다.

    ```html
    <div th:with="frag=~{footer :: #main/text()}">
      <p th:insert="${frag}">
    </div>
    ```

  ### 1.6 Literals and operations

  다양한 유형의 리터럴과 연산을 사용할 수 있습니다.

  * Literals:
    * Text literals: `'one text', 'Another one!',…`
    * Number literals: `0, 34, 3.0, 12.3,…`
    * Boolean literals: `true, false`
    * Null literal: `null`
    * Literal tokens: `one, sometext, main,…`
    
  * Text operations:
    * String concatenation: `+`
    * Literal substitutions: `|The name is ${name}|`

  * Arithmetic operations:
    * Binary operators: `+, -, *, /, %`
    * Minus sign (unary operator): `-`

  * Boolean operations:
    * Binary operators: `and, or`
    * Boolean negation (unary operator): `!, not`

  * Comparisons and equality:
    * Comparators: `>, <, >=, <= (gt, lt, ge, le)`
    * Equality operators: `==, != (eq, ne)`
  
  * Conditional operators:
    * If-then: `(if) ? (then)`
    * If-then-else: `(if) ? (then) : (else)`
    * Default: `(value) ?: (defaultvalue)`

  ### 1.6 Expression preprocessing

  표현식에 대해 알아야 할 마지막 사항은 __ 사이에 지정된 표현식 사전 처리라는 것이 있는데 이는 다음과 같습니다.

  ```html
    #{selection.__${sel.code}__}
  ```

  우리가 보고있는 것은 먼저 실행될 변수 표현식 ($ {sel.code})이 있는데, 그 결과“ALL”은 나중에 실행될 실제 표현식의 일부로 사용될 것입니다. 국제화의 경우 (selection.ALL 키가 있는 메시지를 찾습니다).