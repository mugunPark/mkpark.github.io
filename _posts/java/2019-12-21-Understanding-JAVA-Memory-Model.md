---
title: "Java 메모리 모델의 이해"
classes: wide
excerpt: "[원문] Understanding Java Memory Model"
header:
  overlay_image: /assets/images/develop-unsplash.jpg
  overlay_filter: rgba(255, 255, 255, 0.3)
  actions:
    - label: "Go to"
      url: "https://medium.com/platform-engineer/understanding-java-memory-model-1d0863f6d973"
categories:
  - Java
tags:
  - Memory
last_modified_at: 2019-12-21T17:24:00
---
Java 메모리 모델은 Java 어플리케이션을 개발, 배포, 모니터, 테스트, 성능 튜닝을 하는 Java 개발자들이 필수로 이해해야 합니다. 여기서 Java 메모리 모델과 프로그램을 실행하기위해 JVM 메모리의 각 부분이 어떻게 동작하는지 이야기해보겠습니다.

... {:.text-center}

처음으로, 다음의 JVM 아키텍처 다이어그램을 이해하고 있는지 확인해보세요. 만약 이것이 잘 알고있지 않는다면, 이 글을 읽기 전에 이전 글(["JVM Architecture 이해"](/Understanding-JVM-Architecture))을 한번 읽어보고 오시는것을 추천드립니다.

![image-center]({{ site.url }}{{ site.baseurl }}/assets/images/JVM Architecture.png){: .align-center}

# JVM 메모리 모델

다음 중 일부가 자원 집약적(resource-intensive)인 Java 프로그램을 실행할 때, JVM 메모리 설정에 사용해야 합니다.

* **-Xms** - 초기 Heap 사이즈

* **-Xmx** - 최대 Heap 사이즈

* **-XX:NewSize** - `Young Generation` Heap 사이즈

* **-XX:MaxNewSize** - 최대 `Young Generation` Heap 사이즈

* **-XX:MaxPermGen** - 최대 `Permanent Generation` 사이즈

* **-XX:SurvivorRatio** - new Heap 사이즈 비율(e.g. Young Gen 사이즈가 10m 이고 메모리 스위치가 -XX:SurvivorRatio=2이고, 이 때 `Eden space`는 5m, 두 `Survivor space`는 2.5m 가 될 것입니다. 기본 값은 8 입니다.)

* **-XX:NewRatio** - `Old/New Gen` 사이즈의 비율(기본 값은 2)

그러나 JVM이 메모리에 어떻게 상주하는지 궁금한 적이 있습니까? 다른 소프트웨어와 마찬가지로, JVM은 실행하는 OS 메모리에서 사용가능한 공간을 사용합니다.

![image-center]({{ site.url }}{{ site.baseurl }}/assets/images/Host OS Memory and JVM.png){: .align-center}

하지만, JVM에는 런타임 데이터와 컴파일된 코드를 저장하기위해 구분된 메모리 공간(Heap, Non-Heap, Cache)이 있습니다.

# 1) Heap 메모리

* Heap은 2개로 분리됩니다. - `Young Gen` , `Old Gen`

* Heap은 JVM이 시작될 때 할당됩니다.(-Xms)

* Heap 사이즈는 어플리케이션이 실행되는 동간 증가/감소 합니다.

* 최대 사이즈: -Xmx


![image-center]({{ site.url }}{{ site.baseurl }}/assets/images/JVM Heap Memory.png){: .align-center}


## 1.1) Young Generation

* 새롭게 할당되는 객체를 포함하기위한 공간입니다.

* **Young Gen**은 세 가지 포함합니다. - **Eden Memory** 두 개의 **Survivor Memory** space (S0, S1)

* 새롭게 만들어지는 객체 대부분은 **Eden space**로 갑니다.

* **Eden space**가 객체들로 가득차게되면, **Minor GC**(a.k.a Young Collection)가 실행되고, 모든 survivor 객체들은 **survivor space** 중 하나로 이동 됩니다.

* Minor GC는 또한 suvivor 객체들의 확인하고, 다른 survivor space로 이동시킵니다.  따라서 한번에 survivor space 중 하나는 항상 비어있습니다.

* 여러 번의 GC 후에도 존재하는 객체들은 **Old Gen** 메모리 공간으로 이동됩니다. 일반적으로 Old Gen으로의 이동은 old gen으로 이동하기 전에 young gen 객체들의 한계점을 설정하여 이루어집니다.

## 1.2) Old Generation

* 여러번의 Minor GC 이후에도 여전히 남아있는 오랫동안 생존한 객체들을 포함하기위한 공간입니다.

* Old Gen 공간이 가득차게되면, **Major GC**(a.k.a Old Collection)이 실행됩니다.

# 2) Non-Heap Memory

* **Permanent Generation**(Java 8에서 **Metaspace**로 변경)을 포함합니다.

* Perm Gen은 런타임 상수 풀, 필드, 메소드 데이터, 그리고 메소드, 생성자 코드 및 내부 문자열과 같은 각 클래스 구조를 저장합니다. 

