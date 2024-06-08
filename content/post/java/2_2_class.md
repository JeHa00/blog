---
title: "자바의 클래스에 대해 알아보자 - 2"
date: 2024-01-31T00:33:07+09:00
draft: false
summary: 생성자, 접근 제어자, 상수 변환 예약어에 대해 알아본다.  
tags: ["java"]
categories: "java"
---


# 생성자

객체를 생성하면 객체의 속성마다 특정 값으로 초기화를 한다. 

- 클래스
    ```java
    public class Food {
        public int cost;
        public int price;
        public String name;
    }
    ```

- 인스턴스 
    ```java
    public class FoodMain {
        public static void main(String[] args) {
            Food food1 = new Food();
            food1.name = "제육볶음";
            food1.price = 12000;
            food1.cost = 9000;
        }
    }
    ```

객체를 추가로 생성하여 초기화 작업을 수행해보자.  

- 인스턴스 
    ```java
    public class FoodMain {
        public static void main(String[] args) {
            Food food1 = new Food();
            food1.name = "제육볶음";
            food1.price = 12000;
            food1.cost = 9000;
        
            Food food2 = new Food();
            food2.name = "김밥";
            food2.price = 11000;
            food2.cost = 5000;
        }
    }
    ```

위 작업을 효율적으로 하기 위해서 메서드로 모듈화를 해보자.

```java
public class FoodMain {
    public static void main(String[] args) {
        Food food1 = new Food();
        initFood(food1, "제육볶음", 12000, 9000);

        Food food2 = new Food();
        initFood(food2, "김밥", 11000, 5000);
    }

    public static void initFood(Food food, String name, int price, int cost) {
        food.name = name;
        food.price = price;
        food.cost = cost;
    }
}
```

하지만 이 방식은 이전 챕터에서 언급한 것처럼 객체 지향적이지 않으므로 객체 클래스 안에 이 작업을 하는 메서드를 추가해보자. 

아래와 같이 코드를 작성했다. 그러면 어떤 게 속성이고, 어떤 게 메서드를 통해 전달하려는 값인지 구분할 수 있을까? 

```java
public class Food {
    public int cost;
    public int price;
    public String name;

    public void initFood(String name, int price, int cost) {
        name = name;
        price = price;
        cost = cost;
    }
}
```

위 코드의 경우, 멤버 변수와 매개변수 이름이 같다. 메서드 안에서는 지역 변수의 매개변수가 멤버 변수보다 우선순위를 가진다. 그래서 좌우 모두 매개변수로 인식되어 멤버 변수에 할당되지 못 한다.   

```java
public class FoodMain {
    public static void main(String[] args) {
        Food food1 = new Food();
        Food food2 = new Food();

        food1.initFood("제육볶음", 12000, 9000);
        food1.initFood("김밥", 11000, 5000);

        System.out.println(food1.name);
}}
```

출력 결과로 `null`이 나온다.  

```java
null
```

멤버변수에 할당되지 못한 걸 확인할 수 있다.  

#


### this 키워드가 있는 이유  

그래서 _멤버 변수와 매개변수를 구분하기 위해서 'this' 키워드를 사용해야 한다._

`this`는 **_인스턴스 자신의 참조값을 가리킨다._**  파이썬으로 보자면 `self`와 같다.  

```java
public class Food {
    public int cost;
    public int price;
    public String name;

    public void initFood(String name, int price, int cost) {
        this.name = name;
        this.price = price;
        this.cost = cost;
    }
}
```

하지만 다음과 같이 속성과 매개변수의 이름이 다르면 사용하지 않아도 된다.
자동적으로 구분해서 인식한다. 

```java
public class Food {
    public int cost;
    public int price;
    public String name;

    public void initFood(String name_parameter, int price_parameter, int cost_parameter) {
        name = name_parameter;
        price = price_parameter;
        cost = cost_parameter;
    }
}
```

#

### 생성자의 정의와 장점  

