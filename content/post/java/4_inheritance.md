---
title: "상속과 오버라이딩이 메모리 영역에서 어떻게 이뤄지지?"
date: 2024-01-30T00:35:20+09:00
draft: false
summary: 자바에서는 상속과 오버라이딩을 어떻게 만드는지와 메모리 영역에서 어떻게 작동되는지 알아본다.  
tags: ["java"]
categories: "java"
---

# 상속에 대해서  

### 상속, 부모 클래스, 자식 클래스란?

**_기존 클래스의 필드와 메서드를 새로운 클래스에서 재사용하도록 물려받는 것을 '상속'이라 한다._** 

이 상속을 통해서 자신의 필드와 메서드를 제공하는 클래스를 **_부모 클래스 (슈퍼 클래스)_**, 부모 클래스로부터 필드와 메서드를 상속받는 클래스를 **_자식 클래스 (서브 클래스)_** 라 한다.  

자바에서 상속은 `extends` 라는 키워드를 통해서 수행된다. 이 키워드를 통한 상속은 오직 하나의 클래스만 받을 수 있다. '다중 상속'은 불가능하다. (하지만 이 부분을 해결하는 방법이 있다. 이는 나중에 학습하자.)

상속의 이점에 대해 알아보자.

아래에 상속을 받지 않는 `TomatoPasta` 와 `BaconCreamPasta`가 있다. 

- `TamatoPasta` 클래스
    
    ```java
    public class TomatoPasta {

        int price;
        String name;
        String sauce;

        public void cookTomato() {
            System.out.println("토마토를 조리합니다.");
        }

        public void complete() {
            System.out.println("조리를 완료합니다.");
        }

        public void serve() {
            System.out.println("완료된 음식을 서빙합니다.");
        }
    }
    ```



- `BaconCreamPasta` 클래스

    ```java
    public class BaconCreamPasta {
        
        int price;
        String name;
        String sauce;

        public void cookBacon() {
            System.out.println("베이컨을 조리합니다.");
        }

        public void makeCream() {
            System.out.println("크림 소스를 만듭니다.");
        }

        public void complete() {
            System.out.println("조리를 완료합니다.");
        }

        public void serve() {
            System.out.println("완료된 음식을 서빙합니다.");
        }
    }
    ```

위 2개의 클래스에 상속을 적용해보자.  

상속의 부모 클래스로 `Pasta` 를 만든다. `Pasta`를 한 이유는 토마토 파스타와 베이컨 크림 파스타 모두 파스타의 구체적인 개념이고, `Pasta`는 이 두 파스타의 추상적인 개념이기 때문이다.  

- `Pasta` 클래스

    ```java
    public class Pasta {

        int price;
        String name;
        String sauce;

        public void complete() {
            System.out.println("조리를 완료합니다.");
        }

        public void serve() {
            System.out.println("완료된 음식을 서빙합니다.");
        }
    }
    ```

- `TamatoPasta` 클래스

    ```java
    public class TomatoPasta extends Pasta {

        public void cookTomato() {
            System.out.println("토마토를 조리합니다.");
        }
    }
    ```

- `BaconCreamPasta` 클래스

    ```java
    public class BaconCreamPasta extends Pasta {

        public void cookBacon() {
            System.out.println("베이컨을 조리합니다.");
        }

        public void makeCream() {
            System.out.println("크림 소스를 만듭니다.");
        }
    }
    ```

모든 파스타에서 공통으로 가지는 속성과 기능은 `Pasta` 클래스에서 전달하고, 구체적인 파스타마다 가지는 필드와 기능은 자식 클래스에서 가진다.  

그래서 **_부모가 가지는 것은 상속을 통해 자식도 당연히 가지고 있지만, 자식이 가지고 있는 건 부모가 가질 수 없다._** 

&nbsp;

### 상속과 기능 추가  

그러면 상속의 장점을 느껴보자. 부모 클래스를 상속받은 자식 클래스 세 개가 있다. 

이 자식 클래스에 A라는 메서드와 B라는 필드를 추가할려고 할 때, 상속을 사용하지 않는다면 각 자식 클래스마다 A 메서드와, B 필드를 입력해야 한다. 하지만, '상속'을 사용한다면 자식 클래스가 상속 받은 부모 클래스에 추가한다면 손쉽게 확장할 수 있다.  

