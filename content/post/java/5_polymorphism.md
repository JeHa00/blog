---
title: "자바의 다형적 참조, 추상 클래스 그리고 인터페이스"
date: 2024-03-15T00:34:19+09:00
draft: false
summary: 자바의 메모리 영역과 상속을 토대로 어떻게 다형성이 구현되는지, 다형성을 구현하는 방법 중 하나인 캐스팅, 그리고 효과적으로 다형성을 구현하기 위한 추상 클래스와 인터페이스에 대해 알아본다.  
tags: ["java"]
categories: "java"
---


# 객체지향 프로그래밍의 대표적인 특징들과 다형성  

객체지향 프로그래밍에는 대표적으로 아래의 3가지 특징을 가진다.  

- 캡슐화(encapsulation): 객체의 속성과 기능을 객체 안에서 관리하고, 접근 제어자를 사용하여 클라이언트에게 필요한 것만 제공하는 방식

- 상속(inheritance): 부모 클래스의 속성과 기능을 자식 클래스가 물려받아 사용하는 방식

위 2가지는 시각적으로 쉽게 드러나기 때문에 이해하기 쉽다. 하지만 '다형성(polymorphism)'은 위 2가지 특징보다 조금 어렵다.  

'다형성'은 크게 2가지 이론: 다형적 참조, 메서드 오버라이딩을 알아야 한다.  

먼저 '다형성'의 정의에 대해 알아보자면 **_'다형성'은 하나의 객체가 여러 타입의 객체로 취급받을 수 있는 특성_** 을 말한다.  

그러면 이 '다형성'의 큰 2가지 이론에 대해 알아보자.  

## 다형적 참조

'다형적 참조'는 무엇을 의미하는 걸까? '다형성'이란 의미에서 시작해보자. 하나의 객체가 여러 타입의 객체로 취급받을 수 있는 특성을 말한다고 했으니, **_다형적 참조란 여러 타입의 객체로 취급받을 수 있는 참조 방식_** 을 말하는 것이라 추측할 수 있다. 

그러면 캡슐화, 상속을 이해하기 위해 우리는 코드를 통해 이해한 것처럼 다형적 참조도 코드를 통해 이해해야하는데, 어떤 예시들이 있을까?

상속을 사용하여 변수를 참조할 때에 어떤 경우들이 있을까?

1) 자식 인스턴스를 자식 타입의 변수가 참조하는 것  
2) 부모 인스턴스를 부모 타입의 변수가 참조하는 것  
3) 자식 인스턴스(또는 손자 이하 단계의 인스턴스)를 부모 타입의 변수가 참조하는 것    
4) 부모 인스턴스를 자식 타입의 변수가 참조하는 것  

위 경우들에서 다형적 참조는 3번과 4번에 해당된다. 다형적 참조를 보다 구체적으로 언급하자면 **_'생성된 인스턴스의 클래스 타입 외에 상속 관계에 있는 다른 클래스 타입으로 참조하는 것'_** 을 말한다.

하지만 4번은 가능하지 않다. 위 각 경우를 코드로 확인해보자. 그러면 위 각 경우들을 코드로 나타내보자.  

- 부모 클래스

    ```java
    public class Parent {
        public void ParentMethod() {
            System.out.println("Parent.parentMethod");
        }
    }
    ```

- 자식 클래스

    ```java
    public class Child extends Parent {
        public void childMethod() {
            System.out.println("Child.childMethod");
        }
    }
    ```


- 인스턴스 생성

    ```java
    public class ChildMain {
        public static void main(String[] args) {
            // 1) 자식 인스턴스를 자식 타입의 변수가 참조하는 경우
            System.out.println("Child instance with Child type");
            Child child1 = new Child();
            child1.parentMethod();
            child1.childMethod();

            // 2) 부모 인스턴스를 부모 타입의 변수가 참조하는 경우
            System.out.println("Parent instance with Parent type");
            Parent parent1 = new Parent();
            parent1.parentMethod();

            // 3) 자식 인스턴스를 부모 타입의 변수가 참조하는 경우
            System.out.println("Child instance with Parent type");
            Parent parent2 = new Child();
            parent2.parentMethod();

            // 4) 부모 인스턴스를 자식 타입의 변수가 참조하는 경우
            System.out.println("Parent instance with Child type");
            // 컴파일 오류 발생
            Child child2 = new Parent();
            child2.childMethod()
        }
    }
    ```