매번 이 작업을 위해서 별도의 메서드를 만들어야하는 문제점을 해결하기 위해서 객체 지향이 반영된 프로그래밍 언어에는 이 문제를 해결하기 위해 **_'생성자'_** 라는 게 존재한다. 

그러면 생성자는 무엇이라 정의를 내릴 수 있을까? 생성자로 얻을 수 있는 더 다양한 장점들이 있을까?

> **_생성자는 인스턴스 생성 직후 초기화를 포함한 여러 작업을 한 번에 할 수 있는 도구다. 이 생성자를 통해 3가지 장점을 얻을 수 있다._**
>  
> - 중복 호출 제거   
> - 생성자 호출 필수 - 개발자 실수로 초기화 작업을 놓칠 수 없음  
> - 2번으로 필수값 입력 보장     


특히 2번의 장점이 크다. 생성자 호출을 필수로 하기 때문에 예를 들어 회원 정보가 미입력되는 일이 없다. 

그러면 생성자에 대해 알았으니 바로 생성자 코드라 언급하겠다. 생성자 코드를 작성해보자.  

```java
public class Food {
    public int cost;
    public int price;
    public String name;

    // 생성자  
    public Food(String name, int price, int cost) {
        this.name = name;
        this.price = price;
        this.cost = cost;
    }
}
```

> **_위 작성한 코드를 보면 메서드와 비슷하지만 다음과 같은 차이가 있다._**    
>  
> - 생성자의 이름은 클래스 이름과 같아야 한다. 따라서 첫 글자도 대문자로 시작한다.       
> - 생성자는 반환 타입이 없다. 비워둬야 한다. void도 없어야 한다.        
> - 나머지는 메서드와 동일하다.         


#

### 생성자 호출 시점  

그러면 생성자는 언제 호출되는 걸까?

`new 클래스명()` 을 사용하여 인스턴스를 생성하자마자 바로 생성자가 실행된다. 이게 인스턴스 생성 시 클래스명에 호출자 연산을 사용하는 이유다. `new 생성자명()`으로 이해하면 된다.      

#

### 기본 생성자  

그러면 사용자 정의 생성자가 없으면 `new 클래스명()`을 사용할 수 없는 걸까?

앞선 예제들에서 사용할 수 있는 걸 확인했다. 

- 클래스 
    ```java
    public class Food {
        public int cost;
        public int price;
        public String name;
    }
    ```

- 인스턴스 생성 
    ```java
    public class FoodMain {
        public static void main(String[] args) {
            Food food1 = new Food();

            System.out.println(food1);
    }}
    ```

    - 출력

    ```java
    practice.Food@5a39699c
    ```


생성자 코드를 작성하지 않았는데 왜 인스턴스가 생성된 걸까?

그 이유는 _사용자가 생성자를 만들지 않으면 자바가 알아서 기본 생성자를 만들어 실행하기 때문이다._


```java
public class Food {

    public int cost;
    public int price;
    public String name;

    // java가 생성한 기본 생성자
    public Food() {
    }
}
```

**_자바가 생성하는 기본 생성자는 다음과 같은 특징이 있다._**

- 1. 매개변수가 없는 생성자를 기본 생성자라 한다.
- 2. 클래스에 생성자가 하나도 없으면 자바 컴파일러는 매개변수가 없고 메서드 바디가 없는, 작동하는 코드가 없는 생성자를 만든다.  
- 3. 하지만 생성자가 하나라도 있으면 기본 생성자를 만들지 않는다.  

그래서 사용자 정의 생성자가 없어도 인스턴스 생성이 가능한 이유가 위와 같다.  

파이썬으로 보자면 `__init__` 를 자동적으로 생성 및 호출하여 인스턴스를 생성하는 것과 같다.  

#

### 생성자 오버로딩

_자바에서는 메서드 오버로딩처럼 생성자 또한 오버로딩이 가능한데, `this`를 통해서 할 수 있다._ 

`this`를 단지 자기 자신 인스턴스의 참조값을 참조하는 키워드로만 학습했지만 `this()`로 호출 연산자를 사용하면 해당 매개변수 갯수와 타입에 맞는 생성자에게 전달된다.  

