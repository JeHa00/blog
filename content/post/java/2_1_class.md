---
title: "자바의 클래스에 대해 알아보자 - 1"
date: 2024-01-23T00:32:07+09:00
draft: false
summary: 클래스가 필요한 이유, 클래스 멤버 변수 그리고 메서드, 절차 지향과 객체 지향의 차이점에 대해 알아본다.  
tags: ["java"]
categories: "java"
---


# 클래스가 필요한 이유

파이썬은 리스트가 컨테이너형으로 여러 데이터 타입을 담을 수 있다. 하지만 여태 학습한 자바의 데이터 타입 종류로는 파이썬의 리스트처럼 하는 건 불가능하다. 아래의 예시처럼 배열도 한 가지 타입으로 여러 개를 담을 수 있다. 

```java
int [] scores = {70, 80, 90, 100}
String [] names = {"jeha", "seo", "kim", "hoho"}
```

한 종류의 데이터 타입으로 모을 수 있지만, 한 학생의 데이터가 두 개의 배열에 나누어져 있기 때문에 데이터를 관리하기가 어렵다. 만약 배열에서 값을 얻고자 했다면 각 배열마다 인덱싱으로 얻어내야하는 번거로움이 있다. 학생의 수가 적을 때는 문제가 안되지만 많아지면 문제가 된다. 한 학생의 데이터를 수정해야 한다면 각 배열마다 정확하게 그 학생 정보가 있는 인덱스로 접근하여 수정해야 한다. 이런 방식은 실수할 가능성이 매우 높다.  

**_이럴 때 사용해야하는 게 바로 클래스다. 한 객체와 관련된 데이터를 타입이 달라도 한 곳에 모아놓는 방식이다._**

한 학생과 관련된 데이터를 한 곳에 모아놓는 방식이다. 아래 코드와 같이 클래스 안에 관련된 데이터를 모아놓기 때문에 관리하기 좋다.   

```java
public class Student {

    private int score;
    private String name;
}
```



&nbsp;

---

# 클래스, 객체, 인스턴스 용어 정리 

클래스, 객체, 인스턴스의 의미는 파이썬과 동일하다.  

### 클래스

클래스는 `class` 예약어로 선언된 코드를 의미하는데, 코드 문자적인 의미 말고 실제 설계할 의미로 보자면 인스턴스(또는 객체)를 생성하기 위한 '설계도' 또는 '틀'이다. 그래서 **_객체 또는 인스턴스가 가져야할 속성(변수)과 기능(메서드)을 정의한다._** 

흔히들 클래스를 붕어빵 틀에 비유한다. 붕어빵 틀은 먹을 수 있는 붕어빵과는 다르다. 하지만 붕어빵이라 부를 수 있는 특징들을 잡아준다.  

설계도에 비유한다면 집의 설계도를 생각해보자. 설계도는 실제 집이 아니다. 이 설계도를 토대로 지어진 집이 실제다.  

#

### 객체

**_객체는 클래스에서 정의한 속성과 기능을 가진 구현체다._**  붕어빵 틀에 의해 생성된 붕어빵, 설계도에 의해 건설된 집이다.  

#

### 인스턴스

그렇다면 이 인스턴스는 무엇인가? 무엇을 중점으로 두고 말하냐에 따라 객체냐 인스턴스냐가 결정된다.

**_객체와 동일한 구현체지만 설명할 때 '클래스와의 관계'에 초점을 둔다면 인스턴스, '생성된 구현체'에 초점을 둔다면 객체로 언급한다._**  

관점을 어디에 중점으로 두냐의 차이이므로 객체와 인스턴스를 같은 의미로 생각하자.    

그러면 코드로 위 개념들을 확인해보자.  

- 클래스
    - 자바는 모든 게 클래스로 작성되지만 아래 클래스는 인스턴스 객체를 생성하기 위한 클래스다.

    ```java
    public class Car {

        public String brand;
        public String name;
        public int price;

        // 생성자라 합니다. 나중에 학습합니다.
        public Car(String brand, String name, int price) {
            this.brand = brand;
            this.name = name;
            this.price = price;
        }

        // 메서드라 합니다
        public String getBrand() {
            return brand;
        }

        public String getName() {
            return name;
        }

        public int getPrice() {
            return price;
        }
    }
    ```