예시 코드를 통해 확인해보자. 앞선 예시와 동일한 코드에 속성과 기능을 하나씩 추가하려고 한다. 

- 속성: 면의 종류 정보를 가지고 있는 `noodle` 
- 기능: 면을 삶는 기능인 `cookNoodles()`

상속을 통해 `TomatoPasta`와 `BakonCreamPasta` 클래스에 각각 추가하지 않고 `Pasta` 클래스에 추가한다.  

- `Pasta` 클래스

    ```java
    public class Pasta {

        int price;
        String name;
        String sauce;
        String noodle;  // 추가된 속성

        // 추가된 기능
        public void cookNoodles() {
            System.out.println("면을 삶습니다.")
        }

        public void complete() {
            System.out.println("조리를 완료합니다.");
        }

        public void serve() {
            System.out.println("완료된 음식을 서빙합니다.");
        }
    }
    ```

나머지 `TomatoPasta`와 `BakonCreamPasta` 클래스들은 동일하다.  

만약 새로운 파스타 종류를 추가한다면 `Pasta`를 상속받아 새로 만드는 파스타만의 속성과 기능만 추가하면 된다.  

- 새로 추가하는 `GarlicAndOliveOilPasta` 클래스

    ```java
    public class GarlicAndOliveOilPasta extends Pasta {

        public void makeGarlicOilSauce() {
            System.out.println("마늘과 올리브 오일을 사용해서 소스를 만듭니다.");
        }
    }
    ```


---


# 상속과 메모리 구조 🔆

우리는 앞선 포스팅에서 JVM의 메모리 영역에 대해서 학습했다. 

어떻게 메모리에서 상속이 어떻게 구현되고 작동되는지 알아보자.

위 예시 코드를 기반으로 각 파스타에 대한 인스턴스를 생성한 `PastaMain` 코드가 있다.

- `PastaMain` 클래스  

    ```java
    public class PastaMain {
        public static void main(String[] args) {
            TomatoPasta pasta1 = new TomatoPasta();

            pasta1.cookTomato();
            pasta1.complete();
        }
    }
    ```

그리고 `TomatoPasta` 만의 속성으로 `tomatoOrigin` 을 추가한다. 이 속성의 값은 `Korea`로 고정시킨다. 이러면 클래스 변수로 설정하는 게 좋지만, 상속과 메모리 구조 이해를 위해 한다.  

- `TomatoPasta` 클래스

    ```java
    public class TomatoPasta extends Pasta {

        String tomatoOrigin = "Korea";

        public void cookTomato() {
            System.out.println("토마토를 조리합니다.");
        }
    }
    ```

위 코드가 실행되면 다음 그림에 나온 순서처럼 실행된다.  

{{<figure src="https://github.com/JeHa00/image/assets/78094972/553a5023-c06e-4212-9de2-3d3659d17131" width="90%">}}

&nbsp;

### 인스턴스에는 부모와 자식 클래스 정보가 모두 포함되어 있고, 영역이 구분된다.  

**_인스턴스 참조값이 담겨진 참조 변수의 참조값은 하나이지만, 실제로 그 안에는 부모 클래스와 자식 클래스 정보가 공존한다. 즉, 부모 클래스까지 포함해서 인스턴스를 생성한다._** 그래서 상속이라고 하여 단순히 물려받는게 아니다. **_부모와 자식 클래스의 인스턴스 정보가 모두 생성되고 메모리 영역에서 공간도 구분된다._**  부모 클래스의 멤버 필드의 값 정보는 인스턴스의 부모 공간에 생성되고, 자식 클래스의 멤버 필드의 값 정보는 인스턴스의 자식 공간에 생성된다.

위 내용을 바탕으로 다시 위 그림을 보자. `pasta1` 참조 변수가 `TomatoPasta` 클래스의 인스턴스 참조값을 가지고 있다. 이 참조값이 가리키고 있는 heap 영역을 보면 생성된 인스턴스 내부에는 부모 클래스인 `Pasta`의 영역과 자식 클래스인 `TomatoPasta`의 영역으로 나눠져 있는 걸 확인할 수 있다. 그래서 단지 `TomatoPasta`의 인스턴스 속성만 있는 게 아니라, `Pasta`의 인스턴스 속성도 있는 걸 확인할 수 있다. 

&nbsp;

