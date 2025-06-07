---
title: "스프링 컨테이너, 스프링 빈, 싱글톤 레지스트리란?"
date: 2024-12-15T04:28:22+09:00
draft: false
summary: 스프링 컨테이너와 스프링 빈이 무엇인지 / 빈 생성 및 등록 과정 / 스프링 컨테이너의 인터페이스의 코드 구조 / BeanDefinition / 싱글톤 설계 시 유의사항 / 스프링이 싱글톤 레지스트리가 가능한 이유 에 대해 알아본다.
tags: ["Spring"]
categories: "Spring"
---

# 1. 스프링 컨테이너와 스프링 빈

---

## 1.1 스프링 컨테이너와 스프링 빈이란?

스프링 컨테이너(Spring Container)란 IoC(제어의 역전, Inversion Of Control)와 DI
(의존관계 주입, Dependency Injection)를 구현하기 위해 설계된 역할로, **_개발자 대신 스프링 프레임워크가(IoC) 의존 관계 객체를 스프링 빈(Spring Bean) 객체로 생성하고 주입(DI)하고, 관리까지 전체적으로 담당해 객체 지향 프로그래밍을 위해 만들어진 도구다._** 개발자가 직접하면 생성과 실행이 합쳐서 OCP, SRP가 깨지기 때문이다.

위 개념에서 언급한 스프링 빈(Spring Bean)은 스프링 컨테이너에 저장된 객체를 의미한다. 스프링이 판단해서 스프링 빈으로 적절한 의존 관계가 주입된다.

이 스프링 컨테이너 역할은 코드 상으로 BeanFactory 인터페이스를 통해 구현한다. 하지만 보통 BeanFactory 인터페이스를 상속받는 ApplicationContext 객체를 스프링 컨테이너라 부른다.

스프링 빈(Spring Bean)은 `@Configuration` 애노케이션이 붙은 객체의 메서드 중 `@Bean` 애노테이션이 붙은 메서드의 호출 결과로 생성된 객체를 의미한다.

- ApplicationContext (스프링 컨테이너)
  - `@Configuration`
    - `@Bean`

필요한 객체가 있으면 스프링 컨테이너가 스프링 빈으로 등록된 객체들에서 찾아 의존성 주입을 한다.

<br>

## 1.2 스프링 빈 생성 및 등록 과정

스프링 컨테이너가 빈을 생성 및 등록하는 과정은 다음과 같다.

첫 번째, 스프링 컨테이너 인터페이스에 `@Configuration` 애노케이션이 붙은 객체의 Class 정보를 전달해, 해당 구성 정보를 가지고 있는 스프링 컨테이너를 생성한다.

- 스프링 컨테이너에는 Map 타입으로 '빈 이름'과 '빈 객체'를 사상하여 저장한다.
- 설정 정보 객체

```java
@Configuration
public class AppConfig {
	...
}
```

- 스프링 컨테이너 생성

```java
ApplicationContext applicationContext = new AnnotationConfigApplicationContext(AppConfig.class);
```

두 번째, 이 설정 정보 객체(ex: AppConfig)에 `@Bean` 데코레이션이 존재하는 메서드를 실행해 메서드의 이름을 'Bean 이름'으로 하고, 메서드의 반환값으로 생신 객체를 'Bean'으로 저장한다. 이 때 빈 이름은 중복되면 안된다.

세 번째, 그후, 설정 정보를 참고해 의존관계를 주입(DI)한다.

<br>

## 1.3 스프링 컨테이너의 코드 구조

> _코드에서 ApplicationContext 타입을 클릭해서 들어가면 다음 내용을 확인할 수 있다._

스프링 컨테이너라 불리는 두 객체(BeanFactory와 ApplicationContext)는 인터페이스(interface)이고, ApplicationContext는 BeanFactory를 상속받는다. 그래서 BeanFactory는 스프링 컨테이너의 최상위 인터페이스다.

BeanFactory는 Bean을 조회할 때 사용하는 getBean() 메서드를 제공하는 역할을 수행한다.
ApplicationContext는 이 BeanFactory를 상속받기 때문에 이 메서드를 사용할 수 있으면서
다양한 인터페이스를 상속받는다.

