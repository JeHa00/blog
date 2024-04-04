---
title: "자바에서의 변수는 어떨까?"
date: 2024-01-20T17:06:13+09:00
draft: false
summary: 자바에서의 변수는 파이썬과 어떻게 다른지 알아보자. 
tags: ["java"]
categories: "java"
---

## 변수 생성 관점에서의 차이  

Java는 python과 달린 선언과 초기화 작업이 나눠진다. 

```java
package variable;

public class Variable {
    public static void main(String[] args){
        // 변수 선언
        int a;

        // 변수 초기화
        a = 1;
    }
}
```

하지만 파이썬은 다음과 같이 동시에 진행된다. 

(놀라지 마세요. 변수 사용하는데 필요한 모든 걸 다 작성했습니다.)

```python
a = 1
```

하지만 메모리 관점에서 보면 매커니즘은 다르다.


자바는 선언한 데이터 타입에 맞는 데이터 크기만큼 메모리에 a라는 자리를 만든 후, 이 a라는 자리에 1을 담는다.

하지만 파이썬은 1이라는 정수 객체를 생성한 후, a 변수가 이 객체를 가리키도록 한다. 

자바는 특정 이름을 가진 그릇을 먼저 만든 후 값을 담는다면 파이썬은 먼저 만들어진 값을 가진 그릇에 특정 이름으로 라벨링한다는 그림이다. [[TIL] Python basic 40: Call by object reference](https://jeha00.github.io/post/python/python_basic_40_callbyobjectreference/#1-object-reference)을 참고하자.


---

## 초기화 작업이 필요한 이유?

그러면 왜 초기화 작업이 필요할까?

변수 초기화를 해야하는 이유는 변수를 선언한 메모리 위치에 '의미 없는 그리고 의도하지 않는 값'이 담겨진다. 그래서 이상한 값이 출력될 수 있다. 이 문제를 예방하기 위해서 자바는 _변수 초기화를 강제한다._ 

그래서 자바에서는 초기화하지 않은 변수를 사용할 경우 컴파일 에러가 발생한다. 

```java
package variable;

public class Variable {
    public static void main(String[] args){
        // 변수 선언
        int a;
        
        System.out.println(a); 
    }
}
```

위 코드를 실행해보면 `java: variable a might not have been initialized` 이와 같은 컴파일 에러가 발생한다.  

파이썬을 다루면서 몰랐지만 컴파일 언어의 컴파일 과정에서 에러를 미리 잡아주는 건 너무 좋은 것 같다.  

그래서 이걸 학습한 관점에서 파이썬을 봤을 때 파이썬에서는 별도의 초기화 작업이 없는 이유가 내 셍긱이지만 어차피 초기화를 반드시 해야해서 그런 게 아닌가 싶다.


---

## 항상 초기화를 직접 해줘야 하나?

위 자바 코드를 보면 개발자가 직접 초기화 작업을 진행했다. 

그러면 매번 이렇게 해야할까? 

> **_변수의 종류에 따라 초기화 작업 유무가 결정되는데 지역 변수인 경우 개발자가 직접 초기화를 해주어야 한다. 하지만, 멤버변수는 자바가 자동으로 초기화를 진행해준다._**

### 변수의 종류  

변수의 종류에는 2가지가 있다.

- 지역 변수(Local Variable): Local scope에 선언된 변수로 그 예에는 메서드 바디에 선언된 변수와 메서드의 매개변수가 있다.
- 멤버 변수(필드): 클래스에 선언된 변수로 그 예에는 클래스 변수, 인스턴스 변수가 있다.


지역 변수의 초기화 예시는 위에서 확인해봤으니 클래스 변수의 예를 확인해보자. 

Student 클래스를 하나 생성하고, 이 클래스의 인스턴스를 하나 만들어보자.

- 클래스
    ```java
    package variable;

    public class Student {
        int number;
        float grade;
        String name;
        boolean graduated;
    }
    ```

- 인스턴스
    ```java
    package variable;

    public class StudentMain {
        public static void main(String[] args) {
            Student student = new Student();
            System.out.println("grade: " + student.grade);
            System.out.println("name: " + student.name);
            System.out.println("number: " + student.number);
            System.out.println("graduated: " + student.graduated);
        }
    }
    ```


위 인스턴스를 출력하면 다음과 같다.

```
grade: 0.0
name: null
number: 0
graduated: false
```

초기화 작업을 하지 않았어도 int는 0으로, float는 0.0으로 string은 null로, boolean은 false 초기화가 되어 있는 걸 알 수 있다. 

### 그래서 멤버변수의 자동 초기화값은 다음과 같다.

- `int`: 0
- `float`: 0.0
- `String`: null
- `boolean`: false

---


## 변수 명명법

다음으로 자바에서 변수 컨벤션은 무엇일까?

파이썬에서는 클래스는 PascalCase, 변수는 snake나 lowerCamelCase를 사용한다. 하지만 상수의 경우 대문자로만 입력하여 단어 구분은 언더바(_)를 사용한다.

자바 또한 이와 비슷하다.

클래스는 UpperCamelCase (PascalCase)를 사용하고, 변수를 포함한 나머지는 lowerCamelCase를 사용한다.    

- 상수의 경우 snake case처럼 언더바(_)를 사용하지만 대문자로만 입력한다.  
- 패키지는 모두 소문자를 사용한다.  

---

## 변수의 종류

그러면 자바에서 변수의 종류와 범위는 어떻게 될까?

먼저 파이썬에서는 어떤지 간단히 리뷰를 해보자. 

파이썬에서는 변수의 값은 하나 하나가 객체이기 때문에 그 종류가 정말 무수히 많다. 그래도 기본적인 타입들만 언급하자면 다음과 같다.  

- 정수형(int), 실수형(float), 복소수(complex), 참거짓(boolean), 문자열(string, ""와 ''모두 사용가능)

- 그리고 데이터를 담는 container의 관점에서 정말 기본적인 것만 보자면 list, tuple, dictionary, tuple 이 있다.  

하지만 다시 말하자면 파이썬에서 모든 값들은 다 객체이기 때문에 매우 많다. 위 기본타입을 바탕으로 발전된 것들도 많다.  

자바에서는 다음과 같다.  

**[값의 종류를 기준으로 나눈 변수의 종류]**

- 정수형
    - `byte`: -128 ~ 127 (1byte, 2의 8승)
    - `short` : -32,768 ~ 32,767 (2byte, 2의 16승)
    - `int` : -2,147,483,648 ~ 2,147,483,647 (약 20억) (4byte, 2의 32승)
    - `long` : -9,223,372,036,854,775,808 ~ 9,223,372,036,854,775,807 (8byte, 2의 64승)
        - 숫자를 입력할 때 `int` 범위를 넘는 값을 입력할 때는 `L` 을 입력해야 한다. 소문자도 가능하지만 1과 헷갈리므로 대문자로만 입력하자.   
        - ex: 12345678910L
- 실수형
    - `float` : 대략 -3.4E38 ~ 3.4E38, 7자리 정밀도 (4byte, 2의 32승) 
    - `double` : 대략 -1.7E308 ~ 1.7E308, 15자리 정밀도 (8byte, 2의 64승)
        - `long` 과 동일하게 끝에 `f`를 붙여야 한다.
         - ex: 1233123.023f
- 기타
    - `boolean`: true, false (1byte, 2의 8승)
    - `char`: 문자 하나 (2byte, 2의 16승(8 x 2) ) + 홀따옴표('') 사용
    - `String`: 문자 길이에 따라 메모리 사용량이 동적으로 달라진다 + 쌍따옴표("")를 사용


하지만 실무에서 실제로 사용하는 것들은 `boolean`, `int`, `double`, `String` 이라 한다.
`short`로는 너무 범위가 좁고, `float`로는 정밀도가 낮기 때문이다.  그리고, `char` 처럼 문자 하나만을 표현하는 일은 거의 없기 때문이다.  

모든 변수 종류를 모두 암기하기는 어렵다. 하지만 자주 사용하는 변수 종류들의 범위만이라도 외워두자.  

---

## 자바는 변수를 어떻게 전달할까?

변수 종류를 나누는 기준으로는 위에 언급된 것처럼 '값의 종류' 뿐만 아니라 '값을 전달하는 방식'에 따라 나눠지기도 한다. 

> _바로 '기본형(Primitive Type)' 과 '참조형(Reference Type)' 이다._  


- 기본형: `int`, `long`, `boolean` 처럼 변수에 사용할 값을 직접 넣는 데이터 타입  
- 참조형: 클래스, 배열과 같이 데이터에 접근하기 위한 참조값(주소)를 저장하는 데이터 타입 

즉, 기본형은 실제 사용하는 값을 변수에 담고, 참조형은 실제 사용하는 값을 변수에 담는 게 아니라 객체 또는 배열의 참조값(주소)을 저장한다.    

참조형의 경우 객체의 경우 `.(dot)` 나 배열의 경우 `[]` 를 통해서 메모리 상에 생성된 객체 또는 배열에 찾아가야 사용할 수 있다.  

### 기본형과 참조형의 연산 

기본형은 실제 값이 들어있기 때문에 그대로 계산에 사용할 수 있지만, 참조형은 실제 값이 아닌 참조값이서 연산에 직접적으로 사용할 수 없다. 주소지에 가야 실체가 있기 때문에, 주소지에 접근해야 사용하라 수 있다. 주소지만 가지고 어떻게 연산이 가능하겠는가?

```java
// 기본형
int a = 10; b = 20;
int sum = a + b;
```
위처럼 기본형 연산은 가능하다. 

```java
public class StudentMain {
    public static void main(String[] args) {
        Student student1 = new Student();
        Student student2 = new Student();
        System.out.println(student1 + student2);
    }

}
```

하지만 만약 참조형으로 연산을 할려고 시도한다면 컴파일 시 다음과 같은 에러를 확인할 수 있다. 

```
java: bad operand types for binary operator '+'
  first type:  Student
  second type: Student
```

만약 참조형이 가지고 있는 값을 계산하고 싶다면 다음과 같이 접근해서 계산해야 한다.

```java
public class StudentMain {
    public static void main(String[] args) {
        Student student1 = new Student();
        Student student2 = new Student();
        
        // 수정된 부분
        System.out.println(student1.grade + student2.grade);
    }
}
```

### 기본형과 참조형의 변수 대입 🔆

위 연산 과정을 통해 기본형과 참조형의 차이를 이해할 수 있었다. 그러면 위 차이가 변수를 대입할 때 어떻게 이뤄지는지 확인해보자.

결론부터 말하자면 다음과 같다.

> **_자바는 항상 변수의 값을 복사해서 대입한다._**


그래서 기본형의 경우 실제 사용하는 값이 복사해서 전달된다. 그런데 참조형의 경우 **_실제 사용하는 객체가 아니라 객체의 위치를 가리키는 참조값만 복사된다. 쉽게 이야기해서 건물이 복사되는 게 아닌 건물의 위치인 주소만 복사되는 것이다._**

그러면 기본형의 경우부터 예시를 보자.

```java
public class Variable {
    public static void main(String[] args) {
        int a = 50;
        int b = a;
        System.out.println();
        System.out.println("a = " + a);
        System.out.println("b = " + b);

        a = 30;
        System.out.println();
        System.out.println("변경 a = 30");
        System.out.println("a = " + a);
        System.out.println("b = " + b);

        b = 10;
        System.out.println();
        System.out.println("변경 b = 10");
        System.out.println("a = " + a);
        System.out.println("b = " + b);
     
        System.out.println();
        System.out.println("메서드 호출 전");
        changeValue(a);
        System.out.println("메서드 호출 후 a = " + a);
    }

    public static void changeValue(int x) {
        x = 100;
    }
}
```

위 코드를 컴파일하면 다음과 같다.

```
a = 50
b = 50

변경 a = 30
a = 30
b = 50

변경 b = 10
a = 30
b = 10

메서드 호출 전
메서드 호출 후 a = 30
```

a의 값을 변경하면 b의 값은 변경되지 않는다. b의 값을 변경해도 a의 값은 변경되지 않는다. 즉, 변수 b에 a를 할당할 때 참조값이 아닌 실제 값을 복사해서 전달한다는 걸 알 수 있다.  만약 참조값을 복사해서 전달한다면 a와 b값 모두 동시에 수정된다.  

메서드를 호출할 때에도 메서드에서는 100으로 수정했지만 `a`의 값을 호출하면 그대로 30이다. 이는 변수에 들어있는 실제 값을 복사해서 전달했다는 걸 알 수 있다. 

다음으로 참조형의 예시를 보자. 똑같이 `Student` 클래스를 사용한다. 

- 클래스
    ```java
    package variable;

    public class Student {
        int number;
        float grade;
        String name;
    }
    ```

- 인스턴스
    ```java
    package variable;

    public class StudentMain {
        public static void main(String[] args) {
            Student student1 = new Student();
            Student student2 = student1;
            Student student3 = new Student();
            System.out.println("student1: " + student1);
            System.out.println("student2: " + student2);
            System.out.println("student3: " + student3);
            
        }
    }
    ```

위 코드를 실행하면 다음 결과를 알 수 있다.

```
student1: Week3.Student@22f71333
student2: Week3.Student@22f71333
student3: Week3.Student@13969fbe
```

각 인스턴스의 참조값이 출력된다. `student2`의 경우 `student1`를 할당하면서 동일한 참조값이 복사 및 전달된 걸 확인할 수 있고, `student3`는 새로 생성했기 때문에 다른 참조값을 가진 걸 알 수 있다.  

현재 `Student`의 인스턴스들에는 기본값으로 초기화되어 있다. 그래서 이를 다른 값으로 수정하려고 한다.  

```java
package variable;

public class StudentMain {
    public static void main(String[] args) {
        Student student1 = new Student();
        Student student2 = student1;
        Student student3 = new Student();
        student1.name = "kim";

        System.out.println("student1.name: " + student1.name);
        System.out.println("student2.name: " + student2.name);
        System.out.println("student3.name: " + student3.name);
            
    }
}
```

위 코드는 name 값만 다른 값으로 초기화했다. 출력하면 다음과 같다.

```
student1.name: kim
student2.name: kim
student3.name: null
```

`student1` 과 `student2`는 동일한 참조값을 가지고 있기 때문에, `student1.name`만 변경했지만 `student2.name`도 변경되었다. 
`student3`는 다른 참조값을 가지고 있기 때문에 그대로 기본값인 `null`을 가지고 있다.  

그러면 메서드를 통해 수정할 때는 어떨까?

```java
package variable;

public class StudentMain {
    public static void main(String[] args) {
        Student student1 = new Student();
        Student student2 = student1;
        Student student3 = new Student();

        System.out.println("변경 전 student1.grade: "+ student1.grade);
        changeGradeValue(student1);
        System.out.println("변경 후");
        System.out.println("student1.grade: " + student1.grade);
        System.out.println("student2.grade: " + student2.grade);
        System.out.println("student3.grade: " + student3.grade);
    }
    
    public static void changeGradeValue(Student student) {
        student.grade = 100;
    }
}
```

`changeGradeValue` 메서드를 추가했다. 위 코드를 실행하면 다음과 같다.

```
변경 전 student1.grade: 0.0
변경 후
student1.grade: 100.0
student2.grade: 100.0
student3.grade: 0.0
```

`student1` 객체만 `changeGradeValue`에 전달했지만 `student2.grade`의 값도 변경되었다. 즉, 동일한 참조값을 참조한다는 걸 알 수 있다.  

그래서 객체를 매개변수로 전달할 때에는 이 원리를 항상 유의해야 한다.  


자바 변수에서 제일 중요한 원칙을 다시 읽어보며 이번 포스팅을 끝내도록 한다.



> 🫡 **_자바는 항상 변수의 값을 복사해서 대입한다._**