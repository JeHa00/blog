---
title: "의존관계를 자동 주입하는 방법들"
date: 2025-01-25T17:41:06+09:00
draft: true
summary: 
tags: ["Spring"]
categories: "Spring"
---

# 의존관계를 주입하는 방법 4가지 

의존관계를 주입하는 방법은 크게 4가지 방법이 있다.
- 생성자를 통한 주입 방법
- 수정자 주입(setter 주입)
- 필드 주입
- 일반 메서드 주입

### 생성자를 통한 주입 방법

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

### 수정자 주입(setter 주입)

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


### 필드 주입

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

### 일반 메서드를 통한 주입

일반 메서드를 통해서도 주입받을 수 있으나, 일반적으로 잘 사용하지 않는다고 한다.