위 예시에서 든 생성자를 1번 생성자라 하자.  

- 1번 생성자
    ```java
    public class Food {
        public int cost;
        public int price;
        public String name;

        // 1번 생성자  
        public Food(String name, int price, int cost) {
            this.name = name;
            this.price = price; // 판매가격
            this.cost = cost; // 제조가격
        }
    }
    ```

2번 생성자를 추가한다.

- 2번 생성자: 무료 음식 생성자

    ```java
    // 2번 생성자  
    public Food(String name, int cost) {
        this(name, 0, cost); 
    }
    ```

2번 생성자를 사용하고 싶다면 `Food food2 = new Food("free", 5000);`로 입력한다. 그러면 `this()`에 넘긴 인자들의 갯수와 각 인자의 타입을 분석해서 이에 맞는 생성자로 전달하는 게 `this()` 의 역할이다.  

다음 코드의 출력 결과를 보면 `food2.price`의 값이 0인 걸 알 수 있다.  

```java
public class FoodMain {
    public static void main(String[] args) {
        Food food1 = new Food("제육볶음", 12000, 9000); // 1번 생성자
        Food food2 = new Food("free", 5000); // 2번 생성자

        System.out.println(food1.price); // 출력 결과: 12000
        System.out.println(food2.price); // 출력 결과: 0 
    }
}
```

다음으로 3번 생성자를 추가해보자.

- 3번 생성자: 휴게 시간에 혼자서 해먹는 음식에 대한 생성자  
    
    ```java
    public Food(String name) {
        this(name, 0); 
    }
    ```

3번 생성자를 사용하고 싶으면 `Food food3 = new Food("볶음밥")`로 입력한다.

```java
public class FoodMain {
    public static void main(String[] args) {
        Food food1 = new Food("제육볶음", 12000, 9000); // 1번 생성자
        Food food2 = new Food("free", 5000); // 2번 생성자
        Food food3 = new Food("볶음밥"); // 3번 생성자  

        System.out.println(food1.price); // 출력 결과: 12000
        System.out.println(food2.price); // 출력 결과: 0 
        System.out.println(food3.price); // 출력 결과: 0 
        System.out.println(food3.cost); // 출력 결과: 0 
    }
}
```

출력결과 볶음밥을 생성할 때는 cost, price 모두 입력하지 않았지만 값이 담겨져 있는 걸 알 수 있다.  

3번 생성자의 `this()`는 2번 생성자에 전달되고, 2번 생성자의 `this()`는 1번 생성자에 전달되어 최종적으로 인스턴스가 생성된다.  `int`의 초기화값이 0이라서 헷갈릴 수 있으니 `10`으로 수정해서 다시 출력해보자.  

```
12000
10
10
10
```

원하는 대로 `this()`가 잘 작동되는 걸 알 수 있다.  

#

### this() 유의사항

`this()`를 사용하는데 있어서 유의사항이 있다. 

> _생성자 코드의 첫 줄에만 작성할 수 있다._  

위 유의사항으로 다음 코드는 실행되지 않고 컴파일 에러가 발생된다.  

```java
public Food(String name) {
    System.out.println("This will be not operated");
    this(name, 10);
}
```

발생된 컴파일 에러 내용이다.

```
Call to 'this()' must be first statement in constructor body
```

`this()`는 반드시 생성자의 첫 줄에만 작성해야 한다. 다른 코드를 먼저 실행했다가 에러가 발생하면 인스턴스를 생성할 수 없기 때문에 이런 조치를 취한 것으로 이해된다. 좋은 코드는 제약이 없는 코드가 아닌 제약이 어느 정도 있는 코드라고 생각되기 때문에, 이와 같은 제약은 개인적으로 좋다고 생각된다.  


&nbsp;

---

# 접근 제어자

## 접근 제어자란?

**_해당 클래스 외부에서 특정 필드나 메서드에 접근하는 것을 허용하거나 제한하는 예약어_** 를 말한다. 