ApplicationContext의 소스 코드를 보면 BeanFactory 인터페이스를 바로 찾을 수 없다.

BeanFactory 인터페이스는 이 인터페이스를 상속받는 다른 인터페이스를 통해 제공된다. 바로 ListableBeanFactory, HierarchicalBeanFactory 이 두 인터페이스다.

- ListableBeanFactory는 빈을 조회하는데 사용되는 다양한 메서드를 제공한다.
- HierarchicalBeanFactory는 특정 빈이 존재하는지 확인하는데 사용된다.

그 밖에 다양한 인터페이스를 상속받는다.

- MessageSource: 국제화 기능(한국에서는 한국어로, 영어권에서는 영어로)
- EnvironmentCapable: 로컬, 개발, 운영 등 구분
- ApplicationEventPublisher: 이벤트 발행 및 구독
- ResourceLoader: 외부 리소스 조회

그리고 스프링의 설정 정보는 java외에도 Xml 형식으로도 가능하고, 그 외 다양한 형식으로 전달할 수 있다.
`@Configuration` 처럼 어노테이션 방식으로 전달할 때는 `AnnotationConfigApplicationContext`를 통해서 전달받는다.
그러면 `AnnotatedBeanDefinitionReader`에 의해 설정 정보를 읽고, 빈의 메타 정보인 `BeanDefinition`을 생성한다.

`AnnotationConfigApplicationContext` 객체의 소스 코드다.

```java
// 소스 코드
public class AnnotationConfigApplicationContext extends GenericApplicationContext implements AnnotationConfigRegistry {
    private final AnnotatedBeanDefinitionReader reader;
    private final ClassPathBeanDefinitionScanner scanner;

	...

	public AnnotationConfigApplicationContext(Class<?>... componentClasses) {
        this();
        this.register(componentClasses);
        this.refresh();
    }

	public void register(Class<?>... componentClasses) {
		...
		this.reader.register(componentClasses);
	}
}
```

밑에는 `AnnotatedBeanDefinitionReader`의 소스 코드다.

```java
// 소스 코드
public class AnnotatedBeanDefinitionReader {
    private final BeanDefinitionRegistry registry; // Bean 메타 정보 저장소

	...

    public void register(Class<?>... componentClasses) {
        Class[] var2 = componentClasses;
        int var3 = componentClasses.length;

        for(int var4 = 0; var4 < var3; ++var4) {
            Class<?> componentClass = var2[var4];
            this.registerBean(componentClass);
        }
    }

	public void registerBean(Class<?> beanClass) {
		this.doRegisterBean(beanClass, ...)
	}
}
```

<br>

## 1.4 BeanDefinition

스프링 컨테이너의 설정 정보는 자바 클래스 외에 xml을 사용해서도 생성할 수 있다. 다양한 방법으로 설정 정보를 가질 수 있는 이유는 여기서도 객체 지향의 원리는 역할과 구현을 구분했기 때문이다.

스프링 컨테이너에 스프링 빈 같은 설정 정보는 `BeanDefinition`이라는 인터페이스에 의존한다.

- 빈 설정에 대한 메타 정보이다.
- 경로는 `package org.springframework.beans.factory.config`에 존재한다.
- `@Bean`마다 메타 정보가 하나씩 생성된다.
- 이 정보를 기반으로 스프링 컨테이너에 스프링 빈이 생성 및 등록된다.

`AnnotationConfigApplicationContext` 는 `AnnotatedBeanDefinitionReader` 를 사용해서 `AppConfig.class` 를 읽고 `BeanDefinition` 을 생성한다.

BeanDefinition에 대해서는 너무 깊이있게 이해하기 보다는, 스프링이 다양한 형태의 설정 정보를 BeanDefinition으로 추상화해서 사용하는 것 정도만 이해하면 된다.

<br>

## 1.5 스프링 빈 조회하기

등록된 빈도 조회할 수 있다.

- case 1: Bean 객체로 조회할 수 있다. (getBean(빈 객체 클래스 정보))
  - getBean(MemberService.class)
