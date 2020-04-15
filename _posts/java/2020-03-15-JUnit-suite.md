---
title: " Aggregating tests in suites"
classes: wide
excerpt: "[원문] Aggregating tests in suites"
header:
  overlay_image: /assets/images/develop-unsplash.jpg
  overlay_filter: rgba(255, 255, 255, 0.3)
  actions:
    - label: "Go to"
      url: "https://github.com/junit-team/junit4/wiki/Aggregating-tests-in-suites"
categories:
  - Java
  - Test
tags:
  - JUnit
last_modified_at: 2020-04-15T14:14:00
---

## Aggregating tests in suites

  Suite을 러너로 사용하면 많은 클래스로부터 테스트를 포함하는 suite을 수동으로 빌드 할 수 있습니다. 이는 JUnit3.8.x의 `static Test suite()` 메소드 동일한 JUnit 4의 기능 입니다. 이를 사용하기 위해서는 `@RunWith(Suite.class)` 와  `@SuiteClasses(TestClass1.class, ...)` 어노테이션을 사용합니다. 이 클래스가 실행될 때, 모든 suite 클래스들에 포함한 테스트를 실행할 것입니다.

### Example

  아래의 클래스는 suite 어노테이션를 사용한 코드 조각이며, 다른 구현은 요구되지 않습니다. 이 특정 클래스를 실행하기위해 사용할 JUnit 4 테스트 러너가  `org.junit.runners.Suite`임을 지정하는 `@RunWith` 어노테이션을 참고하세요.  이것은 Suite 러너에게 포함할 테스트 클래스와 순서를 알려주는 `@Suite.SuiteClasses` 어노테이션과 함께 사용합니다.

  ```java
    import org.junit.runner.RunWith;
    import org.junit.runners.Suite;

    @RunWith(Suite.class)
    @Suite.SuiteClasses({
      TestFeatureLogin.class,
      TestFeatureLogout.class,
      TestFeatureNavigate.class,
      TestFeatureUpdate.class
    })

    public class FeatureTestSuite {
      // the class remains empty,
      // used only as a holder for the above annotations
    }
  ```