위 인스턴스 생성 코드를 실행하면 `Child child2 = new Parent2()`에서 다음 에러가 발생한다.

```java
java: incompatible types: example.Parent cannot be converted to example.Child
```

`Parent` 인스턴스는 `Child` 타입을 가질 수 없다는 의미다.  


## 다형적 참조가 가능한 이유

그러면 위 1,2,3번은 가능하고, 4번은 가능하지 않은 이유에 대해 알아보자.

자식 인스턴스가 메모리에 어떻게 생성되는지 이미지로 확인하고 싶으면 [상속과 오버라이딩이 메모리 영역에서 어떻게 이뤄지지?- 상속과 메모리 구조](https://jeha00.github.io/post/java/4_inheritance/#%EC%83%81%EC%86%8D%EA%B3%BC-%EB%A9%94%EB%AA%A8%EB%A6%AC-%EA%B5%AC%EC%A1%B0-)를 확인하자.


### 1) 자식 인스턴스를 자식 타입의 변수가 참조하는 경우

인스턴스를 생성할 때, `Child`의 인스턴스만 생성되는게 아니라 `Parent`의 인스턴스도 생성된다. 인스턴스는 `Child` 타입과 `Parent` 타입을 모두 가지고 있다. 그래서 참조 변수 생성할 때 자식 타입과 부모 타입 모두 가능하다. 그래서 인스턴스의 타입을 자식 위치에 있는 `Child`로 정할 수 있다. 

메서드 실행의 경우 `child1.parentMethod()`의 실행은 `Child` 클래스에서 `parentMethod()`를 찾지 못하자, 부모인 `Parent` 클래스로 올라가서 찾아 실행한 경우다.

#

### 2) 부모 인스턴스를 부모 타입의 변수가 참조하는 경우

인스턴스를 생성할 때 `Parent`의 인스턴스만 생성되고, `Child`의 인스턴스는 생성되지 않은 경우다. 여기서 `parent1.childMethod()`를 실행하면 `childMethod()`를 찾을 수 없다는 에러가 발생한다. 

#

### 3) 자식 인스턴스를 부모 타입의 변수가 참조하는 경우

1번에서 인스턴스를 생성할 때 `Parent`와 `Child`의 인스턴스가 모두 생성된다고 했다. 그래서 참조 변수의 타입을 `Parent`로 할 수 있다. 

그러면 1번과의 차이점은 무엇일까? 바로 **_참조변수의 타입이 이 상속 관계에 있는 클래스들의 조회 시작 위치를 결정하기 때문에 자식 위치에 있는 클래스 정보를 조회할 수 없다._** 

작성된 코드를 보면 참조변수의 타입이 바뀐 것이다. 참조변수의 타입이그러면 코드가 작동될 때 무슨 차이점을 만들까?

이전 포스팅에서 상속 관계는 자식 클래스에서 부모 클래스 방향으로 올라가면서 조회할 수 있지만, 반대 방향으로는 찾기 위해 내려갈 수 없다고 했다. 

참조 변수의 타입이 `Parent`이기 때문에 클래스 조회 순서가 `Parent`부터 시작한다. 자식 인스턴스여도 `parent2.childMethod()`를 실행할 수 없다는 의미다. 왜냐하면 상속 관계에 있는 클래스들의 조회방향은 자식에서 부모 방향으로만 위로만 갈 수 있기 때문이다. 

`Parent` 클래스가 만약 `GrandParent` 라는 클래스를 상속받으면 `Parent` 클래스에서 `GrandParent` 클래스 방향으로 조회한다. 하지만 현재 위 코드에 따르면 `Parent`가 끝이기 때문에 위로 올라갈 클래스는 없다. 

#

### 4) 부모 인스턴스를 자식 타입의 변수가 참조하는 경우

이 인스턴스의 경우, `Parent` 클래스의 인스턴스만 생성된 경우라서 `Child` 클래스의 인스턴스 정보는 없다.

그래서 참조 변수의 타입을 `Child`로 할 경우, 생성된 인스턴스에 `Child` 정보가 없기 때문에 다음과 같은 에러가 발생했던 것이다.

```java
java: incompatible types: example.Parent cannot be converted to example.Child
```

그러면 4번과 같은 상황에서 `Child` 타입으로 변환하고 싶으면 어떻게 해야할까?

이 문제를 해결하기 위해서 필요한 게 '캐스팅(Casting)'이다. 캐스팅에 대해서 알아보자.

&nbsp;

---

# 캐스팅(Casting)  

캐스팅의 의미는 '주조'라는 '액체 상태의 금속을 원하는 형틀에 부어 넣어 굳혀서 모양을 만드는 작업'을 말한다. 

다른 형태로 만드는 것이라고 이해하면 되겠다. 위 내용에서 학습한 것과 이어서 이해하자면 **_다른 타입으로 변환하는 것 _** 을 **_캐스팅_** 이라 이해하자.  

캐스팅에는 'Down casting(다운 캐스팅)'과 'Up casting(업 캐스팅)'이 존재한다.  

- 다운 캐스팅: 상속 관계에 있는 클래스 관계 중 부모 클래스 타입에 속한 변수를 자식 클래스 타입으로 변경하는 것
- 업 캐스팅: 상속 관계에 있는 클래스 중 자식 클래스 타입을 부모 클래스 타입으로 변경하는 것

그러면 각 캐스팅에 대해 알아보자.  


## 다운 캐스팅(Down casting)

다운 캐스팅은 위에서 설명한 대로 **_부모 클래스 타입에 속한 참조 변수를 자식 클래스 타입으로 변경하는 것_** 을 말한다.  

영구적으로 다운 캐스팅을 하는 방식이 있고, 일시적으로 하는 방식이 있다. 각 방식에 대해 알아보자.  

### 영구적 다운 캐스팅  

어떻게 해서 영구적으로 가능한 것일까? **_바로 다운 캐스팅한 결과를 자식 클래스 타입으로 선언한 새로운 참조 변수에 담는 것이다._**

`Parent` 타입을 `Child` 타입으로 다운 캐스팅하기 위해서는 생성된 인스턴스에 `Child` 인스턴스가 있어야 가능하다. 즉, `Child` 클래스의 인스턴스로 생성한 후, `Parent` 타입으로 참조 변수를 초기화해야 가능하다. 

```java
public class ChildMain {
    public static void main(String[] args) {
        ...

        // 자식 인스턴스를 부모 타입의 변수가 참조하는 경우
        System.out.println("Child instance with Parent type");
        Parent parent2 = new Child();
        parent2.parentMethod();

        // Down casting
        Child child = (Child) parent2;
        child.childMethod();

        ...
    }
}
```

위 영구적 다운 캐스팅의 경우도 `parent2`에 있는 참조값을 복사해서 읽는다. JAVA에서 대입은 항상 기존 것을 절대 손대지 않는다. 그래서 실행순서를 보다 쪼개서 이해하면 다음과 같다.

```java
// 첫 번째
Child child = (Child) parent2;

// 두 번째
Child child = (Child) x111; // 참조값을 복사해서 읽은 후, 자식 타입으로 지정

// 세 번째
Child child = x111; // 
```

아래 코드를 실행하면 `parent2`와 `child` 모두 동일한 식별값을 확인할 수 있다. 

- 실행 코드

    ```java
    System.out.println(child);
    System.out.println(parent2);
    ```

- 실행 결과

    ```java
    example.Child@3cb5cdba
    example.Child@3cb5cdba
    ```

**_그래서 동일한 참조값을 가지고 있어도, 인스턴스에 변환할 타입 정보가 존재한다면 가능하다는 걸 알 수 있다._** 

### 다운 캐스팅 시도 시 오류 발생   

만약 정보가 없다면 어떻게 될까?

```java
...
    // 부모 인스턴스를 자식 타입의 변수가 참조하는 경우
    System.out.println("Parent instance with Child type");
    Parent poly = new Parent();
    Child c = (Child) poly;
```

위 poly 참조 변수가 가리키는 인스턴스에는 `Parent` 클래스의 인스턴스 정보가 가지고 있다. 그래서 위 코드를 실행하면 다음과 같은 에러가 발생한다.

```java
class example.Parent cannot be cast to class example.Child 
(example.Parent and example.Child are in unnamed module of loader 'app')
```

변환할 수 없다고 뜬다.

**_항상 다운 캐스팅을 할 때에는 인스턴스에 변환하려는 타입의 인스턴스 정보가 있는지 먼저 확인하자._**


### 일시적 다운 캐스팅

위 다운 캐스팅 코드 예시는 다운 캐스팅한 후, 이를 참조 변수에 담았다. 이와 반대로 일회성으로만 사용하기 위해 참조 변수에 담지 않고, 일시적으로만 다운 캐스팅하여 사용하고 싶다면 어떻게 해야할까? 

```java
package example;

public class ChildMain {
    public static void main(String[] args) {
        ...

        System.out.println("Child instance with Parent type");
        Parent parent2 = new Child();

        // 영구적 다운 캐스팅
        Child child = (Child) parent2;

        // 일시적 다운 캐스팅
        ((Child parent2)).childMethod();
        ...
    }
}
```

위 코드에서 확인할 수 있는 것처럼 `()`를 사용하여 일시적으로 다운 캐스팅을 할 수 있다. `((Child) x111).childMethod()` 라고 생각하자. 

**_이렇게 일시적 다운 캐스팅을 사용하면 별도의 변수 없이 인스턴스의 자식 타입의 기능을 사용할 수 있다._**


&nbsp;


## 업 캐스팅(Up casting)  

이제는 반대로 업 캐스팅(up casting)에 대해 알아보자. 업 캐스팅은 **_자식 클래스 타입을 부모 타입으로 변경하는 것_** 을 말한다. 아래 업 캐스팅 코드를 확인해보자.  

```java
package example;

public class ChildMain {
    public static void main(String[] args) {
        // 자식 인스턴스를 자식 타입의 변수가 참조하는 경우
        System.out.println("Child instance with Child type");
        Child child1 = new Child();
        Parent parent1_casted = (Parent) child1;
        Parent parent2_casted = child1;
        parent1_casted.parentMethod();
        parent2_casted.parentMethod();

        ...
    }
}
```

`parent1_casted` 와 `parent2_casted`가 업 캐스팅에 의해 생성된 참조 변수다. 이 참조 변수를 보면 `parent1_casted`는 `(Parent)`를 명시했고, `parent2_casted`는 `(Parent)`를 생략했다. **_업캐스팅은 (타입)을 생략할 수 있다. 다운캐스팅은 생략할 수 없다. 업캐스팅은 매우 자주 사용하기 때문에 생략을 권장한다.**_**


## 안전한 업캐스팅, 위험한 다운 캐스팅 그리고 주의사항 

그러면 왜 업 캐스팅은 생략할 수 있고, 다운 캐스팅은 생략할 수 없을까?

업 캐스팅의 경우 인스턴스를 생성하면 해당 타입의 상위 부모 타입은 함께 생성된다. 따라서 **_상위 타입으로만 변경하는 업캐스팅은 메모리 상에 인스턴스가 모두 존재하기 때문에 캐스팅을 생략할 수 있다._**

하지만 다운 캐스팅의 경우, 인스턴스에 존재하지 않는 하위 타입으로 캐스팅할 경우 컴파일 오류와 런타임 오류 발생할 수 있다. 따라서 **_개발자가 이런 문제를 인지하고 사용해야 한다는 의미로 명시적으로 캐스팅을 해야한다._**


### 컴파일 오류(compile error) vs 런타임 오류(runtime error)

다운 캐스팅 시 발생하는 컴파일 오류와 런타임 오류에 대해 확인하기 전에 먼저 이 두 오류 간 차이에 대해 정리해보자.  

컴파일 오류는 변수명 오타, 잘못된 클래스 이름, 변수 이름 등 자바 프로그램을 실행하기 전 컴파일 단계에서 발생하는 오류다. 그래서 오류지만 IDE에서 즉시 확인할 수 있기 때문에 안전하고 좋은 오류라고 볼 수 있다.  

하지만 런타임 오류는 오류 이름대로 프로그램이 실행되고 있는 시점에 발생하는 오류다. 그래서 오류 중에서도 매우 안좋은 오류다. 왜냐하면 고객이 프로그램을 사용하는 중에 발생하기 때문이다. 이 오류는 IDE에서 즉시 확인할 수 없다.  

&nbsp;

### 다운 캐스팅의 컴파일 오류와 런타임 오류  

다운 캐스팅 시 런타임 오류가 발생하는 경우는 [다운 캐스팅 시도 시 오류 발생](http://jeha00.github.io/post/java/5_polymorphism/#%EB%8B%A4%EC%9A%B4-%EC%BA%90%EC%8A%A4%ED%8C%85-%EC%8B%9C%EB%8F%84-%EC%8B%9C-%EC%98%A4%EB%A5%98-%EB%B0%9C%EC%83%9D) 경우를 확인하면 된다. 부모 클래스의 인스턴스를 생성한 후 자식 클래스 타입의 참조 변수에 담을려고 할 때 발생한다. 왜냐하면 부모 클래스의 인스턴스에는 자식 클래스의 인스턴스 정보가 없기 때문이다.  

그렇다면 다운 캐스팅 시도 시 발생할 수 있는 컴파일 오류는 무엇이 있을까?

이 포스팅 시작 시 언급했던 4가지 경우 중 한 가지인 '부모 인스턴스를 자식 타입의 변수가 참조하는 경우'의 코드를 보면 알 수 있다. 

```java
public class ChildMain {
    public static void main(String[] args) {
        ...

        // 4) 부모 인스턴스를 자식 타입의 변수가 참조하는 경우
        System.out.println("Parent instance with Child type");
        Child child2 = new Parent(); // 컴파일 오류 발생
    }
}
```

그러면 `Child child2 = new Parent();` 코드에 빨간 밑줄이 생기면서 IDE에 아래 내용의 에러 내용이 생긴다.

```java
Incompatible types. Found: 'example.Parent', required: 'example.Child'
```

이 또한 `Parent` 인스턴스에는 `Child` 인스턴스 내용이 없기 때문에 발생하는 에러다.  


&nbsp;

---

# instanceof

### instanceof dmfh

그러면 다운 캐스팅 시 위 에러들을 피하기 위해서는 **_참조 변수가 내가 원하는 클래스의 인스턴스 정보를 가지고 있는지 확인하는 게 필요하다. 이럴 때 사용하는게 `instanceof`다._**  

`instanceof`를 사용하기 위한 매개변수는 다음과 같으며, `boolean` 값을 반환한다.  

```java
<인스턴스 참조값> instanceof <확인하려는 타입명>
```

아래 예시 코드를 보자. 

```java
package example;

public class ChildMain {
    public static void main(String[] args) {
        // 자식 인스턴스를 자식 타입의 변수가 참조하는 경우
        System.out.println("---- Child instance with Child type ----");
        Child child1 = new Child();
        System.out.println(child1 instanceof Child);
        System.out.println(new Child() instanceof Child);

        // 부모 인스턴스를 부모 타입의 변수가 참조하는 경우
        System.out.println("\n---- Parent instance with Parent type ----");
        Parent parent1 = new Parent();
        System.out.println(parent1 instanceof Parent);
        System.out.println(new Parent() instanceof Parent);

        // 자식 인스턴스를 부모 타입의 변수가 참조하는 경우
        System.out.println("\n---- Child instance with Parent type ----");
        Parent parent2 = new Child();
        System.out.println(parent2 instanceof Parent);
        System.out.println(parent2 instanceof Child);
        System.out.println(new Child() instanceof Parent);
    }
}
```

위 코드를 실행하면 아래와 같다.  

```java
---- Child instance with Child type ----
true  // child1 instanceof Child 의 결과
true  // new Child() instanceof Child 의 결과

---- Parent instance with Parent type ----
true  // parent1 instanceof Parent 의 결과
true  // new Parent instanceof Parent 의 결과

---- Child instance with Parent type ----
true  // parent2 instanceof Parent 의 결과
true  // new Child() instanceof Parent 의 결과
true  // parent2 instanceof Child 의 결과
```

- `child1` 참조 변수는 `Child` 클래스의 인스턴스 정보를 가지고 있으므로 `true`다. 그리고 `new Child()`도 당연히 `True`다.

- `parent1` 참조 변수는 `Parent` 클래스의 인스턴스 정보를 가지고 있으므로 `true`다. 그리고, `new Parent()`도 당연히 `True`다.

- `parent2` 참조 변수는 `Parent` 클래스의 인스턴스 정보를 가지고 있으므로 `true`다. 

아직 언급하지 않은 2가지가 있다. `new Child() instanceof Parent`와 `parent2 instanceof Child` 다. 이 2가지의 출력 결과는 `true`다. 

- `Child` 클래스는 `Parent` 클래스를 상속받고 있고, 상속받는 클래스의 인스턴스를 생성한다는 걸 확인했다.

- `parent2` 참조 변수는 타입이 `Parent`이지만 `Child`의 인스턴스 정보를 가지고 있는 걸 확인했다. 왜냐하면 `parent2`는 `Child` 인스턴스의 참조값을 가지고 있기 때문이다.  

그래서 `instanceof`를 통해 상속하는 클래스의 인스턴스 정보를 가지고 있는 걸 확인했다.  

&nbsp;

---

# 다형성과 메서드 오버라이딩  

다형성을 이루는 이론에는 '다형적 참조'와 '메서드 오버라이딩'이 있다고 했다. 이번 단원에서 다룰 내용이 바로 '메서드 오버라이딩'이다. 다형성과 메서드 오버라이딩에서 꼭 기억해야할 점은 **_오버라이딩된 메서드가 항상 우선권을 가진다_** 는 점이다. 오버라이딩(Overriding)은 단어의 의미처럼 기존 기능을 덮어 새로운 기능을 재정의하는 것이다. 이것이 다형성과 어떻게 연결되냐면 _자식의 메서드를 정의할 때 부모 메서드를 오버라이딩하여 정의했고, 자식 인스턴스 참조값을 가지고 있는 부모 타입의 참조 변수를 생성한 후, 이 참조 변수로 오버라이딩된 메서드를 실행하면 부모 타입이어도 오버라이딩된 자식 메서드가 실행된다는 것이다._  

그러면 코드를 통해 이 내용을 확인해보자.


- 부모 클래스

    ```java
    public class Parent {
        public void method() {
            System.out.println("I am a parent");
        }
    }
    ```

- 자식 클래스

    ```java
    public class Child extends Parent {
        
        @Override
        public void method() {
            System.out.println("I am a child");
        }
    }
    ```

- 인스턴스 생성

    ```java
    public class ChildMain {
        public static void main(String[] args) {
            Parent parent1 = new Parent();
            parent1.introduce();

            Parent parent2 = new Child();
            parent2.introduce();
        }
    }
    ```

- 실행 결과

```java
I am a parent
I am a child
```



실행 결과를 보면 `parent1`를 통해 `introduce` 메서드를 실행할 때와 `parent2`를 통해 `introduce`를 실행한 결과가 다른 걸 확인할 수 있다. **_위 결과를 통해서 생성된 인스턴스에 부모의 메서드를 오버라이딩한 자식의 메서드가 있다면 참조 변수가 부모 타입이어도 오버라이딩한 자식의 메서드가 실행된다는 걸 알 수 있다._**  


&nbsp;

---


# 다형성 활용

이번 단원에서는 다형성의 두 가지 중요한 이론인 '다형적 참조'와 '메서드 오버라이딩'을 활용하여 코드를 수정해가며 그 필요성을 느껴보자. 

예시 코드로 4개의 클래스를 만들었다. 

- PotatoPizza

    ```java
    package pizza;

    public class PotatoPizza {

        public void addIngredient() {
            System.out.println("감자 재료를 추가합니다.");
        }
    }
    ```

- SweetPotatoPizza

    ```java
    package pizza;

    public class SweetPotatoPizza {

        public void addIngredient() {
            System.out.println("고구마 무스 재료를 추가합니다.");
        }
    }
    ```

- AllMeatPizza

    ```java
    package pizza;

    public class AllMeatPizza {

        public void addIngredient() {
            System.out.println("고기 6종류 재료를 추가합니다.");
        }
    }
    ```

- BaconCheddarCheesePizza

    ```java
    package pizza;

    public class BaconCheddarCheesePizza {

        public void addIngredient() {
            System.out.println("베이컨과 체다 치즈를 추가합니다.");
        }
    }
    ```

그리고 각 클래스의 인스턴스를 생성한 후, 메인 재료를 추가하는 작업을 수행한다. 


- PizzaMain

    ```java
    package pizza;

    public class PizzaMain {

        public static void main(String[] args) {
            PotatoPizza potatoPizza = new PotatoPizza();
            SweetPotatoPizza sweetPotatoPizza = new SweetPotatoPizza();
            BaconCheddarCheesePizza baconCheddarCheesePizza = new BaconCheddarCheesePizza();
            AllMeatPizza allMeatPizza = new AllMeatPizza();

            potatoPizza.addIngredient();
            sweetPotatoPizza.addIngredient();
            baconCheddarCheesePizza.addIngredient();
            allMeatPizza.addIngredient();
        }
    }
    ```

- 실행 결과

    ```java
    감자 재료를 추가합니다.
    고구마 무스 재료를 추가합니다.
    베이컨과 체다 치즈를 추가합니다.
    고기 6종류 재료를 추가합니다.
    ```

하지만 각 피자마다 새로운 피자를 만든다는 작업 안내와 종료 안내를 하고 싶습니다. 그래서 아래와 같이 코드를 추가했습니다. `.ingredient()` 메서드를 실행하기 전과 후 출력문을 추가했습니다.

```java
System.out.println("새로운 피자를 만듭니다.");
potatoPizza.addIngredient();
System.out.println("새로운 피자를 만들었습니다.");

System.out.println("새로운 피자를 만듭니다.");
sweetPotatoPizza.addIngredient();
System.out.println("새로운 피자를 만들었습니다.");

System.out.println("새로운 피자를 만듭니다.");
baconCheddarCheesePizza.addIngredient();
System.out.println("새로운 피자를 만들었습니다.");

System.out.println("새로운 피자를 만듭니다.");
allMeatPizza.addIngredient();
System.out.println("새로운 피자를 만들었습니다.");
```

위 코드는 계속 반복된다는 문제점이 있다. 이 반복을 줄이기 위해서 어떤 방법들을 사용할 수 있을까?

1) `addIngredient()`에 시작 및 종료 가이드문을 추가한다.  
2) 위 인스턴스들을 매개변수로 받으면서, 시작 및 종료 가이드문까지 포함하는 새로운 메서드를 생성한다.
3) 인스턴스들을 한 배열에 담아 반복문을 통해 호출한다.  

