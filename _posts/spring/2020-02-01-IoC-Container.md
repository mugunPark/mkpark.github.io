---
title: " The IoC Container"
classes: wide
excerpt: "[원문] When to use intern() method of String in Java?"
header:
  overlay_image: /assets/images/develop-unsplash.jpg
  overlay_filter: rgba(255, 255, 255, 0.3)
  actions:
    - label: "Go to"
      url: "https://docs.spring.io/spring/docs/5.2.3.RELEASE/spring-framework-reference/core.html#beans"
categories:
  - Spring Framework
tags:
  - IoC Container
last_modified_at: 2019-12-08T14:14:00
---

## 1. The Ioc Container

### 1.1 Srping IoC Container와 Bean 소개

이 장에서는 IoC(Inversion of Control) 원칙의 Spring Framework 구현에 대해 설명합니다. IoC는 의존성 주입(DI)이라고도 합니다. 의존성 주입은 객체가 생성자 인자, 팩토리 메소드의 인자, 팩토리 메소드에서 생성되거나 반환된 후 객체 인스턴스에 설정된 프로퍼티를 통해서만 의존성을 정의하는 프로세스 입니다. Container는 Bean을 생성할 때 의존성을 주입합니다. 이 프로세스는 근본적으로 클래스의 직접적인 생성 또는 Service Locator 패턴과 같은 방법을 사용해서 스스로 의존성의 인스턴스화 또는 위치를 제어하는 Bean의 정반대 입니다.

`org.springframework.beans`와 `org.springframework.context` 패키지는 Spring Framework의 IoC Container의 기반입니다. 인터페이스는 모든 객체 타입을 관리할 수 있는 많은 설정 방법을 제공합니다. ApplicationContext는 BeanFactory의 서브 인터페이스 입니다. 다음을 추가합니다.

  * Sring AOP와 더 쉽게 통합

  * 메시지 자원 처리

  * 이벤트 처리

  * 웹 응용프로그램에서 사용하는 WebApplication과 같은 응용 프로그램 계층 별 컨텍스트