이 예약어가 필요한 첫 번째 이유는 '보안'이다. 필드에 직접 접근하여 값을 수정하는 걸 막기 위해서다. 

예를 들어 빵을 하루 최대 공장에서 100개만 생성하도록 했다. 그래야 기계가 망가지지 않는다. 그런데 다른 직원이 이를 모르고 최대 150개로 생성하도록 설정했을 경우, 공장 기계는 망가진다.  

이런 비지니스 로직이 아래 코드와 같이 메서드에 담겨져 있어서, 직접 수정이 아닌 메서드를 통해서만 주문이 가능하다. 

```java
public class BreadFactory {

    public int count;

    public void order(int orderCount){
        if (orderCount < count) {
            System.out.println("빵을 주문합니다.");
            count += orderCount;
        } else {
            System.out.println("빵을 주문할 수 없습니다.");
        }
    }
}
```

하지만 아래 코드와 같이 어느 직원이 임의의로 직접 수정하면 주문은 들어갔으니 기계는 돌아가고 아무도 모른다. 

```java
public class BreadFactoryMain {
    public static void main(String[] args) {
        BreadFactory breadFactory = new BreadFactory();

        breadFactory.order(50); // 이 방식으로는 가능

        breadFactory.count = 150; // 이렇게 직접 수정하면 문제 발생
    }
}
```

그러면 `count` 필드를 접근 제어자 `private`을 사용하여 직접 접근을 막아보자. 

```java
package practice.a;

public class BreadFactory {

    private int count; // public int 에서 private int로 수정

    ...
}
```

다시 main 코드를 실행하보면 다음 컴파일 에러가 발생된다.  

```java
java: count has private access in practice.BreadFactory
```

그래서 직접 특정 필드나 메서드에 접근하는 걸 제한하고자 필요한 게 '접근 제어자'다. 이보다 더 중요한 이유인 **_캡슐화_** 가 있는데, 이는 접근 제어자의 마지막 소챕터에서 나눠보도록 한다.  

## 접근 제어자의 종류

접근 제어자의 종류에는 4종류가 있다.

- `private`: 모든 외부 호출을 막는다. 
- `default`: 같은 패키지 안에서 호출은 허용한다. (아무것도 적지 않는 게 default)
- `protected`: 같은 패키지 안에서 호출과 다른 패키지여도 상속 관계의 호출을 허용한다. 
- `public`: 모든 외부 호출을 허용한다.  

차단 정도는 `private` > `default` > `protected` > `public` 순서로 오면서 줄어든다. `private`이 가장 많이 차단하고, `public`이 가장 많이 허용한다. `proteced`를 설명하기 위해서는 '상속' 개념을 알아야하기 때문에 상속에 대해 학습할 때 같이 설명한다.  


## 접근 제어자가 사용되는 곳: 멤버 변수(Field), 메서드

접근 제어자는 멤버 변수 그리고 메서드에 사용된다. 지역 변수에는 사용되지 못한다.  

그럼 아래 코드로 알아보자!

접근 제어자를 학습할 때는 아래 코드로 학습하는 게 개인적으로 제일 낫다고 생각된다.  

- 각기 다른 접근 제어자가 입력된 멤버변수와 메서드가 있다. 
- 그리고 모든 메서드들을 호출하는 하나의 메서드가 존재한다.  

```java
package practice.a;

public class AccessData {

    public int publicField;
    int defaultField;
    private int privateField;

    public void publicMethod() {
        System.out.println("==== publicMethod 호출: " + publicField);
    }

    void defaultMethod() {
        System.out.println("==== defaultMethod 호출: " + defaultField);
    }

    private void privateMethod() {
        System.out.println("==== privateMethod 호출: " + privateField);
    }

    public void innerAccess() {
        System.out.println("내부 호출");
        publicField = 100;
        defaultField = 200;
        privateField = 300;
        publicMethod();
        defaultMethod();
        privateMethod();
    }
}
```

### 같은 패키지에서 호출 

그러면 외부에서 위 코드를 호출해보자. 패키지 위치는 같은 패키지로 'practice.a'다.