1번 방법의 경우, `addIngredient()` 메서드의 역할을 넘어버리기 때문에 1번 방법은 제외한다. 그러면 2번과 3번 방법만을 생각할 수 밖에 없는데, 매개변수를 선언할 때 이 매개변수의 타입을 어떻게 해야하며, 배열 선언 시에도 타입을 어떻게 해야할까? 위 피자 종류들의 타입은 다 다르기 때문에 현재 코드로는 불가능하다. 코드로 확인해보자.  

```java
package pizza;

public class PizzaMain {

    public static void main(String[] args) {
        ...
        makeNewPizza(SweetPotatoPizza); // 타입이 다른 피자를 makeNewPizza에 넘겼다.
    }

    public static void makeNewPizza(PotatoPizza pizza) {
        System.out.println("새로운 피자를 만듭니다.");
        pizza.addIngredient();
        System.out.println("새로운 피자를 만들었습니다.");
    }
}
```

위와 같이 코드를 작성하면 컴파일 에러가 발생한다. 그 이유는 타입이 다르기 때문이다. 이는 다른 피자 타입도 마찬가지다.  

```java
'makeNewPizza(pizza.PotatoPizza)' in 'pizza.PizzaMain' cannot be applied to '(pizza.SweetPotatoPizza)'
```