- case 2: 만약 해당 Bean 객체로 된 것이 두 개 이상이면 Bean 객체와 Bean 이름을 함께 사용해 Bean을 조회하면 된다. (getBean(빈 이름, 빈 객체 클래스 정보))
- case 3: 조회하는 빈 객체에 여러 자식들이 존재하면 자식들까지 가져온다.(getBeansOfType(특정 타입의 빈 클래스 정보))
- case 4: 만약 특정 자식을 조회하고 싶으면 빈 이름을 사용하면 된다. (case2)

&nbsp;

# 2. 싱글톤 레지스트리

---

## 2.1 싱글톤 패턴이란?

> _해당 객체를 서버에서 1개만 생성해 공유해서 사용하도록 설계하는 패턴_

스프링 컨테이너는 싱글톤 방식을 사용한다. 그러면 왜 싱글톤 방식을 사용하는지 이해하기 위해, 이 방식을 사용하지 않을 경우 발생되는 문제점에 대해 알아보자.

만약 스프링 빈을 사용하는 것 없이 AppConfig의 설정 객체를 가져오려면 AppConfig의 인스턴스를 통해서 메서드를 실행하면 된다.

```java
void pureContainer() {

	AppConfig appConfig = new AppConfig();
	//1. 조회: 호출할 때 마다 객체를 생성
	MemberService memberService1 = appConfig.memberService();
	//2. 조회: 호출할 때 마다 객체를 생성
	MemberService memberService2 = appConfig.memberService();
	//참조값이 다른 것을 확인
	System.out.println("memberService1 = " + memberService1);
	System.out.println("memberService2 = " + memberService2);
	//memberService1 != memberService2
	assertThat(memberService1).isNotSameAs(memberService2);
    }
```

각 메서드의 실행 결과 다음 객체를 새로 생성해서 반환한다.

- MemberService
- OrderService
- MemberRepository
- DiscountPolicy

**_만약 웹 애플리케이션이라면 request 요청이 들어올 때마다 이 서비스 객체를 호출한다면 매번 새로운 객체가 생성된다. 그러면 한 번 사용하고 사용되지 않으면 해당 서비스 객체는 GC에 의해 반복적으로 삭제된다. 이 방식은 '메모리 낭비가 심하다'는 단점이 존재한다._**

**_그래서 이에 대한 해결책이 해당 객체를 서버에서 1개만 생성해 공유해서 사용하도록 설계한다. 이러한 패턴을 '싱글톤(singleton) 패턴'이라 한다. 이러한 싱글톤 패턴의 특징 때문에 객체 인스턴스를 2개 이상 생성하지 못하도록 막아야 하므로 생성자의 접근 제어자를 반드시 `private`로 지정해야 한다._**

만약 싱글톤으로 사용되어야 하는 객체가 있다면 다음과 같이 코드를 구성하면 된다.

- 클래스 변수로 단 하나의 인스턴스만 사용되고, 외부에서 접근하지 못하도록 `private static final`을 지정한다.
- 해당 객체를 사용하고 싶으면 특정 메서드를 통해서만 접근하도록 한다.

```java
public class SingletonService {
	//1. static 영역에 객체를 딱 1개만 생성해둔다.
	private static final SingletonService instance = new SingletonService();

	//2. public으로 열어서 객체 인스턴스가 필요하면 이 static 메서드를 통해서만 조회하도록 허용한다.
    public static SingletonService getInstance() {
        return instance;
	}

    // 3. 생성자를 private으로 선언해서 외부에서 new 키워드를 사용한 객체 생성을 못하게 막는다.
    private SingletonService() {
    }

	public void logic() { System.out.println("싱글톤 객체 로직 호출");
	}
}
```

<br>

## 2.2 스프링 컨테이너가 싱글톤인지 확인하기

앞서 스프링 컨테이너가 싱글톤 방식을 사용한다고 했는데, 직접 확인해보자. 스프링에서 사용하는 객체 호출 방식을 사용해보자.

```java
@Test
@DisplayName("스프링 컨테이너와 싱글톤")
void springContainer() {
	ApplicationContext ac = new
 AnnotationConfigApplicationContext(AppConfig.class);
	//1. 조회: 호출할 때 마다 같은 객체를 반환
	MemberService memberService1 = ac.getBean("memberService",
 MemberService.class);
	//2. 조회: 호출할 때 마다 같은 객체를 반환
	MemberService memberService2 = ac.getBean("memberService",
 MemberService.class);
//참조값이 같은 것을 확인
	System.out.println("memberService1 = " + memberService1);
	System.out.println("memberService2 = " + memberService2);
	//memberService1 == memberService2
	assertThat(memberService1).isSameAs(memberService2);
 }

```

