---
title: "자바의 클래스는 메모리 영역에서는 어떻게 생성될까?"
date: 2024-01-26T00:32:17+09:00
draft: false
summary: 메모리의 각 메서드 영역, 스택 영역, 힙 영역에 무엇이 생성되는지 알아보고,  NullPointerException이 왜 발생하는지 알아본다.  
tags: ["java"]
categories: "java"
---

# Introduction

이번 포스팅에서는 자바의 JVM(Java Virtual Machine) 메모리 구조가 어떤 영역으로 구성되어 있고, 자바의 메서드와 변수들이 이 메모리에 어떻게 생성되고 사라지는지 알아본다.  

JAVA 프로그램을 실행하면 컴퓨터 메모리에는 OS, JVM, other applications 들이 올라간다. 그러면  `.java` 파일을 `javac` 컴파일러에 의해서 `.class` 파일로 컴파일된다. 그러면 JVM의 `Class Loader System`에 의해서 이 `.class` 파일을 읽어 `Runtime Data Area` 에 여러 값들이 올라가고, 생성되면서 실행된다. 

그래서 오늘 배울려고 하는 자바의 메모리 구조가 JVM의 이 `Runtime Data Area` 메모리 공간 구조에 대해 이야기하려는 것이다.

&nbsp;

---

# JVM 메모리 영역의 구성과 각 영역의 역할  

JVM의 `Runtime Data Area`는 아래와 같은 메모리 영역을 가지고 실행한다.    

자바의 메모리 구조는 메서드(method) 영역, 스택(stack) 영역, 힙(heap) 영역 3개로 나눌 수 있다.  