**_이렇게 타입이 다른 것들을 하나의 타입으로 취급하여 특정 작업을 수행할 필요가 있을 때 '다형적 참조'를 사용하면 된다._**

위 클래스들을 자식 클래스로 만들도록 부모 클래스 `Pizza`를 만들어보자.  

```java
package pizza;

public class Pizza {

    public void addIngredient() {}
}
```

그러면 자식 클래스로 만들기 위해 예를 들어 `PotatoPizza`만 예시로 나타낸다. `extends` 예약어를 사용하여 `Pizza` 부모 클래스를 상속받고, `@Override`를 사용해서 부모 메서드를 오버라이딩한다. 다른 피자 타입들도 상속받는다.

```java
package pizza;

public class PotatoPizza extends Pizza {

    @Override
    public void addIngredient() {
        System.out.println("감자 재료를 추가합니다.");
    }
}
```

그러면 `makeNewPizza` 메서드의 파라미터 타입을 `Pizza`로 수정하여 다음과 같이 코드 반복을 줄여보자.  

- 수정된 코드
    ```java
    package pizza;

    public class PizzaMain {

        public static void main(String[] args) {
            ...
            makeNewPizza(potatoPizza);
            makeNewPizza(sweetPotatoPizza);
            makeNewPizza(baconCheddarCheesePizza);
            makeNewPizza(allMeatPizza);
        }

        public static void makeNewPizza(Pizza pizza) { // 매개변수 타입 수정  
            System.out.println("새로운 피자를 만듭니다.");
            pizza.addIngredient();
            System.out.println("새로운 피자를 만들었습니다.");
        }
    }
    ```