### 메모리 공간 크기

그래서 메모리 공간 크기의 관점에서 보자면 부모 클래스의 필드와 속성까지 생성되기 때문에 메모리 공간이 더 필요하다.    

위 내용을 바탕으로 다시 위 그림을 보면 왜 메모리 공간 크기가 더 필요한지 직관적으로 받아들여질 수 있다.  

&nbsp;

### 상속 관계 객체 호출 시, 대상 타입 지정

**_인스턴스 내부에 부모 클래스 속성까지 포함해서 생성되기 때문에, 참조 변수 선언 시 어떤 객체 타입으로 할지를 정확하게 지정해줘야 한다._** 왜냐하면 인스턴스에는 부모 클래스에 대한 정보와 자식 클래스에 대한 정보가 모두 포함되어 있기 때문에, 어느 클래스부터 조회할지를 결정해야 한다. 이를 결정하는 방법이 바로 호출하는 변수의 타입(클래스)을 기준으로 선택하기 때문이다.

위 내용을 바탕으로 그림을 다시 보면 참조 변수 `pasta1`을 선언할 때 `TomatoPasta`로 타입을 지정했기 때문에 메서드 실행 시, 첫 번째로 조회하는 클래스 타입이 `TomatoPasta`임을 알 수 있다.  


&nbsp;

### 참조 방향

지정한 본인 타입에서 기능을 찾지 못하면 상위 부모 타입으로 기능을 찾아서 실행한다. **_만약 더 더 상위 부모 타입까지 찾았는데 찾지 못한다면 컴파일 오류가 발생한다. 왜냐하면 참조 방향이 아래에서 위로만 올라가기 때문이다._**  처음에는 본인 타입에서 필요한 필드나 메서드를 찾는다. 없으면 부모 타입에서 찾는다. 이렇게 계속 더 상위 부모에서 필요한 정보를 찾는다.   

위 내용을 바탕으로 그림을 다시 보면 `pasta1.complete()`를 실행할 때 `TomatoPasta` 클래스에서 찾지 못하자 `extends` 예약어를 통해서 부모 클래스를 인식하여 `Pasta` 부모 클래스로 올라가 `complete()` 메서드를 실행한 걸 확인할 수 있다. 

그러면 위 코드를 수정해서 컴파일 오류를 발생시켜보자. 

```java
    public class PastaMain {
        public static void main(String[] args) {
            Pasta pasta1 = new TomatoPasta();

            pasta1.cookTomato();
        }
    }
```

위와 같이 수정했다. 인스턴스는 `TomatoPasta`의 인스턴스이기 때문에 인스턴스에는 부모 클래스와 자식 클래스의 인스턴스 속성 정보가 모두 포함되어 있다. 참조 방향이 위에서 아래로 올라간다면 위 코드는 컴파일 에러가 발생되고, 방향 상관없이 찾는다면 에러가 발생하지 않는다. 

수정하니 다음 같이 컴파일 에러가 발생했다.  

```
java: cannot find symbol
  symbol:   method cookTomato()
```

위 결과를 통해서 **_참조 방향은 반드시 아래에서 위로만 올라간다_** 는 걸 확인했다.  


---
# 오버라이딩(Overriding)

부모에서 정의한 기능을 자식이 그대로 사용하는 경우도 있지만, 자식한테 보다 맞는 방식으로 기능을 재정의하고 싶을 때도 있다. **_부모에게서 상속 받은 기능을 자식이 재정의하는 것을 메서드 오버라이딩(Overriding)이라 한다._** 오버라이딩은 `@Override` 키워드를 사용한다. 

위 사용한 코드를 바탕으로 오버라이딩을 해보자. `Pasta` 클래스의 `serve()` 메서드를 재정의해보자. 

- `TomatoPasta` 클래스
    ```java
    public class TomatoPasta extends Pasta {

        String tomatoOrigin = "Korea";

        public void cookTomato() {
            System.out.println("토마토를 조리합니다.");
        }

        @Override
        public void serve() {
            System.out.println("완료된 토마토 파스타를 바질 잎과 함께 서빙합니다.");
        }


    }
    ```

