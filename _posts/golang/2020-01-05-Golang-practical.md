---
title: "실용적인 Go : 유지 보수가능한 Go 프로그램 작성을 위한 현실적인 조언"
classes: wide
excerpt: "[원문] Practical Go: Real world advice for writing maintainable Go programs"
header:
  overlay_image: /assets/images/develop-unsplash.jpg
  overlay_filter: rgba(255, 255, 255, 0.3)
  actions:
    - label: "Go to"
      url: "https://dave.cheney.net/practical-go/presentations/qcon-china.html"
categories:
  - Golang
tags:
  - goroutine
  - concurrency
last_modified_at: 2020-01-05T20:00:00
---

## 1. 원칙

### 1.1 단순함(simplicity)

 > 단순함은 신뢰를 위한 전제 조건이다. - Edsger W. Dijkstra

왜 우리는 단순함을 위해 노력 해야하고 왜 Go 프로그램이 단순해지는 것이 중요할까요 ?

우리는 모두 "이 코드를 이해할 수 없다" 말하는 상황을 격고 있고, 우리는 모드 변경을 만드는 것이 두려운 프로그램을 만들고 있을 것입니다. 왜냐하면 코드를 이해할 수 없을 수 없고, 어떻게 수정해야하는지 알지 못하기 때문에 프로그램의 다른 부분에 영향을 받는것이 걱정되기 때문입니다. 이것은 복잡합니다.

 > 소프트웨어 디자인을 구성하는 방법에는 두가지가 있습니다. 첫 번째 방법은 명백하게 결함이 없도록 매우 단순하게 만드는 것이고 두 번째 방법은 명백하게 결함이 없도록 매우 복잡하게 만드는 것입니다. 첫 번째 방법은 훨씬 더 어렵다. - C.A.R Hoare

복잡성은 신뢰할 수 없는 소프트웨어에서 신뢰할 수 있는 소프트웨어를 만드는 것입니다. 복잡성은 소프트웨어 프로젝트를 죽입니다. 그러므로 단순함은 Go의 최종 목표입니다. 우리가 어떤 프로그램을 작성하든, 우리는 그것들이 단순하다는데 동의 할 수 있어야 합니다. 

### 1.2. 가독성(Readability)

 > 가독성은 유지 보수에 필수적입니다. — Mark Reinhold (JVM language summit 2018)

Go 코드를 읽을 수 있어야하는 이유는 무엇이며 왜 가독성을 위해 노력해야 할까요 ?

  > 사람들이 읽을 수 있도록, 그리고 기계가 실행할 수 있도록 프로그램을 작성해야합니다. - Hal Abelson and Gerald Sussman (Structure and Interpretation of Computer Programs)

가독성은 Go 프로그램 뿐만아니라 모든 소프트웨어는 사람에 의해 작성되고 또 다른 사람에 의해 읽어지기 때문에 중요합니다. 기계가 소프트웨어를 실행한다는 사실은 부수적인 것입니다.

코드는 작성되는 시간보다 더 많은 시간 읽혀집니다. 코드의 한 부분은 수백 번, 아마도 수천 번 읽습니다.

  > 프로그래머에게 가장 중요한 기술은 효과적으로 생각을 전달하는 능력입니다. — Gastón Jorquera 

가독성은 프로그램이 무엇을 하는지 이해할 수 있게 만드는 핵심입니다. 프로그램이 무엇을 하는지 이해할 수 없다면 어떻게 그것을 유지보수 할 수 있겠습니까? 소프트웨어가 유지보수 될 수 없다면 코드를 다시 작성해야하고 Go로 더 이상 코드를 만들수 없을 것입니다.

자신을 위해 프로그램을 작성하는 경우 한 번만 실행하거나 프로그램을 볼 수있는 유일한 사람이라면 자신을 위한 일을 하십시오. 그러나 많은 사람이 기여하는 소프트웨어의 한 부분이거나 요구사항, 기능 또는 환경이 변화하는 환경에서 오랜 사용된다면 프로그램이 유지 보수 가능하게 되는 목표를 가져야합니다.

**유지 관리 가능한 코드를 작성하는 첫 번째 단계는 코드를 읽을 수 있도록하는 것입니다.**

### 1.3 생산성

  > 디자인  코드 작동하도록 배열하고 언제든지 변경 가능한 예술입니다. - Sandi Metz
  
마지막 원칙으로 생산성을 강조하고 싶습니다. 개발자 생산성은 큰 주제이지만 작업에 도구를 기다리거나 이질적인 코드 기반에서 절망하는데 얼마나 많은 시간을 소비하는가로 압축할 수 있습니다. Go 프로그래머는 Go로 많은 것을 할 수 있다고 생각해야 합니다.

