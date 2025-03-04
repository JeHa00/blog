---
title: "스프링 빈 자동 등록 방법: 컴포넌트 스캔"
date: 2025-01-18T16:01:00+09:00
draft: false
summary: 수동으로 스프링 빈을 등록하는 것 다음으로 @ComponentScan을 통한 스프링 빈 자동 등록하는 방법을 알아본다.
tags: ["Spring"]
categories: "Spring"
---

# 🔆 스프링 빈을 등록하는 또 다른 방법: @ComponentScan

설정 정보에 @Configuration과 @ComponentScan 애노테이션을 추가한다. 그러면 @Component 애노테이션이 붙은 클래스를 스캔해서 스프링 빈으로 등록한다. 아래 코드로 보자면 AutoAppConfig라는 스프링 컨테이너가 생성되고, 이 컨테이너에 @Component 애노테이션이 붙은 클래스가 스프링 빈으로 등록된다.

```java
package SpringBasic.core;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

@Configuration
@ComponentScan
public class AutoAppConfig {

}
```

- @Component 애노테이션이 붙은 예시

    ```java
    @Component
    public class MemoryMemberRepository implements MemberRepository {}

    ...


    @Component
    public class RateDiscountPolicy implements DiscountPolicy{}
    ```


위 사태로만 등록하면 AutoAppConfig는 AppConfig과 달리 의존관계가 명확히 드러나지 않기 때문에 의존관계를 파악할 수 없다.
그래서, 빈으로 등록되는 클래스 안에서 의존 관계 주입도 해결해야 한다. @Autowired 애노테이션을 등록하려는 빈의 생성자에 추가한다.

```java
@Component
public class MemberServiceImpl implements MemberService {
    private final MemberRepository memberRepository;
     
    @Autowired
    public MemberServiceImpl(MemberRepository memberRepository) {
        this.memberRepository = memberRepository;
    }
}
```

또한, @Autowired를 사용하면 다음과 같이 여러 의존관계도 한 번에 주입받을 수 있다.

```java
@Component
public class OrderServiceImpl implements OrderService {
    private final MemberRepository memberRepository;
    private final DiscountPolicy discountPolicy;

    @Autowired
    public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy
    discountPolicy) {
        this.memberRepository = memberRepository;
        this.discountPolicy = discountPolicy;
    }
}
```


## @ComponentScan으로 스프링 빈 등록하는 단계 

@ComponentScan은 @Component 애노테이션이 붙은 모든 클래스를 스프링 빈으로 등록한다. 등록하는 과정을 알아보자.

1) @Component 애노테이션이 붙은 클래스를 스프링 빈으로 생성한다.
2) 만약 생성자에 @Autowired 애노테이션이 있으면 생성자를 통해 의존 관계를 설정한다. 단, 생성자가 딱 1개만 있으면 @Autowired를 생략해도 자동 주입된다. 스프링 빈에만 해당된다.
3) 의존관계가 있을 경우 스프링 컨테이너에서 타입을 기준으로 찾는다.
4) 동일한 타입이 있으면 해당되는 스프링 빈을 주입하면서 스프링 빈으로 등록한다.
5) 아무리 의존관계가 많아도 스프링 빈 레지스트리에서 다 찾아 자동으로 주입한다.


등록 시 스프링 빈의 이름은 기본적으로 클래스의 맨 첫 글자가 소문자로 바뀌어 등록된다. 예를 들어서 MemberServiceImpl 클래스라면 memberServiceImpl로 등록된다. 만약 이름을 직접 등록하고 싶다면 `@Component('memberService')` 이름을 부여한다.

&nbsp;

---

# 컴포넌트 스캔의 기본 스캔 대상 

컴포넌트 스캔이 스캔하는 대상은 다음과 같다.
- @Component
- @Controller
- @Service
- @Repository
- @Configuration

그런데 @Controller, @Service, @Repository, @Configuration의 소스 코드를 보면 @Component를 포함고 있는 것을 알 수 있다. (애노테이션은 상속되지 않는다. 자바 언어가 지원하는 게 아닌 스프링이 지원하는 기능이다.)


각 애노테이션마다 스프링이 바라보는 인식이 다르다.

- @Controller: 스프링 MVC의 컨트롤러로 처리
- @Repository: 스프링 데이터 접근 계층으로 인식하고, 데이터 계층의 예외를 스프링 예외로 변환한다.
- @Configuration: 스프링 설정 정보로 인식하고, 스프링 빈이 싱글톤을 유지하도록 추가 처리한다.
- @Service: 이 애노테이션은 스프링에게 특별히 전달하는 처리는 없지만, 개발자들에게 핵심 비지니스 로직이 여기 있겠구나라고 비지니스 계층 인식에 도움이 된다. 

&nbsp;

---

# 중복 등록과 충돌 

