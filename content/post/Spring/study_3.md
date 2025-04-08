---
title: "의존관계를 주입하는 여러 방법들"
date: 2025-01-25T17:41:06+09:00
draft: false
summary: 의존관계를 주입하는 여러 방법들에 대해 알아본다.
tags: ["Spring"]
categories: "Spring"
---

# 0. Introduction

의존관계를 주입하는 여러 방법들에 대해 알아본다. 의존관계를 주입하는 4가지 방을 다루는 첫 단원은 코드 설계의 관점이라 바로 이해가 되었지만, 나머지들은 도구 사용법이라서 실제 프로젝트를 만들어갈 때 참고하려고 정리해준다.


---


# 1. 의존관계를 주입하는 방법 4가지 

의존관계를 주입하는 방법은 크게 4가지 방법이 있다.
- 생성자를 통한 주입 방법
- 수정자 주입(setter 주입)
- 필드 주입
- 일반 메서드 주입

### 1.1 생성자를 통한 주입 방법

생성자를 통해서 의존관계를 주입받기 때문에, 호출 시점 단 한번만 호출되는 것이 보장된다. 그래서 **_불변, 필수 의존관계_** 에 사용된다. 
이전 Spring 학습 글에서 예시로 보여준 것이 이 주입 방법이다.

스프링 빈은 생성 단계와 의존관계를 주입하는 두 단계로 나눌 수 있는데, 생성자 주입은 bean 등록을 위해 생성자를 호출하므로 생성 단계와 주입 단계를 같이 한다. 

```java
@Component
public class OrderServiceImpl implements OrderService {
    private final MemberRepository memberRepository;
    private final DiscountPolicy discountPolicy;

    public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
        this.memberRepository = memberRepository;
        this.discountPolicy = discountPolicy;
    }
}
```

### 1.2 수정자 주입(setter 주입)

필드의 값을 변경하는 수정자 메서드인 setter를 통해 의존관계를 주입하는 방법이다. 단 한 번만 사용할 수 있는 생성자와 달리 계속 사용할 수 있기 때문에 **_선택, 변경_** 가능성이 있는 의존관계에 사용된다. 변경 가능하기 때문에 필드를 final로 선언하면 안된다.

```java
@Component
public class OrderServiceImpl implements OrderService {
    private MemberRepository memberRepository;
    private DiscountPolicy discountPolicy;


    @Autowired
    public void setMemberRepository(MemberRepository memberRepository) {
        this.memberRepository = memberRepository;
    }
}
```

자바빈 프로퍼티에서는 필드값을 조회하고 수정하는 메서드의 명칭을 getXxx, setXxx 라는 형식으로 작명해서 사용하기로 정했는데 이를 '자바빈 프로퍼티 규약' 이라 한다.  


### 1.3 필드 주입

필드에 바로 주입하는 방법이다. 코드가 간결하지만 외부에서 변경이 불가능하기 때문에 테스트하기 힘들다는 큰 단점이 있다. 그래서 DI 프레임워크가 없으면 아무것도 할 수 없다. 차라리 생성자 주입을 사용하도록 하자.

```java
@Component
public class OrderServiceImpl implements OrderService {
    @Autowired
    private MemberRepository memberRepository;
    @Autowired
    private DiscountPolicy discountPolicy;
}
```

### 1.4 일반 메서드를 통한 주입

일반 메서드를 통해서도 주입받을 수 있으나, 일반적으로 잘 사용하지 않는다고 한다. 생성자 주입과 수정자 주입을 통해 주로 다 하므로 이 방식은 사용하지 않는다.

스프링 컨테이너가 관리하는 스프링 빈이어야 동작한다. 스프링 빈이 아닌 일반 자바 클래스에 @Autowired를 적용하면 동작하지 않는다.

### 1.5 결론

과거에는 수정자 주입과 필드 주입을 많이 사용했지만, 최근에는 스프링을 포함한 DI 프레임워크 대부분이 생성자 주입을 권장한다. 그 이유는 위에서 언급한 내용을 정리하면 다음과 같다.
- 대부분의 의존관계 주입은 한번 일어나면 애플리케이션 종료시점까지 변경하지 않는다. 변경되어서도 안된다. 
- 그런데, 수정자 주입을 사용하면 수정 메서드를 public으로 열어야 한다. 이러면 누군가 실수로 변경할 수 있으므로 좋은 설계 방법이 아니다.
- 생성자이기 때문에 final 키워드를 사용할 수 있어서 확실하게 불변으로 설계할 수 있다. 
- 생성자 주입은 객체 생성 시 딱 한 번만 호출되므로 불변으로 설계할 수 있다. 
- 생성자 주입은 프레임워크에 의존하지 않고, 순수한 자바 언어의 특징을 잘 살리는 방법이기도 하다. 
- 기본으로 생성자 주입을 사용하고, 필수 값이 아닌 경우에는 옵션이 필요하면 수정자 주입 방식을 사용하면 된다. 
- 필드 주입은 사용하지 말자. 


---

# 2. 롬복(Lombok)을 사용해 생성자 코드 만들기 

