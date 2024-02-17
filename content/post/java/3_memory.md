---
title: "자바의 클래스는 메모리 영역에서는 어떻게 생성될까?"
date: 2024-01-26T00:32:17+09:00
draft: false
summary: 메모리의 각 메서드 영역, 스택 영역, 힙 영역에 무엇이 생성되는지 알아보고,  NullPointerException이 왜 발생하는지 알아본다.  
tags: ["java"]
categories: "java"
---


`static` 영역은 단 하나만 존재하는 영역이므로 중복과 메모리 비효율 문제를 모두 해결할 수 있다.  (?) 
- 해당 클래스 안에 하나의 영역에만 존재한다는 건가 아니면 메모리 전체에서 단 하나만 존재한다는 건가?


배열, 객체: 동적 메모리 할당  




# 자바의 메모리 구조 

이번 포스팅에서는 자바의 메모리 구조가 어떤 영역으로 구성되어 있고, 자바의 메서드와 변수들이 이 메모리에 어떻게 생성되고 사라지는지 알아본다.  

### 메모리 영역의 구성과 각 영역의 역할  

JVM은 아래와 같은 메모리 영역을 가지고 실행한다.  

자바의 메모리 구조는 메서드(method) 영역, 스택(stack) 영역, 힙(heap) 영역 3개로 나눌 수 있다.  