농담으로 Go는 C++ 프로그램이 컴파일되는 것을 기다리는 동안 디자인되었다고 합니다. 빠른 컴파일은 Godml 핵심 기능이고 새로운 개발자를 끌어들이는 핵심입니다. 컴파일 속도는 여전히 개선 중이지만 다른 언어로 몇 분이 걸리는 컴파일은 Go에서 몇 초가 걸린다고 말할 수 있습니다. 이것은 Go 개발자가 언어 고유의 안정성 문제 없이 다이나믹 언어로 작업하는 것 처럼 생산성이 높아집니다.

개발자 생산성에 대한 문제에 보다 근본적으로 Go 개발자는 코드 읽기 위해 작성하고 누군가에 의해 읽혀질 것이라고 생각하고 코드를 작성해야한다는 것을 자각해야합니다. Go는 툴 또는 사용자 정의를 통해 모든 코드가 특정한 스타일로 포멧되도록 적용합니다. 이것은 프로젝트 고유의 스타일을 배우는 시간을 줄이고 실수를 발견하는데 도움이 됩니다.

Go 프로그래머는 많은 컴파일 오류를 디깅하는데 몇 일씩 시간을 보내지 않습니다. 복잡한 빌드 스크립트나 코드를 배포하는데 많은 시간을 낭비하지 않습니다. 그리고 가장 중요한 것은 동료가 쓴 내용을 이해하려고 시간을 소비하지 않습니다.

## 2. 식별자(Identifiers)

처음으로 이야기할 주제는 식별자입니다. 식별자는 변수의 이름, 함수의 이름 메소드의 이름, 타입의 이름 패키지의 이름 등의 이름을 나타내는 단어입니다. 

  > 좋지 않은 이름을 붙이는 것은 좋지 않은 디자인의 증상입니다. - Dave Cheney

Go의 문법은 제한되어 있으므로 프로그램에서 작성한 이름은 프로그램의 가독성에 큰 영향을 미칩니다. 가독성은 좋은 코드의 품질을 정의하므로 좋은 이름을 선택하는 것은 Go 코드의 가독성에 중요합니다.

### 2.1 간결함이 아닌 명확함을 위한 식별자 선택

  > 명확한 코드는 중요합니다. 한 줄로 할 수 있는 일은 세 줄로 해야합니다.

Go는 한 줄로 작성하기에 최적화된 언어가 아닙니다. Go는 프로그램에서 적은 라인 수로 작성하기에 최적화된 언어가 아닙니다. 디스크에 소스 코드의 크기나 편집기에서 프로그램을 작성하는데 걸리는 시간을 최적화하지 않습니다. 

  > 쫗은 이름 작성은 재미있는 농담과 같습니다. 이름을 설명해야 한다면 그것은 재미 없는 것입니다. - Dave Cheney

