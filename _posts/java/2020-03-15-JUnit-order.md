---
title: "테스트 실행 순서"
classes: wide
excerpt: "[원문] Test execution order"
header:
  overlay_image: /assets/images/develop-unsplash.jpg
  overlay_filter: rgba(255, 255, 255, 0.3)
  actions:
    - label: "Go to"
      url: "https://github.com/junit-team/junit4/wiki/Test-execution-order"
categories:
  - Java
  - Test
tags:
  - JUnit
last_modified_at: 2020-04-15T14:14:00
---

## 테스트 실행 순서

  기본적으로, JUnit은 테스트 메소드 호출의 실행 순서를 지정할 수 없습니다. 지금까지 메소드는 리플랙션 API가 반환하는 순서로 단순히 호출하였습니다. 그러나 Java 플랫폼은 특정 순서를 지정하지 않으므로 JVM 순서를 사용하는 것은 현명하지 않으며 실제로 JDK 7은 다소 임의의 순서를 반환합니다. 물론, 잘 작성된 테스트 코드는 어떤 순서도 가정하지 않지만 일부는 예측할 수 있으며 예측 가능한 실패는 특정 플랫폼의 임의 실패보다 낫습니다. 

  버전 4.11부터 JUnit은 기본적으로 결정적이지만 예측할 수없는 순서 (MethodSorters.DEFAULT)를 사용합니다. 테스트 실행 순서를 변경하려면 @FixMethodOrder를 사용하여 테스트 클래스에 어노테이션을 달고 사용 가능한 MethodSorters 중 하나를 지정하십시오.

  * *@FixMethodOrder(MethodSorters.JVM)*: 테스트 메소드를 JVM이 리턴 한 순서대로 둡니다. 이 순서는 실행마다 다를 수 있습니다.

  * *@FixMethodOrder(MethodSorters.NAME_ASCENDING)*: 테스트 메소드를 메소드 이름의 사전순으로 정렬합니다. 

### Example

  아래의 클래스는 suite 어노테이션를 사용한 코드 조각이며, 다른 구현은 요구되지 않습니다. 이 특정 클래스를 실행하기위해 사용할 JUnit 4 테스트 러너가  `org.junit.runners.Suite`임을 지정하는 `@RunWith` 어노테이션을 참고하세요.  이것은 Suite 러너에게 포함할 테스트 클래스와 순서를 알려주는 `@Suite.SuiteClasses` 어노테이션과 함께 사용합니다.

  ```java
    import org.junit.FixMethodOrder;
    import org.junit.Test;
    import org.junit.runners.MethodSorters;

    @FixMethodOrder(MethodSorters.NAME_ASCENDING)
    public class TestMethodOrder {

        @Test
        public void testA() {
            System.out.println("first");
        }
        @Test
        public void testB() {
            System.out.println("second");
        }
        @Test
        public void testC() {
            System.out.println("third");
        }
    }
  ```

  위의 코드는 테스트 방법을 이름 순서대로 오름차순으로 정렬합니다.