아래 내용의 출처: [김영한의 실전 자바 - 기본편](https://www.inflearn.com/course/%EA%B9%80%EC%98%81%ED%95%9C%EC%9D%98-%EC%8B%A4%EC%A0%84-%EC%9E%90%EB%B0%94-%EA%B8%B0%EB%B3%B8%ED%8E%B8/dashboard)

- 메서드 영역: 프로그램을 실행하는데 필요한 공통 데이터를 관리한다. 이 영역은 프로그램의 모든 영역에서 공유한다.  
    - 클래스 정보:  클래스의 실행 코드, 필드, 메서드(인스턴스 메서드, 클래스 메서드 포함), 생성자 코드등 모든 실행 코드 존재  
    - static 영역: `static` 변수들을 보관한다.  
    - 런타임 상수 풀: 프로그램을 실행하는데 필요한 '공통 리터럴 상수'를 보관한다.  (자바의 최적화 영역)

- 스택 영역: 자바 실행 시, 하나의 실행 스택이 생성된다. 각 스택 프레임은 지역 변수, 중간 연산 결과, 메서드 호출 정보 등을 포함한다.  
    - 스택 프레임: 스택 영역에 쌓이는 네모 박스가 하나의 스택 프레임이다. 메서드를 호출할 때마다 하나의 스택 프레임이 쌓이고, 메서드가 종료되면 해당 스택 프레임이 제거된다.  
    - 각 쓰레드별로 하나의 실행 스택이 생성된다. 쓰레드 수 만큼 스택 영역이 생성된다. 

- 힙 영역: 객체(인스턴스)와 배열이 생성되는 영역이다. GC가 이루어지는 주요 영역이며, 더 이상 참조되지 않는 객체는 GC에 의해 제거된다. 



### 동적 메모리 할당
**_객체와 배열이 힙 영역에 생성되는 이유는 객체와 배열같은 참조형은 기본형과 달리 크기가 동적으로 바뀌기 때문이다. 이를 '동적 메모리 할당'이라 한다. 그래서 기본형과 달리 복잡한 데이터 구조를 만들고 관리할 수 있다._** 

하지만 기본형은 사용할 값을 직접 저장하는 이유는 크기가 정해져 있기 때문이다. 이에 따라 더 빠르게 메모리를 관리할 수 있지만 참조형처럼 복잡한 데이터 구조를 관리할 수 없다.  


### 메서드에 대한 메모리 할당

같은 클래스로 부터 생성된 객체라도, 인스턴스 내부의 변수 값은 서로 다를 수 있지만, 메서드는 공통된 코드를 공유한다. 따라서 객체가 생성될 때, 인스턴스 변수에는 메모리가 할당되지만, 메서드에 대한 새로운 메모리 할당은 없다. 

메서드는 메서드 영역에서 공통으로 관리되고 실행된다. 인스턴스 메서드도 메서드 영역에 생성되지만, 인스턴스 생성 시 메서드 영역에 만들어진 메서드를 연결해서 사용한다. 

메서드 영역 안에 있는 클래스 정보를 바탕으로 new 연산자를 만날 때마다 힙 영역에 새로운 인스턴스를 만든다. 그리고, 인스턴스마다 각각 멤버변수를 가지고 있다. 




&nbsp;

## 스택 영역과 힙 영역 이해하기  

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


### 정리

위 내용을 정리하면 다음과 같다. 자바는 스택 영역을 사용해서 메서드 호출과 지역 변수(매개변수 포함)를 관리한다.

- 메서드를 계속 호출하면 이 메서드에 대한 스택 프레임이 추가되면서 스택 프레임이 쌓인다.  
- 지역 변수(매개변수 포함)는 스택 영역에서 관리한다.  
- 스택 프레임이 종료되면 지역 변수도 함께 제거된다.  
- 스택 프레임이 모두 제거되면 프로그램도 종료된다.

&nbsp;

## 메서드 영역까지 합쳐서 이해하기

스택 영역과 힙 영역에 대해 이해했으니 메서드 영역까지 합쳐서 이해해보자. [클래스 메서드(정적, static 메서드)와 인스턴스 메서드](https://jeha00.github.io/post/java/2_1_class/#%ED%81%B4%EB%9E%98%EC%8A%A4-%EB%A9%94%EC%84%9C%EB%93%9C%EC%A0%95%EC%A0%81-static-%EB%A9%94%EC%84%9C%EB%93%9C%EC%99%80-%EC%9D%B8%EC%8A%A4%ED%84%B4%EC%8A%A4-%EB%A9%94%EC%84%9C%EB%93%9C)에서 사용한 코드 중 일부를 가져와 메모리 관점에서 이해해보자.

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
            System.out.println("Car.count = " + Car.count);
            System.out.println("Car name = " + car1.getName());
            method1();
            System.out.println(" ========== main end ==========");
        }

        public static void method1() {
            System.out.println("========== method1 start ==========");
            Car car2 = new Car("k5", 20000);
            System.out.println("Car.count = " + Car.count);
            System.out.println("Car price = " + car2.getPrice());
            method2();
            System.out.println("========== method1 end ==========");
        }

        public static void method2() {
            System.out.println("========== method2 start ==========");
            Car car3 = new Car("k7", 30000);
            Car.callClassMethod();
            System.out.println("Car price = " + car3.getPrice());
            System.out.println("Car name = " + car3.getName());
            System.out.println("========== method2 end ==========");
        }
    }
    ```



main frame -> CarMain의 method1 호출: 인스턴스 생성 -> method2 호출: 인스턴스 생성 + callInstanceMethod 호출 -> getName, getCost 호출로 코드를 작성하자.

그래서 왼쪽부터 오른쪽으로 스택 프레임 쌓이면서 중간 중간 힙 영역에 메서드 생성되는 것 설명
각 메서드 호출 종료될 때마다 스택 프레임 사라지고, 인스턴스 참조되는 게 사라지면 인스턴스도 삭제되는 것 그림으로 설명  

그리고 힙 영역에 인스턴스 생성될 때마다 메서드 영역의 클래스 변수 count가 증가되는 것도 표시

인스턴스 메서드에서 클래스 메서드 접근하는 것도 표시 

추가로 클래스 메서드에서 인스턴스 메서드에 접근하는 게 왜 안되는 지도 표시  
- 특정 인스턴스의 기능을 사용하려면 참조값을 알아야 하는데, 정적 메서드는 참조값 없이 호출한다.

&nbsp;

---

# 변수와 생명 주기

그러면 위 학습한 내용을 변수의 관점에서 정리해보자.  

### 지역변수(매개변수 포함)
위 내용에서 학습한 대로 지역 변수는 스택 영역에 있는 스택 프레임 안에 보관된다. 호출된 메서드가 종료되면 스택 프레임도 제거 되는데 이때 해당 스택 프레임에 포함된 지역 변수도 함께 제거된다. 따라서 지역 변수는 생존 주기가 짧다.

### 인스턴스 변수
인스턴스에 있는 멤버 변수를 인스턴스 변수라 한다. 인스턴스 변수는 힙 영역을 사용한다. 힙 영 역은 GC(가비지 컬렉션)가 발생하기 전까지는 생존하기 때문에 보통 지역 변수보다 생존 주기가 길다. 이 인스턴스 객체에 대한 참조 횟수가 0이 되면 GC에 의해서 삭제된다.  

### 클래스 변수
클래스 변수(static 변수, 정적 변수)는 메서드 영역의 static 영역에 보관되는 변수이다. 메서드 영역은 프로그램 전체에서 사용하는 공용 공간이다. 클래스 변수는 해당 클래스가 JVM에 로딩 되는 순간 생성된다. 그리고 JVM이 종료될 때 까지 생명주기가 어어진다. 따라서 가장 긴 생명주기를 가진다.

### 클래스 변수에 static이 붙는 이유 🔆

`static` 이 정적이라는 이유는 바로 위 클래스 변수에 대한 설명에 답이 있다. **_힙 영역에 생성되는 인스턴스 변수는 동적으로 생성되고 제거된다. 반면에 `static` 인 정적 변수는 거의 프로그램 실행 시점에 딱 만들어지고, 프로그램 종료 시점에 제거된다. 정적 변수는 이름 그대로 정적이다._**



&nbsp;

---
# null과 GC

null은 참조형 변수에는 


&nbsp;

---

# NullPointerException

&nbsp;

---


# 