```java
package practice.a;

public class AccessMain {
    public static void main(String[] args) {
        AccessData data = new AccessData();

        data.publicField = 1;
        data.publicMethod();

        data.defaultField = 2;
        data.defaultMethod();

        // private은 호출 불가능
        data.privateField = 3;
        data.privateMethod();

        data.innerAccess();
    }
}
```

위 코드의 다음 2가지 부분에서 에러가 발생한다. 

- `data.privateField`
- `data.privateMethod()`

발생된 에러는 다음과 같다.

```java
java: privateField has private access in practice.AccessData
```

그러면 에러가 난 부분을 주석처리하고 다시 컴파일한 후 결과는 다음과 같다.

```java
==== publicMethod 호출: 1
==== defaultMethod 호출: 2
==== 내부 호출
==== publicMethod 호출: 100
==== defaultMethod 호출: 200
==== privateMethod 호출: 300
```

위 결과를 통해서 다음 내용들을 확인할 수 있다.  

- `public`은 어디서든지 접근할 수 있어서 `public` member 변수와 method가 모두 접근 가능했다.  

- `default`는 같은 패키지에서만 접근 가능하다. 위 두 코드는 같은 패키지이기 때문에 접근 가능하다.

- 그래서 `public`과 `default`로 설정한 메서드와 변수 모두 직접 접근하여 수정이 가능했고, 메서드를 통해서 수정이 가능했다.  

- `private`의 경우 클래스 내부에서만 접근이 가능하기 때문에, 클래스 내부 메서드에서 `private` 멤버 변수와 메서드에 접근할 수 있다. 그래서 외부에서 직접 접근할 수 없다는 걸 에러로 확인했고, `innerMethod()`를 통해서 호출된 `privateMethod()`를 확인할 수 있다.  


### 다른 패키지에서 호출

패키지 위치는 다른 패키지로 'practice.b'다.

```java
package practice.b;

import practice.a.AccessData;

public class AccessMain {
    public static void main(String[] args) {
        AccessData data = new AccessData();

        data.publicField = 1;
        data.publicMethod();

        // default 호출 불가능  
        // data.defaultField = 2;
        // data.defaultMethod();

        // private은 호출 불가능
        //  data.privateField = 3;
        //  data.privateMethod();

        data.innerAccess();
    }
}
```

`private`은 이전과 동일한 에러가 발생된다. 다른 패키지에서 호출하니 다음과 같은 다른 에러가 발생했다.

```java
java: defaultField is not public in practice.a.AccessData; cannot be accessed from outside package
```

`default` 키워드에서 발생된 에러다. 동일한 패키지가 아니어서 접근할 수 없다는 내용이다. 위 에러를 바탕으로 `default`는 동일한 패키지 내에서만 접근할 수 있는 걸 확인했다.  

#

## 클래스에도 사용되는 접근 제어자  

접근 제어자는 멤버 변수, 메서드 그리고 클래스에도 사용된다.  

아래 코드를 실행하면 같은 패키지이기 때문에 에러가 발생되지 않는다.  

```java
package practice.a;

public class PublicClass {
    public static void main(String[] args) {
        PublicClass publicClass = new PublicClass();
        DefaultClass1 class1 = new DefaultClass1();
        DefaultClass2 class2 = new DefaultClass2();
    }
}

// 접근 제어자: default
class DefaultClass1 {
}

// 접근 제어자: default
class DefaultClass2 {
}
```

하지만 위 코드 내용을 `package practice.b`로 위치시켜서 호출하면 다음 에러가 발생한다.

```java
java: practice.a.DefaultClass1 is not public in practice.a; cannot be accessed from outside package
```

동일한 패키지가 아니어서 접근할 수 없다는 내용이다. 이처럼 클래스에도 적용된다.  


## 그래서 이게 왜 필요한 건데?

객체 지향 프로그래밍을 적용하면서 객체 중심으로 데이터와 해당 데이터를 처리하는 메서드를 하나로 묶은다. 