- 실행 결과  

    ```java
    새로운 피자를 만듭니다.
    감자 재료를 추가합니다.
    새로운 피자를 만들었습니다.
    ...
    새로운 피자를 만듭니다.
    고기 6종류 재료를 추가합니다.
    새로운 피자를 만들었습니다.
    ```

이렇게 해서 2번 방법을 적용했다. 아직 메서드가 인스턴스 갯수만큼 반복되는 걸 확인했다. 이를 줄이기 위해서 3번 방법인 배열을 적용해보자. 배열도 선언 시 타입을 입력해야하기 때문에 파이썬의 리스트와 달리 동일한 타입들로만 담을 수 있다. 

```java
Pizza[] pizzaList = {potatoPizza, sweetPotatoPizza, baconCheddarCheesePizza, allMeatPizza};

for (Pizza pizza : pizzaList) {
    makeNewPizza(pizza);
}
```

동일한 결과를 얻을 수 있고, 반복횟수가 줄어들고, 가독성이 더 향상된 걸 확인할 수 있다!


### 문제점

하지만 위 코드들로는 메서드 오버라이딩에 대해 강제성이 없기 때문에, 놓칠 경우 자식의 메서드가 아닌 부모의 메서드가 호출되어 실행되는 문제점이 존재한다. 

