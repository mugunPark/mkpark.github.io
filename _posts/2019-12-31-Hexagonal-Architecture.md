---
title: "Hexagonal Architecture: 3가지 원칙과 구현 예제"
classes: wide
excerpt: "[원문] Hexagonal Architecture: three principles and an implementation example"
header:
  overlay_image: /assets/images/develop-unsplash.jpg
  overlay_filter: rgba(255, 255, 255, 0.3)
  actions:
    - label: "Go to"
      url: "https://blog.octo.com/hexagonal-architecture-three-principles-and-an-implementation-example/"
tags:
  - Architecture
last_modified_at: 2019-12-31T00:00:00
---

*[Alistair Cockburn](https://en.wikipedia.org/wiki/Alistair_Cockburn)*에 의해 2005년에 소개된 Hexagonal 아키텍처는 많은 장점을 가지고 2015년부터 새로운 관심을 받는 소프트웨어 아키텍쳐 입니다.

Hexagonal 아키텍처는 다음과 같은 의도로 만들어졌습니다.

응용 프로그램을 사용자, 프로그램, 자동화된 테스트 또는 배치 스크립트로 동일하게 실행되고, 최종 실행 환경과 데이터베이스와 별도로 개발, 테스트할 수 있게 합니다.

자동화된 테스트로 응용 프로그램을 제어하거나 데이터베이스와 별도로 개발 및 테스트하는 것의 장점을 살펴보기 위해서 *[the test pyramid by practice](https://blog.octo.com/en/the-test-pyramid-in-practice-1-5/)* 글을 먼저 읽어 보는 것을 추천해 드립니다.

Hexagonal 아키텍처는 매우 매력적이며 다른 유익한 효과를 가지고 있습니다. 그것은 응용 프로그램의 핵심 비지니스를 분리하고, 다른 모든 것과 독립적으로 동작을 자동으로 테스트 할 수 있습니다. 이것이 이 아키텍처가 왜 Domain-Driven Design(DDD)로 개발하는 개발자들의 마음을 사로잡을 수 있었는지에 대한 이유일 수 있습니다. 하지만 주의해야 합니다. DDD와 hexagonal 아키텍처는 서로를 보완해 줄 수 있지만, 반드시 함께 사용할 필요는 없는 두 가지 고유한 개념입니다.

마지막으로 이 아키텍처는 설정하는 것을 더 복잡하게 만들지는 않습니다. 몇 가지 간단한 규칙과 원칙에 기반 둡니다. 이러한 원칙이 실제로 무엇을 의미하는지 살펴보도록 하겠습니다.


# Hexagonal 아키텍처의 원칙

Hexagonal 아키텍처는 3가지 원칙과 기술에 기반을 둡니다.

* Application, Domain, Infrastructure를 명확하게 분리됩니다.

* 의존성은 Application과 infrastructure로부터 Domain으로 향합니다.

* Port와 Adapter를 사용하여 경계를 분리합니다. 


## 원칙 1. Application, Domain, Infrastructure 분리

![image-center]({{ site.url }}{{ site.baseurl }}/assets/images/Hexagonal Principle1.png){: .align-center}

### 왼쪽의 Application

사용자 또는 외부 프로그램이 응용 프로그램과 상호 작용합니다. 이런 상호작용을 허용하는 코드를 포함합니다. 일반적으로 사용자 인터페이스 코드, API를 위한 HTTP 라우터, 외부 프로그램으로의 JSON 직렬화가 여기에 있습니다.

*Alistair Cockburn*은 Application 측면을 Left Side 또는 User Side라고 말합니다.

### 중앙의 Domain 

Application과 Infrastructure로부터 분리되기를 원하는 부분입니다. 비지니스 로직을 구현하고 관여하는 모든 코드를 포함합니다. 비지니스 용어와 순수한 비지니스로직은 응용 프로그램이 해결하는 구체적인 문제와 관련이 있으며, 다양하고 구체적으로 만드는 모든 것이 중심에 있습니다. 이상적으로 코딩하는 방법을 알지 못하는 Domain 전문가는 이 부분에서 코드를 읽고 불일치를 지적할 수 있습니다.

*Alistair Cockburn*은 Domain 측면을 Center 또는 비지니스 로직이라고 말합니다.

### 오른쪽 Infrastructure

응용 프로그램에 무엇이 필요하고, 동작하기 위해 무엇이 필요한지 찾을 수 있습니다. 데이터베이스와 상호작용하고 파일 시스템을 호출하는 코드 또는 사용자가 의존하는 다른 응용 프로그램에 대한 HTTP 호출을 처리하는 코드와 같은 본질적인 Infrastructure의 세부 정보를 포함합니다.

*Alistair Cockburn*은 Infrastructure 측면을 Right Side 또는 Server Side라고 말합니다.


다음 원칙에 따라 Application, Domain, Infrastructure 간의 논리적 분리가 가능합니다.

### 왜 중요한가 ?

이러한 분리의 첫 번째 중요한 특징은 문제를 분리한다는 것입니다. 언제든지 다른 두 가지( 응용 프로그램 로직, 비지니스 로직 또는 인프라 로직)와 거의 독립적으로 단일 로직에 초점을 맞출 수 있습니다. 각 로직이 섞이지 않아 이해하기 쉽고, 각 로직의 제약은 다른 로직에 덜 영향을 미칩니다.

다른 특징은 코드를 비지니스 로직에 집중해서 작성할 수 있습니다. 그것은 디렉터리 또는 모듈에서 분리하여 모든 개발자에게 명시할 수 있고, 프로그램의 나머지 부분을 인지하지 않고도 정의, 개선 테스트 할 수 있습니다. 이것이 결국 제품에 들어가는 비지니스에 대한 개발자의 이해이기 때문에 중요합니다.

그리고 마지막으로 자동화된 테스트의 관점에서 합리적인 노력으로 테스트에 성공할 것입니다.

* 개별적인 전체 Domain

* Infrastructure에 독립적으로 Application과 Domain 사이의 통합

* Application과 독립적으로 Infrastructure에 Domain 사이의 통합


## Application의 예제

더 구체적으로 이 원칙을 설명하기 위해 Thomas Pierrain([@tpierrain](https://twitter.com/tpierrain) 과 Alistair Cockburn([@TotherAlistair](https://twitter.com/TotherAlistair))이 2017년에 제안한"Hexagon"에서 Alistair 중에 사용 된 작은 예를 사용합니다.

작은 응용 프로그램의 목적은 콘솔의 표준 출력에 시를 작성하는 커맨드라인 프로그램을 제공하기 위한 것입니다. 

프로그램의 예상되는 출력의 예

```
$ ./printPoem
Here is some poem:
I want to sleep
Swat the files
Softly, please.
-- Masaoka Shiki (1867 - 1902)
Type enter to exit...
```

세 구역(Application, Domain, Infrastructure)을 올바르게 설명하기 위해 이 응용 프로그램은 외부 시스템(파일)에서 시를 검색할 것입니다. 응용 프로그램을 데이터베이스에 연결할 수도 있습니다. 원칙은 동일합니다.

이러한 맥락에서 어떻게 이 첫 번째 원칙, 즉 세 구역으로 분리를 적용할 수 있을까요? 어떻게 왼쪽(어떤 드라이브), 중앙(핵심 비즈니스) 및 오른쪽(구동 대상)으로 분리할까요?


![image-center]({{ site.url }}{{ site.baseurl }}/assets/images/02_app_domain_infra.png){: .align-center}

### Application

사용자의 관점에서 보면 프로그램은 콘솔 프로그램처럼 표현됩니다. 그렇기 때문에 콘솔의 개념은 왼쪽의 Application일 것입니다. 콘솔을 통해서 사용자가 Domain을 구동하게 됩니다.

### Infrastructure

기술적으로 이 경우에 시는 파일에 저장됩니다. 이 파일의 개념은 오른쪽의 Infrastructure에 있습니다. 비지니스는 Infrastructure의 실행으로 시를 요청할 것이고 PoetryLibraryFileAdapter에 구체적으로 구현될 것입니다.
여기서 위에 언급된것 처럼 시의 저장소(파일, 데이터베이스, 웹 서비스 등등)를 쉽게 변경할 수 있습니다. 그러므로 실제로 파일과 같은 저장소의 구현은 기술적인 세부 사항(기술 구현 세부 사항이라고도 함) 입니다.

### Domain

이 경우 핵심 비지니스인 사용자에 주는 가치는 시를 읽는다는 것입니다. 예를 들어 PoetryReader 클래스 코드에서 개념을 구체화 할 수 있습니다.

### Application -> Domain

비지니스 관점에서 요청이 콘솔 프로그램으로부터 발생한 것인지 다른 프로그램인지는 중요하지 않습니다. 추상화하고자 하는 기술적 세부사항입니다. 이것은 정확히 초기 의도 중 하나입니다. 그러므로 Domain에는 콜솔 개념이 없습니다. 그러나 응용 프로그램이 사용자의 관점(=제공하는 서비스)에서 허용하는 것은 시를 요구하는 것입니다. 그것은 Domain(IRequestVerses에 의해 구체화함)에서 찾을 수 있고 Application에서 Domain으로의 상호작용을 허용한다는 개념입니다.

### Domain -> Infrastructure

비슷하게 Domain 측면에서 시가 파일에 저장되든지 데이터베이스에 저장되는지는 중요하지 않습니다. 외부 시스템에 독립적으로 프로그램을 테스트할 수 있는 것을 원할 것입니다. Domain에는 파일의 개념이 없습니다. 동작을 위해서 Domain은 여전히 시를 가져는 것이 필요합니다. IObtainPoems 인터페이스 형태로 Domain에서 시를 얻을 수 있습니다. 그것은 Domain이 Infrastructure와의 상호작용으로 시를 얻을 수 있다는 것입니다.

**참고**: 여기에서 다이어그램을 볼 때 클래스들 사이의 관계를 보기 위해 화살표를 관찰하기 시작할 것입니다. 채워진 화살표는 호출 또는 구성 상호 작용(composition interaction)을 말합니다. 그리고 비어있는 화살표는 상속 관계를 말합니다.

## 원칙2. 의존의 방향은 안쪽으로 향한다.

이것은 목표를 달성하기 위한 필수 원칙입니다. 이미 이전 원칙에서 이것을 보기 시작했습니다.

![image-center]({{ site.url }}{{ site.baseurl }}/assets/images/03_di_to_domain.png){: .align-center}

프로그램은 콘솔과 테스트에 의해 제어될 수 있습니다. Domain에는 콘솔의 개념이 없습니다. Domain은 Application에 의존하지 않고 그것은 Application이 Domain에 의존한다는 것입니다. Application(ConsoleAdapter)의 시 요청은 IRequestVerses 에 따라 다릅니다.

비슷하게 프로그램은 외부 시스템에 독립적으로 테스트 될 수 있고, Domain은 Infrastructure에 의존하지 않습니다. 그것은 Domain에 의존하는 Infrastructure와 반대입니다. 시는 IObtainPoems 인터페이스를 통해 얻을 수 있습니다. 기술적으로 Infrastructure에 있는 클래스는 Domain에 정의된 인터페이스를 상속하여 구현할 것이고 의존성 역전(dependency inversion)에 대해서는 아래에서 자세히 볼 수 있습니다.


### 내부와 외부(Inside and outside)

의존성 관계를 화살표로 보면 이 원칙은 중앙 Domain을 내부로 정의하고 나머지는 외부로 정의합니다 (그림 참조). Hexagonal 아키텍처에 대해 논의할 때 이러한 내부와 외부 개념을 많이 이야기합니다. 기억하고 전달하는 기본 포인트가 될 수도 있습니다.

다시 말해 Domain에 모든 것이 의존하고, Domain은 어떠한 것에도 의존하지 않는다는 말입니다. Alistair Cockburn은 초기 문제를 해결하기 위한 Application과 Infrastructure 의 차이점보다 더 구조적인 내부와 외부의 이러한 경계를 주장합니다.

![image-center]({{ site.url }}{{ site.baseurl }}/assets/images/04_outside.png){: .align-center}


## 원칙3.경계는 인터페이스로 분리됨

![image-center]({{ site.url }}{{ site.baseurl }}/assets/images/05_interfaces.png){: .align-center}

응용 프로그램 코드는 비지니스 코드에 정의된 인터페이스(IRequestVerese를 통해 비지니스 코드를 실행합니다. 그리고 비니지스 코드는 또한 비지니스 코드(IObtainPoemes)에 정의된 인터페이스를 통해 Infrastructure를 실행합니다. 이 인터페이스는 내부와 외부 사이를 분리되게 합니다.

### 비유1 : Ports & Adapters

* Ports는 Interface / Adapters는 Interface(Port)의 구현으로 생각하면 됨.

Hexagonal 아키텍처는 내부와 외부의 상요 작용을 나타내기 위해 Ports와 Adapters를 사용합니다. 아래 이미지는 Domain이포트에 정의된 사양을 따르는 경우 모든 종류의 Adapter들을 상호 교환적으로 연결할 수 있는 Port를 정의한다는 것입니다.

![image-center]({{ site.url }}{{ site.baseurl }}/assets/images/06_port_adaptor.png){: .align-center}

예를 들어, 유닛 테스트에 사용하는 하드 코딩된 데이터 소스 또는 통합 테스트에서 사용하는 실제 데이터베이스에 연결하는 Domain의 Port를 생각해 볼 수 있습니다.단지 Infrastructure에서의 구현 및 Adapter들을 올바르게 코딩하면 Domain은 변경에 영향을 받지 않습니다.

비지니스 코드에 의해 정의된 이러한 인터페이스는 외부(Outside)와의 상호작용을 가능하게 하고 분리하는 Port는 Ports & Adapters 비유에서의 Port입니다.

* 앞서 언급했듯이 Port는 비지니스에 의해 정의되므로 내부(Inside)에 있습니다.

반면에 Adapters는 외부 코드를 나타내며, Port와 나머지 응용 프로그램 또는 Infrastructure 사이를 연결합니다. 

* 여기서 Adapters는 ConsoleAdapter와 PoetryLibraryFileAdapter이고 외부(Outside)에 있습니다.

### 비유2 : Hexagone

![image-center]({{ site.url }}{{ site.baseurl }}/assets/images/07_hexagone.png){: .align-center}

이 아키텍처에 이름을 붙인 또 다른 비유는 위의 그림에서 볼 수 있듯이 Hexagon입니다. 왜 Hexagon일까요? 주된 이유는 다이어그램에 어러 개의 Ports와 Adapters를 표현하기 위한 모양을 그리는 것이 쉽기 때문입니다. 그리고 Hexagonal 아키텍처의 표현은 Ports & Adapters 표현보다 더 대중적입니다.

# 상세1. 코드는 어떻게 내부와 외부로 구성될까?

위에서 보았던 원칙 외에 각 영역에 원하는 데로 정확하게 코드를 구성할 수 있습니다.

비지니스 코드, 내부 등에 대해서는 비지니스 로직에 따라 모듈을 구성하는 것은 선택할 수 있습니다. 

피해야 할 구성은 타입별로 클래스를 그룹화하는 것입니다. 예를 들어 "port" 디렉터리 또는 "repositories", 디렉터리, "services" 디렉터리 입니다. **모듈 또는 디렉터리의 구성을 포함하여 비지니스 코드는 100% 비지니스만 생각하십시오!** 이상적인 케이스는 디렉터리 또는 비니지스 로직 모듈 열고 프로그램이 해결하는 비지니스 문제를 직시 이해하는 것입니다. "repositories","services"또는 기타"managers"디렉토리 만 보는 것이 아닙니다.

**참고**

* [Four Strategies for Organizing Code](https://medium.com/@msandin/strategies-for-organizing-code-2c9d690b6f33)

* [Presentation Domain Data Layering](https://martinfowler.com/bliki/PresentationDomainDataLayering.html)

# 상세2. 런타임

런타임 의존성을 만족시키기 위해 이 모든 것을 어떻게 정확히 인스턴스 화 할까요? 의존성 주입(Dependency Injection) 프레임워크를 사용하고 있다면 이 질문을 스스로 할 필요는 없습니다. 그러나 Hexagonal 이키텍처를 이해하기 위해서는, 응용 프로그램이 시작할 때 무슨 일이 일어나는지 보는 것은 흥미로울 것이라고 생각합니다. 그리고 이것을 하기 위해서는 최소한 이 글을 읽는 동안은 의존성 주입 프레임워크를 사용하지 마세요.

예를 들어, 모든 것을 손으로 인스턴스 화 하는 경우 애플리케이션의 진입 점을 작성하는 방법은 다음과 같습니다.

```c#
class Program
{
    static void Main(string[] args)
    {
        // 1. Instantiate right-side adapter ("go outside the hexagon")
        IObtainPoems fileAdapter = new PoetryLibraryFileAdapter(@".\Peoms.txt");

        // 2. Instantiate the hexagon
        IRequestVerses poetryReader = new PoetryReader(fileAdapter);

        // 3. Instantiate the left-side adapter ("I want ask/to go inside")
        var consoleAdapter = new ConsoleAdapter(poetryReader);

        System.Console.WriteLine("Here is some...");
        consoleAdapter.Ask();

        System.Console.WriteLine("Type enter to exit...");
        System.Console.ReadLine();
    }
}
```

인스턴스 화 순서는 일반적으로 오른쪽에서 왼쪽입니다.

1. 처음으로 Infrastructure 측면의 파일을 읽는 fileAdapter 를 인스턴스 화 합니다.

2. Application에서 호출되고 생성자에 fileAdapter를 인자로 받는 poetryReader인 Domain 클래스 인스턴스 화 입니다.

3. poetryReader를 실행하고 콘솔에 작성하는 Application 측면의 consoleAdapter를 인스턴스 화합니다. 여기서 poetryReader는 생성자자의 인자로 consoleAdapter에 주어집니다.

내부는 외부에 의존하면 안 된다고 합니다. 그렇다면 왜 Infrastructure의 코드인 fileAdapter를 Domain의 코드인 poetryReader에 주입할까요 ?

스키마와 코드를 보면 PoetryLibraryFileAdapter(Infrastructure 측) 뿐만 아니라 fileAdapter도 상속에 의해서 IObtainPoems의 인스턴스이기 때문에 가능합니다.

실제로 PoetryReader는 IObtainPoems에 의존하지 PoetryLibraryFileAdater에 의존하지 않습니다. 다음의 코드에서 PoetryReader의 시그니쳐를 보면 확인할 수 있습니다.

```c#
public PoetryReader(IObtainPoems poetryLibrary)
{
    this.poetryLibrary = poetryLibrary;
}
```
PoetryLibraryFileAdapter와 PoetryReader는 약하게 결합(weakly couple)해 있습니다.

# 상세3. 의존성 역전 (Dependency Inversion)

fileAdapter가 그것의 정의를 위해 비지니스에 의존한다는 것은 사실입니다. 그러나 그것은 런타임에 poetryReader가 실제로 fileAdapter의 인스턴를 제어할 수 있다는 것은 의존성 역전으 전형적인 사례입니다.

실제로 IObtainPoems 인터페이스가 없다면 비지니스 코드는 정의를 위해 Infrastructure에 의존하기 때문에 그것은 피해야합니다.

![image-center]({{ site.url }}{{ site.baseurl }}/assets/images/08_no_interface.png){: .align-center}

인터페이스는 이 의존성의 방향을 반대로 할 수 있습니다.

![image-center]({{ site.url }}{{ site.baseurl }}/assets/images/09_interface.png){: .align-center}

외부 시스템에 독립적인 비지니스를 만드는 것 외에도 오른쪽의 인터페이스는 [SOLID](https://en.wikipedia.org/wiki/SOLID)의 D 또는 [의존성 역전 원칙](https://en.wikipedia.org/wiki/Dependency_inversion_principle)을 충족할 수 있습니다.

1. 고수준 모듈은 저수준 모듈에 의존하면 안 됩니다. 둘 다 추상화에 의존해야 합니다.

2. 추상화는 세부 사항에 의존하면 안 됩니다. 세부 사항은 추상화에 따라 달라져야 합니다.

인터페이스가 없으면 저수준 모듈(Infrastructure)에 의존하는 고수준 모듈(Domain)을 갖게됩니다.

**참고** : Application와 Domain 사이의 상호작용을 위한 의존성은 당연히 오른쪽 방향입니다.

![image-center]({{ site.url }}{{ site.baseurl }}/assets/images/10_driving-driven.png){: .align-center}

이 상호작용의 구현에서 다른점은 Application-Domain과 Domain-Infrastructure사이의 다른 관계와 관련되어 있습니다.

# 상세4. 왜 왼쪽에 인터페이스가 있을까?

Application과 Domain 사이의 의존성은 이미 오른쪽 방향이고 IRequestVerses 인터페이스의 역할은 의존성을 변경하는 것이 아닙니다.

하지만 그것은 Application과 Domain사이의 결합면에서 명백하게 제한하는 것은 여전히 흥미롭습니다. 

실제로 PoetryReader 클래스는 IRequestVerses 인터페이스와는 다른 메소드가 있을 수 있습니다. 그것은 ConsoleAdapter가 이것을 몰라도 된다는 것이 중요합니다.

클라이언트가 사용하지 않는 메소드에 대한 의존을 강제하면 안됩니다.

![image-center]({{ site.url }}{{ site.baseurl }}/assets/images/11_interface_segregation.png){: .align-center}


# Hexagonal 아키텍쳐 테스팅

일반적인 경우에 왼쪽 코드의 역할은 테스트 프레임워크에서 직접 실행될 수 있습니다. 실제로 테스트 코드는 비지니스 로직 코드를 직접 호출할 수 있습니다.

![image-center]({{ site.url }}{{ site.baseurl }}/assets/images/12_tests.png){: .align-center}

**참고** : 그림은 오른쪽 부분이 교체되지 않았으므로 통합 테스트를 보여줍니다. 교체 할 수도 있습니다 (아래 참조).


## Infrastructure의 일부 코드를 어떻게 변경할 수 있을까요 ?

오른쪽 코드는 비지니스가 주도해야합니다. 일반적으로 단위 테스트를 작성하려면 테스트하려는 대상에 따라 Mock 또는 다른 형태의 [TestDouble](https://martinfowler.com/bliki/TestDouble.html)으로 대체하십시오. 

사용자, 프로그램, 자동 테스트 또는 배치 스크립트를 통해 응용 프로그램을 구동하고 가능한 실행 시스템 및 데이터베이스와 별도로 개발 및 테스트 할 수 있습니다.

이것이 Application 과 Infrastructure 코드를 테스트하지 못하게 하는것은 아니며, 모든 코드는 테스트할 가치가 있습니다. 이 주제에 대해서는 *[the test pyramid by practice](https://blog.octo.com/en/the-test-pyramid-in-practice-1-5/)*를  다시 참조하세요.

실제로 우리가 대체하거나 대체하지 않는 것을 결합함으로써 우리는이 아키텍처를 통해 우리가 원하는 것을 테스트 할 수 있음을 알 수 있습니다.

* 전체 Domain은 개별적으로
* Infrastructure 측면에서 독립적으로 Application과 Domain 간 통합
* Application 측에서 독립적으로 Domain과 Infrastructure 통합