- 출력 결과

  ```java

  memberService1 = SpringBasic.core.member.MemberServiceImpl@5c2375a9
  memberService2 = SpringBasic.core.member.MemberServiceImpl@5c2375a9
  ```

**_스프링 컨테이너는 객체를 하나만 생성해서 계속해서 사용하는 것을 확인할 수 있다._**

<br>

## 2.3 싱글톤 사용 시 단점과 해결책

> _싱글톤 레지스트리(singleton registry) 디자인 패턴을 적용해 해결한다._

그러면 위 코드를 봤을 때 싱글톤의 단점을 정리해보자.

1. 이 패턴을 구현하기 위해 코드 품이 많이 든다.
2. 위 클래스 변수처럼 인터페이스가 아닌 구현체에 의존하므로 DIP와 OCP를 위반한다.
3. private 생성자로 자식 클래스를 만들기 어렵다.
4. 싱글톤은 테스트용 객체를 생성할 수 없기 때문에 테스트가 어렵다.
5. **_무엇보다 하나의 객체를 여러 객체에서 사용하기 때문에 상태 유지(stateful)로 설계할 경우, 의도치 않은 값을 얻을 수 있다._**

스프링에서는 이 단점을 어떻게 해결하는지 알아보자. 스프링에서는 앞서 스프링 빈으로 확인했을 때 다음 사항을 확인할 수 있다.

1. 싱글톤으로 코드를 입력하지 않아도,
2. DIP와 OCP 위반 없이,
3. private 생성자로부터 자유롭게,
4. 테스트 코드로도,

싱글톤이 사용되는 것을 확인할 수 있다.

어떻게 이게 가능한 것일까?

**_스프링 컨테이너가 '싱글톤 레지스트리(singleton registry) 패턴' 방식이기 때문에 가능하다._** 싱글톤과 레지스트리는 디자인 패턴의 각각 한 종류다. 싱글톤은 앞서 언급했으니 넘어가면 레지스트리는 객체를 전역적으로 관리하고 제공하는 디자인 패턴을 의미한다. 중앙 저장소(레지스트리)를 통해 객체를 관리하고 필요할 때 조회해 사용하는 방식이라는 의미다. 그래서 싱글톤과 레지스트리 두 가지 패턴을 합쳐서 만든 것이 스프링 컨테이너이다.

즉, 스프링 빈을 싱글톤으로 관리하는 저장소라는 의미다. 스프링 컨테이너인 ApplicationContext은 Bean이 이미 존재하면 기존에 있던 것을 사용하고, 없으면 새로 생성해서 등록하는 방식을 사용하기 때문에 각 Bean을 싱글톤으로 유지하면서, 싱글톤으로 발생되는 위 4가지 문제점을 해결할 수 있다.

<br>

## 2.4 아직 해결되지 않은 문제: stateful

> _필드를 없애는 방향으로 진행하자._

아직 stateful 문제는 해결되지 않았다. 하나의 객체 인스턴스를 공유하기 때문에 싱글톤 객체는 상태를 유지하게 설계하면 안된다. 무상태로 설계 해야 한다. 이 말은 다음 내용을 의미한다.

1. 특정 클라이언트에 의존적인 필드가 있으면 안된다.
2. 특정 클라이언트가 값을 변경할 수 있는 필드가 있으면 안된다.
3. 가급적 읽기만 가능해야 한다.
4. 필드 대신 자바에서 공유되지 않는 지역 변수, 파라미터, ThreadLocal 등을 사용해야 한다.

싱글톤으로 공유하는 객체에 수량을 의미하는 amount 필드가 있다고 할 때, A 클라이언트가 이 amount에 10을 저장한다. 그 다음, B 클라이언트가 바로 13을 저장한다. 그후, A 클라이언트가 amount 값을 조회할 때 10이 나와야 하지만, 13이 나오는 문제점이 생긴다.

그래서 **_필드를 없애는 방향으로 진행해야 한다._**