그래서 나온 게 '추상 클래스'다. 이에 대해 다음 단원에서 알아보자.  



&nbsp;

---

# 추상 클래스

이전 단원에서 언급한 문제를 해결하기 위해서 사용하는 방법이 '추상 클래스(abstract class)'다. **_추상적인 개념만 제공하기 때문에 실체인 인스턴스가 존재하지 않고, 오직 상속을 목적으로만 사용되기 때문에 부모 클래스 역할을 한다._** 그래서 반드시 오버라이딩해야 하는 메서드를 이 부모 클래스에 정의할 수 있다.  

**_추상 클래스를 만들 때는 반드시 수행해야하는 규칙 3가지_** 가 있다.

- 추상 클래스, 추상 메서드를 정의할 때 '추상'이 들어가는 뭐든지 `abstract` 키워드를 붙여야 한다.  
- 추상 메서드가 하나라도 있는 클래스는 추상 클래스로 선언해야 한다.  
    - 그렇지 않으면 컴파일 오류가 발생한다.  
    - 추상 메서드는 메서드 바디가 없어서 불완전한 메서드다.  
- 추상 메서드는 자식 클래스가 반드시 오버라이딩해서 사용해야 한다.


## 순수 추상 클래스

위 코드들에서는 추상 메서드가 하나인 경우만을 확인했다. 이번에는 모든 메서드가 추상 메서드인 '순수 추상 클래스' 경우를 확인해보자.  

&nbsp;

---


# 인터페이스  




&nbsp;

---