여기서 한 가지 더 나아가는 게 '캡슐화'다. 보안과 편리한 인터페이스 제공하기 위해 데이터는 모두 숨기고, 외부에 노출시킬 필요가 있는 기능(메서드)만을 드러낸다. 이를 위해 필요한 게 바로 **_'접근 제어자'_** 다.  

**_그래서 캡슐화는 다음과 같이 3가지로 정리할 수 있다._**

- 객체의 속성(데이터)은 가장 필수로 숨긴다.  
- 객체의 내부에서만 사용하는 기능도 숨긴다.  
- 외부에 노출시킬 필요가 기능(메서드)만 드러낸다.  

**_반드시 객체의 데이터는 객체가 제공하는 기능인 메서드를 통해서만 접근해야 한다._**

캡슐화가 잘된 코드를 하나 확인해보자.

- `Food` 클래스

    ```java
    package practice;

    public class Food {
        private String name;
        private int price;
        private int quantity;

        public Food(String name, int price, int quantity) {
            this.name = name;
            this.price = price;
            this.quantity = quantity;
        }

        public String getName() {
            return name;
        }

        public int getTotalPrice() {
            return calculateTotalPrice();
        }

        private int calculateTotalPrice() {
            return price * quantity;
        }
    }
    ```


- `FoodCart` 클래스

    ```java
    package practice;

    public class FoodCart {
        private Food[] foods = new Food[5];
        private int foodCount;

        public void addFood(Food food) {
            if (foodCount >= foods.length) {
                System.out.println("음식을 더 담을 수 없습니다.");
                return;
            }

            foods[foodCount] = food;
            foodCount++;
        }

        public void displayItems() {
            System.out.println("장바구니 음식 출력");
            for (int i = 0; i < foodCount; i++) {
                Food food = foods[i];
                System.out.println("음식명: " + food.getName() + ", 합계: " + food.getTotalPrice());
            }

            System.out.println("전체 가격 합: " + calculateTotalOrderPrice());
        }

        public int calculateTotalOrderPrice() {
            int totalPrice = 0;
            for (int i = 0; i < foodCount;  i++) {
                Food food = foods[i];
                totalPrice += food.getTotalPrice();
            }
            return totalPrice;
        }

    }
    ```

## 캡슐화의 최종 장점

> **_클라이언트에게 좋다._**

만든 수 백개의 메서드를 그대로 노출시키면 클라이언트의 사용성이 좋지 않다. 그래서 좋은 캡슐화 설계는 클라이언트의 사용 경험을 높인다.  


&nbsp;

---

# final 키워드

이 `final` 키워드는 '초기화 후 값 변경 불가능'을 의미한다. 그리고 이 키워드는 `variable`에 붙일 수 있다.

`final` 키워드는 멤버 변수에 사용할 수 있다.  

결론부터 말하자면 멤버 변수가 초기화가 안된 경우라면 초기화된 후 값 변경이 불가능하다. 초기화가 이미 된 경우라면 상수로 취급되어 생성자를 통해서 초기화가 될 수 없다. 

### 초기화가 안된 경우

음식점을 개업하면서 '김밥' 메뉴의 가격은 물가가 올라도 안올리겠다는 다짐으로 가격을 나타내는 멤버 변수에 `final`을 추가했다. 메뉴 이름은 초기화되면 변경할 필요가 없다.  

- 클래스
    ```java
    public class Food {
        int cost;
        final int price;
        final String name;

        public Food(String name, int price, int cost) {
            this.name = name;
            this.price = price;
            this.cost = cost;
        }
    }
    ```

- 인스턴스 생성
    ```java
    public class FoodMain {
        public static void main(String[] args) {
            Food food1 = new Food("김밥", 8000, 4000);

            System.out.println("food1.name: " + food1.name);
            System.out.println("food1.price: " + food1.price);
            }
    }
    ```

    - 출력 결과

    ```java
    food1.name: 김밥
    food1.price: 8000
    ```


하지만 인플레이션과 금리 인상으로 판매가를 높일려고 했으나 수정할 수가 없다. 