<br>

## 2.5 @Configuration에서 싱글톤이 유지되는 이유

> _바이트코드 조작 라이브러리 때문에 유지가 된다._

싱글톤 개념에 대해서 알았으니 다시 AppConfig를 확인해보자. 각 메서드가 몇 번 호출되었는지 확인하기 위해 출력 함수로 로그를 추가한다.

```java
// AppConfig.java


@Configuration
public class AppConfig {
    @Bean
    public MemberService memberService() {
        System.out.println("call AppConfig.memberService");
        return new MemberServiceImpl(memberRepository());
    }

    @Bean
    public OrderService orderService() {
        System.out.println("Call AppConfig.orderService");
        return new OrderServiceImpl(memberRepository(), discountPolicy());
    }

    @Bean
    public MemberRepository memberRepository() {
        System.out.println("Call AppConfig.memberRepository");
        return new MemoryMemberRepository();
    }

    @Bean
    public DiscountPolicy discountPolicy() {
        System.out.println("Call AppConfig.discountPolicy");
        return new RateDiscountPolicy();
    }
}

```

만약 스프링 빈이 생성된다면 `MemberRepository`는 몇 번 호출될까?

- MemberService에서 한 번
- OrderService에서 한 번
- 마지막 memberRepository에서 한 번

memberRepository가 총 3번이 호출되어 나는 다음 순서로 호출될 것이라 예상했다.

```text
call AppConfig.memberSerivce -> memberService에 의한 호출
call AppConfig.memberRepository -> memberService에 의한 호출
call AppConfig.orderService -> orderService에 의한 호출
call AppConfig.memberRepository -> orderService에 의한 호출
call AppConfig.discountPoilcy -> orderService에 의한 호출
call AppConfig.memberRepository -> memberRepository 의한 호출
call AppConfig.discountPoilcy -> discountPoilcy 의한 호출
```

하지만 바로 밑의 결과처럼 다른 결과를 확인할 수 있다.

```text
call AppConfig.memberService
call AppConfig.memberRepository
call AppConfig.orderService
call AppConfig.discountPoilcy
```

어째서 그런 것일까? 아래 코드를 확인해보자.

- 테스트 코드

  ```java
  @Test
  void configurationTest() {
      ApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);

      AppConfig appConfig = ac.getBean(AppConfig.class);
      System.out.println("AppConfig를 getBean으로 가져온 경우: " + appConfig);
      System.out.println("AppConfig를 바로 조회한 경우: " + AppConfig.class);
  }
  ```

- 결과

  ```text
  AppConfig를 getBean으로 가져온 경우: SpringBasic.core.AppConfig$$SpringCGLIB$$0@7a55af6b
  AppConfig를 바로 조회한 경우: class SpringBasic.core.AppConfig
  ```

ac는 `@Configuration`을 추가해 스프링 빈으로 등록된 객체다. 하지만 순수한 자바 객체로서의 스프링 빈이 아니다.
'CGLIB'라는 바이트코드 조작 라이브러리를 사용해 `@Configuration`이 붙은 클래스를 상속받는 클래스를 만들고, 이 클래스 객체를 등록한 것이다.
그래서 `CGLIB` 라는 문자를 확인할 수 있다.

이 바이트코드 조작 라이브러리가 스프링 빈을 싱글톤으로 작동되도록 보장한다.

예를 들어 @Bean이 붙은 메서드마다 스프링 빈 저장소에 해당 빈 이름이 이미 존재한다면 생성하지 않는다. 없다면 생성 후 컨테이너에 등록한다.

만약 @Configuration 없이 @Bean만 적용하면 스프링 빈으로는 등록이 되지만, 싱글톤으로 유지되지 않는다.

- 호출 결과

```
call AppConfig.memberService
call AppConfig.memberRepository
call AppConfig.orderService
call AppConfig.memberRepository
call AppConfig.memberRepository
```

그래서 항상 스프링 설정 정보는 @Configuration을 사용하자.

<br>

---

# Reference

- [스프링 MVC 1편 - 백엔드 웹 개발 핵심 기술](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-1/dashboard)
- [Introduction to the Spring IoC Container and Beans](https://docs.spring.io/spring-framework/reference/core/beans/introduction.html)
