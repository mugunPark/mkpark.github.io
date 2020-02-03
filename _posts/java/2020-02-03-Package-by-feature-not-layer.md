---
title: " Package by feature, not layer"
classes: wide
excerpt: "[원문] Package by feature, not layer"
header:
  overlay_image: /assets/images/develop-unsplash.jpg
  overlay_filter: rgba(255, 255, 255, 0.3)
  actions:
    - label: "Go to"
      url: "http://www.javapractices.com/topic/TopicAction.do?Id=205"
categories:
  - Java
tags:
  - Package
last_modified_at: 2020-02-03T14:14:00
---

응용 프로그램을 만들 때 제일 처음 하는 질문은 "어떻게 패키지를 나눌까? 입니다. 일반적인 비니지스 응용 프로그램의 경우 이 질문에 대한 답은 두 가지 방법이 있을 수 있습니다.

## Package By Feature

Package-by-feature는 기능 단위로 패키지를 구성하는 것입니다. 하나의 디렉터리/패키지에 한 기능과 관련된 모든 항목들을 위치시키는 것입니다.  이는 패키지가 높은 응집력과 모듈성을 갖고 패키지 사이에 결합을 최소화 합니다. 서로 밀접하게 작동하는 항목들은 나란히 배치됩니다. 이런 항목들은 응용 프로그램 전체에 퍼져 있지 않습니다. 경우에 따라서 기능을 제거할 때 하나의 디렉터리를 삭제하는 것으로 끝날 수 있습니다.(삭제 잡업은 최대 모듈성을 위한 좋은 테스트로 간주될 수 있습니다: 한 번의 작업으로 항목을 삭제할 수 있는 경우에만 최대 모듈성을 갖습니다.)

Package-by-feature에서 패키지 이름은 중요한 도메인의 상위 수준에 해당합니다. 예를 들어, 약 처방 응용 프로그램은 아마 다음과 같은 패키지를 가질 것입니다.

  * com.app.doctor
  * com.app.drug
  * com.app.patient
  * com.app.presription
  * com.app.report
  * com.app.security
  * com.app.webmaster
  * com.app.util
  * 등등...

각 패키지는 일반적으로 특별한 기능과 관련된 항목만 포함하고, 다른 기능들은 포함하지 않습니다. 예를 들어, com.app.doctor 패키지는 아마 다음 세 가지 항목을 포함할 것입니다.

  * DoctorAction.java - action 또는 controller 객체
  * Doctor.java - Model 객체
  * DoctorDAO.java - Data Access 객체
  * 데이터베이스 항목(SQL Statement)
  * 사용자 인터페이스 항목(아마도 웹 앱의 경우 JSP)

패키지가 Java 코드뿐만 아니라 다른 파일도 포함할 수 있습니다. 실제로, package-by-feature가 원하는대로 동작하도록 하기위해서는 지정된 기능과 관련된 모든 항목(사용자 인터페이스, Java 코드, 데이터베이스 항목 등)이 기능 전용의 단일 디렉터리에 있어야합니다. 

경우에 따라서, 기능/패키지는 응용 프로그램에서 모든 다른 기능에서 사용되지 않습니다. 그런 경우라면 디렉터리 삭제로 간단하게 제거될 것입니다. 만약 실제로 일부 다른 기능에 의해 사용이 된다면 제거는 한번의 삭제와 같이 간단하지 않을 것입니다.

즉, package-by-feature는 한 패키지가 다른 패키지에 속하는 항목을 절대로 사용할 수 없음을 의미하지 않습니다. 오히려 package-by-feature는 **기본적으로 package-private을  기본 범위로 선호**하고, 필요할 때만 항목의 범위를 public으로 확대합니다.

## Package By Layer

package-by-layer에서 최상위 수준의 패키지는 다양한 응용 프로그램 layer들을 반영합니다.

  * com.app.action
  * com.app.model
  * com.app.dao
  * com.app.util

여기서 각 기능은 "구현 카테고리"라고 불릴 수있는 여러 디렉토리에 구현되어 있습니다. 각 디렉터리는 일반적으로 다른 항목들과 밀접하게 관련되지 않는 항목을 포함합니다. 패키지는 **낮은 응집력과 낮은 모듈성**을 가지고 패키지 사이에 높은 결합을 가집니다. 결과적으로 기능을 수정하려면 다른 디렉터리에서 파일을 편집해야합니다. 또한 한번의 작업으로 기능 삭제를 거의 수행할 수 없습니다.

## 권장 사항 : Package By Feature

일반적인 비지니스 응용 프로그램은 Package By Feature가 두 가지 중에서 우수한 것으로 보입니다.

### 더 높은 모듈성

위에서 언급한 바와 같이, package-by-feature만이 높은 응집력, 높은 모듈성 및 패키지 간의 낮은 결합을 가집니다.

### 더 쉬운 코드 네비게이션

특정 작업에 필요한 모든 항목이 일반적으로 동일한 디렉토리에 있기 때문에 유지 보수 프로그래머는 항목을 훨씬 적게 검색해야합니다. package-by-feature를 장려하는 일부 도구는 패키지 이름 지정 규칙을 사용하여 지루한 코드 탐색 문제를 완화합니다. 하지만 package-by-feature는 디렉터리 간 네비게이션 필요성을 크게 줄임으로써 이러한 규칙의 필요성을 우선합니다.

### 더 높은 추상화 수준

높은 수준의 추상화를 유지하는 것은 지속적인 가치의 프로그래밍 원칙 중 하나입니다. 그것은 문제에 대해 생각하는 것을 더쉽게 만들고, 구현 세부 사항보다 기본적인 서비스를 강조합니다. 높은 수준의 추상화의 직접적인 이점으로 응용 프로그램은 자체 문서화됩니다.: 응용 프로그램의 전체 크기는 패키지의 수로 전달되고 기본 기능은 패키지 이름으로 전달됩니다. 반면에 package-by-layer의 근본적인 결함은 구현 세부 사항을 높은 수준의 추상화보다 우선한다는 점입니다.


### 기능과 레이어의 분리

package-by-feature는 여전히 레이어 분리라는 아이디어를 존중하지만, 분리는 별도의 클래스를 사용하여 구현됩니다. 반면에 package-by-layer는 별도의 클래스와 별도의 패키지를 사용하여 분리를 구현하므로 필요하거나 바람직하지 않습니다.

### 범위를 최소화

범위 최소하는 다른 지속적인 가치의 또 다른 기본 원칙입니다. 여기서  package-by-feature는 일부 클래스의 public에서  package-private 범위로 줄일 수 있습니다. 이것은 중대한 변화이며 파급 효과를 최소화 하는데 도움이 됩니다. 반면에 package-by-layer는 package-private 범위를 효과적으로 버리고 거의 모든 항목을 public으로 구현해야합니다. 이것은 비밀을 유지함으로써 파급 효과를 최소화할 수 없기 때문에 근본적인 결함입니다.

### 더 나은 성장 스타일

package-by-feature에서 각 패키지의 클래스들은 특정 기능에 관련된 항목으로 제한됩니다. 만약 패키지가 너무 커진다면 자연스럽게 두개 이상의 패키지로 리펙토링될 수 있습니다. 반면에 package-by-layer는  monolithic 입니다. 응용 프로그램 사이즈가 커짐으로써  패키지 수는 거의 동일하게 유지되는 반면 각 패키지의 클래스 수는 제한없이 증가합니다. 