스프링 빈을 자동, 수동 모두 등록이 가능하다는 것을 배웠다. 여기서 한 번 생각해볼 부분이 있다. 등록하는 과정에서 충돌이 발생하지 않을까? 과연 한 번에 등록이 잘 될까? 완벽하게 한 번에 되는 것은 불가능하다고 생각된다. 스프링에서는 이 부분을 어떻게 대처했을까?

1) 자동 vs 자동
이름이 같은 경우 오류(ConflictingBeanDefinitioonException)를 발생시킨다.

두 스프링 빈 객체에 @Component('service')로 등록한 후 테스트를 실행하면 아래 내용을 확인할 수 있다.

```text
org.springframework.beans.factory.BeanDefinitionStoreException: Failed to parse configuration class [SpringBasic.core.AutoAppConfig]
```


2) 수동 vs 자동
수동 빈 등록과 자동 빈 등록이 충돌될 경우, 수동 빈 등록이 우선권을 가져서 수동 빈이 자동 빈을 오버라이딩한다.
@ComponentScan이 있는 설정 정보에 @Bean(name={다른 빈과 동일한 이름}) 으로 추가해보자. 실행하면 다음과 같이 오버라이딩했다는 로그를 확인할 수 있다.

```text
Overriding bean definition for bean 'memoryMemberRepository' with a different definition: replacing
```


이러한 설정 정보 충돌은 의도적이기보다는 여러 설정이 꼬여져 만들어지는 경우가 대부분이다. 그래서 스프링 부트에서는 수동과 자동 빈 등록이 충돌되면 오류가 발생하는 것을 기본값으로 두었다.

&nbsp;

---

# 무엇을 기준으로 자동, 수동 중 무엇을 사용할까?

기본적으로 자동을 사용하자. 

그러면 언제 수동 빈 등록을 사용할까?

애플리케이션은 크게 업무와 기술 지원 로직으로 나눌 수 있다고 한다. 

업무 로직에 사용되는 빈은 비지니스 요구사항을 개발할 때 추가또는 변경된다. 
기술 지원에 사용되는 빈은 데이터베이스 연결, 공통 로그 처리처럼 업무 로직 지원을 위한 하부 기술이나 공통기술이다.

업무 로직은 양이 많고, 패턴이 존재하기 때문에 자동 기능을 적극 사용하자. **_하지만 애플리케이션에 광범위하게 영향을 미치는 기술 지원 객체는 수동 빈으로 등록해서 딱! 설정 정보에 바로 나타나게 하는 것이 유지보수에 좋다._**

스프링과 스프링 부트에 자동으로 등록된 수 많은 빈은 건들이지 말자.


&nbsp;

---

# 필터

@Component만으로 충분하기 때문에 이 필터를 사용할 일은 거의 없다. 최대한 스프링의 기본 설정에 맞추어 사용하는 것을 권장한다. 

이런 내용도 있다는 것만 알아두자.

다음과 같이 필터를 통해 컴포넌트 스캔에 추가하거나, 제외할 대상을 지정할 수 있다. 

```java
@ComponentScan(
    includeFilters = @Filter(type = FilterType.ANNOTATION, classes =추가할 컴포넌트.class),
    excludeFilters = @Filter(type = FilterType.ANNOTATION, classes =배제할 컴포넌트.class)
)
```

필터 타입에는 5가지 옵션이 있다.

- ANNOTATION: 기본값, 애노테이션을 인식해서 동작한다.
- ASSIGNABLE_TYPE: 지정한 타입과 자식 타입을 인식해서 동작한다.
- ASPECTJ: AspectJ 패턴 사용
- REGEX: 정규 표현식으로 지정한다는 의미다.
- CUSTOM: TypeFilter 이라는 인터페이스를 구현해서 처리한다.

&nbsp;

---


# 탐색 위치와 기본 스캔 대상 

컴포넌트 스캔의 기본 스캔 경로는 @ComponentScan이 붙은 설정 정보 클래스의 패키지가 시작 위치가 되어 하위 컴포넌트를 모두 스캔한다. 이 방식의 단점은 모든 자바 클래스를 컴포넌트 스캔하면 라이브러리도 포함되기 때문에 시간이 오래 걸리기 때문에 다음과 같이 시작 위치를 지정할 수 있다. 

```java
@ComponentScan(
    basePackages = "SpringBasic.core"
    basePackageClasses = AutoAppConfig.class
)
```

하지만, 주로 사용하는 방법은 패키지 위치를 지정하지 않고, 설정 정보 클래스의 위치를 프로젝트 최상단에 두는 것이다. 최근 스프링 부트도 이 방식을 기본으로 제공한다. 최상단에 두는 이유는 하위에 있는 모든 것이 자동으로 컴포넌트 스캔의 대상이 되기 때문이다. 그리고, 메인 설정 정보는 프로젝트를 대표하는 정보이기 때문에 프로젝트 시작 루트에 두는 것이 좋다. 


&nbsp;


------

# Reference
- [스프링 MVC 1편 - 백엔드 웹 개발 핵심 기술](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-1/dashboard)