명확함의 핵심은 Go 프로그램에서 식별을위해 선택하는 이름입니다. 좋은 이름의 특징에 대해 알아보겠습니다.

  * **좋은 이름은 간결합니다.** 좋은 이름이 가능한 가장 짧을 필요는 없지만 관련없는 단어를 사용하지 말아야합니다. 좋은 이름은 하는 일을 명확하게 설명합니다.

  * **좋은 이름은 설명적입니다.** 좋은 이름은 내용이 아니라 변수 또는 상수가 어떻게 사용되는지를 설명해야 합니다. 좋은 이름은 패키지의 내용이 아니라 목적을 설명해야합니다. 더 정확하게 이름은 그것이 나타내는 것을 설명하는 것이 더 좋은 이름입니다.

  * **좋은 이름은 예측 가능해야합니다.** 이름만으로 심볼이 사용되는 방식을 에상할 수 있어야합니다. 이것은 전통적으로 설명하는 이름을 선택하는 방법입니다. 이것이 바로 Go 프로그래머가 관용적이라고 할 때 이야기하는 것입니다.

  이러한 각 속성에 대해 자세히 설명하겠습니다.

  ### 2.2 식별자 길이

  때로는 사람들이 짧은 변수 이름을 추천하는 Go 스타일을 비판합니다. Rob Pike는 "Go 프로그래머는 올바른 길이의 실별자를 원한다."라고 이야기 했습니다.

  Andrew Gerrand는 더 긴 식별자를 사용하여 더 중요한 것을 표시하도록 제안합니다.

    > 이름의 선언과 사용하는 곳의 거리가 멀수록 이름은 더 길어야합니다. - Andrew Gerrand

  이것으로부터 우리는 몇가지 지침을 만들 수 있습니다.

    * 짧은 변수 이름은 선언과 마지막 사용 사이의 거리가 짧을 때 사용.

    * 긴 변수 이름은 스스로를 정당화해야합니다. 더 길수록 더 많은 가치를 제공해야합니다. 비효율적으로 긴 이름은 페이지의 무게에 비해 적은양의 의미를 전달합니다.

    * 변수 이름에 타입의 이름을 포함하지 마십시오.

    * 상수는 값이 어떻게 사용되는지가 아닌 저장되는 값을 설명해야 합니다.

    * 반복 및 분기의 경우 한 글자 변수, 파라미터 및 리턴 값은 한 단어, 함수 및 패키지 레벨 선언은 여러 단어를 사용

    * 메소드, 인터페이스, 패키지는 한 단어 사용

    * 패키지의 이름은 참조하기 위해 사용하는 이름의 일부라는 것을 기억하고 사용하십시오.

    예를 봅시다.
    ```go
      type Preson struct {
        Name string
        Age int
      }

      // AverageAge returns the average age of people.
      func AverageAge(people []Person) int {
        if len(people) == 0 {
          return 0
        }

        var count, sum int
        for _, p := range people {
          sum += p.Age
          count += 1
        }

        return sum / count
      }
    ```
  예제에서 range 변수 p는 선언되고 다음 행에서 한번만 참조됩니다. p는 페이지와 함수 실행 중 매우 짧은 시간 동안 지속됩니다. p의 값에 흥미를 가진 사람은 단지 두 라인만 보면 됩니다.

  people는 함수 파라미터에 선언되었고, 함수가 종료될 때 까지 존재합니다. sum과 count도 마찬가지 이므로 더 긴 이름을 정당화합니다. 많은 라인을 봐야하기 때문에 고유한 이름을 부여해야합니다.

  sum의 경우 s, count의 경우 c를 사용할 수 있지만 프로그램의 모든 변수의 중요도를 동일한 수준으로 줄이게 되는 것일 겁니다. people을 대신에 p를 사용할 수 있지만 그것은 for...range 구문이 호출하는 문제가 남아있습니다. 짧은 시간 동안 존재하는 반복문의 변수가 person으로부터 분리된 값의 일부분 보다 더 긴 이름을 가지면 이상하게 보일 것입니다.

  {% capture notice-text %}
    빈 줄을 사용하여 단락을 사용하여 문서의 흐름을 나누는 것과 같은 방식으로 함수 흐름을 나눕니다.AverageAge에는 세 가지 작업이 순서대로 발생합니다. 첫 번째는 전제 조건으로, 사람들이 비어 있으면 0으로 나누지 않고 두 번째는 합계와 수의 누적이며 마지막은 평균의 계산입니다.
  {% endcapture %}

  <div class="notice--info">
    <h4>TIP</h4>
    {{ notice-text | markdownify }}
  </div>

  ### 2.2.1 문맥은 핵심입니다.

  이름 작성에 대한 대부분의 조언은 상황에 따라 다르다는 것이 중요합니다. 나는 그것이 규칙이 아니라 원칙이라고 말하고 싶습니다.

  i와 index는 서로 다릅니다. 둘 중 어떤 것이 좋다고 확실하게 이야기 할 수는 없습니다.

  ```go
    for index := 0; index < len(s); index++ {
      // 
    }
  ```
  이것이 더 읽기 쉽다고 생각할 수도 있습니다.
  ```go
    for i := 0; i < len(s); i++ {
      //
    }
  ```
  하지만 꼭 그렇지만은 않습니다. 왜냐하면 i 와 index는 범위를 나타내고, 그것은 for 루프의 본문으로 제한되고 이후 추가적인 내용은 프로그램을 이해하는데 거의 도움을 주지 않기 때문입니다.

  하지만, 이 함수들 중 어떤 것이 더 읽기 쉬울까요 ?

  ```go
    func (s *SNMP) Fetch(oid []int, index int) (int, error)
  ```

  또는

  ```go
    func (s *SNMP) Fetch(o []int, i int) (int, error)
  ``` 

  이 예제에서 oid는 SNMP Object ID의 축약이기 때문에 o로 짧게 쓰는 것은 프로그래머가 문서에서 읽는 일반 표기법에서 코드에 떠 짧은 표기법으로 변환해야함을 의미합니다. 비슷하게 index에서 i로 줄이는 것은 각 OID의 하위 값이 Index인 SNMP 메시지에서 i가 무엇을 나타내는지 모호해집니다.

  {% capture notice-text %}
    동일한 선언에서 축약형과 일반 표기법을 혼용해서 사용하지 마세요. 
  {% endcapture %}

  <div class="notice--info">
    <h4>TIP</h4>
    {{ notice-text | markdownify }}
  </div>

  ### 2.3 타입을 변수 이름에 사용하지 마세요.

  애완 동물이름에 개와 고양이를 붙이지 않는 것과 마찬가지로 변수 이름 뒤에 타입을 사용해서는 안됩니다. 또한 변수 이름에 타입 이름을 붙여서도 안된됩니다.

  변수의 이름은 타입이 아닌 내용을 설명해야합니다.

  ```go
    var userMap map[string]*User
  ```

  이 선언의 무엇이 좋은것일 까요? 변수가 Map이라는 것, *User 타입과 관련되어 있는 것을 볼 수 있기때문에 좋은 선언이라고 생각할 수도 있습니다. 하지만 usersMap은 map이고 정적 타입 언어인 Go는 다른 타입이 요구되는 곳에서 실수로 사용할 수 없도록 합니다. 그렇기 떄문에 Map 접미사는 불필요합니다.

  이제 다음과 같은 다른 변수를 선언하면 어떻게 될지 고려하십시오.

  ```go
    var (
      companiesMap map[string]*Company
      productsMap map[string]*Products
      userMap map[string]*User
    )
  ```

  userMap, companiesMap, productsMap 3가지 map 타입의 변수가 있고 모두 string에 다른 타입을 매핑합니다. 이들이 map이라는 것을 알고, 또한 map 선언은 다른 맵을 대신 사용하지 못한다는 것을 알고 있습니다. 컴파일러는 map[string]*User가 필요한 코드에 companiesMap을 사용한다면 오류를 throw 할 것입니다. 이 상황에서 Map 접미사는 코드의 명확성을 향상시키지 않으며, 그것은 단지 입력할 수 있는 추가 상용구 입니다.

  변수 타입과 같은 모든 접미사를 붙이지 않는 것이 좋다고 생각합니다.

  ```go
    type Config struct{
      //
    }

    func WriteConfig(w io.Writer, config *Config)
  ```

  *Config 파마리터를 config로 명명하는 것은 불필요합니다. 그것이 *Config라는 것을 알고있기 떄문입니다.

  이 경우에는 변수의 유효범위가 짧다면 conf 또는 c로 하는 것을 고려하십시오.

  언제나 범위에 하나 이상의 *Config가 있을 때 conf1 및 conf2로 하는 것은 original 및 updated로 하는 것보다 정보를 주지 못하는 것과 같이 후자가 실수할 가능성이 더 적습니다.

  {% capture notice-text %}
    **패키지 이름에 좋은 변수 이름이 될 만한 이름을 사용하지 마세요.**

    Import하는 식별자 이름은 패키지 이름을 포함합니다. 예를들어 context패키지의 Context 타입은 context.Context일 것입닌다. 이것은 패키지에서 context를 변수 또는 타입으로 사용할 수 없게 합니다.

    ```go
      func WriteLog(context context.Context, message string)
    ```

    이것은 컴파일 실패할 것입니다. 이것이 context.Context 타입에 대한 로컬 선언이 전통적으로 ctx인 이유입니다.

    ```go
      func WriteLog(ctx context.Context, message string)
    ```

  {% endcapture %}

  <div class="notice--info">
    <h4>NOTE</h4>
    {{ notice-text | markdownify }}
  </div>

  ### 2.4 일관된 네이밍 스타일 사용

  좋은 이름의 다른 특징은 예측 가능해야 한다는 것입니다. 코드를 읽는 사람은 처음 코드를 볼때 이름의 사용을 이해할 수 있어야 합니다. 일반적인 이름을 보게되면 마지막으로 본 이후로 의미가 변경되지 않았다면 예측할 수 있습니다.

  예를들어 코드에서 데이터베이스 핸들을 전달하는 경우  매개 변수가 나타날 때마다 이름이 동일한 지 확인하십시오. d *sql.DB, dbase *sql.DB, DB *sql.DB, database *sql.DB 보다는 다음과 같이 통일하십시오.

  ```go
    db *sql.DB
  ```

  db 를 보게되면, 그것이 *sql.DB이고 로컬 선언이거나 호출에 의해 제공되었다는 것을 알 수 있습니다.

  메소드 리시버도 비슷합니다. 그 타입의 모든 메소드에 동일한 리시버 이름을 사용하십시오. 이는 코드를 읽는 사람이 타입의 메소드 리시버를 사용하기가 더 쉬워집니다.

  {% capture notice-text %}
    Go에서 잛은 리시버 이름에 대한 규칙은 지금까지 제공된 조언과 상충됩니다. 이는 단지 snake_case가 아닌 CamelCase를 사용하는 것처럼 선호되는 스타일이된 초기 선택 중 하나입니다.
  {% endcapture %}

  <div class="notice--info">
    <h4>NOTE</h4>
    {{ notice-text | markdownify }}
  </div>

  {% capture notice-text %}
    Go 스타일은 리시버가 단일 문자 이름을 가지거나 타입의 줄임말을 사용하도록 합니다. 리비서의 이름이 때로는 메소드 파마리터의 이름과 충돌하는 것을 볼 수 있을 것입니다. 이 경우 파라미터 이름을  조금 길게 만드는 것을 고려하고 새로운 파라미터 이름을 일관되게 사용하는 것을 잊어버리지 마십시오.
  {% endcapture %}

  <div class="notice--info">
    <h4>TIP</h4>
    {{ notice-text | markdownify }}
  </div>

  마지막으로, 어떤 단일 문자는 전통적으로 루프와 카운팅과 관련됩니다. 예를들어 i, j, k는 일반적으로 간단한 for 루프를 위한 루프 유도 변수 입니다. n은 일반적으로 카운팅 또는 계산과 관련됩니다. v는 일반 인코딩 함수의 값에 대한 약어 이며, k는 맵의 키에 일반적으로 사용되며 , s는 종종 문자열 유형의 매개 변수에 대한 약어로 사용됩니다.

  위의 db 예제와 마찬가지로 프로그래머는 i가 루프 유도 변수가 될 것으로 기대합니다. i가 항상 루프 변수인지 확인하면 for 루프 외부의 다른 컨텍스트에서 사용되지 않습니다. 코드를 읽는 사람이 i 또는 j라는 변수를 만나게 되면 루프가 가까이 있다는 것을 알게 됩니다.

  {% capture notice-text %}
    i, j, k 변수를 사용하는 너무 많은 중첩 루프를 발견하면 함수를 더 작은 단위로 나누세요.
  {% endcapture %}

  <div class="notice--info">
    <h4>TIP</h4>
    {{ notice-text | markdownify }}
  </div>

  ### 2.5 일관된 선언 스타일 사용

  Go는 변수를 선언하는 6개의 다른 방법이 있습니다.

  * var x int = 1

  * var x = 1

  * var x int; x = 1

  * var x = int(1)

  * x := 1

  물론 다른 선언 스타일이 더 있을 수도 있습니다. 이것은 Go의 설계자들은 아마 실수였다는 것을 알고 있을 것이지만 그것을 지금 변경하기에는 너무 늦었습니다. 이러한 변수 선언의 다른 방식을 어떻게 우리는 Go 개발자들이 서로 다른 스타일을 사용하는 것을 방지할 수 있을까요?

  프로그램에서 변수를 선언하는 방식에 대해 제한을 하고 싶습니다.

  * **변수를 선언하고 초기화하지 않을 때는 var를 사용하세요.** 함수에서 늦게 초기화되는 변수를 선언할 때는 var 키워드를 사용하세요.

  ```go
    var players int     // 0

    var things []Thing  // an empty slice of Things

    var thing Thing     // empty Thing struct

    json.Unmarshall(reader, &thing)
  ```

  var는 이 변수가 의도적으로 가리키는 타입의 zero 값으로 선언되었다는 것을 말하는 단서 역할을 합니다. 이것은 또한 일관된 패키지 레벨에서 사용하는 짧은 선언과 달리 var를 사용하는 패키지 레벨에서 변수를 선언해야하는 요구 사항과 일치합니다. 하지만 이후에 패키지 수준 변수를 사용해서는 안된다고 이야기 할 것 입니다.

  * **선언과 초기화를 할 때는 :=를 사용하세요.** 변수 선언과 초기화를 함께 할 때는 암묵적으로 변수가 zero 값으로 초기화되서는 안된다는 것을 말하고, 짧은 변수 언언을 사용하는 것을 권장합니다. 이는 :=의 왼쪽에 있는 변수는 의도적으로 초기화되고 있음을 코드를 읽는 사람에게 분명히 합니다.

  이유를 설명하기 위해 이전 예제를 살펴 보겠습니다. 이번에는 의도적으로 각 변수를 초기화합니다

  ```go
    var players int = 0

    var things []Thing = nil

    var thing *Thing = new(Thing)
    json.Unmarshall(reader, thing)
  ```

  첫 번째와 세 번째 예에서 Go에서는 하나의 타입이 다른 타입으로 자동으로 변환되지 않기 때문에 할당 연산자의 왼쪽의 타입은 오른쪽과 같은 타입이어야 합니다. 컴파일러는 오른쪽의 타입에서 선언된 변수의 유형을 유추 할 수 있습니다. 예를 들어 다음과 같이 더 간결하게 작성할 수 있습니다.

  ```go
    var players = 0

    var things []Thing = nil

    var thing = new(Thing)
    json.Unmarshall(reader, thing)
  ```

  players를 0으로 명시적으로 초기화하는 것은 불필요합니다 왜냐하면 0은 players의 zero 값이기 때문입니다. 그래서 초기화를 하는 대신 zero 값을 사용할 것이라는 점을 분명히하는 것이 더 좋습니다.
  
  ```go
    var players int
  ```

  두 번째 선언은 어떻습니까? 유형을 지우고 쓸 수 없습니다

  ```go
    var things = nil
  ```

  왜냐하면 nil은 타입을 가질수 없기 때문입니다. 대신 우리는 선택의 여지가 있습니다. slice의 zero 값을 원합니까?

  ```go
    var things []Thing  
  ```

  또는 비어있는 slice를 만들기를 원합니까?

  ```go
    var things = make([]Thing, 0)
  ```

  만약 후자를 원한다면 이것은 slice의 zero값이 아니기 때문에 짧은 선언을 사용해서 코드를 읽는 사람에게 명확히 해야합니다. 

  ```go
    things := make([]Thing, 0)
  ```
  코드를 읽는 사람에게 명시적으로 초기화하도록 선택했다고 알려줍니다.
  
  이것은 세 번째 선언으로 연결됩니다.

  ```go
    var thing = new(Thing)
  ```

  둘 다 변수를 명시적으로 초기화하고 있으며 일부 Go 프로그래머가 싫어하는 자주 사용하지 않는 new 키워드를 사용하고 있습니다. 짧은 선언 구문을 적용하면 다음과 같습니다.
  
  ```go
    thing := new(Thing)
  ```
  thing이 Thing의 포인터인 new(Thing)의 리턴값으로 명시적으로 초기화되는 것을 명확히 하였지만 여전히 일반적이지 않은 new를 사용하고 있습니다. 간단한 리터럴 구조체 초기화 형식을 사용하여 이 문제를 해결할 수 있습니다.

  ```go
    thing := &Thing{}
  ```

  이것은 new(Thing)과 같은 기능을 합니다. 따라서 일부 Go 프로그래머는 왜 중복으로 있는 것인지 화냅니다. 하지만 이것은 Thing의 zero 값인 Thing{}의 포인터로 thing을 명시적으로 초기화하는 것을 의미합니다. 

  대신 thing이 zero 값으로 선언되고 &연산자를 사용해서 thing의 주소를 json.Unmarshall에 전달합니다.

  ```go
    var thing Thing
    json.Unmarshall(reader, &thing)
  ```

  {% capture notice-text %}
    물론 경험에 따라 예외가 있습니다. 예를 들어, 때로는 두 변수가 밀접하게 관련되어 있을 수 있습니다.
    
    ```go
      var min int
      max := 1000
    ```

    다음과 같이 선언하면 더 읽기 쉽습니다.
   
    ```go
      min, max := 0, 1000 
    ```
  {% endcapture %}

  <div class="notice--info">
    <h4>NOTE</h4>
    {{ notice-text | markdownify }}
  </div>

  **요약**
    
    * 초기화 없는 변수 선언할 때는 var 사용
    
    * 변수를 선언하고 명시적으로 초기화할 때는 := 사용 

  {% capture notice-text %}
    까다로운 선언을 분명히하십시오.

    무언가가 복잡하면 복잡해 보일 것입니다.
    
    ```go
      var length uint32 = 0x80
    ```

    여기서 length는 특별한 숫자 타입을 요구하는 라이브러리를 사용될 수 있으며 짧은 선언 형식 보다 length가 명시적으로 uint32 타입이라는 것이 더 분명합니다.
   
    ```go
      length := uint32(0x80)
    ```
    첫 번째 예제에서는 고의로 var 선언을 사용하여 규칙을 위반했습니다. 일반적인 형식과 다른 이것은 예외 적인 상황도 있다는 것을 보여주는 예입니다.
  {% endcapture %}

  <div class="notice--info">
    <h4>NOTE</h4>
    {{ notice-text | markdownify }}
  </div>

  ### 2.6 팀 플레이어가 되십시오.

    읽기 쉽고 유지 보수가 가능한 코드를 생성하는 소프트웨어 엔지니어링의 목표에 대해 이야기했습니다. 대부분의 직장에서 일할 때 다른 사람들과 같이 일할 것입니다. 

    파일의 중간에 스타일을 변경하면 부자연스러울 것입니다.  당신이 선호하는 접근 방식이 아니더라도 균일성은 개인 취향보다 유지 관리에 더 중요합니다. 

  ## 주석

    > 좋은 코드는 많은 주석이 있고, 나쁜 코드에는 많은 주석이 필요합니다.  — Dave Thomas and Andrew Hunt(The Pragmatic Programmer)

    주석은 Go 프로그램의 가독성을 위해 매우 중요합니다. 주석은 다음 세 가지 중 하나를 수행합니다.

      1. 주석은 무엇을 하는지를 설명해야합니다.

      2. 주석은 무엇을 어떻게 수행하는지를 설명해야합니다.

      3. 주석은 이유에 대해 설명해야합니다.

    첫 번째 형식은 public 심볼에 대한 주석에 이상적입니다.

      ```go
        // Open opens the named file for reading.
        // If successful, methods on the returned file can be used for reading.
      ```

    두 번째 형식은 함수 내부 주석에 이상적입니다.

      ```go
        // queue all dependant actions
        var results []chan error
        for _, dep := range a.Deps {
                results = append(results, execute(seen, dep))
        }
      ```

    세 번째 형태에서 이유는 처음 두 형태를 대체하지 않기 때문에 독특하지만 동시에 그것은 무엇을 어떻게 바꾸는지에 대한 설명은 아닙니다. 페이지에서 읽는 코드의 외부 요인을 설명하기 위한 스타일입니다.  이러한 요인들이 문맥에서 벗어나는 경우가 종종 생기며, 주석은 해당 맥락을 제공하기 위해 존재합니다.

      ```go
        return &v2.Cluster_CommonLbConfig{
          // Disable HealthyPanicThreshold
            HealthyPanicThreshold: &envoy_type.Percent{
              Value: 0,
            },
        }
      ```

    이 예제에서 HealthyPanicThreshold를 0 으로 설정했을 때 어떤 효과가 있는지 명확하지 않을 수 있습니다. 0값이 HealthyPanicThreshold를 비활성화한다는 것을 명확히하기 위해 필요합니다.

  ### 3.1 값과 상수의 주석은 목적이아닌 내용을 설명해야 합니다.

    앞서 변수 또는 상수의 이름에 그 목적을 설명해야한다고 언급했습니다. 변수 나 상수에 주석을 추가 할 때 해당 주석은 변수 목적이 아니라 변수 내용을 설명해야합니다.

    ```go
      const randomNumber = 6 // determined from an unbiased die
    ```

    이 예제에서 주석은 randomNumber에 값 6이 할당된 이유와 6이 파생된 위치를 설명합니다. 주석은 randomNumber가 어디서 사용될 것인지를 설명하지 않습니다. 다음은 몇 가지 예입니다.

    ```go
      const (
        StatusContinue           = 100 // RFC 7231, 6.2.1
        StatusSwitchingProtocols = 101 // RFC 7231, 6.2.2
        StatusProcessing         = 102 // RFC 2518, 10.1

        StatusOK                 = 200 // RFC 7231, 6.3.1
    ```

    HTTP와 관련하여 숫자 100은 RFC 7231, 섹션 6.2.1에 정의 된대로 StatusContinue입니다.

    {% capture notice-text %}
      초기 값이 없는 변수의 경우 주석은 이 변수가 어디서 초기화되는지를 설명해야합니다.

      ```go
        // sizeCalculationDisabled indicates whether it is safe
        // to calculate Types' widths and alignments. See dowidth.
        var sizeCalculationDisabled bool
      ```

      여기서 주석은 dowidth함수가 sizeCalculationDisabled의 상태를 관리한다는 것을 코드를 읽는 사람에게 알려줍니다.
    {% endcapture %}

    <div class="notice--info">
      <h4>TIP</h4>
      {{ notice-text | markdownify }}
    </div>

    {% capture notice-text %}
      Kate Gregory의 팁입니다. [3] 때로는 주석에 숨어있는 변수의 더 좋은 이름을 찾을 수 있을 것이다.
      ```go
        // registry of SQL drivers
        var registry = make(map[string]*sql.Driver)
      ```

      registry가 레지스트리의 목적에 대해 충분히 설명하지 못하기 때문에 개발자가 주석을 추가했습니다. 레지스트리는 무엇입니까?

      변수의 이름을 sqlDrivers로 바꾸면 이제 이 변수의 목적이 SQL 드라이버를 보유하는 것입니다.

      ```go
        var sqlDrivers = make(map[string]*sql.Driver)
      ```

      이제 주석이 중복되어 제거 할 수 있습니다.

    {% endcapture %}

    <div class="notice--info">
      <h4>TIP</h4>
      {{ notice-text | markdownify }}
    </div>

  ### 3.2 항상 public 심볼은 문서화하십시오.

    godoc는 패키지를 위한 문서이기 때문에 항상 변수, 상수, 함수, 메소드와 같은 public 심볼의 주석을 추가해야합니다.

    Google 스타일 가이드에 따르면 두 가지 규칙이 있습니다.

      * 명확하거나 짧지 않은 모든 public 함수는 주석을 추가해야 합니다.

      * 라이브러리의 모든 함수는 길이와 복잡도에 관계없이 주석을 추가해야 합니다.

    ```go
      package ioutil

      // ReadAll reads from r until an error or EOF and returns the data it read.
      // A successful call returns err == nil, not err == EOF. Because ReadAll is
      // defined to read from src until EOF, it does not treat an EOF from Read
      // as an error to be reported.
      func ReadAll(r io.Reader) ([]byte, error)
    ```

    이 규칙에 한 가지 예외가 있습니다. interface를 구현한 메소드는 문서화할 필요가 없습니다. 구체적으로 이렇게하지 마십시오.

    ```go
      // Read implements the io.Reader interface
      func (r *FileReader) Read(buf []byte) (int, error)  
    ```

    이 주석은 어떠한 정보도 주지않습니다. 메소드가 하는일이 무엇인지 알려주지 않고 실제로 더 나쁜 것은 문서의 다른 곳을 찾아 보라고 말합니다. 이 상황에서 주석을 완전히 제거하는 것이 좋습니다.

    다음은 io 패키지의 예입니다.

    ```go
      // LimitReader returns a Reader that reads from r
      // but stops with EOF after n bytes.
      // The underlying implementation is a *LimitedReader.
      func LimitReader(r Reader, n int64) Reader { return &LimitedReader{r, n} }

      // A LimitedReader reads from R but limits the amount of
      // data returned to just N bytes. Each call to Read
      // updates N to reflect the new amount remaining.
      // Read returns EOF when N <= 0 or when the underlying R returns EOF.
      type LimitedReader struct {
        R Reader // underlying reader
        N int64  // max bytes remaining
      }

      func (l *LimitedReader) Read(p []byte) (n int, err error) {
        if l.N <= 0 {
          return 0, EOF
        }
        if int64(len(p)) > l.N {
          p = p[0:l.N]
        }
        n, err = l.R.Read(p)
        l.N -= int64(n)
        return
      }
    ```

    LimitedReader 선언에는 이를 사용하는 함수가 바로 앞에오고 LimitedReader.Read선언은 LimitedReader 자체의 선언을 따릅니다.LimitedReader.Read에는 문서 자체가 없지만 io.Reader의 구현이라는 것이 분명합니다.

    {% capture notice-text %}
      함수를 작성하기 전에 함수를 설명하는 주석을 작성하십시오. 주석을 작성하기가 어렵다면 작성하려는 코드가 이해하기 어렵다는 신호입니다.
    {% endcapture %}

    <div class="notice--info">
      <h4>TIP</h4>
      {{ notice-text | markdownify }}
    </div>

  #### 3.2.1 잘못된 코드에 주석을 달지 말고 다시 작성하십시오

    > Don’t comment bad code rewrite it  — Brian Kernighan

    특정 코드 조각을 강조하는 주석은 충분하지 않습니다. 이러한 주석 중 하나를 발견하면 나중에 리팩토링하라는 이슈를 제기해야합니다. 

    표준 라이브러리의 전통은 TODO 스타일 주석에 이를 발견한 사람의 사용자 이름으로 주석을 달아주는 것입니다.

    ```go
      // TODO(dfc) this is O(N^2), find a faster way to do this.
    ```

    사용자 이름은 해당 사용자가 문제를 해결하겠다고 약속 한 것이 아닙니다. 그러나 수정할 때 물어볼 가장 좋은 사람 일 수 있습니다. 다른 프로젝트는 TODO에 날짜 또는 이슈 번호로 주석을 답니다.
    
  #### 3.2.2 코드 블록을 주석 처리하지 않고 리팩토링

    > 좋은 코드는 최고의 문서입니다. 주석을 추가하려고 할 때 '이 주석이 필요하지 않도록 코드를 어떻게 개선 할 수 있습니까?' 코드를 개선 한 다음 더 명확하게 문서화하십시오.  — Steve McConnell

    함수는 한 가지 일만 해야합니다. 함수의 나머지 부분과 관련이 없기 때문에 코드 조각에 주석을 달면 자신의 함수로 추출하는 것을 고려하십시오

    이해하기 쉬울뿐만 아니라, 작은 기능은 독립적으로 테스트하기가 더 쉽습니다. 직교(orthogonal) 코드를 자체 기능으로 분리하면 해당 이름이 필요한 모든 문서일 수 있습니다.