- 인스턴스 및 객체
    - 위에서 선언한 클래스를 가지고 `new` 예약어를 사용하여 인스턴스를 생성한다.  
    - 아래 코드에서 생성된 `car1` 과 `car2`가 인스턴스이자 객체다. 비유하자면 붕어빵이고 건물이다.  
    - **_메모리의 관점에서 보자면 실제 메모리에 만들어진 실체가 인스턴스 또는 객체다. new라는 예약어가 메모리에 새로 생성하라는 의미다._**
    - 변수 챕터에서 확인했듯이 인스턴스가 생성되면서 `car1`과 `car2`에는 메모리에 생성된 참조값 주소가 담겨진다. ([기본형과 참조형의 변수 대입](http://localhost:1313/post/java/1_variable/#%EA%B8%B0%EB%B3%B8%ED%98%95%EA%B3%BC-%EC%B0%B8%EC%A1%B0%ED%98%95%EC%9D%98-%EB%B3%80%EC%88%98-%EB%8C%80%EC%9E%85-))

    ```java
    public class CarMain {
        public static void main(String[] args) {
            Car car1 = new Car("kia", "k3", 10000);
            Car car2 = new Car("kia", "k5", 20000);
        }
    }
    ```


&nbsp;

---



# 멤버 변수

이번에는 '멤버 변수'에 대해서 알아본다. 

**_멤버 변수는 클래스 안에 선언되고, 메서드 밖에 선언된 변수를 말한다. 멤버 변수를 다른 말로 '필드(Field)' 라고도 한다._** 

위 `Car` 클래스의 `brand`, `name`, `price`가 멤버 변수에 속한다.  

변수 챕터에서도 언급했지만 변수는 멤버 변수와 지역 변수로 나눠지고 이에 따라 자동 초기화 유무가 결정된다. 어떻게 결정되는지는 [자바에서의 변수는 어떨까? - 항상 초기화를 직접 해줘야 하나?](https://jeha00.github.io/post/java/variable/#%ED%95%AD%EC%83%81-%EC%B4%88%EA%B8%B0%ED%99%94%EB%A5%BC-%EC%A7%81%EC%A0%91-%ED%95%B4%EC%A4%98%EC%95%BC-%ED%95%98%EB%82%98)을 확인하자.  

**_메모리의 관점에서 보자면 인스턴스가 생성될 때 이 멤버 변수를 사용하는데 필요한 메모리 공간도 함께 확보한다._**

이 멤버 변수는 인스턴스 변수와 정적 변수(클래스 변수)로 나눠지는데 어떻게 해서 구분할 수 있는지 알아보자.  

## 멤버 변수의 종류


### 인스턴스 변수

위 `Car` 클래스에서 선언된 멤버변수 `brand`, `name`, `price` 들은 인스턴스 변수이다.

#

### 클래스 변수, 정적(static) 변수

`static` 이라는 키워드가 있어야 정적 변수 또는 클래스 변수로 인식된다. 

**_정적 변수가 필요한 이유는 인스턴스마다 다른 값을 가지고 있는 독립적인 인스턴스 변수보다, 모든 인스턴스에서 동일한 값을 공유하기 위해 클래스 변수(정적 변수)를 사용한다. 그리고 메모리 사용 관점에서도 효율적이기 때문이다._**

그러면 이를 코드로 확인해보자. 생성된 차가 총 몇 대인지를 알고 싶다. 

- 클래스
    ```java
    public class Car {

            public static int count; // 정적 변수

            public String brand; // 인스턴스 변수
            public String name; // 인스턴스 변수
            public int price; // 인스턴스 변수  
            
            public Car() {
                count++;
            }
    }
    ```

- 실행 코드  
    ```java
    public class CarMain {
        public static void main(String[] args) {
            Car car1 = new Car();
            Car car2 = new Car();
            Car car3 = new Car();

            System.out.println(car1.count);       
            System.out.println(Car.count);       
        }
    }
    ```

    - 출력 결과
    ```java
    3
    3
    ```

만약 이를 인스턴스 변수로 총 생성 카운트를 세고 싶다면 생성 갯수를 담당하는 별도의 외부 인스턴스를 생성해야 한다.    

- 생성 갯수를 담당하는 클래스

    ```java
    public class Counter {

            public int count; // 인스턴스 변수
    }
    ```

- Car 클레스

    ```java
    public class Car {

        public String name; // 인스턴스 변수
        public int price; // 인스턴스 변수  

        public Car(String name, int price, Counter counter) {
            this.name = name;
            this.price = price;
            counter.count++;
        }
    }
    ```

- 실행 코드

    ```java
    public class CarMain {
        public static void main(String[] args) {
            Counter counter = new Counter();
            Car car1 = new Car("k3", 10000, counter);
            Car car2 = new Car("k3", 20000, counter);

            System.out.println(counter.count);

        }
    }
    ```

    - 결과

    ```java
    2
    ```

코드가 훨씬 복잡해지는 걸 알 수 있다. 그래서 이런 문제를 해결하기 위해서는 정적 변수를 쓰는게 효율적이다.


## 변수마다 접근하는 방식  

그러면 멤버 변수를 어떻게 읽을 수 있을까? 


### 인스턴스 변수

위 예시 코드에서 이미 확인했겠지만 인스턴스 변수의 경우 인스턴스 고유의 값이기 때문에 `.`(dot) 키워드를 사용하여 인스턴스 변수가 가지고 있는 참조값을 통해 접근하여 인스턴스 변수 값을 읽을 수 있다.  

```java
car1.name
car1.price
```

#

### 클래스 변수, 정적(static) 변수


클래스 변수, 정적 변수, static 변수도 인스턴스를 통해서 접근이 가능하다. 하지만 이런 접근은 인스턴스 변수라는 착각을 일으키기 때문에, **_클래스명을 통해서 static 변수에 접근하는 걸 권장한다._**  

```java
Car.count
```

클래스 변수의 메모리 사용과 주소 관점의 설명은 이 부분을 참고하라.  

&nbsp;

---

# 메서드

## 메서드 기본 학습

멤버 변수에 대해 알아봤으니 다음으로 클래스의 메서드를 알아보자.  

메서드(Method)는 클래스 안에 정의된 함수다. 그래서 함수와 메서드란 개념이 프로그래밍 언어에는 보통 존재한다. 하지만, 자바의 경우 모든 게 클래스이기 때문에 함수라는 개념은 없고 '메서드'란 개념만 존재한다.  

### 메서드 구성, 호출, 용어 정리

메서드는 크게 '메서드 선언부(Method Declaration)'과 '메서드 본문(Method Body)'으로 나눌 수 있다.

주문한 음식의 가격과 수량 정보를 받아 전체 비용을 반환하는 메서드를 만들었다.

```java
public static int getFoodTotalPrice(int count, int price) {
    return count * price;
}
```

그러면 어디가 선언부이고 본문인지 분석해보자.

- `public static int getFoodTotalPrice(int count, int price)` 가 메서드 선언부
    - `public`: 접근 제어자로 나중에 학습한다.  
    - `static`: 이전 챕터에서 학습한 것처럼 객체를 생성하지 않고 사용할 수 있는 정적(클래스) 메서드라는 의미다.  
    - `int`: 반환값을 의미한다.  
    - `getFoodTotalPrice`: 해당 메서드의 이름으로, 이 이름과 호출 연산자를 사용하여 호출한다. 
    - `(int count, int price)`: 메서드를 호출할 때 전달하는 입력 값 매개변수들을 정의한다.  

- 나머지 부분은 메서드 본문이다.  
    - 메서드가 수행해야하는 코드 블록
    - 코드 블록이 실행되면 순서대로 실행된다.  
    - **_메서드를 호출하는 곳에서는 메서드 선언부만 알고 본문은 모른다._**
    - 메서드에서 생성된 값을 메서드 외부로 주기 위해서는 `return`을 사용해야 한다.  


메서드를 호출하기 위해서는 메서드 호출 시 인자의 갯수와 각 타입이 메서드 선언부의 매개변수의 갯수와 각 타입이 모두 일치해야 한다.  
- 정확하게 갯수와 타입이 일치한 경우:  `getFoodTotalPrice(0, 0)` -> 호출 O
- 타입이 일치하지 않은 경우: `getFoodTotalPrice("Hello", "Java")` -> 호출 x
- 갯수가 일치하지 않은 경우: `getFoodTotalPrice(0)` -> 호출 x  


인자와 매개변수가 뭔지 모른다면 아래 정리를 읽어보자. 

- 인수(Argument): 인자라고도 하며 메서드 호출 시 괄호 안에 넘기는 값을 의미한다.  
- 매개변수(parameter): '매개'와 '변수'의 합성어로 메서드 호출부와 메서드 본문 사이에서 값을 전달하는 역할을 하는 변수라는 의미다.  

메서드를 포함하여 함수를 호출하는 부분인지, 함수를 선언하는 부분인지에 따라 결정된다.  

#

### 메서드의 매개변수와 반환값 

위 예시 메서드처럼 반드시 매개변수가 있어야하는 것이 아니고, 반환값이 있어야 하는 게 아니다. 

매개변수가 없으면 `getFoodTotalPrice()` 처럼 빈 괄호로 선언하면 된다.  

반환값이 없으면 메서드 선언부에 `public static int`에서 `int`를 `void`로 바꿔야 한다. `void`는 반환값이 없다는 의미다. 이런 경우 `return`을 사용하지 않아도 된다.  
- `public static void` 

입력값과 반환값이 없는 메서드를 작성해보자. 

```java 
             // 바로 밑에 void를 작성한 걸 확인하자  
public static void introduceMe() {
    System.out.println("Hello my name is jeha");
}
```

출력은 하지만 반환값은 없다. 

만약 `int`를 반환한다면? 

```java
public static int returnInteger() {
    return 1;
}
```

만약 `boolean`을 반환한다면?

```java
public static boolean returnBoolean() {
    return true;
}
```

만약 `String`을 반환한다면?

```java
public static String returnString() {
    return "Hello";
}
```

반환값이 있으면 반드시 `return`을 써서 메서드 선언부에 일치하는 값을 반환해야 한다. 


#

### 메서드의 return 문  

메서드는 `return`문을 만나면 그 즉시 해당 메서드를 빠져나간다. 그래서 조건문에 따라 `return`문을 작성한다면 모든 조건문에 작성해야하므로 매 마지막에 작성하는 것도 좋은 방법이다.

아래와 같이 작성하면 age가 20이상인 경우 해당 메서드에서 빠져나올 수 없다. 

```java
public static boolean isAdult() {
    Scanner input = new Scanner(System.in);

    int age = input.nextInt();

    if (age >= 20) {
        System.out.println("성인 입니다.");
    } else {
        System.out.println("성인이 아닙니다.");
        return false;
    }
}
```

다음과 같이 `return true;`를 추가하거나 `result` 변수에 담아서 마지막에 `return result` 방식으로 하는 2가지 방식이 있다. 

```java
public static boolean isAdult() {
    Scanner input = new Scanner(System.in);

    int age = input.nextInt();

    if (age >= 20) {
        System.out.println("성인 입니다.");
        return true;
    } else {
        System.out.println("성인이 아닙니다.");
        return false;
    }
}
```
또는

```java
public static boolean isAdult() {
    Scanner input = new Scanner(System.in);

    int age = input.nextInt();

    boolean result;

    if (age >= 20) {
        System.out.println("성인 입니다.");
        result = true;
    } else {
        System.out.println("성인이 아닙니다.");
        result = false;
    }

    return result;
}
```

작성하려는 메서드의 목적에 따라 하나를 골라 작성하면 된다.

#

### 메서드 시그니처(method signature)

메서드 오버로딩에 대해 알아보기 전에 '메서드 시그니처'에 대해 알아보자. 

**_메서드 시그니처는 자바에서 메서드를 구분할 수 있는 고유한 식별자나 서명을 의미하는데, 메서드의 이름과 매개변수 타입(순서 포함)까지 포함되어 있다. 반환값은 시그니처에 포함되지 않는다._**

그래서 이 메서드 시그니처가 다르면 메서드 이름이 같아도 다른 메서드로 간주한다. 

#

### 메서드 오버로딩(method overloading)

**_메서드 오버로딩은 이 메서드 시그니처를 이용하는데, 메서드 이름은 동일하지만 매개변수의 타입 또는 갯수 또는 둘 다 다른 메서드를 여러 개 정의하는 걸 말한다._**

그러면 한 가지 기능을 하는 메서드에 대해 오버로딩을 적용해보자. 

입력받은 팀원을 소개하는 문자열을 반환한다.  

```java
public static String introduce(String member) {
    return "내 팀원은 " + member + " 입니다.";
}
```
출력 결과는 다음과 같습니다.

```java
내 팀원은 홍길동 입니다.
```


- 매개변수가 하나 늘어난 메서드 오버로딩  

    ```java
    public static String introduce(String member1, String member2) {
        return "내 팀원은 " + member1 + " " + member2 + " 입니다.";
    }
    ```

    - 출력 결과

    ```java
    내 팀원은 홍길동 김제하 입니다.
    ```

- 타입이 다른 메서드 오버로딩  

    ```java
    public static String introduce(int member) {
        return "내 팀원은 " + member + " 입니다.";
    }
    ```

    - 출력 결과

    ```java
    내 팀원은 1 입니다.
    ```

- 타입과 갯수가 모두 다른 메서드 오버로딩

    ```java
    public static String introduce(String member1, int member2) {
        return "내 팀원은 " + member1 + " " + member2 +  " 입니다.";
    }
    ```
    - 출력 결과
    
    ```java
    내 팀원은 홍길동 1 입니다.  
    ```

모두 다 한 번에 실행이 가능하다. 하지만 완전히 동일한 메서드가 2개 존재하면 에러가 발생된다. 만약 `(int memeber)`를 `(String member)`로 수정할 경우 다음과 같은 에러가 발생한다.  

```
Ambiguous method call: both 'CarMain.introduce(String)' and 'CarMain.introduce(String)' match
```

그래서 '메서드 시그니처'가 다른 메서드만이 오버로딩이 가능하다.  

&nbsp;

## 클래스 메서드(정적, static 메서드)와 인스턴스 메서드

이제 `static`에 대해서 알았으니 얘기하자면 `static`이 붙은 메서드가 정적 메서드, `statc` method, 클래스 메서드이고, `static`이 안붙은 메서드가 인스턴스 메서드다.

**_주로 클래스 메서드는 클래스 변수에 접근하기 위해 사용되고, 인스턴스 메서드는 인스턴스 변수에 접근하기 위해 사용된다._**

메서드를 설명하기 위해 다음 코드와 같이 함께 본다.

- 클래스 

    ```java
    public class Car {

        public String name; // 인스턴스 변수
        public int price; // 인스턴스 변수
        public static int count; // 클래스 변수(정적 변수)

        public Car(String name, int price) {
            this.name = name;
            this.price = price;
            count++;
        }

        // 클래스 메서드
        public static int getCount() {
            System.out.println("생성된 총 차 댓수: " + count);
            return count;
        }

        // 클래스 메서드
        public static void callClassMethod() {
            System.out.println(" ===== Enter in callClassMethod =====");
            getCount(); // 클래스 메서드 내에서 클래스 메서드 호출  

        }

        // 인스턴스 메서드
        public String getName() {
            System.out.println(" ===== Enter in getName =====");
            getCount(); // 인스턴스 메서드 내에서 클래스 메서드 호출
            return name;
        }

        // 인스턴스 메서드 
        public int getPrice() {
            System.out.println(" ===== Enter in getPrice =====");
            getCount(); // 인스턴스 메서드 내에서 클래스 메서드 호출  
            return price;
        }

    }
    ```

- 인스턴스

    ```java
    public class CarMain {
        public static void main(String[] args) {
            Car car1 = new Car("k3", 10000);

            Car.getCount(); // 클래스를 통한 클래스 메서드 접근
            Car.callClassMethod(); // 클래스를 통한 클래스 메서드 접근 
            car1.getCount(); // 인스턴스를 통한 클래스 메서드 접근
            car1.getName(); // 인스턴스를 통한 인스턴스 메서드 접근
            car1.getPrice(); // 인스턴스를 통한 인스턴스 메서드 접근  

        }
    }
    ```

### 인스턴스 메서드 호출  

단 인스턴스 메서드는 반드시 생성된 인스턴스를 통해서만 호출할 수 있다. 클래스명으로는 접근할 수 없다.  

만약 `Car.getName()` 으로 접근하려고 한다면 다음과 같은 에러가 발생된다.

```
non-static method getName() cannot be referenced from a static context
```

클래스를 통한 메서드 호출은 static 예약어가 붙은 것만 가능하다는 의미다. 왜 그런건지는 클래스와 인스턴스가 생성되는 메모리 영역에 대해 알면 쉽게 이해할 수 있다. **_클래스에 대해 생성되는 메모리 영역과 인스턴스가 생성되는 메모리 영역이 다르기 때문이다._** 더 자세한 내용은 이 부분을 참고하자.  

#

### 클래스 메서드, 정적(static) 메서드 호출

위 코드에서 확인할 수 있듯이 인스턴스 메서드와 달리 정적 메서드는 클래스 이름과 인스턴스 이름을 통한 접근 모두 가능하다. **_이 정적 메서드는 주로 객체 생성 없이 메서드의 호출만으로 필요한 기능을 수행할 때 주로 사용한다. 예를 들어 간단한 메서드 하나로 끝나는 메서드에 사용된다._**

#

### 메서드 간 호출

위 코드에 작성한 것처럼 다음 순서로만 호출이 가능하다

- 인스턴스 메서드 -> 클래스 메서드 호출 O
- 클래스 메서드 -> 인스턴스 메서드 호출 x
- 클래스 메서드 -> 클래스 메서드 호출 O

클래스와 인스턴스의 메모리 영역에 대해서 알면 쉽게 이해할 수 있다. 설명은 이 부분을 보자.

그래서 위 코드를 실행하면 다음 출력을 볼 수 있다. 화살표는 출력된 게 아니라 내 설명이다.

```
생성된 총 차 댓수: 1                     <--- Car.getCount()의 결과
===== Enter in callClassMethod ===== <--- Car.callClassMethod에 진입
생성된 총 차 댓수: 1                     <--- Car.callClassMethod 내부에서 getCount 클래스 메서드 호출
생성된 총 차 댓수: 1                     <--- car1.getCount()의 결과
===== Enter in getName =====         <--- car1.getName에 진입
생성된 총 차 댓수: 1                     <--- getName 인스턴스 메서드 내부에서 호출한 getCount 클래스 메서드
===== Enter in getPrice =====        <--- car1.getPrice에 진입
생성된 총 차 댓수: 1                     <--- getPrice 인스턴스 메서드 내부에서 호출한 getCount 클래스 메서드
```

클래스 메서드에서 인스턴스 메서드를 호출하는 방법이 있긴 하다. 바로 클래스 메서드 매개변수로 인스턴스를 받아서 클래스 메서드 내부에서 이 인스턴스를 통해 인스턴스 메서드에 접근하면 된다. 

하지만 클래스 메서드에서 인스턴스 메서드를 호출하는 건 좋지 못하다. 클래스 메서드는 클래스와 관련된 메서드이기 때문에 적절하지 않다.  


&nbsp;


---

# 절차 지향 프로그래밍과 객체 지향 프로그래밍 

이 두 프로그래밍 방식은 서로 대치되는 개념이 아니다. 단지 무엇에 더 중점을 두냐의 다양한 관점들일 뿐이다. 그러면 이 두 가지 방식에 대해 알아보자.  

### 절차 지향 프로그래밍
절차 지향 프로그래밍은 명칭 그대로 '절차'를 중요하게 여기는 방식이라서 프로그램의 흐름을 작성한 코드 순서대로 따르면서 처리하는 방식이다. 그래서 **_'어떻게 '_** 를 중심으로 프로그래밍한다.

#

### 객체 지향 프로그래밍
객체 지향 프로그래밍은 명칭 그대로 '객체'를 중요하게 여긴다. 우리가 살아가는 세상에서 존재하는 사물이나 사건을 객체로 보고, 이 객체들 간의 상호작용을 중심으로 프로그래밍하는 방식이다.  그래서 **_'무엇 '_** 을 중심으로 프로그래밍한다.


### 그러면 위 차이점들이 코드에서 어떻게 드러날까?

절차 지향 방식으로 코드를 작성했다가 객체 지향 방식으로 리팩토링하면서 어떤 문제점들이 해결되는지 코드로 확인해보자. 

- 클래스
    ```java
    public class Car {

        public int velocity;
        public boolean isOn;

    }
    ```

- 인스턴스 및 main
    
    ```java
    public class CarMain {
        public static void main(String[] args) {
            Car car = new Car();

            // 시동 걸기
            turnOn(car);

            // 속도 올리기
            velocityUp(car);
            velocityUp(car);
            velocityUp(car);

            // 속도 내리기
            velocityDown(car);
            velocityDown(car);
            velocityDown(car);

            // 시동 끄기
            turnOff(car);
        }

        public static void turnOn(Car car) {
            System.out.println("시동을 겁니다");
            car.isOn = true;
        }

        public static void turnOff(Car car) {
            System.out.println("시동을 끕니다");
            car.isOn = false;
        }

        public static void velocityUp(Car car) {
            System.out.println("속도를 10 높입니다");
            car.velocity += 10;
        }

        public static void velocityDown(Car car) {
            System.out.println("속도를 10 내립니다");
            car.velocity -= 10;
        }
    }
    ```


위 절차 지향으로 작성된 코드는 **_데이터와 기능이 분리되어 있다._** 데이터는 `Car`에 있지만 기능은 `CarMain`에 있다. 데이터와 이 데이터를 사용하는 기능은 매우 연관되어 있기 때문에, 데이터가 변경되면 이 데이터를 사용하는 기능들도 함께 변경해야 한다. 이렇게 분리되어 있으면 유지보수 관점에서도 관리 포인트가 두 곳으로 늘어난다.  

그러면 객체 지향 방식으로 리팩토링을 해보자. `Car`라는 개념을 객체로 온전히 만드는 게 중요하다. '객체 지향'의 말처럼 객체를 지향하자. 그래서 프로그램을 실행하는 순서(절차)보다 이 자동차 클래스가 어떤 속성(데이터)를 가지고 어떤 기능(메서드)를 제공하는지에 초점을 두고 작성해야 한다. 


- 클래스
    ```java
    public class Car {

        public int velocity;
        public boolean isOn;
        
        public void turnOn() {
            System.out.println("시동을 겁니다");
            isOn = true;
        }

        public void turnOff() {
            System.out.println("시동을 끕니다");
            isOn = false;
        }

        public void velocityUp() {
            System.out.println("속도를 10 높입니다");
            velocity += 10;
        }

        public void velocityDown() {
            System.out.println("속도를 10 내립니다");
            velocity -= 10;
        }
    }
    ```


- 인스턴스 및 main
    ```java
    public class CarMain {
        public static void main(String[] args) {
            Car car = new Car();

            car.turnOn(); // 객체야 시동 켜라
            car.velocityUp(); // 객체야 속도 높여라
            car.velocityUp();
            car.velocityUp();
            car.velocityDown(); // 객체야 속도 낮춰라
            car.velocityDown();
            car.velocityDown();
            car.turnOff(); // 객체야 시동 꺼라
    }}
    ```

리팩토링한 후 다음 변화들을 느낄 수 있다.

1. 객체의 속성과 기능은 객체 하나로 묶었다. 유지보수의 관점에서 보자면 한 곳으로 줄어들었다. 이 말은 변경도 더 쉬워졌다는 의미다.

2. 객체를 생성하고 필요한 기능(메서드)만을 호출하면 된다. 이 객체를 사용하는데 필요한 모든 건 객체 안에 있다. 그래서 사용자는 이 객체 내부에 대한 정보를 몰라도 이 객체가 제공하는 기능을 단지 호출해서 사용할 수 있다. 

3. 실제 실행코드도 훨씬 짧아지고, 코드 읽기가 더 쉬워졌다.  


&nbsp;

### 절차 지향과 객체 지향의 차이: 캡슐화  

실행되는 main 코드를 보면 절차 대로 작성된 것을 또한 알 수 있다. 절차 지향과 객체 지향은 서로 대치되는 방식이 아닌 단지 무엇에 더 중점을 두고 프로그래밍을 하느냐의 차이일 뿐이다. 그러면 이 차이를 한 단어로 요약하자면 **_캡슐화_** 의 유무라고 판단된다. 

**_캡슐화란 속성과 기능을 하나로 묶어서, 필요한 기능을 메서드를 통해 외부에 선별적으로 제공하는 것을 말한다._**  

캡슐화의 관점에서 보자면 객체의 속성과 행위를 어떻게 두냐에 따라 절차 지향인지 객체 지향인지를 알 수 있다. 절차 지향 프로그래밍은 _객체의 속성과 행위를 따로 두는 방식_ 이고, 객체 지향 프로그래밍은 _객체의 속성과 행위를 묶은 방식_ 이다. 

하지만 위 객체 지향 방식으로 작성한 코드는 완전히 캡슐화를 담아내지 못한 코드다. 캡슐화의 '선별적으로' 라는 부분을 담아내지 않았기 때문이다. 이 부분은 '접근 제어자' 부분에서 정리한다.  



&nbsp;

---



# 이번 포스팅 주제에 관한 파이썬과의 차이점

단지 호기심의 관점으로 자바와 파이썬을 비교해본다

## 멤버 변수와 메서드  

### 타입 강제

자바에서는 메서드의 매개변수의 타입과 반환값 타입을 반드시 입력해야 한다. '강제성'이 존재하지만 파이썬에서는 그렇지 않다. 자바가 엄격하다가 느낄 수 있지만 사람이기에 이런 엄격함은 필요하다고 생각한다. 

#

### 메서드 오버로딩

파이썬은 오버로딩 개념이 없다. 하지만 팩킹과 언팩킹을 사용하여 받은 변수의 갯수를 가변적으로 받아서 한 메서드에서 여러 개의 변수를 받을 수 있다.  


#

### 멤버 변수와 메서드 구분 방식

파이썬에서 클래스의 멤버 변수는 자바와 동일하게 클래스와 인스턴스 변수로 나눠졌다. 하지만 메서드의 경우, `self` 인자를 받는 메서드는 인스턴스 메서드, `cls` 인자와 `@classmethod` 데코레이터를 받는 클래스 메서드, 아무런 인자를 받지 않고 `@staticmethod`라는 데코레이터를 받은 메서드는 정적 메서드라 한다.

하지만 자바에서는 정적 메서드와 클래스 메서드를 동일하게 불렀고, 인스턴스 메서드와 클래스 메서드를 구분하는 방법이 받는 인자가 아닌 `static` 예약어의 사용 유무인 것도 신기했다.  

| 언어 | 파이썬 | 자바 | 
| ---- | ---- | ---- |
| 인스턴스 변수 키워드 | self | x |
| 클래스 변수 키워드 | x | static | 
| 인스턴스 메서드 키워드 | self | x | 
|클래스 메서드 키워드 | cls, @classmethod | static | 
| 정적 메서드 키워드 | @staticmethod | 클래스 메서드와 정적 메서드가 같음|
 
#

### 메서드 간 호출  

인스턴스와 클래스에서 클래스 메서드 그리고 인스턴스 메서드를 호출하는 것도 다르다.

- 인스턴스로 클래스 메서드 호출: 자바와 파이썬 모두 가능
- 클래스로 인스턴스 메서드 호출: 자바는 불가능, 파이썬은 인스턴스 메서드에 인자로 인스턴스를 넘기면 가능  

또한 다른 종류의 메서드들끼리 호출하는 것도 다르다

- 인스턴스 메서드 -> 인스턴스 메서드
    - 자바에서는 매개변수 없이 호출 가능하지만, 파이썬에서는 `self` 매개변수를 통해 호출 가능
- 인스턴스 메서드 -> 클래스 메서드
    - 자바에서는 매개변수 없이 호출 가능하지만, 파이썬에서는 `__class__` 매직 메서드를 통해 호출 가능
- 클래스 메서드 -> 클래스 메서드
    - 자바에서는 매개변수 없이 호출 가능하지만, 파이썬에서는 `cls` 매개변수를 통해 호출 가능  
- 클래스 메서드 -> 인스턴스 메서드
    - 자바는 클래스 메서드의 인자로 인스턴스를 받아야 호출 가능하지만, 파이썬에서는 불가능하다.  



&nbsp;