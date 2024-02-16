---
title: "자바의 클래스는 메모리 영역에서는 어떻게 생성될까?"
date: 2024-01-26T00:32:17+09:00
draft: true
summary: 메모리의 각 메서드 영역, 스택 영역, 힙 영역에 무엇이 생성되는지 알아보고,  NullPointerException이 왜 발생하는지 알아본다.  
tags: ["java"]
categories: "java"
---


`static` 영역은 단 하나만 존재하는 영역이므로 중복과 메모리 비효율 문제를 모두 해결할 수 있다.  (?) 
- 해당 클래스 안에 하나의 영역에만 존재한다는 건가 아니면 메모리 전체에서 단 하나만 존재한다는 건가?


배열, 객체: 동적 메모리 할당  


# 자바의 메모리 구조 

자바의 메모리 구조가 어떻게 구성되었는지 먼저 설명하고, 코드를 실행할 때 각 영역에 어떻게 올라가는지 그림으로 확인해보자.

JVM이 아래와 같은 메모리 영역을 가지고 실행한다.  

자바의 메모리 구조는 메서드(method) 영역, 스택(stack) 영역, 힙(heap) 영역 3개로 나눌 수 있다.  

아래 내용의 출처: [김영한의 실전 자바 - 기본편](https://www.inflearn.com/course/%EA%B9%80%EC%98%81%ED%95%9C%EC%9D%98-%EC%8B%A4%EC%A0%84-%EC%9E%90%EB%B0%94-%EA%B8%B0%EB%B3%B8%ED%8E%B8/dashboard)

- 메서드 영역: 프로그램을 실행하는데 필요한 공통 데이터를 관리한다. 이 영역은 프로그램의 모든 영역에서 공유한다.  
    - 클래스 정보:  클래스의 실행 코드, 필드, 메서드, 생성자 코드등 모든 실행 코드 존재  
    - static 영역: `static` 변수들을 보관한다.  
    - 런타임 상수 풀: 프로그램을 실행하는데 필요한 '공통 리터럴 상수'를 보관한다.  (자바의 최적화 영역)

- 스택 영역: 자바 실행 시, 하나의 실행 스택이 생성된다. 각 스택 프레임은 지역 변수, 중간 연산 결과, 메서드 호출 정보 등을 포함한다.  
    - 스택 프레임: 스택 영역에 쌓이는 네모 박스가 하나의 스택 프레임이다. 메서드를 호출할 때마다 하나의 스택 프레임이 쌓이고, 메서드가 종료되면 해당 스택 프레임이 제거된다.  
    - 각 쓰레드별로 하나의 실행 스택이 생성된다. 쓰레드 수 만큼 스택 영역이 생성된다. 

- 힙 영역: 객체(인스턴스)와 배열이 생성되는 영역이다. GC가 이루어지는 주요 영역이며, 더 이상 참조되지 않는 객체는 GC에 의해 제거된다.  


그러면 위 내용을 이해하기 위해 실제 코드가 실행되면 어느 영역에 무엇이 올라가는지 확인해보자.
[클래스 메서드(정적, static 메서드)와 인스턴스 메서드](https://jeha00.github.io/post/java/2_1_class/#%ED%81%B4%EB%9E%98%EC%8A%A4-%EB%A9%94%EC%84%9C%EB%93%9C%EC%A0%95%EC%A0%81-static-%EB%A9%94%EC%84%9C%EB%93%9C%EC%99%80-%EC%9D%B8%EC%8A%A4%ED%84%B4%EC%8A%A4-%EB%A9%94%EC%84%9C%EB%93%9C)에서 사용한 코드를 다시 가져와 메모리 관점에서 이해해보자.


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


&nbsp;

---


# null과 GC

&nbsp;

---

# NullPointerException

&nbsp;

---


# 