```java
food1.price = 10000;
```

다음과 같은 에러가 발생된다.

```java
java: cannot assign a value to final variable price
```

**_`final` 키워드가 붙은 변수는 초기화되면 수정할 수가 없다._**  

### 초기화가 된 경우

이번에는 이미 변수가 초기화 후 수정하려는 경우를 확인해보자.  

장사가 안되서 폐업을 결정했다. 남은 재료들을 빨리 소진하기 위해 모든 메뉴 5000원을 결정하여 다음과 같이 코드를 작성했다.

- 클래스
    ```java
    public class Food {
        int cost;
        final int price = 5000;
        final String name;

        public Food(String name, int cost) {
            this.name = name;
            this.cost = cost;
        }
    }
    ```

- 인스턴스
    
    ```java
    public class FoodMain {
        public static void main(String[] args) {
            Food food1 = new Food("김밥",4000);

            System.out.println("food1.price: " + food1.price);

        }
    }
    ```

    - 출력 결과
    
    ```java
    food1.price: 5000
    ```

그러다가 갑자기 폐업하지 말라는 시민들의 요청이 있어서 메뉴 가격을 다시 수정하려고 한다.

```java
food1.price = 10000;
```

위 코드를 실행하면 다음과 같은 결과를 또 확인할 수 있다. 

```java
java: cannot assign a value to final variable price
```

**_`final` 예약어로 선언된 변수가 한 번 초기화되면 수정될 수 없다._**


### static 사용 시 final 고려하기

만약 인스턴스 변수이고, 인스턴스마다 동일한 값으로 초기화가 되면 이는 `static` 변수로 만드는 낫다. 모든 인스턴스가 같은 값을 사용하기 때문에 메모리 낭비다. 또한 같은 값이 계속 생성되는 건 명확한 중복이기 때문이다.

아래 코드를 보면 한 묶음에 붕어빵 2000원 어치에 3개 정보라는 인스턴스를 생성했다. 붕어빵 2000원 어치에 3개는 고정된 정보다. 무조건 한 묶음만 판다고할 때 굳이 인스턴스를 생성할 때마다 정보를 입력받는 건 중복된 값을 사용하기에 메모리 낭비다. 이런 경우 `static` 과 `final` 을 사용하는게 현명하다.

- 클래스
    ```java
    public class FishShapedBun {

        public int price;
        public int count;

        public FishShapedBun(int price, int count) {
            this.price = price;
            this.count = count;
        }
    }
    ```

- 인스턴스

    ```java
    public class FishShapedBunMain {
        public static void main(String[] args) {
            FishShapedBun bundle1 = new FishShapedBun(2000, 3); // price, count
            FishShapedBun bundle2 = new FishShapedBun(2000, 3);
            FishShapedBun bundle3 = new FishShapedBun(2000, 3);
        }
    }
    ```

그래서 다음과 같이 클래스 코드를 수정해보자.

```java
    public class FishShapedBun {

    public static final int price = 2000;
    public static final int count = 3;

    }
```

아래의 값을 확인하면 2000, 300을 확인할 수 있고 변경할 수 없다.  

```java
package practice;

public class FishShapedBunMain {
    public static void main(String[] args) {
        FishShapedBun bundle1 = new FishShapedBun();
        System.out.println(bundle1.count);
        System.out.println(bundle1.price);

        bundle1.price = 2; // 에러 발생
    }
}
```

발생한 에러 내용은 다음과 같다.
    
```java
java: cannot assign a value to final variable count
```

### 상수의 이점

위에서 알아본 대로 `final` 키워드를 사용해서 변하지 않는 값을 만들 수 있다. 이 수를 **_'상수'_** 라고 한다. 애플리케이션 안에는 다양한 상수가 존재할 수 있다. 수학, 시간 등등 실생활에서 사용하는 상수부터, 애플리케이션의 다양한 설정을 위한 상수들도 있다. 아래 코드를 통해 여러 예시들을 확인해보자.  