* 사이즈는 -XX:PermSize, -XX:MaxPermSize을 사용하여 변경될 수 있습니다.

![image-center]({{ site.url }}{{ site.baseurl }}/assets/images/JVM Non-Heap과 Cache Memory.png){: .align-center}


# 3) Cache Momory

* Code Cache를 포함합니다.

* JIT 컴파일러가 만든 컴파일된 코드(i.e native 코드), JVM 내부 구조, 로드된 프로파일러 에이전트 코드 및 데이터 등을 저장합니다.

* Code Cache가 한계값을 초과하면 비워집니다(객체는 GC에 의해 재할당되지 않습니다.). 


# Stack vs. Heap

지금까지 Java Stack 메모리에 대해서는 어떠한 언급을 하지 않았습니다. 왜냐하면 그 차이점을 별도로 강조하고 싶었습니다. 먼저, 아래 이미지를 보고 얼마나 이해되는지 확인해보세요.

![image-center]({{ site.url }}{{ site.baseurl }}/assets/images/JVM Stack Non-Heap Heap.png){: .align-center}

Java Stack 메모리는 스레드의 실행에 사용되고, 메소드의 값들 포함하며, Heap의 다른 객체들의 참조합니다.

![image-center]({{ site.url }}{{ site.baseurl }}/assets/images/stack vs heap.png){: .align-center}


간단한 프로그램으로 Stack과 Heap 이 어떻게 동작하는지 보겠습니다. 

```Java
class Person {
    int pid;
    String name;
// constructor, setters/getters
}
public class Driver {
    public static void main(String[] args) {
        int id = 23;
        String pName = "Jon";
        Person p = null;
        p = new Person(id, pName);
    }
}
```

![image-center]({{ site.url }}{{ site.baseurl }}/assets/images/Stack Heap example.jpg){: .align-center}


# 변경

위의 Java 메모리 모델은 대부분 일반적으로 논의되는 구현입니다. 하지만 최신 JVM 버전은 다음과 같은 새 메모리 공간을 도입하는 등 수정이 있습니다.

* **Keep Area** - 가장 최근에 할당된 객체들을 포함하는 Young Gen의 새 메모리 공간. 다음 Young Gen까지 GC는 수행되지 않습니다. 이 공간은 Minor GC가 시작되기 전에 할당되었기 대문에 객체가 승격되지 않도록 합니다.

* **Metaspcace** - Java 8 이후, Perm Gen은 Metaspace로 변경되었습니다. Perm Gen은 항상 고정된 최대 사이즈를 가졌지만, Mataspace는 자동으로 사이즈를 증가시킬 수 있습니다(OS가 제공하는 사이즈 까지). 클래스 로더가 활성 상태면, 메타데이터는 Metaspace에서 활성 상태로 유지되고 해제할 수 없습니다.

# 메모리와 관련된 이슈

심각한 메모리 문제가 발생하면 JVM이 다운되고 아래와 같이 프로그램 출력에 오류 표시가 발생합니다

* **java.lnag.StackOverFlowError** - Stack 메모리가 가득찬것을 나타냄

* **java.lang.OutOfMemoryError: Java heap space** - Heap 메모리가 가득찬것을 나타냄

* **java.lang.OutOfMemoryError: GC Overhead limit exceeded** — GC 오버헤드 한도에 도달함을 나타냄

* **java.lang.OutOfMemoryError: Permgen space** —  Permgen space가 가득찬것을 나타냄

* **java.lang.OutOfMemoryError: Metaspace** — Metaspace가 가득찬것을 나타냄 (since Java 8)

* **java.lang.OutOfMemoryError: Unable to create new native thread** — 너무 많은 스레드가 이미 생성되었고 JVM에서 사용가능한 모든 메모리를 사용했기 때문에 더이상 새로운 native 스레드를 생성할 수 없다는 것을 나타냄

* **java.lang.OutOfMemoryError: request size bytes for reason** — swap 메모리 공간이 어플리케이션에서 완전이 소비되었음을 나타냄

* **java.lang.OutOfMemoryError: Requested array size exceeds VM limit**– 어플리케이션이 플랫폼의 허용된 사이즈보다 더 많은 배열 사이즈를 사용하는 것을 나타냄


하지만, 확실히 이해해야 하는 것은 이런 출력은 실제 오류가 아니라 JVM에 미치는 영향만 나타낼 수 있다는 것입니다. 실제 오류와 근본 원인 조건은 코드 어딘가에서 발생할 수 있습니다.(e.g. 메모리 누수, GC 문제, 동기화 문제) 리소스 할당 또는 하드웨어 설정일 수 있습니다.

그러므로 문제를 해결하기 위해 영향을 받는 리소스 크기를 늘리는 것이 좋습니다.아마 리소스 사용량을 모니터하는 것이 필요 할 것이고, 각 카테고리를 프로파일하고, heap 덤프를 통해 코드를 확인하고, 디버깅, 최적화해야할 수 도 있습니다. 

