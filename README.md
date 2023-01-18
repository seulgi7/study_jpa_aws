# CHAPTER01.
# 프로젝트 환경
    - Java : 8
    - Gradle : 4.10.2

# CHAPTER02. 스프링 부트에서 테스트 코드를 작성하자.

## 2.1 테스트 코드 소개

TDD와 단위 테스트(Unit Test)는 다른 이야기.
햐
### TDD

- 테스트가 주도하는 개발.

- 테스트 코드를 먼저 작성하는 것부터 시작한다.

- 레드 그린 사이클

    - (Red) 항상 실패하는 테스트를 먼저 작성하고
    - (Green) 테스트가 통과하는 프로덕션 코드를 작성하고
    - (Refactor) 테스트가 통과하면 프로턱션 코드를 리팩토링 한다.

  <img src="https://blog.kakaocdn.net/dn/cECfwD/btqBUTp55pN/kpRuUmLg6k7IzmDr12aFS0/img.gif" alt="햄코미 Study Stack :: TDD(Test-Driven Development)란?" style="zoom:50%;" />

### 단위 테스트 (Unite Test)

- 단위 테스트란?

    - 기능 단위의 테스트 코드를 작성하는 것.

    - TDD와 달리 테스트 코드를 꼭 먼저 작성해야 하는 것도 아니고, 리팩토링도 포함되지 않는다.

    - 순수하게 테스트 코드만 작성하는 것을 이야기 함.

- 장점

    - 개발단계 초기에 문제를 발견하게 도와준다.

    - 개발자가 나중에 코드를 리팩토링하거나 라이브러리 업그레이드 등에서 기존 기능이 올바르게 작동하는지 확인할 수 있다.  ex)회귀 테스트.

    - 기능에 대한 불확실성을 감소시킬 수 있다.

    - 시스템에 대한 실제 문서를 제공한다. 즉, 단위테스트 자체가 문서로 사용할 수 있다.



- 필자의 경험담

    - 필자가 단위 테스트를 배우기 전 진행한 개발방식.

        1. 코드를 작성하고
        2. 프로그램(Tomcat)을 실행한 뒤
        3. Postman과 같은 API 테스트 도구로 HTTP 요청하고
        4. 요청결과를 System.out.println()으로 눈으로 검증한다.
        5. 결과가 다르면 다시 프로그램(Tomcat)을 중지하고 코드를 수정한다.

      이 때, 2~5는 매번 코드를 수정할 때마다 반복해야만 한다.

    - 단위테스트후 바뀐 변화

        - 톰캣을 계속 내렸다가 다시 실행하는 일의 반복이 없어짐.
            - 수정할때마다 톰캣을 재구동하며 확인하는 일은 테스트 코드가 없다 보니 눈과 손으로 직접 수정된 기능을 확인할 수 밖에 없기 때문이다.
        - System.out.println()을 통해 눈으로 검증해야하는 수동검증이 필요 없어짐.
            - 테스트 코드를 작성하면 더는 사람이 눈으로 검증하지 않게 자동검증이 가능하다.
        - 개발자가 만든 기능을 안전하게 보호해준다.
            - 새로운 기능이 추가될 때, 기존 기능이 잘 작동되는 것을 보장해 주는 것.



- 테스트 코드 작성을 도와주는 프레임 워크
    - Junit - Java
    - DBUnit - DB

## 2.2 Hello Controller 테스트 코드 작성하기

>  패키지 네이밍 규칙 : 일반적으로 패키지명은 웹 사이트 주소의 역순으로 한다.

### @SpringBootApplication

- @SpringBootApplication으로 인해 스프링 부트의 자동 설정, 스프링 Bean 읽기와 생성을 모두 자동으로 설정된다.
- 위치 : 항상 **프로젝트 최상단에 위치**해야 한다.
    - 왜? @SpringBootApplication이 있는 위치 부터 설정을 읽어가기 때문이다.

```java
package com.spring.admin;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```



> 스프링 부트에서는 **내장  WAS** 를 사용하는 것을 권장한다.
>
> - 왜? **언제어디서나 같은 환경에서 스프링 부트를 배포**할 수 있기 때문이다.
    >
    >   - 내장 WAS를 사용하면 항상 서버에 톰캣을 설치할 필요가 없게되고, 스프링 부트로 만들어진  Jar 파일(실행 가능한 Java 패키징 파일)을 실행하면 됨.
          >     - 내장 WAS란 별도로 외부에  WAS를  두지 않고 애플리케이션을 실행 할 때 내부에서 WAS를 실행하는 것을 이야기함.