간단히 말해서 BeanFactory는 설정 프레임워크와 기본 기능 제공하고 ApplicationContext는 더 많은 엔터프라이즈 별 기능을 추가합니다. ApplicationContext는 BeanFactory의 완벽한 상위 집합이고 이 장에서는 단지 Spring IoC Container에 대한 설명만 합니다.  ApplicationContext 대신 BeanFactory 사용에 대한 자세한 정보는 [The BeanFactory](#the-beanFactory)를 보십시오.

Spring에서 응용 프로그램의 중심이되고, Spring Ioc Container에 의해 관리되는 객체들을 Bean이라고 합니다. Bean은 Sring IoC Container에 의해 인스턴스화 및 구성되고 관리되는 객체입니다. 그렇지 않으면, Bean은 단순히 응용 프로그램에서 많은 객체중 하나입니다. Bean과 그 사이의 종속성은 Container가 사용하는 구성 메타 데이터에 반영됩니다.

### 1.2 Container 개요

`org.springframework.context.ApplicationContext` 인터페이스는 Spring IoC Container를 나타내고, Bean을 인스턴스화, 구성 및 조립 합니다. Container는 구성 메타 데이터에서 인스턴스화, 구성 및 조립할 객체에 대한 명령을 가져옵니다. 이 구성 메터 데이터는 XML, Java Annotation 또는 java 코드에 표현됩니다. 응용 프로그램을 구성하는 객체와 객체들간의 상호 의존성을 표현할 수 있습니다.

ApplicationContext 인터페이스의 여러 구현은 Spring에서 제공합니다. stand-alone 응용 프로그램에서 일반적으로 ClassPathXmlApplicationContext 또는 FileSystemXmlpllicationContext의 인스턴스를 생성합니다. XML이 구성 메터데이터를 정의하는 전통적인 형식이지만 추가 메터 데이터 포멧에 대한 지원을 선언적으로 활성화하기 위해 몇가지 XML 구성을 제공함으로써 Java Annotation 또는 코드를 메터 데이터 형식으로 사용하여 Container에 지시할 수 있습니다.

대부분의 응용 프로그램 시나리오에서 Spring IoC Container의 인스턴스화를 사용자 코드에서 직접할 필요는 없습니다. 예를 들어 웹 응용 프로그램 시나리오에서 일반적으로 응용 프로그램의 web.xml 파일에 있는 간단한 8줄 정도의 boilerplate면 충분합니다. [Spring Tool Suite](https://spring.io/tools)(Eclipse 기반 개발 환경)를 사용한다면 몇 번의 클릭과 입력으로 boilerplate 설정을 쉽게 생성할 수 있습니다.

다음의 다이어그램은 Spring의 작동 방식을 개략적으로 보여줍니다. 응용 프로그램 클래스는 구성 메타 데이터와 결합되므로 AppicationContext를 생성하고 초기화된 후에 완전히 구성되고 실행 가능한 시스템 또는 응용 프로그램을 갖게됩니다.


![image-center]({{ site.url }}{{ site.baseurl }}/assets/images/container-magic.png){: .align-center}

#### 1.2.1 구성 메타 데이터

위의 다이어그램이 보여주는것 처럼 Spring IoC Container는 구성 메타 데이터를 사용합니다. 이 구성 메타 데이터는 개발자가 응용 프로그램에서 객체 인스턴스화, 구성, 조립하도록 Spring Container에게 지시하는 방법을 나타냅니다.

구성 메타 데이터는 전통적으로 단순하고 직관적인 XML 형식으로 제공되는데 이 장의 대부분은 Spring IoC Container의 주요 개념과 기능을 전달하는데 사용합니다.

{% capture notice-text %}
    구성 메타 데이터 형식으로 XML만 허용되는 것은 아닙니다. Spring IoC Container 자체는 이 구성 메타 데이터가 실제로 작성된 형식과 완전히 분리되어 있습니다. 요즘 많은 개발자들이 Spring 애플리케이션을 위해 Java 기반 구성을 선택합니다.
{% endcapture %}

<div class="notice--info">
  <h4>TIP</h4>
  {{ notice-text | markdownify }}
</div>

Spring Container에서 다른 형식의 메타 데이터를 사용하는 방법에 대한 정보

  * **Annotation 기반 구성**: Spring 2.5에서 도입되었습니다.

  * **Java 기반 구성**: Spring 3.0에서 시작되고, 핵심 Spring Framework 중 하나가된 Spring JavaConfig 프로젝트에서 많은 기능이 제공됩니다. 따라서 XML 파일 대신 Java를 사용하여 응용 프로그램 클래스 외부의 Bean을 정의할 수 있습니다. 이러한 기능을 사용하기 위해서는 @Configuration, @Bean, @Import, @DependsOn과 같은 Annotation을 사용합니다.

Spring 구성은 컨테이너가 관리해야하는 하나 이상의 Bean 정의로 구성됩니다. XML 기반 구성 메타 데이터는 `<beans/>` 내부에 `<bean/>`을 사용해서 Bean을 구성합니다. Java 구성은 일반적으로 @Configration 클래스 메소드에 @Bean을 사용합니다.

이러한 Bean 정의는 응용 프로그램에서 만드는 실제 객체와 일치합니다. 일반적으로 서비스 계층 객체, 데이터에 접근하는 객체(DAO), Structs Action 인스턴스와 같은 객체, Hibernate SessionFactories와 같은 인프라 객체, JMS Queues 등을 정의합니다. 일반적으로 Container에서 세분화된 도메인 객체를 구성하지 않습니다. 왜냐하면 일반적으로 도메인 객체를 생성하고 읽는 것은 DAO와 비지니스 로직이 해야하기 때문입니다. 하지만 AspectJ와 Spring Integration을 사용해서 IoC Container의 제어 밖에서 생성되는 객체를 구성할 수 있습니다.

다음 예제는 XML 기반 구성 메타 데이터의 기본 구조를 보여줍니다.

```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://www.springframework.org/schema/beans
          https://www.springframework.org/schema/beans/spring-beans.xsd">

      <bean id="..." class="...">  
          <!-- collaborators and configuration for this bean go here -->
      </bean>

      <bean id="..." class="...">
          <!-- collaborators and configuration for this bean go here -->
      </bean>

      <!-- more bean definitions go here -->

  </beans>
```

* id는 각 Bean 정의를 식별하는 문자열 입니다.

* class는 Bean의 타입을 정의하고 패키지을 포함하는 전체 클래스 이름을 사용합니다.

#### 1.2.2 Container 인스턴스화

ApplicationContext 생성자에 제공되는 경로는 Container가 로컬 파일 시스템, Java CLASSPATH 등과 같은 다양한 외부 리소스에서 구성 메타 데이터를 로드 할 수 있도록하는 리소스 문자열 입니다.   

```java
  ApplicationContext context = new ClassPathXmlApplicationContext("services.xml", "daos.xml");
```

{% capture notice-text %}
    Spring의 IoC Container가에 대해 배우고 나면 (참고 자료에 설명 된) Spring의 Resource 추상화에 대해 더 알고 싶을 것이다. 이것은 URI 구문에 정의 된 위치에서 InputStream을 읽는 편리한 메커니즘을 제공한다. 특히, 응용 프로그램 컨텍스트 및 자원 경로에 설명된대로 자원 경로는 응용 프로그램 컨텍스트를 구성하는데 사용됩니다.
{% endcapture %}

<div class="notice--info">
  <h4>TIP</h4>
  {{ notice-text | markdownify }}
</div>

다음 예제는 서비스 계층 객체(services.xml) 구성 파일을 보여줍니다.

```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://www.springframework.org/schema/beans
          https://www.springframework.org/schema/beans/spring-beans.xsd">

      <!-- services -->

      <bean id="petStore" class="org.springframework.samples.jpetstore.services.PetStoreServiceImpl">
          <property name="accountDao" ref="accountDao"/>
          <property name="itemDao" ref="itemDao"/>
          <!-- additional collaborators and configuration for this bean go here -->
      </bean>

      <!-- more bean definitions for services go here -->

  </beans>
```

다음 예제는 데이터 접근 객체(daos.xml) 구성 파일을 보여줍니다.

```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://www.springframework.org/schema/beans
          https://www.springframework.org/schema/beans/spring-beans.xsd">

      <bean id="accountDao"
          class="org.springframework.samples.jpetstore.dao.jpa.JpaAccountDao">
          <!-- additional collaborators and configuration for this bean go here -->
      </bean>

      <bean id="itemDao" class="org.springframework.samples.jpetstore.dao.jpa.JpaItemDao">
          <!-- additional collaborators and configuration for this bean go here -->
      </bean>

      <!-- more bean definitions for data access objects go here -->

  </beans>
```

위의 예제에서 서비스 계층은 PetStoreServiceImpl 클래스, JpaAccountDao와 JpaItemDao 타입의 DAO 객체를 구성됩니다. property의 name은 JavaBean 속성의 이름을 나타내고, ref는 Bean 정의의 이름을 나타냅니다. id와 ref 사이의 연결은 협업 객체 간의 종속성을 나타냅니다. 객체의 종속성 구성에 대한 자세한 내용은 종속성을 참조하십시오.

##### XML 기반 구성 메타 데이터 작성

Bean 정의가 여러 파일로 나누어져 있는 것이 유용할 수 있습니다. 흔히 각 개별 XML 구성 파일은 아키텍처의 로직 계층 또는 모듈을 나타냅니다.

모든 XML들로 부터 Bean 정의를 가져오기위해 응용 프로그램 컨텍스트 생성자를 사용할 수 있습니다. 위의 예제에서 보았듯이 생성자에는 여러 Resource를 줄 수 있습니다.  다른 방법으로는 다른 파일에서 Bean 정의를 가져오기위해 `<import/>`를 사용할 수 있습니다. 다름 예제는 어떻게 하는지를 보여줍니다.

```xml
  <beans>
      <import resource="services.xml"/>
      <import resource="resources/messageSource.xml"/>
      <import resource="/resources/themeSource.xml"/>

      <bean id="bean1" class="..."/>
      <bean id="bean2" class="..."/>
  </beans>
```

위의 에제에서 외부 Bean 정의는 세 개의 파일로 부터 가져옵니다. 모든 경로는 import하는 파일의 상대 경로 입니다 그렇기 때문에 services.xml 파일은 동일한 디렉터리 또는 classpath에 있어야하고, messageSource.xml과 themeSource.xml 파일은 동일한 경로의 resources 디렉터리에 있어야합니다. 또한 보다시피 처음 / 는 없어도 상관없습니다. 그러나 이러한 경로가 상대적이므로 /를 전혀 사용하지 않는 것이 좋습니다. 최상위 레벨 `<beans />` 요소를 포함하여 가져오는 파일의 컨텐츠는 Spring Schema에 따라 유효한 XML Bean 정의 여야합니다.

{% capture notice-text %}
  상대 "../"경로를 사용하여 상위 디렉토리의 파일을 참조 할 수는 있지만 권장되지는 않습니다. 그렇게하면 현재 응용 프로그램 외부에 있는 파일에 대한 종속성이 작성됩니다. 특히 이 참조는 classpath: URL(예: classpath:../services.xml)에 권장하지 않습니다.  여기서 런타임 해결 프로세스는 "가장 가까운" 클래스 경로 루트를 선택한 다음 상위 디렉토리를 살펴 봅니다. Classpath 구성을 변경하면 다른 잘못된 디렉토리를 선택할 수 있습니다.

  상대 경로 대신에 절대 경로를 사용할 수 있습니다. 예: file:C:/config/services.xml 또는 classpath:/config/services.xml. 그러나 응용 프로그램 구성을 특정 절대 경로에 의존해야합니다. 일반적으로 런타임시 JVM 시스템 특성에 대해 해결되는 "$ {…}"플레이스 홀더를 통해 절대 위치 (예 : "$ {…}")에 대해 간접적인 위치를 유지하는 것이 좋습니다.
{% endcapture %}

<div class="notice--info">
  <h4>TIP</h4>
  {{ notice-text | markdownify }}
</div>

네임 스페이스 자체는 import 기능을 제공합니다. 일반 Bean 정의 이외의 추가 구성 기능은 Spring에서 제공하는 XML 네임 스페이스 (예 : context 및 util 네임 스페이스)에서 선택할 수 있습니다.


#### 1.2.3 Container 사용

ApplicationContext는 서로 다른 Bean의 레지스트리 및 의존성을 관리할 수 있는 팩토리 인터페이스 입니다. `T getBean(String name, Class<T> requiredType)` 메소드를 사용하는 경우 Bean 인스턴스를 찾을 수 있습니다.

ApplicationContextsms Bean 정의를 읽고 사용할 수 있도록 해줍니다. 

```java 
  // create and configure beans
  ApplicationContext context = new ClassPathXmlApplicationContext("services.xml", "daos.xml");

  // retrieve configured instance
  PetStoreService service = context.getBean("petStore", PetStoreService.class);

  // use configured instance
  List<String> userList = service.getUsernameList();
```

가장 유연한 변형은 다음 예제와 같이 Reader (예 : XML 파일의 경우 "XmlBeanDefinitionReader")와 결합 된 "GenericApplicationContext"입니다.

```java
  GenericApplicationContext context = new GenericApplicationContext();
  new XmlBeanDefinitionReader(context).loadBeanDefinitions("services.xml", "daos.xml");
  context.refresh();
```

동일한 ApplicationContext에서 이러한 Reader를 혼합하고 일치시켜 다양한 구성 소스에서 Bean 정의를 읽을 수 있습니다.

Bean의 인스턴스를 getBean을 사용하여 가져올 수 있습니다. ApplicationContext 인터페이스는 Bean을 찾기 위한 몇 가지 다른 메소드를 가지고 있습니다. 그러나 응용 프로그램에서 결코 사용해서는 안됩니다. 실제로, 애플리케이션 코드는 getBean() 메소드를 전혀 호출하지 않아야하므로 Spring API에 전혀 의존하지 않아야합니다. 예를 들어, Spring과 웹 프레임 워크의 통합은 컨트롤러 및 JSF 관리 Bean과 같은 다양한 웹 프레임 워크 컴포넌트에 대한 의존성 주입을 제공하므로 @Autowrie를 통해 특정 Bean에 대한 종속성을 선언 할 수 있습니다.