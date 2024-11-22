---
title: "모든 객체가 Object를 상속받아야 하는 이유"
date: 2024-06-18T22:50:08+09:00
draft: false
summary: Object 클래스가 있어야 하는 이유를 정리해보자.
tags: ["java"]
categories: "java"
---

## 모든 객체는 Object를 상속받는다고?

> _모든 객체는 Object 객체를 상속받으므로 최종 부모는 Object야._

모든 객체는 Object 객체를 상속받는다. 어떤 객체도 최상위로 올라가면 Object 객체로 끝난다. 마치 파이썬의 type 클래스처럼 말이다. 

"에...? 그런데 자바에서 사용자 정의 자료형인 클래스를 사용해 객체를 생성할 때 extends를 사용해 Object라는 객체를 상속받지 않아~" 

이렇게 생각들 수 있다. 왜냐하면 상속도 **_명확히 드러내서 하는 상속(명시적 상속)_** 과 **_드러내지 않고 진행되는 상속(묵시적 상속)_** 이 있기 때문이다. 명시적 상속은 이름에서 추측할 수 있듯이 여태 배운 상속하는 방법인 `extends`를 사용하는 상속이다. 그렇다면 대조적으로 '묵시적 상속'은 `extends`를 입력하지 않는 것을 알 수 있다. 즉, 입력하지 않아도 컴파일러가 `extends Object` 코드를 알아서 넣어주기 때문에 `Object`를 상속받는다. 
`Object`가 최상위이므로 null이 출력되는 것을 알 수 있다.

```java
public class Test {
    public static void main(String[] args) {

        Integer value = new Integer(1);
        Class cls = value.getClass();
        Class superCls = cls.getSuperclass();
        Class superSuperCls = superCls.getSuperclass();

        System.out.println(cls);
        System.out.println(superCls);
        System.out.println(superSuperCls);
        System.out.println(superSuperCls.getSuperclass());
    }
}
```

```text
# 출력 결과
class java.lang.Integer
class java.lang.Number
class java.lang.Object
null
```


&nbsp;

---


## 그러면 모든 클랙스가 상속받는 최상위 클래스를 만든 이유가 뭐야? 

> _2가지 이유로 공통 기능을 제공하고, 다형성을 구현하기 위해서야_


### 공통 기능 제공하기 

객체를 만들어 사용하다 보면 모든 객체에 필요한 공통된 기능이 있다는 것을 알 수 있다. 이 기능을 미리 만들어놓고 상속을 받아 사용한다면 재사용성이 높을 것이다. 하지만, 이런 기능을 객체를 만들 때마다 항상 새로운 메서드로 정의해서 만든다면 상당히 번거롭다. 또한, 만든다고 해도 개발자마다 일관성이 없을 것이다. 어떤 개발자는 `toString()` 이라 하고, 다른 개발자는 `representString()` 으로 다른 이름으로 만들 수 있다. 

그래서 `Object` 라는 최상위 객체를 만들어 모든 객체에 필요한 공통 기능을 제공할 수 있다. 

`Object` 객체가 제공하는 것 중 대표적으로는 다음 3가지를 꺼낼 수 있다. 
- `toString()`: 객체의 정보를 표현하는 것
- `equals()`: 객체의 같음을 비교하는 것
- `getClass()`: 객체의 클래스 정보를 제공하는 것  
- `hashcode()`: 객체 참조값을 해쉬화해 고유 id를 생성한다.

### 다형성 구현하기

`Object` 객체는 최상위 객체이기 때문에 어떠한 객체도 담을 수 있다. 
- `Object` 배열에 모든 타입을 담을 수 있다. 
- 매개변수 타입을 `Object`로 지정하면 모든 객체를 수용할 수 있다.

```java
Integer integer = new Integer(1);
String str = new String(" ");
Character chr = new Character(' ');

Object[] objects = {integer, str, chr};
```

하지만 한계가 있다. 특정 객체의 메서드를 사용하려면 먼저 해당 객체 타입으로 타입 캐스팅해야 한다.
왜냐하면 다른 객체의 메서드가 정의되어 있지 않아, 메서드 오버라이딩을 활용할 수 없다. 지정된 타입부터 속성과 메서드를 조회하기 때문이다. 