- `BaconCreamPasta` 클래스 

    ```java
    public class BaconCreamPasta extends Pasta {

        public void cookBacon() {
            System.out.println("베이컨을 조리합니다.");
        }

        public void makeCream() {
            System.out.println("크림 소스를 만듭니다.");
        }

        @Override
        public void serve() {
            System.out.println("완료된 베이컨 크림 파스타에 치즈를 뿌려서 서빙합니다.");
        }
    }
    ```

&nbsp;


### @Override annotation(애노테이션)이 필요한 이유  

`@` 이 붙은 부분을 annotation(애노테이션)이라 하며, '주석'을 의미한다. 하지만 일반적인 주석이 아니라, 프로그램이 읽을 수 있는 특별한 주석이다. 

이 `@Override`를 통해서 상위 클래스의 메서드를 오버라이드하는 것임을 나타낸다. 그러면 상위 클래스의 어떤 메서드를 오버라이드한 건지를 컴파일러가 확인한다. **_오버라이딩 조건을 만족시키지 않으면 컴파일 에러를 발생시켜서 실수로 오버라이딩을 못하는 경우를 방지한다._** 예를 들어 위 코드를 기준으로 보자면 부모 클래스에 `serve()` 메서드가 없다면 컴파일 오류가 발생한다. **_그래서 권장하지만 필수로 이 애노테이션을 사용하자._**

컴파일 에러가 발생하는 경우에 대해 더 이야기해보겠다. 부모의 메서드를 오버라이딩한 자식 메서드를 호출하려고 한다. 그런데 파라미터의 타입을 잘못 지정해서 또는 메서드 이름에 오타가 생겨서 자식 클래스의 메서드가 아닌, 부모 클래스의 메서드를 호출하는 경우를 방지하기 위해 `@Override` 애노테이션을 사용한다. 이 애노테이션이 없어도 오버라이딩은 되지만, 에러를 잡아내지 못하기 때문에 필수로 사용해야 한다.  

&nbsp;

### 오버라이딩과 메모리 구조

애노테이션을 통해서 자식 클래스에 메서드를 재정의했으므로, 참조 변수의 타입이 자식 클래스이면 자식 클래스부터 조회하기 시작하여 부모 타입을 찾지 않고, 원하는 메서드를 찾아 실행된다.  

&nbsp;

### 오버로딩(Overloading)과 오버라이딩(Overriding)의 차이  

오버라이딩은 이미 알아봤으니 오버로딩에 대해서만 설명하겠다.  


메서드 오버로딩(method overloading)은 **_메서드 이름이 같고 매개변수(파라미터)가 다른 메서드를 여러개 정의하는 것을 메서드 오버로딩(Overloading)이라 한다._** 오버로딩은 번역하면 과적인데, 과하게 물건을 담았다는 뜻이다. 따라서 같은 이름의 메서드를 여러 개 정의했다고 이해하면 된다. 

메서드 시그니처에 대해 다시 짚어보자. 메서드 시그니처의 의미는 다음과 같다.

- 메서드 시그니처 = 메서드 이름 + 파라미터의 타입, 순서, 갯수 등의 정보  

오버라이딩은 이름은 같고 파라미터의 타입, 순서, 갯수가 다른 메서드를 만드는 것이기 때문에 결국 메서드 시그니처가 같지 않아 동일한 이름이어도 오버로딩이 가능한 것이다.  

&nbsp;

### 오버라이딩의 조건

메서드 오버라이딩의 조건은 까다롭다. 그러니 **_최소한 메서드 시그니처가 같아야 오버라이딩이 가능하다_** 는 사실부터 시작해보자.  

오버라이딩의 조건을 풀어서 설명하겠다.

- 메서드 이름: 메서드 이름이 같아야 한다.  
- 메서드 매개변수: 매개변수의 타입, 순서, 갯수가 같아야 한다.  

위 내용을 보면 메서드 시그니처가 같아야 한다는 걸 알 수 있다. 여기에 추가로 다음과 같은 조건들이 더 있다.  

- 반환 타입: 반환 타입이 같아야 한다. 단, 반환 타입이 하위 클래스 타입일 수 있다.  
- 접근 제어자: 오버라이딩 메서드의 접근 제어자는 상위 클래스의 메서드보다 더 제한적이면 안된다.  
    - 예를 들어 상위 클래스의 메서드가 `protected`로 선언되어 있으면 오버라이딩된 하위 클래스의 메서드는 `public` 또는 `protected` 로만 가능하다. `private`과 `default`로 선언할 수 없다.  