> 스프링에서는 롬복이라는 라이브러리를 사용해 생성자, 조회자, 수정자를 자동으로 생성할 수 있어서 스프링과 자주 사용한다.

롬복 라이브러리를 사용해 생성자 코드를 자동으로 만드는 것을 알아보자. 롬복 라이브러리는 Dependencies에서 Lombok을 추가하면 된다. build.gradle에는 다음과 같이 추가한다. 

```
configurations {
     compileOnly {
         extendsFrom annotationProcessor
} }
```
그후, Preferences -> plugin -> lombok 검색해서 설치를 진행 -> 재시작 순서로 진행한다.


그러면 롬복이 어떤 역할을 하는지 코드 관점에서 확인해보자. 현재 아래 코드를 가지고 있다고 하자.

```java
@Componet
public class OrderServiceImpl implements OrderService {
    private final MemberRepository memberRepository;
    private final DiscountPolicy discountPolicy;

    @Autowired
    public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
        this.memberRepository = memberRepository;
        this.discountPolicy = discountPolicy;
    }
}
```

생성자가 1개이므로 @Autowired 를 생략할 수 있다. 그리고, 롬복 라이브러리가 제공하는 `@RequiredArgsConstructor` 를 사용하면 final이 붙은 필드를 모아 생성자를 자동으로 만들어주어 아래와 같이 작성만 하면 된다. 매우 간결해졌다. 
(import lombok.RequiredArgsConstructor 를 입력해야 한다.)


```java
@Componet
public class OrderServiceImpl implements OrderService {
    private final MemberRepository memberRepository;
    private final DiscountPolicy discountPolicy;

}
```

롬복을 스프링에서 사용하려면 다음 단계를 거쳐야 한다.

1. Preferences(window는 settings) -> Plugin -> lombok 검색 설치 진행(재시작)
2. Preferences -> Annotation Processors 검색 -> Enable annotation processing 체크(재시작)
3. import lombok.Getter, import lombok.Setter 를 작성하면 getter, setter를 만들지 않아도 자동으로 넣어준다.

---

# 3. 조회 빈이 2개 이상인 경우

@Autowired는 기본적으로 타입(Type)으로 조회하므로 ApplicationContext의 getBean 메서드와 동일하다. 
예를 들어 코드가 다음과 같다고 하자.

```java
@Autowired
private DiscountPolicy discountPolicy
```

이면 `ac.getBean(discountPolicy.class)` 와 유사하게 동작한다. 빈 조회 시 선택된 빈이 2개 이상이면 문제가 발생한다. 예를 들어 위 타입을 구현한 두 객체가 있을 경우, `NoUniqueBeanDefinitionException` 가 발생한다. 하위 타입으로 지정하면 DIP를 할 수 없다. 이런 경우 어떻게 해야할까?

총 3가지 방법이 있다.

- @Autowired 필드명 매칭: 동일한 타입을 가져왔는데 빈이 여러 개라면 필드명이 매칭되는 것 하나를 가져온다.
    - 첫 번째, 타입을 매칭한다.
    - 두 번째, 타입 매칭 결과가 2개 이상이면 필드명, 파라미터 명으로 빈 이름을 매칭한다. 
- @Qualifier -> @Qualifiter끼리 매칭 -> 빈 이름 매칭
    - @Qulifier는 추가 구분자를 붙여주는 방법으로, 빈 이름을 변경하는 것은 아니다.
    
    ```java
    // Case 1
    @Component
    @Qualifier("mainPolicy")
    public class RateDiscountPolicy implements DiscountPolicy {}
    ```

    ```java
    // Case 2: 매개변수에 사용할 경우
    @Autowired
    public OrderServiceImpl(@Qulifiter("mainDiscountPolicy") DiscountPolicy discountPolicy)
    ...
    ```


    - @Qualifier는 @Qualifier를 찾는 용도로만 사용해야 한다. 

- @Primary 사용: 여러 빈이 매칭되면 @Primary를 가지고 있는 빈이 우선권을 가지도록 하는 방법이다. 
    ```java
    @Component
    @Primary
    public class RateDiscountPolicy implements DiscountPolicy {}

    @Component
    public class FixDiscountPolicy implements DiscountPolicy {}
    ```

@Qulifier와 @Primary가 부딪힐 때 우선순위는 전자가 더 높다. 스프링은 자동보다 수동이, 넓은 범위 선택보다 좁은 범위의 선택권이 우선 순위가 높다. 

또한 @Qulifier의 경우, 주입받을 때 반드시 @Qulifier를 매개변수든지 다 붙여야 하지만 @Primary는 그럴 필요가 없다. 그래서 다음과 같이 사용할 수 있다. **_메인 데이터베이스의 커넥션을 획득하는 스프링 빈은 @Primary를 적용하고, 서브 데이터베이스가 존재할 경우 서브 데이터베이스의 커넥션 빈응ㄹ 획득할 때 @Qualifier를 명시적으로 획득하는 방식을 사용하면 코드를 깔끔하게 유지할 수 있다._**


