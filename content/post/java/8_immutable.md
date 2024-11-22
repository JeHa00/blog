---
title: "불변형 객체가 있어야 하는 이유"
date: 2024-11-22T10:00:23+09:00
draft: false
summary: 불변형 객체가 왜 있어야 하는지 알아본다.
tags: ["java"]
categories: "java"
---


### 기본형과 참조형 리뷰

이번 포스팅에서는 불변 객체, String 객체 그리고 StringBuilder 객체를 점진적으로 알아가려 한다. 


여태 학습한 내용을 보면 자바에는 자료형이 기본형과 참조형이 있다는 것을 알 수 있다. **_자바는 항상 값을 복사해서 대입한다_** 는 사실을 다시 떠올려보자. 이 사실에 따라 두 자료형 모두 전달하는 값이 존재한다. 기본형은 해당 타입의 실제 값을 복사해서 전달하고, 참조형은 참조값을 복사해서 전달한다. 코드로 확인해 보자.


- 기본형

    ```java
    int a = 10;
    int b = a;

    a = 20; 
    System.out.println("a = " + a);
    System.out.println("b = " + b);
    ```
    ```text
    // 실행 결과
    a = 20
    b = 10
    ```
    - b에 할당 연산자를 사용해 a를 전달하면 a와 동일한 값이 전달된다.  
    - a의 값을 다른 값으로 수정해도 b에는 영향을 미치지 않는다.

위 결과로 기본형은 타입의 실제 값을 복사해서 전달한다는 것을 알 수 있다. 똑같이 10이어도 동일한 메모리 주소를 가지지 않으므로 하나가 수정되어도 다른 값에 영향을 주지 않는다. 다음으로 참조형을 확인해보자.

- 참조형

    ```java
    public class Address {
        private String value; 

        public Address(String value) {
            this.value = value; 
        }

        public void setAddress(String value) {
            this.value = value;
        }

        public String getAddress() {
            return value; 
        }

        @Override
        public String toString() {
            return "Address{" +
                    "value='" + value + '\'' +
                    '}';
        }
    }
    ```

    ```java
    // 실행 main 메서드
    Address a = new Address("서대문구");
    Address b = a; 
    System.out.println("a = " + a);
    System.out.println("b = " + b);

    a.setAddress("강남구"); 
    System.out.println("a = " + a);
    System.out.println("b = " + b);
    ```

    - 실행 결과
        ```text
        a = Address{value='서대문구'}
        b = Address{value='서대문구'}
        a = Address{value='강남구'}
        b = Address{value='강남구'}
        ```

    - b에 할당 연산자를 사용해 a를 입력하면 기본형과 달리 Address 객체의 참조값이 복사되어 전달된다.
    - 참조형은 실제 값이 아닌 참조값(메모리 주소 값)을 복사해서 전달하므로 a의 값을 변경하면 b의 값도 변경되는 것을 확인할 수 있다.




### 참조형의 사이드 이펙트와 해결책

참조형의 이런 특성 때문에 의도치 않은 변경이 발생할 수 있다. 이 문제를 해결하려면 제일 쉬운 방법은 다른 객체를 생성하는 방법이다. 그러면 예를 들어 a와 b는 서로 다른 참조값을 가지게 되고, a가 수정되어도 b는 그대로다. 하지만 논리적으로 동일한 값을 가지는데 

보다 근본적으로는 객체 공유를 막으면 되지만, 자바문법 상 참조형 변수의 대입은 아무런 문제가 없어서 불가능하다. 

또 다른 방법은 없을까? 

사이드 이펙트가 발생하는 일차적인 원인은 공유된 값이 변경된 것에 있다. 그렇다면 참조값이 가리키는 객체의 정보를 변경할 때 객체 자체를 변경하기보다는 새로운 객체를 생성해서 반환하면 공유 참조로 인한 문제가 발생하지 않을 것이다. 이처럼 **_객체의 상태(객체 내부 값)가 변하지 않는 객체를 불변 객체(Immutable Object)라 한다._**

그러면 `private String value` 로 된 부분을 `private final String value`로 수정한다. `setValue()` 메서드를 만들지 않는 방법도 있으나 보다 명확하게 의도를 드러내기 위해서 `final`을 추가한다.

`Address` 객체를 다음과 같이 수정하면 불변 객체로 바꿀 수 있다. 
- `final` 추가
- `setAddress` 삭제
- 생성자를 통해서만 값을 설정할 수 있고, 이후 값 변경은 불가능하다.

```java
public class Address {
    private final String value; 

    public Address(String value) {
        this.value = value; 
    }

    public String getAddress() {
        return value; 
    }

    @Override
    public String toString() {
        return "Address{" +
                "value='" + value + '\'' +
                '}';
    }
}
```


### 불변 객체여도 값을 변경해야 한다면?

불변 객체이지만 값을 변경해야 하는 메서드가 필요하다면 **_'변경된 값을 가지는 새로운 불변 객체'_** 를 생성해 반환하면 된다. 

만약 위 `Address` 객체를 통해서 값을 변경하고 싶다면 아래 메서드를 추가한다.

```java
public Address setAddress(String value) {
    return new Address(value);
}
```


```java
// 실행 main 메서드
Address a = new Address("서대문구");
Address b = a; 
System.out.println("a = " + a);
System.out.println("b = " + b);

a = a.setAddress("강남구"); 
System.out.println("a = " + a);
System.out.println("b = " + b);
```

- 실행 결과
    ```text
    a = Address{value='서대문구'}
    b = Address{value='서대문구'}
    a = Address{value='강남구'}
    b = Address{value='서대문구'}
    ```

b의 값까지 변경되지 않는 것을 알 수 있다. a 변수의 입장에서는 값이 변경된 거지만, 사실 새로운 불변형 객체를 생성한 것이 정확한 표현이다.


### 불변형 객체를 언급한 이유 

가변형 객체가 모다 일반적이지만, 언급한 이유는 String 클래스가 바로 불변형 객체이기 때문이다. 그리고 문자열이지만 가변형으로 사용하기 위해 존재하는 것이 StringBuilder 클래스다. 이 두 가지에 대해 다음 포스팅에서 알아보자. 