- `static`, `final`, `private` 키워드가 붙은 메서드는 오버라이딩 될 수 없다.  
    - `static`: 클래스 레벨에서 작동하므로 인스턴스 레벨에서 사용하는 오버라이딩은 의미가 없다. 클래스 이름으로 접근하면 되기 때문이다. 
    - `final`: 이 키워드를 사용하면 메서드 오버라이딩을 금지한다.  
    - `private`: 해당 클래스에서만 접근 가능하기 때문에, 하위 클래스에서 보이지 않아 오버라이딩을 할 수 없다.  
- 생성자: 생성자는 오버라이딩할 수 없다. `super()`를 통해 부모 클래스의 생성자로 값을 전달해야 한다.  

조건들이 위와 같이 많지만 이해하면 크게 어려운 게 없다.  

---

# super

위 생성자 오버라이딩 관련해서 `super` 키워드를 언급했다. 이 키워드는 상속 개념과 관련하여 많이 사용되고, 언급되는 키워드다. 이 키워드에 대해 정리해보자.  

`super` 키워드를 통해서 2가지를 사용할 수 있다. '부모 참조'와 '부모 생성자'다. 



### 부모 참조

'부모 참조'부터 알아보자.

상속과 오버라이딩을 사용한다고 해도 오버라이딩되지 않은 부모의 메서드를 사용하고 싶거나, 부모의 필드값을 사용하고 싶을 때에는 어떻게 해야할까?

부모와 자식의 필드명이 같거나 메서드가 오버라이딩 되어 있으면, 자식에서 부모의 필드나 메서드를 호출할 수 없다. 필드명이 같으면 자식의 필드명으로 먼저 인식되어 부모의 필드를 사용할 수 없고, 메서드가 오버라이딩 되어 있으면 부모의 메서드가 아닌 오버라이딩한 자식의 메서드를 사용한다.  

이럴 때 `this` 처럼 자기 자신 인스턴스를 참조할 때 사용하듯이 부모 클래스를 참조할 때 `super` 키워드를 사용하면 부모를 참조할 수 있다. `super` 는 이름 그대로 부모 클래스에 대한 참조를 나타낸다.


아래 코드를 보자. 부모 클래스의 필드명과 자식 클래스의 필드명 모두에 `name`이 있다.   
메서드도 `serve()` 로 자식 클래스에서 오버라이딩 되어 있다. 이때 자식 클래스에서 부모 클래스의 `name` 과 `serve()` 를 호출하고 싶다면 `super` 키워드를 사용하면 된다.

- `Pasta` 클래스

```java
package extends1.pra;

public class Pasta {

    String name = "Pasta";

    ...

    public void serve() {
        System.out.println("완료된 음식을 서빙합니다.");
    }
}

```

- `TomatoPasta` 클래스

    ```java
    package extends1.pra;

    public class TomatoPasta extends Pasta {

        String name = "TomatoPasta";

        String tomatoOrigin = "Korea";

        ...
    
        @Override
        public void serve() {
            System.out.println("완료된 토마토 파스타를 바질 잎과 함께 서빙합니다.");
        }

        public void call() {
            System.out.println("자식 클래스: " + this.name);
            System.out.println("부모 클래스: " + super.name);
        }
        this.serve();
        super.serve();
    }
    ```

`TomatoPasta pasta1 = new TomatoPasta();` 를 통해서 인스턴스 생성 후, `pasta1.call()`를 실행하면 다음과 결과를 확인할 수 있다.

- 결과

    ```java
    자식 클래스: TomatoPasta
    부모 클래스: Pasta
    완료된 토마토 파스타를 바질 잎과 함께 서빙합니다.
    완료된 음식을 서빙합니다.
    ```

`super`를 통해서 '부모 참조'로 자식 클래스에서 부모 클래스 안에 선언된 것들에 접근할 수 있다는 걸 확인할 수 있다.  하지만 이는 정확히 말하자면 접근 제어자로 허용했기 때문이다. 이에 대해서는 이 포스팅의 맨 마지막 단원에서 확인해본다.  

&nbsp;

### 생성자  

그 다음으로 `super` 키워드를 사용하여 자식 클래스에서 부모 클래스의 생성자를 호출해보자.  