아래 내용의 출처: [김영한의 실전 자바 - 기본편](https://www.inflearn.com/course/%EA%B9%80%EC%98%81%ED%95%9C%EC%9D%98-%EC%8B%A4%EC%A0%84-%EC%9E%90%EB%B0%94-%EA%B8%B0%EB%B3%B8%ED%8E%B8/dashboard)

아래 내용이 어려우면 내가 만든 코드 예시를 보면서 이해해보자. 

- 스택 영역부터 이해하고 싶으면 [여기]()부터, 메서드 영역까지 포함해서 이해하고 싶으면 [여기]()부터 보자.  


### 메서드 영역 또는 정적 영역 (method 또는 static area)
클래스 레벨의 정보를 저장하기 때문에, 프로그램을 실행하는데 필요한 공통 데이터를 관리한다고 볼 수 있다. 이 영역은 프로그램의 모든 영역에서 공유하며 아래 정보들이 섞여 저장된다. 크게 2가지로 분류할 수 있다.     

- 클래스 정보:  클래스의 실행 코드, 필드, 메서드(인스턴스 메서드, 클래스 메서드 포함), 생성자 코드 등 모든 실행 코드 존재  
    - 클래스 정보의 일부로서 `static` 변수들도 보관한다.  

- 런타임 상수 풀: 프로그램을 실행하는데 필요한 '공통 리터럴 상수'를 보관한다. (자바의 최적화 영역)

### 스택 영역 (stack area)
자바 실행 시, 하나의 실행 스택이 생성된다. 각 스택 프레임은 지역 변수, 중간 연산 결과, 메서드 호출 정보 등을 포함한다.  

- 스택 프레임: 스택 영역에 쌓이는 네모 박스가 하나의 스택 프레임이다. 메서드를 호출할 때마다 하나의 스택 프레임이 쌓이고, 메서드가 종료되면 해당 스택 프레임이 제거된다.  

- 각 쓰레드별로 하나의 실행 스택이 생성된다. 쓰레드 수 만큼 스택 영역이 생성된다. 

### 힙 영역 (heap area)
객체(인스턴스)와 배열이 생성되는 영역이다. GC가 이루어지는 주요 영역이며, 더 이상 참조되지 않는 객체는 GC에 의해 제거된다. 


&nbsp;

---

# 메모리 할당에 대한 추가적인 내용  

### 동적 메모리 할당
**_객체와 배열이 힙 영역에 생성되는 이유는 객체와 배열같은 참조형은 기본형과 달리 크기가 동적으로 바뀌기 때문이다. 이를 '동적 메모리 할당'이라 한다. 그래서 기본형과 달리 복잡한 데이터 구조를 만들고 관리할 수 있다._** 

하지만 기본형은 사용할 값을 직접 저장하는 이유는 크기가 정해져 있기 때문이다. 이에 따라 더 빠르게 메모리를 관리할 수 있지만 참조형처럼 복잡한 데이터 구조를 관리할 수 없다.  


### 메서드에 대한 메모리 할당

같은 클래스로 부터 생성된 객체라도, 인스턴스 내부의 변수 값은 서로 다를 수 있지만, 메서드는 공통된 코드를 공유한다. 따라서 객체가 생성될 때, 인스턴스 변수에는 메모리가 할당되지만, 메서드에 대한 새로운 메모리 할당은 없다. 

메서드는 메서드 영역에서 공통으로 관리되고 실행된다. 인스턴스 메서드도 메서드 영역에 생성되지만, 인스턴스 생성 시 메서드 영역에 만들어진 메서드를 연결해서 사용한다. 

메서드 영역 안에 있는 클래스 정보를 바탕으로 new 연산자를 만날 때마다 힙 영역에 새로운 인스턴스를 만든다. 그리고, 인스턴스마다 각각 멤버변수를 가지고 있다. 


&nbsp;

---

# 스택 영역과 힙 영역 이해하기  

먼저 스택 프레임에 대해 이해하기 위해서 아래 코드를 보자. 자료구조 스택에 대한 설명은 생략한다.

- 클래스

    ```java
    public class Car {

        private String name;

        public Car(String name) {
            this.name = name;
        }

        public String getName() {
            return name;
        }
    }
    ```

- 인스턴스 및 main

    ```java
    public class CarMain {
        public static void main(String[] args) {
            System.out.println("main start");
            method1();
            System.out.println("main end");
        }

        static void method1() {
            System.out.println("method1 start");
            Car car1 = new Car("black");
            method2(car1);
            System.out.println("method1 end");
        }

        static void method2(Car car) {
            System.out.println("method2 start");
            System.out.println("car.name= " + car.getName());
            System.out.println("method2 end");
        }
    }
    ```

- 호출결과

    ```md
    main start
    method1 start
    method2 start
    car.name= black
    method2 end
    method1 end
    main end
    ```

`main()` -> `method1()` -> `method2()`를 호출하는 코드다.  
`method1()`에서 `Car`의 인스턴스를 생성한다. 생성한 인스턴스의 참조값을 `method2()`에 넘겨서, `.getName()` 메서드를 호출한다.  

그러면 스택 영역에 어떻게 생기는지 그림으로 확인해보자. 


### 과정 1 ~ 3

![image](https://github.com/JeHa00/image/assets/78094972/dd8dc573-59ae-4f2e-8624-343d6644caa1)

- 과정 1: 처음 `main()` 메서드를 실행하여, `main()` 스택 프레임이 생성된다.
- 과정 2: `main()`에서 `method1()`을 호출하여 `method1()`의 스택 프레임이 생성된다.
    - `method1`에서 `Car` 클래스의 인스턴스를 '힙 영역'에 생성한다. 
    - 참조값은 `car1`에 보관한다.
- 과정 3: `car1`에 보관하고 있는 참조값을 복사하여 `method2()`의 매개변수 `car`에 전달한다.  
    - `car1`과 `car` 모두 힙 영역에 생성된 동일한 `Car` 인스턴스를 참조하고 있다.  

### 과정 4 ~ 6

![image](https://github.com/JeHa00/image/assets/78094972/1d39cf1d-ca56-4fd5-99b9-1b4b4b5d6592)

- 과정 4: `method2()` 가 종료된다. `method2()` 스택 프레임이 제거되면서 `car`도 제거된다. `Car` 인스턴스를 참조하지 않게 되었다.  
- 과정 5: `method1()` 이 종료된다.  `method1()` 스택 프레임이 제거되면 `car1`도 제거된다. `Car` 인스턴스를 참조하지 않게 되었다. 
- 과정 6: 이제 `Car` 인스턴스를 참조하는 곳이 더 없기 때문에 사용되는 곳도 없다. 프로그램에서 사용되지 않는 개체이기 때문에, GC에 의해서 제거된다.  

최종적으로 `main()`도 종료되면서 `main()` 스택 프레임도 제거된다. 

스택이라는 자료구조처럼 마지막에 들어온 게 먼저 종료되는 방식임을 확인할 수 있다.  


## 정리

위 내용을 정리하면 다음과 같다. 자바는 스택 영역을 사용해서 메서드 호출과 지역 변수(매개변수 포함)를 관리한다.

- 메서드를 계속 호출하면 이 메서드에 대한 스택 프레임이 추가되면서 스택 프레임이 쌓인다.  
- 지역 변수(매개변수 포함)는 스택 영역에서 관리한다.  
- 스택 프레임이 종료되면 지역 변수도 함께 제거된다.  
- 스택 프레임이 모두 제거되면 프로그램도 종료된다.

&nbsp;

---

# 메서드 영역까지 합쳐서 이해하기

이제는 [클래스 메서드(정적, static 메서드)와 인스턴스 메서드](https://jeha00.github.io/post/java/2_1_class/#%ED%81%B4%EB%9E%98%EC%8A%A4-%EB%A9%94%EC%84%9C%EB%93%9C%EC%A0%95%EC%A0%81-static-%EB%A9%94%EC%84%9C%EB%93%9C%EC%99%80-%EC%9D%B8%EC%8A%A4%ED%84%B4%EC%8A%A4-%EB%A9%94%EC%84%9C%EB%93%9C)에서 사용한 코드 중 일부를 가져와 메서드 영역까지 합쳐서 이해해보자.

- 클래스 

    ```java
    public class Car {

        private String name; // 인스턴스 변수
        private int price; // 인스턴스 변수
        private static int count; // 클래스 변수(정적 변수)

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
            System.out.println(" --- Enter in callClassMethod ---");
            getCount(); // 클래스 메서드 내에서 클래스 메서드 호출
        }

        // 인스턴스 메서드
        public String getName() {
            System.out.println(" --- Enter in getName ---");
            getCount(); // 인스턴스 메서드 내에서 클래스 메서드 호출
            return name;
        }

        // 인스턴스 메서드
        public int getPrice() {
            System.out.println(" --- Enter in getPrice ---");
            getCount(); // 인스턴스 메서드 내에서 클래스 메서드 호출
            return price;
        }
    }
    ```

- 인스턴스

    ```java
    public class CarMain {
        public static void main(String[] args) {
            System.out.println("=========== main start ===========");
            Car car1 = new Car("k3", 10000);
            System.out.println("Car name = " + car1.getName());
            method1();
            System.out.println("=========== main end ============");
        }

        public static void method1() {
            System.out.println("========== method1 start ==========");
            Car car2 = new Car("k5", 20000);
            System.out.println("Car price = " + car2.getPrice());
            method2(car2);
            System.out.println("========== method1 end ==========");
        }

        public static void method2(Car car) {
            System.out.println("========== method2 start ==========");
            Car.callClassMethod();
            System.out.println("Car price = " + car.getPrice());
            System.out.println("Car name = " + car.getName());
            System.out.println("========== method2 end ==========");
        }
    }
    ```

그러면 위 코드가 `main` -> `method1` -> `method2`로 실행되면서 각 영역마다 어떻게 진행되는지 확인해보자.


## 1) main 메서드 시작


### main 스택 프레임 생성 후, heap 영역에 인스턴스 생성

{{<figure src="https://github.com/JeHa00/image/assets/78094972/36e62986-dfda-43ff-82d0-ca9f71a7e842" width="90%">}}

1. 코드가 실행되면서 메서드 영역에 클래스 정보들이 다 올라간다. 
2. `main`이 실행되고 스택 영역에 main 스택 프레임이 생성된다.  
3. heap 영역에 `Car` 인스턴스가 생성된다. 즉, 이 인스턴스의 참조값이 생긴다. car1의 `name`은 'k3' 이고, `price`는 10000이다.

- 출력 코드

    ```java
    =========== main start ===========
    ```

&nbsp;

### getName 스택 프레임 생성


{{<figure src="https://github.com/JeHa00/image/assets/78094972/bb71ddba-87d0-430e-91bc-de00f79c0419" width="90%">}}

4. `car1.getName` 호출 
5. `getName` 스택 프레임이 스택 영역에 쌓인다. 이 때 getName 호출에 사용한 해당 인스턴스의 참조값이 `getName` 스택 프레임에 저장된다.  
6. `getName()` 메서드를 실행하기 위해 메서드 영역에 있는 `getName()` 인스턴스 메서드 코드를 읽고, `println` 출력문부터 실행된다.


- 출력 코드
    ```java
    =========== main start ===========
    --- Enter in getName ---
    ```

&nbsp;

### getCount 스택 프레임 생성 및 제거 


{{<figure src="https://github.com/JeHa00/image/assets/78094972/695ef74b-f091-40b5-9c8b-12ca78649b0b" width="90%">}}


7. `getName()` 메서드 코드를 읽는 과정에서 `getCount()` 클래스 메서드가 호출되어 스택 프레임에 쌓인다.
8. 메서드 영역에서 `getCount()`를 실행하여 정적 변수인 `count`의 값을 반환한다. 

- 출력 코드

    ```java
    =========== main start ===========
    --- Enter in getName ---
    생성된 총 차 댓수: 1
    ```

9. 그러면 `getCount()`에 대한 실행이 끝나므로 아래 이미지처럼 이 클래스 메서드에 대한 스택 프레임은 사라진다. 



{{<figure src="https://github.com/JeHa00/image/assets/78094972/60cdedd0-b830-422a-a061-1c9ac9cda86b" width="90%">}}

&nbsp;

### getName 스택 프레임 제거  

{{<figure src="https://github.com/JeHa00/image/assets/78094972/5e62d0ca-376b-47da-91ff-5d31ce414bb0" width="90%">}}


10. `getCount()` 메서드가 종료되었기 때문에 `getName()` 메서드는 인스턴스의 `name`을 반환하면서 종료된다. 
11. 이에 따라 스택 영역의 해당되는 스택 프레임이 사라진다.  
12. `name`을 반환했기 때문에 `main` 메서드에서 `println`이 다음과 같이 출력된다.  

- 출력 코드

    ```java
    =========== main start ===========
    --- Enter in getName ---
    생성된 총 차 댓수: 1
    Car name = k3
    ```

&nbsp;


### 힙 영역에서 Car 인스턴스 제거  

13. `car1` 참조 변수가 가리키는 인스턴스를 아무도 참조하고 있지 않으므로 GC에 의해서 삭제된다.  

{{<figure src="https://github.com/JeHa00/image/assets/78094972/5ff663fb-edfe-4f83-add8-d2cac658dbd9" width="90%">}}


&nbsp;

## 2) method1 시작

### method1 스택 프레임 생성

{{<figure src="https://github.com/JeHa00/image/assets/78094972/c136fbd3-bf85-4e94-a9c1-fbebd5f65e20" width="90%">}}

14. 그리고 코드가 작성된 순서 따라서 `method1()`이 호출되면서 `method1()`에 대한 스택 프레임이 스택 영역에 쌓인다.  

- 출력 코드

    ```java
    =========== main start ===========
    --- Enter in getName ---
    생성된 총 차 댓수: 1
    Car name = k3
    ========== method1 start ==========
    ```

&nbsp;

### 힙 영역에서 인스턴스 생성

15. method1이 실행되면서 `new` 예약어를 사용하여 Car 클래스의 인스턴스가 heap 영역에 생성된다.   
16. 인스턴스 객체에 대한 참조값이 `car2` 변수에 담겨진다.  

{{<figure src="https://github.com/JeHa00/image/assets/78094972/fbae1c96-e2b2-4f2d-8040-241b4697890e" width="90%">}}

&nbsp;

### getPrice 스택 프레임 생성

17. `car2.getPrice()`가 호출되면서 이 인스턴스 메서드에 대한 스택 프레임이 생성된다. 이 스택 프레임에는 `car2`에 담겨진 인스턴스 참조값이 담겨있다. 그래서 메서드 영역에 있는 `getPrice()` 코드가 이 메서드의 스택 프레임에 있는 인스턴스 참조값을 사용하여 해당 인스턴스에게 접근하여 인스턴스 변수의 정보들을 얻어 실행된다. 

{{<figure src="https://github.com/JeHa00/image/assets/78094972/498710ba-a1f7-46ec-8878-66c523ef7b11" width="90%">}}

- 출력 코드
    ```java
    =========== main start ===========
    --- Enter in getName ---
    생성된 총 차 댓수: 1
    Car name = k3
    ========== method1 start ==========
    --- Enter in getPrice ---
    ```

&nbsp;

### getCount 스택 프레임 생성 및 제거 

{{<figure src="https://github.com/JeHa00/image/assets/78094972/1d398e46-f9ae-4935-985f-c6e65f524a22" width="90%">}}

18. `getPrice()` 메서드 코드를 읽는 과정에서 `getCount()` 클래스 메서드가 호출되어 스택 프레임에 쌓인다.

19. 메서드 영역에서 `getCount()`를 실행하여 정적 변수인 `count`의 값을 반환한다. 

- 출력 코드

    ```java
    =========== main start ===========
    --- Enter in getName ---
    생성된 총 차 댓수: 1
    Car name = k3
    ========== method1 start ==========
    --- Enter in getPrice ---
    생성된 총 차 댓수: 2
    ```

20. 그러면 `getCount()`에 대한 실행이 끝나므로 아래 이미지처럼 이 클래스 메서드에 대한 스택 프레임은 사라진다. 

{{<figure src="https://github.com/JeHa00/image/assets/78094972/be5af219-665f-4161-8c76-a0cf0290f425" width="90%">}}

&nbsp;

### getPrice 스택 프레임 제거

{{<figure src="https://github.com/JeHa00/image/assets/78094972/a7f110f9-1462-4ccc-89e3-8d8a46b8547b" width="90%">}}

21. `getCount()` 호출이 종료되었으므로 `price` 값을 반환하면서 실행이 종료되어 이 메서드에 대한 스택 프레임이 스택 영역에서 사라진다.


- 출력 코드

    ```java
    =========== main start ===========
    --- Enter in getName ---
    생성된 총 차 댓수: 1
    Car name = k3
    ========== method1 start ==========
    --- Enter in getPrice ---
    생성된 총 차 댓수: 2
    Car price = 20000
    ```

&nbsp;

## 3) method2 시작 

### method2와 callClassMethod 스택 프레임 생성

{{<figure src="https://github.com/JeHa00/image/assets/78094972/bacbbae5-4eac-4c6f-a09e-9d20785543c2" width="90%">}}

22. 그 다음으로 `method2()` 가 호출되면서 스택 영역에 이 메서드에 대한 스택 프레임이 생성된다. 이 스택 프레임에는 car 매개변수에 대한 정보가 저장되어 있다. `method1()`에서 생성된 인스턴스 정보가 car 매개변수에 가지고 있다. 그래서 `car1`과 달리 GC에 의해서 삭제되지 않는다.  


23. 메서드 영역에서 정적 메서드인 `callClassMethod()` 코드를 찾아 실행한다.
스택 영역에 `callClassMethod()` 스택 프레임이 스택 영역에 쌓인다.  

- 출력

    ```java
    =========== main start ===========
    --- Enter in getName ---
    생성된 총 차 댓수: 1
    Car name = k3
    ========== method1 start ==========
    --- Enter in getPrice ---
    생성된 총 차 댓수: 2
    Car price = 20000
    ========== method2 start ==========
    --- Enter in callClassMethod ---
    ```

&nbsp;

### getCount 스택 프레임 생성 및 제거 후, callClassMethod 스택 프레임 제거  

{{<figure src="https://github.com/JeHa00/image/assets/78094972/02be3d5c-112c-4fb3-9899-fd73a4d4dcd6" width="90%">}}

24. `callClassMethod` 안에서 `getCount()` 정적 메서드가 또한 호출되면서 `getCount()` 스택 프레임이 스택 영역에 쌓인다. 

25. `getCount()` 메서드가 실행되기 위해 메서드 영역에서 이 메서드의 코드 정보를 읽어오고, 정적 변수인 `count` 정보를 반환한다. 

- 출력
    ```java
    =========== main start ===========
    --- Enter in getName ---
    생성된 총 차 댓수: 1
    Car name = k3
    ========== method1 start ==========
    --- Enter in getPrice ---
    생성된 총 차 댓수: 2
    Car price = 20000
    ========== method2 start ==========
    --- Enter in callClassMethod ---
    생성된 총 차 댓수: 2
    ```

26. `getCount()` 메서드 실행이 끝나서 `getCount()`의 스택 프레임이 제거된다.  

27. 스택 프레임에서 `getCount()` 스택 프레임이 사라진 후 `callClassMethod()` 스택 프레임이 사라진다. 

{{<figure src="https://github.com/JeHa00/image/assets/78094972/69277716-a0bc-4f85-84e8-a9bd6572b721" width="90%">}}


&nbsp;

### getPrice 스택 프레임 생성 및 제거  그리고 getCount 스택 프레임 생성 및 제거  

28. 그 다음에 `method2()` 스택 프레임이 가지고 있는 car 참조 변수를 통해서 힙 영역에 있는 인스턴스에 접근하여 `getPrice()` 호출한다.`getPrice()` 스택 프레임이 스택 영역에 쌓인다.  

29. `getPrice()` 메서드 안에 진입하면서 `getCount()`를 호출하여, `getCount()` 스택 프레임이 스택 영역에 쌓인다.  

30. 그러면 여태 했던 것처럼 똑같이 메서드 영역에 있는 `getCount()` 코드가 실행된다. 

{{<figure src="https://github.com/JeHa00/image/assets/78094972/7945e77d-90c1-4764-8409-4277a7844a02" width="90%">}}


31. 종료되면 스택 영역에서 `getCount()`의 스택 프레임은 사라진다. 그리고, `getPrice()` 실행도 끝나서 `getPrice()` 스택 프레임이 다시 사라진다. 

{{<figure src="https://github.com/JeHa00/image/assets/78094972/1964a7f5-bc87-4029-86b7-326d76b372e5" width="90%">}}


- 출력

    ```java
    =========== main start ===========
    --- Enter in getName ---
    생성된 총 차 댓수: 1
    Car name = k3
    ========== method1 start ==========
    --- Enter in getPrice ---
    생성된 총 차 댓수: 2
    Car price = 20000
    ========== method2 start ==========
    --- Enter in callClassMethod ---
    생성된 총 차 댓수: 2
    --- Enter in getPrice ---
    생성된 총 차 댓수: 2
    Car price = 20000
    ```

&nbsp;

### getName 스택 프레임 생성 그리고 getCount 스택 프레임 생성 및 제거

32. 그 다음으로 `getName()` 호출되어 스택 영역에 스택 프레임이 쌓인다.

33. `getName()` 안에서 `getCount()` 클래스 메서드를 호출한다.  

34. `getCount()` 메서드에서 메서드 영역에 있는 정적 변수 값인 count를 반환한다.  

{{<figure src="https://github.com/JeHa00/image/assets/78094972/2b8c2040-63c0-441d-8661-e30796e4ebe1" width="90%">}}



- 출력
    ```java
    =========== main start ===========
    --- Enter in getName ---
    생성된 총 차 댓수: 1
    Car name = k3
    ========== method1 start ==========
    --- Enter in getPrice ---
    생성된 총 차 댓수: 2
    Car price = 20000
    ========== method2 start ==========
    --- Enter in callClassMethod ---
    생성된 총 차 댓수: 2
    --- Enter in getPrice ---
    생성된 총 차 댓수: 2
    Car price = 20000
    --- Enter in getName ---
    생성된 총 차 댓수: 2
    Car name = k5
    ```

35. 실행이 끝나면 `getCount()` 스택 프레임이 제거된다. 또한, `getName()` 실행 종료에 따라 이 메서드의 스택 프레임도 사라진다.  

{{<figure src="https://github.com/JeHa00/image/assets/78094972/2f904c31-4788-4a2b-9566-b8949d5296ce" width="90%">}}

&nbsp;

## 4) 각 메서드 종료

### method2 스택 프레임 제거 후, method1 스택 프레임 제거

36. `method2()` 실행이 다 끝났기 때문에 `method2()` 스택 프레임이 사라진다. 

{{<figure src="https://github.com/JeHa00/image/assets/78094972/ab6573a3-59e2-4db6-9180-0180e9bf7dd8" width="90%">}}

- 출력
    ```java
    =========== main start ===========
    --- Enter in getName ---
    생성된 총 차 댓수: 1
    Car name = k3
    ========== method1 start ==========
    --- Enter in getPrice ---
    생성된 총 차 댓수: 2
    Car price = 20000
    ========== method2 start ==========
    --- Enter in callClassMethod ---
    생성된 총 차 댓수: 2
    --- Enter in getPrice ---
    생성된 총 차 댓수: 2
    Car price = 20000
    --- Enter in getName ---
    생성된 총 차 댓수: 2
    Car name = k5
    ========== method2 end ==========
    ```


37. `method1()` 종료되면서 `method1()`의 스택 프레임도 사라진다.  

{{<figure src="https://github.com/JeHa00/image/assets/78094972/e77eff7f-13ca-40e4-9398-0cb5ccbdc435" width="90%">}}

- 출력
    ```java
    =========== main start ===========
    --- Enter in getName ---
    생성된 총 차 댓수: 1
    Car name = k3
    ========== method1 start ==========
    --- Enter in getPrice ---
    생성된 총 차 댓수: 2
    Car price = 20000
    ========== method2 start ==========
    --- Enter in callClassMethod ---
    생성된 총 차 댓수: 2
    --- Enter in getPrice ---
    생성된 총 차 댓수: 2
    Car price = 20000
    --- Enter in getName ---
    생성된 총 차 댓수: 2
    Car name = k5
    ========== method2 end ==========
    ========== method1 end ==========
    ```

&nbsp;

### 힙 영역에 인스턴스 제거  

38. 이에 따라 `car2` 참조 변수가 가지고 있는 참조값의 대상인 인스턴스가 힙 영역에서 GC에 의해 삭제된다. 이 인스턴스를 참조하는 게 이제 아무도 없기 때문이다.  

{{<figure src="https://github.com/JeHa00/image/assets/78094972/ba74bcaf-d7df-41ed-85b3-67cc323f1121" width="90%">}}

&nbsp;

### main 스택 프레임 제거 그리고, 프로그램 종료  

39. 마지막으로 `main` 메서드 실행도 종료되면서 `main` 스택 프레임도 스택 영역에서 사라진다.

- 출력

```java
    =========== main start ===========
    --- Enter in getName ---
    생성된 총 차 댓수: 1
    Car name = k3
    ========== method1 start ==========
    --- Enter in getPrice ---
    생성된 총 차 댓수: 2
    Car price = 20000
    ========== method2 start ==========
    --- Enter in callClassMethod ---
    생성된 총 차 댓수: 2
    --- Enter in getPrice ---
    생성된 총 차 댓수: 2
    Car price = 20000
    --- Enter in getName ---
    생성된 총 차 댓수: 2
    Car name = k5
    ========== method2 end ==========
    ========== method1 end ==========
    =========== main end ============
```

40. 프로그램이 종료된다.  




&nbsp;

---

# 변수와 생명 주기

그러면 위 학습한 내용을 변수의 관점에서 정리해보자.  

### 지역변수(매개변수 포함)
위 내용에서 학습한 대로 지역 변수는 스택 영역에 있는 스택 프레임 안에 보관된다. 호출된 메서드가 종료되면 스택 프레임도 제거 되는데 이때 해당 스택 프레임에 포함된 지역 변수도 함께 제거된다. 따라서 지역 변수는 생존 주기가 짧다.

### 인스턴스 변수
인스턴스에 있는 멤버 변수를 인스턴스 변수라 한다. 인스턴스 변수는 힙 영역을 사용한다. 힙 영 역은 GC(가비지 컬렉션)가 발생하기 전까지는 생존하기 때문에 보통 지역 변수보다 생존 주기가 길다. 이 인스턴스 객체에 대한 참조 횟수가 0이 되면 GC에 의해서 삭제된다.  

### 클래스 변수
클래스 변수(static 변수, 정적 변수)는 메서드 영역의 static 영역에 보관되는 변수다. 메서드 영역은 프로그램 전체에서 사용하는 공용 공간이다. 클래스 변수는 해당 클래스가 JVM에 로딩되는 순간 생성된다. 그리고 JVM이 종료될 때 까지 생명주기가 어어진다. 따라서 가장 긴 생명주기를 가진다.

### 클래스 변수에 static이 붙는 이유 🔆

`static` 이 정적이라는 이유는 바로 위 클래스 변수에 대한 설명에 답이 있다. **_힙 영역에 생성되는 인스턴스 변수는 동적으로 생성되고 제거된다. 반면에 `static` 인 정적 변수는 거의 프로그램 실행 시점에 딱 만들어지고, 프로그램 종료 시점에 제거된다. 정적 변수는 이름 그대로 정적이다._**



&nbsp;

---

# null과 GC

이번 챕터에서는 null에 대해서 알아보자. **_null 값은 참조형 변수에서 아직 가리키는 대상이 없으면 넣는 값으로, 값이 존재하지 않다는 걸 말한다._** 비유하자면 택배를 보내기 위해서 주소지에 주소를 입력해야하는데, 아직 결정되지 않아서 주소지를 공란으로 냅둔 상태로 볼 수 있다.  

코드로 확인해보자.

- 클래스
    
    ```java
    public class Data {
        int value;
    }
    ```

- 인스턴스 및 main
    
    ```java
    public class DataMain {
        public static void main(String args[]) {
            Data data = null;
            System.out.println("1. data = " + data);
            data = new Data();
            System.out.println("2. data = " + data);
            data = null;
            System.out.println("3. data = " + data);
        }
    }
    ```

- 실행 결과
    
    ```java
    1. data = null
    2. data = Week5.Data@17f6480
    3. data = null
    ```

위 코드에서 확인한 것처럼 인스턴스 참조값을 받은 변수에 다시 null을 할당받을 경우, 기존에 생성한 인스턴스는 어떻게 될까?

이 인스턴스를 참조하는 변수는 존재하지 않는다. 아무도 참조하지 않는 상황이다. 이런 상황이 되면 이 인스턴스의 참조값을 다시 구할 방법은 없다. **_해당 인스턴스에 다시 접근할 방법이 존재하지 않는다. 이런 경우 메모리의 입장에서는 사용되지 않고 메모리 공간만 차지하기 때문에 불필요하다. 그래서 JVM의 GC(Garbage Collector)가 더 이상 사용하지 않는 인스턴스라고 판단되면 자동으로 메모리에서 제거해준다. 힙 영역에 생성되었다가 제거된다._**    

하지만, 어딘가에서 한 군데라도 계속 참조하고 있다면 계속 힙 영역에서 계속 생존한다.  

### 인스턴스를 참조하는 static 변수  
static 변수는 뒤에서 설명하겠지만 한 번 생성되면 프로그램이 종료될 때까지 유지된다. 이 static 변수가 특정 인스턴스의 참조값을 가지고 있다면(참조하고 있다면) 이 인스턴스는 프로그램이 종료될 때까지 GC에 의해서 제거되는 일이 발생되지 않는다. 

하지만 이 static 변수에 null을 할당하면 참조되었던 인스턴스는 다른 변수가 참조하는 일이 없기 때문에 GC에 의해서 삭제된다.


&nbsp;

---

# NullPointerException

다음으로 Null과 관련된 에러인 `NullPointerException`에 대해 알아보자. 

참조값이 존재한다고 판단하여 참조값을 통해 특정 값에 접근했는데, 사실 그 참조값은 존재하지 않는 `null`이어서 발생되는 에러다. 

코드로 화인해보자.  

- 클래스

    ```java
    public class Data {
        int value;
    }
    ```

- 인스턴스 및 main

    ```java
    public class DataMain {
        public static void main(String args[]) {
            Data data = null;
            System.out.println("1. data = " + data);
            data.value = 1;
        }
    }
    ```

- 결과

    ```java
    1. data = null
    Exception in thread "main" java.lang.NullPointerException
        at Week5.DataMain.main(DataMain.java:7)
    ```


코드에서 알 수 있듯이 `null`은 참조할 주소가 존재하지 않는다는 뜻이다. 이 null에 접근하여 `value` 값에 값을 할당하려고 했으니 위와 같은 결과가 발생했다.  

단지 변수에 `null` 을 할당하는 것까지는 문제가 발생되지 않는다. **_null을 할당받은 변수를 통해 속성과 행위에 접근하려고할 때 에러가 발생된다._**



&nbsp;

---


# final  

`final`로 선언된 클래스 변수와 인스턴스 변수 모두 클래스 안에 선언된 정보이기 때문에 '메서드 영역'에 생성된다. 