- 아래 코드는 컴파일 에러가 발생한다. 

    ```java
    public static void print(Object object, int i) {
        System.out.println(object.charAt(i));
    }
    ```

    ```java
    Cannot resolve method 'charAt' in 'Object'
    ```

- 다음과 같이 다운 캐스팅을 진행한 후, 사용하면 컴파일 에러가 발생하지 않는다.

    ```java
    public static void print(Object object, int i) {
        String value = (String) object;
        System.out.println(value.charAt(i));
    }
    ```

하지만, `Object` 객체에서 제공하는 메서드를 오버라이딩한다면 가능하다. 예를 들어 `toString()` 이나 `equals()`를 오버라이딩하면 Object 객체로 받아도 오버라이딩된 메서드가 실행된다.

- `Person` 객체를 생성해 본다.
    ```java
    public class Person {
        String name;
        Person(String name) {
            this.name = name;
        }

        @Override
        public String toString() {
            return "Person{" +
                    "name='" + name + '\'' +
                    '}';
        }
    }
    ```

- `main()` 메서드에서 실행한다. 
    ```java
    public static void main(String[] args) {
        Person person = new Person("철수");
        print(person)
    }
    
    public static void print(Object object) {
        System.out.println(object);
    }
    ```

    ```java
    Person{name='철수'}
    ```

&nbsp;

---


## Object 객체가 제공하는 메서드에 대해 알아보자.

### toString()

파이썬의 `__str__()` 메서드처럼 객체 정보를 문자열 형태로 제공하는 메서드다. `System.out.println()` 이나 `System.out.print()`에 객체 참조값을 전달하면 전달된 객체 내에서 `toString()`을 호출한다. 

### equals(): 동일성과 동등성 

프로그래밍에서 '객체가 같다'는 의미는 2가지로 분리해서 볼 수 있다. 
- 동일성: `==` 연산자를 사용해서 두 객체의 참조값이 동일한지를 비교
- 동등성: `equals()` 메서드를 사용해 두 객체가 논리적으로 동일한지 비교  

```java
String str01 = new String("철수");
String str02 = new String("철수");
System.out.println(str01 == str02);
System.out.println(str01.equals(str02));
```

위 두 객체를 기준으로 설명하자면 두 객체는 동등하지만 동일하지 않다. 

String처럼 자바에서 원래 있는 자료형이 아닌 사용자가 만든 자료형이면 어떨까?

`Person` 객체를 만들어보자. 속성은 String 타입의 `name` 필드만 존재한다.

```java
public class Person {
    public String name;

    Person(String name){
        this.name = name;
    }
}
```

그리고 인텔리제이의 도움을 받아 `equals()` 메서드를 구현한다. 도움을 받는 이유는 이 메서드를 정확히 구현하는 것은 쉽지 않다. 아래 규칙을 다 지켜야 한다. 
- 반사성: 객체는 자기 자신과 동등해야 한다.
    - `x.equals(x)` 는 항상 `true` 다.
- 대칭성: 두 객체가 서로에 대해 동일하면, 양방향으로 동일해야 한다. 
    - `x.equals(y)`가 `true`면, `y.equals(x)`도 `true`여야 한다.
- 추이성: 한 객체가 두 번째 객체와 동일하고, 두 번째가 세 번째 객체와 동일하면 첫 번째와 세 번째도 동일해야 한다.
- 일관성: 객체의 상태가 변경되지 않는 한 항상 동일한 값을 반환해야 한다.
- null에 대한 비교: 모든 객체는 null과 비교했을 때 `false`를 반환해야 한다.

그래서 IDE가 만들어주는 것을 대부분 사용한다. 

&nbsp;

---

## 요약

모든 객체의 최상위 객체는 `Object` 객체이다. `extends Object`로 명시적으로 입력하지 않아도 컴파일러가 알아서 입력한다. 최상위 객체가 존재하는 이유는 모든 객체에 특정 메서드들을 공통으로 제공하기 위해서고, 다형성을 구현하기 위해서다. 

타입을 `Object`로 지정하면 모든 객체를 받을 수 있다. 만약 최상위 객체가 제공하는 메서드를 오버라이딩하는 객체를 생성한다면 `Object` 타입으로 전달해도 오버라이딩된 메서드가 실행된다. 만약 오버라이딩된 메서드가 없다면 실행하고 싶은 메서드를 가지고 있는 객체로 다운캐스팅을 한 후, 실행해야 한다. 