상속과 메모리 구조 단원에서 상속 관계의 인스턴스를 생성하면 메모리의 힙 영역에는 부모와 자식 클래스의 인스턴스 정보가 모두 생성된다는 걸 확인했다. 위 코드 예시를 보자면 `TomatoPasta`를 생성하면 부모인 `Pasta`까지 함께 만들어진다는 것이다. **_따라서 부모와 자식 각각의 생성자가 모두 반드시 호출되어야 한다. 이를 위해서는 자식 클래스의 생성자에서 반드시 부모 클래스의 생성자를 호출해야 한다._**  이럴 때 사용하는 게 바로 `super(...)`다. 

아래 코드를 보자.


- `Pasta` 클래스

    ```java
    public class Pasta {

        ...

        public Pasta() {
            System.out.println("부모 클래스 Pasta 생성자 호출");
        }
        
        ...
    }
    ```

- `TomatoPasta` 클래스

    ```java
    public class TomatoPasta extends Pasta {

        String name;

        public TomatoPasta(String name) {
            super();
            this.name = name;
            System.out.println("자식 클래스 TomatoPasta 호출");
        }
    }
    ```

- `PastaMain` 클래스

    ```java
    public class PastaMain {
        public static void main(String[] args) {
            TomatoPasta pasta1 = new TomatoPasta("TomatoPasta");
        }
    }
    ```

위 `PastaMain` 클래스를 실행하면 다음 결과를 확인할 수 있다.

- 결과

    ```java
    부모 클래스 Pasta 생성자 호출
    자식 클래스 TomatoPasta 호출
    ```

자식 클래스의 생성자 안에 부모 클래스의 생성자를 `super()`를 사용해서 호출한 걸 확인할 수 있다. 

그러면 이번에는 `super()`를 빼고 실행해보자.  

- `TomatoPasta` 클래스

    ```java
    public class TomatoPasta extends Pasta {

        String name;

        public TomatoPasta(String name) {
            // super() 를 주석처리 했다. 
            this.name = name;
            System.out.println("자식 클래스 TomatoPasta 호출");
        }
    }
    ```

다시 `PastaMain`을 실행하면 동일한 결과를 확인할 수 있다. 어째서일까? **_매개변수가 없는 기본 생성자는 생략이 가능하기 때문에 super()를 입력하지 않아도 자동적으로 부모 클래스의 생성자가 호출된다. 기본 생성자를 많이 사용하기 때문에 이런 기능이 추가되었다._**  

그 다음으로 부모의 기본 생성자 호출을 자식 생성자 안에서 첫 줄에 하는 게 아닌 두 번째 줄에다가 해보자.  

- `TomatoPasta` 클래스

    ```java
    public class TomatoPasta extends Pasta {

        String name;

        public TomatoPasta(String name) {
            this.name = name;
            super();
            System.out.println("자식 클래스 TomatoPasta 호출");
        }
    }
    ```

위 코드 상태로 다시 `PastaMain`을 실행하면 다음과 같은 에러를 확인할 수 있다. 

- 결과

    ```java
    java: call to super must be first statement in constructor
    ```

위 결과를 통해서 **_부모 클래스의 생성자를 호출하는 건 자식 클래스 생성자의 첫 번째 줄에 선언해야 한다_** 는 걸 확인할 수 있다. 

그러면 다음으로 기본 생성자가 아닌 사용자 정의 생성자를 사용해보자. 이를 위해서 새로운 코드를 사용하려고 한다. `ClassA`, `ClassB`, `ClassC`를 만들려고 한다. `ClassA`는 `ClassB`의 부모 클래스이고, `ClassB`는 `ClassC`의 부모 클래스다. `super()`를 사용해서 `ClassC`의 생성자에서 `ClassB`의 생성자를, `ClassB`의 생성자에서 `ClassA`의 생성자를 호출할 것이다.  


- `ClassA` 클래스
    
    ```java
    public class ClassA {

        public ClassA(int firstValue) {
            System.out.println("===== ClassA 생성자 호출 =====");
            System.out.println("ClassA 생성자의 firstValue: " + firstValue);
            System.out.println("===== ClassA 생성자 호출 끝 =====");
        }
    }
    ```