```java
public class Constant {
    
    // 애플리케이션 설정 상수
    public static final int MAX_COUNTS = 500;

    // 수학 상수
    public static final double PI = 3.14;

    // 시간 상수
    public static final int DAYS_IN_WEEK = 7;
    public static final int HOURS_IN_DAY = 24; 
    public static final int MINUTES_IN_HOUR = 60;
    public static final int SECONDS_IN_MINUTE = 60;
}
```

이런 상수들은 애플리케이션 전반에서 사용되기 때문에 `public`을 자주 사용한다.  반드시 이래야하는 건 아니고, 특정 위치에서만 접근된다면 이에 맞는 접근 제어자를 사용하면 된다.  

그러면 애플리케이션 설정 상수를 사용하는 장점에 대해 알아보자. 

```java
public static void make(int currentCount) {
    if (currentCount < 500) {
        System.out.println("붕어빵을 만듭니다.");
    } else {
        System.out.println("붕어빵이 소진되었습니다.");
    }
}
```

위와 같이 코드를 작성하면 500이란 숫자가 뭘 의미하는지 모른다. 이럴 때 상수를 사용하면 무슨 의미인지 바로 알 수 있다. 

상수를 사용하여 또 다른 상수를 표현할 수도 있다.

```java
public class Constant {
    
    ...
    public static final int MINUTES_IN_HOUR = 60;
    public static final int SECONDS_IN_MINUTE = 60;
    public static final int SECONDS_IN_HOUR = SECONDS_IN_MINUTE * MINUTES_IN_HOUR;
}
```



## 기본형과 참조형의 관점에서

기본형의 경우, 기본형의 값을 변경할 수 없다는 건 위 예시들을 통해서 알았다.  

이번에는 참조값을 저장하는 참조형에 대해 `final`을 사용해보자.  

#

### 참조형은 참조값을 변경할 수 없다

참조형은 변수 선언 시점에 참조값을 할당했으므로 더는 참조값을 변경할 수 없다. 다른 객체를 참조할 수 없다. 하지만 참조 대상의 객체 값은 변경할 수 있다. 

- 클래스

    ```java
    public class FishShapedBun {
        public int price = 2000;
        public int count = 3;
    }
    ```

- 인스턴스

    ```java
    public class FishShapedBunMain {
        public static void main(String[] args) {
            final FishShapedBun bundle1 = new FishShapedBun();
            bundle1 = new FishShapedBun();
        }
    }
    ```

- 실행 결과

    ```java
    java: cannot assign a value to final variable bundle1
    ```

하지만 `bundle1.count = 5` 로 수정 가능하다.  

```java
public class FishShapedBunMain {
    public static void main(String[] args) {
        final FishShapedBun bun1 = new FishShapedBun();
        // bun1 = new FishShapedBun();

        System.out.println(bun1.count);
        bun1.count = 5;
        System.out.println(bun1.count);
    }
}
```

실행 결과는 다음과 같다.

```java
3
5
```



**_참조값 주소를 담은 변수에 final 선언을 하면 참조값 주소는 변경할 수 없다. 그리고 이 final 선언이 이 주소가 가리킨 객체의 속성까지 영향을 주지 않는다._** 

&nbsp;

---

# 이번 포스팅 주제에 관한 파이썬과의 차이점

단지 호기심의 관점으로 자바와 파이썬을 비교해본다

#### 생성자

| 언어 | 파이썬 | 자바 |
| ---- | ---- | ---- |
| 인스턴스 참조값 키워드 | self | this | 
| 생성자 이름 | \_\_init\_\_ | class 이름 | 
| 기본 생성자 유무 | O | O | 
| 생성자 오버로딩 유무 | X | O | 

파이썬에는 기본적으로 오버로딩이 내장되어 있지 않다. 하지만 packing, unpacking 개념을 사용해서 이 부분을 구현할 수 있다. 

### 상수

파이썬은 별도의 상수 선언을 위한 키워드는 존재하지 않고, 상수 취급하는 변수의 네임 컨벤션만 존재한다.  

&nbsp;