>   - 만약 외장 WAS를 사용한다고 하면 모든 서버는 WAS의 종류와 버전, 설정을 일치시켜야만 한다.
>
>



### @RequestController

- 컨트롤러를 JSON을 반환하는 컨트롤러로 만들어 준다.
- 예전에는 @ResponserBody를 각 메소드마다 선언했던 것을 한번에 사용할 수 있게 해준다고 생각하면 된다.

### @GetMapping

- HTTP Method인 Get의 요청을 받을 수 있는 API를 만들어준다.
- 예전에는 @RequestMapping(method = RequestMethod.GET)으로 사용되었다.

### @RunWith(SpringRunner.class)

- 테스트를 진행할 때 JUnit에 내장된 실행자 외에 다른 실행자를 실행시킨다.
    - 여기서는 SpringRunner라는 스프링 실행자를 사용한다.
- 스프링 부트 테스트와 JUnit 사이에 연결자 역할을 한다.

### @ WebMvcTest

- 여러 스프링 테스트 어노테이션 중, Web(Spring MVC)에 집중할 수 있는 어노테이션.
- 선언할 경우 @Controller, @Controllerㅁ

### @Autowired

- 스프링이 관리하는 빈(Bean)을 주입 받습니다.

### @private MockMvc mvc

- 웹 API를 테스트할 때 사용한다.
- 스프링 MVC 테스트의 시작점.
- 이 클래스를 통해 HTTP GET, POST 등에 대한 API 테스트를 할 수 있다.



## 2.3 롬복 소개 및 설치하기

- 롬복 ( Lombok )

    - 자바 개발자들의 필수 라이브러리.

    - 자바 개발할 때 자주 사용하는 코드 Getter, Setter, 기본생성자, toString 등을 어노테이션으로 자동 생성해줍니다.

    - 프로젝트에 롬복 추가 방법

        1. build.gradle  에 다음의 코드를 추가한다.

           ```
           compile('org.projectlombok:lombok')
           ```

        2. build.gradle 새로고침 → 라이브러리를 내려받는다.

        3. Lombok Plugin 설치

        4. 롬복 설정 : Settings > Build > Compiler > Annotation Processors

           ☐ Enable annocation processing 체크

           >  ✔️ 단, 롬복은 프로젝트마다 설정해야한다.
           >
           >  - build.gradle에 라이버리를 추가하는 것과 Enable annotaion processing을 체크하는 것은 프로젝트마다 진행해야 한다.
           >  - 플러그인 설치는 한 번만 하면 된다.



## 2.4 Hello Controller 코드를 롬복으로 전환하기

### @Getter
- 선언된 모든 필드의 get 메소드를 생성해 줍니다.

### @ResponseArgsConstructor
- 선언된 모든 final 필드가 포함된 생성자를 생성해 줍니다.
- final이 없는 필드는 생성자에 포함되지 않습니다.

### assertThat
- assertj라는 테스트 검증라이브러리의 검증 메소드.
- 검증하고 싶은 대상을 메소드 인자로 받는다.
- 메소드 체이닝이 지원되어 isEqualTo와 같이 메소드를 이어서 사용할 수 있다.

> Junit과 비교하여 assertj의 장점
>
> - CoreMatchers와 달리 추가적으로 라이브러리가 필요하지 않다.
    >   - Junit의 assertThat을 쓰게 되면 is()와 같이 CoreMatchers 라이브러리가 필요하다.
> - 자동완성이 좀 더 확실하게 지원된다.
    >   - IDE에서는 CoreMatchersd와 같은 Matcher 라이브러리의 자동완성 지원이 약하다.

  
### isEqualTo

- assertj의 동등 비교 메소드.
- assertThat에 있는 값과 isEqualTo의 값을 비교해서 같을 때만 성공.

### @RequestParam

- 외부에서 API로 넘긴 파라미터를 가져오는 어노테이션.
- 여기서는 외부에서 name (@RequestParam("name") ) 이란 이름으로 넘긴 파라미터를 메소트 파라미터 name (String name)에 저장하게 된다.

### @param

- API 테스트할 때 사용될 요청 파라미터를 설정한다.
- 단, 값은 String만 허용된다.
- 숫자/날짜 등의 데이터도 등록할 때는 문자열로 변경해야만 가능하다.

### jsonPath

- JSON 응답값을 필드별로 검증할 수 있는 메소드.
- $를 기준으로 필드명을 명시한다. ex) name → $.name , amount → $.amount