- `ClassB` 클래스
    
    ```java
    public class ClassB extends ClassA{


        public ClassB(int firstValue) {
            super(firstValue);
            System.out.println("===== ClassB 생성자 1 호출 =====");
            System.out.println("ClassB 생성자 1의 firstValue: " + firstValue);
            System.out.println("===== ClassB 생성자 1 호출 끝 =====");
        }

        public ClassB(int firstValue, int secondValue) {
            this(firstValue);
            System.out.println("===== ClassB 생성자 2 호출 =====");
            System.out.println("ClassB 생성자 2의 firstValue: " + firstValue);
            System.out.println("ClassB 생성자 2의 secondValue: " + secondValue);
            System.out.println("===== ClassB 생성자 2 호출 끝 =====");
        }
    }
    ```


- `ClassC` 클래스

    ```java
    public class ClassC extends ClassB{

        public ClassC() {
            super(1, 2);
            System.out.println("===== ClassC 생성자 호출 =====");
        }
    }
    ```

- `Main` 클래스

    ```java
    public class Main {
        public static void main(String[] args) {
            ClassC instance = new ClassC();
        }
    }
    ```

위 코드를 보면 다음 사항을 다시 알 수 있다.

- `extends`를 사용하여 부모 클래스로 무엇을 받을지 지정했다.
- 생성자의 첫 줄에 `super()`를 사용하여 부모 클래스의 생성자를 반드시 첫 번째로 호출했다. 
- `this()`를 사용하여 생성자 오버로딩을 했다.  


위 코드를 실행하면 결과는 다음과 같다.

- 결과

    ```java
    ===== ClassA 생성자 호출 =====
    ClassA 생성자의 firstValue: 1
    ===== ClassA 생성자 호출 끝 =====
    ===== ClassB 생성자 1 호출 =====
    ClassB 생성자 1의 firstValue: 1
    ===== ClassB 생성자 1 호출 끝 =====
    ===== ClassB 생성자 2 호출 =====
    ClassB 생성자 2의 firstValue: 1
    ClassB 생성자 2의 secondValue: 2
    ===== ClassB 생성자 2 호출 끝 =====
    ===== ClassC 생성자 호출 =====
    ```

메서드 호출마다 스택 영역에 해당 메서드의 스택 프레임이 쌓인다.

그리고, 스택이므로 후입선출 구조임을 다시 기억하면서 위 결과를 분석해보자.  

생성자 호출 순서는 다음과 같다.

- ClassC 생성자 진입 -> ClassB 생성자 2 호출 -> ClassB 생성자 1 호출 -> ClassA 생성자 호출

그래서 호출 후 출력 순서는 위 결과처럼 ClassA부터 시작해서 ClasB 생성자 1 -> ClassB 생성자 2 -> ClassC 생성자 순서로 출력되는 것이다.    



&nbsp;

---



# 상속과 접근 제어  

이번에는 상속 관계의 두 클래스에 접근 제어자를 적용해보자. 개인적으로 접근 제어를 설명하기 위한 예시 코드로 다음 코드가 제일 좋다고 생각한다.

아래 두 클래스는 서로 다른 패키지에 존재한다.

- `Parent` 클래스

    ```java
    package example.parent; // 패키지 위치

    public class ParentClass {

        private int privateValue;
        int defaultValue;
        protected int protectedValue;
        public int publicValue;


        private void privateMethod() {
            System.out.println("ParentClass privateMethod");
        }

        void defaultMethod() {
            System.out.println("ParentClass defaultMethod");
        }

        protected void protectedMethod() {
            System.out.println("ParentClass protectedMethod");
        }

        public void publicMethod() {
            System.out.println("Enter in ParentClass publicMethod");
            System.out.println("publicValue = " + publicValue);
            System.out.println("protectedValue = " + protectedValue);
            System.out.println("defaultValue = " + defaultValue);
            System.out.println("privateValue = " + privateValue);
        }
    }
    ```


- `Child` 클래스

    ```java
    package example.child;  // 패키지 위치

    import example.parent.ParentClass;

    public class ChildClass extends ParentClass {

        public void call() {
            publicMethod();
            protectedMethod();
        }
    }
    ```

- `ChildMain` 클래스

    ```java
    package example;  // 패키지 위치

    import example.child.ChildClass;

    public class ChildMain {
        public static void main(String[] args) {
            ChildClass child = new ChildClass();

            child.call();
            child.defaultMethod();
            child.privateMethod();
            child.protectedMethod();
        }
    }
    ```

&nbsp;

---

