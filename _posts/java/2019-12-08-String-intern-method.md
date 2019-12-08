---
title: "String의 intern() 메소드는 언제 사용해야하는가 ?"
classes: wide
excerpt: "[원문] When to use intern() method of String in Java?"
header:
  overlay_image: /assets/images/develop-unsplash.jpg
  overlay_filter: rgba(255, 255, 255, 0.3)
  actions:
    - label: "Go to"
      url: "https://javarevisited.blogspot.com/2015/12/when-to-use-intern-method-of-string-in-java.html"
categories:
  - Java
tags:
  - String
last_modified_at: 2019-12-08T14:14:00
---

`String.intern()` 메소드는 **Java에서 문자열(String) 중복 문제** 를 처리하는데 사용할 수 있습니다.

`intern()`을 주의해서 사용하면 중복된 문자열(String) 객체에 의해 소비되는 많은 힙 메모리를 절약할 수 있다.

문자열(String) 객체가 만약 다른 문자열과 동일한 내용을 포함하지만 다른 메모리에 위치했다면중복되었다고 한다. eg. `str1 != str2` 그러나 `str1.equals(str2)`는 같다.

문자열(String) 객체는 보통 Java 어플리케이션에서 많은 양의 힙 메모리를 소비하므로, 중복을 줄이기위해 `intern()` 메소드를 사용하는 것이고, Java에서 제공하는 `String pool 기능`을 사용하십시오.

`intern()` 메소드는 문자열(String) 객체를 유지하는데 사용할 수 있고, 재사용을 위해 String pool에 저장할 수 있습니다.

예들 들어, “`ABC`”와 같은 문자열(String) 리터럴을 만들 때 `String pool`에 자동으로 저장되지만, new 로 문자열(String) 객체가 생성될 때는 저장되지 않는다.e.g. `new String(“abc”)`, 그것은 같은 문자열(String) 이지만, 다른 메모리 위치에 새로운 객체가 생성된다. 이것이 **중복 문자열**입니다.

New를 사용하여 생성된 객체에서 `intern()` 메소드를 호출하면, JVM이 `String pool`에 이 문자열을 추가하고, 누군가 `abc` 를 만들 때 마다 알려줄 수 있다. `abc` 는 새로운 객체가 만들어지는 대신 반환될 것입니다.

이 방법은 얼마나 많은 문자열(String)들이 프로그램에서 중복되는지에 따라 Java에서 많은 메모리를 절약할 수 있습니다.

## `String.intern()` 메소드에 대한 몇 가지 중요한 사항

알아 두면 좋은 `java.lang.String` 클래스의 `intern()` 메소드에 대한 중요한 점: 

1) `String.intern()` 메소드는 **JDK1.1**부터 String 클래스에 있다. 이것은 문자열(String) 객체를 반환됩니다.. `intern` 메소드가 실행될 때, `String pool`에 `equals()`가 true를 반환하는 문자열이 이미 포함되어 있다면, `String pool` 에서 문자열(String) 객체가 반환될 것이고, 그렇지 않으면 그것은 `String pool에 추가 될 것입니다.

2) `s1`과 `s1`에서 `intern()` 메소드를 호출한 후에 이고, `s1.equals(s2)`이 true인 경우 둘 다 `String pool`에서 동일한 문자열(String) 상수를 가리키므로 `s1.inern() == s1.inern()`도 true입니다.

3) Java 6 이전에, 제어되지 않는 `String.intern()` 메소드의 사용은 `java.lang.OutOfMemory: PermGen space`이 발생할 수 있었습니다. `String pool`은 물리적으로 Java Heap의 PermGen 영역에 위치되어 JVM(32M-96M)에서 상당히 작았기 때문이고, 수정되었습니다.

Java 7 이후 부터, `intern()` 메소드는 `String pool`이 JVM의 메인 heap space에 위치되었기 떄문에 보다 더 유용해졌습니다.

이는 `String.intern()` 메소드를 사용으로 문자열 중복을 더 줄이는데 도움이 될 것입니다.

아래의 다이어그램에서 `s3`과 `s4`는 `String pool`에서 동일한 문자열 객체 “java”를 참조있기 떄문에 문자열이 `String pool`에 있는 것을 볼 수 있고, `s1`과 `s4`는 `String pool`에 있지도 문자열(String) 리터럴을 사용하지도 않았기 때문에 다른 객체를 참조하는 것을 볼 수 있다.

![image-center]({{ site.url }}{{ site.baseurl }}/assets/images/String literal vs String Object in Java.png){: .align-center}

4) 알아야 할 또 다른 것은 `intern()` 메소드는 non-static 메소드이고 Java에서 문자열 리터럴 또는 문자열 객체를 사용하여 호출되어야한다는 것입니다.

---

이것들이 Java에서 `String.intern()` 메소드를 사용할 때에 대한 모든 것입니다.

Java 8 업데이트 20에서는 또한 `String deduplication`으로 불리는 새로운 기능이 소개되었고, 그것은 코드 한 줄 작성 없이 문자열 중복에 의해 발생하는 [Memory footprint](https://en.wikipedia.org/wiki/Memory_footprint)를 줄일 수 있습니다. 그러나 불행하게도, 그것은 오직 G1 가비지 컬렉터에서 유효하고 `ConcurrentMarkSweep` 가비지 컬렉터를 사용한다면 그것을 사용할 수 없습니다.