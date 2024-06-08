---
title: "객체 지향 5원칙 중 단일 책임 원칙(SRP)을 예제 코드에 적용해보자"
date: 2024-04-15T01:28:04+09:00
draft: false
summary: 객체 지향 5원칙인 SOLID 중 단일 책임 원칙인 Single Responsibility principle을 자바 코드에 적용해보자.
tags: ["java"]
categories: "java"
---

최근에 자바를 학습하기 시작하면서 한 예제를 풀어보았다. 

'이 예제를 풀었으니 다른 예제를 풀어볼까?' 했으나 자바는 객체 지향이 강한 언어라는 특성을 알게 되어 객체 지향 원칙에 대해 조금씩 공부해가고 있다. 

그래서 풀은 이 예제에  객체지향 5원칙 중 '단일 책임 원칙'인 SRP, Single Responsibility Principle을 적용해보려 한다. 

### 문제 요구사항  
내가 풀은 문제의 요구사항은 다음과 같다.

문제 이름은 '선택한 식재료 총 구매 금액 구하기' 다.

1. 식재료의 종류에는 1) 감자, 2) 옥수수, 3) 수박이 있다. 3가지 중 하나를 입력받아야 한다.
2. 1)을 선택하면 '1번 감자를 선택하셨습니다.' 라는 문구가 떠야 한다. 나머지 2번과 3번도 동일하다. 
3. 식재료를 선택한 후, 식재료에 따라 식재료 가격대를 입력받아야 한다. 이 가격대는 식재료마다 다르다.
    - 감자: 1000원, 2000원, 3000원
    - 옥수수: 4000원, 5000원, 6000원
    - 수박: 10000원, 20000원, 30000원 
4. 가격대를 선택하며 예를 들어 '감자 1000원어치를 선택하셨습니다.' 라는 문구가 출력되야 한다. 
5. 그 후, 주문 갯수를 입력받은 후 총 구매 금액을 출력하라.  


### 단일 책임 원칙 미적용 코드  

그러면 이 코드를 단일 책임 원칙을 적용하기 전 코드부터 살펴보자. 단 이 코드에는 제약을 두고 작성했다. 

- Array, class는 사용하지 못한다.
- 변수 선언 및 초기화, 조건문, 입력, 메서드만을 사용한다.  

자바를 학습하는 과정에서 작성한 코드임을 기억해주시면 감사하겠다.  

```java
import java.util.Scanner;

public class OrderIngredient {
    public static void main(String[] args) {
        int optionForItem, price;
        String itemName;
        price = 0;

        optionForItem = chooseOption();

        if (optionForItem == 1) {
            itemName = "감자";
            price = choosePrice(itemName, price);
        } else if (optionForItem == 2) {
            itemName = "옥수수";
            price = choosePrice(itemName, price);
        } else {
            itemName = "수박";
            price = choosePrice(itemName, price);
        }

        int amount = chooseAmount(itemName, price);
        System.out.println(itemName + " " + price + "원 어치를 총 " + amount + "개 주문하셨습니다.");
    }

    // 식재료를 선택하는 역할: 받는 인자값은 없고, 선택된 메뉴 번호를 반환
    public static int chooseOption() {
        Scanner input = new Scanner(System.in);
        System.out.println("다음 세 가지 중 고르시오.");

        System.out.println("1. 감자.  2. 옥수수. 3. 수박.");
        return input.nextInt();
    }

    // 가격 선택 인터페이스를 제공하는 역할
    public static int choosePrice(String itemName, int price) {
        if (itemName.equals("감자")) {
            System.out.println(itemName + "를 선택하셨습니다.");
            price = choosePriceOption(1000, 2000, 3000);
        } else if (itemName.equals("옥수수")) {
            System.out.println(itemName + "를 선택하셨습니다.");
            price = choosePriceOption(4000, 5000, 6000);
        } else {
            System.out.println(itemName + "을 선택하셨습니다.");
            price = choosePriceOption(10000, 20000, 30000);
        }

        return price;
    }

    // 가격을 선택하는 역할
    public static int choosePriceOption(int option1Price, int option2Price, int option3Price) {
        Scanner input = new Scanner(System.in);
        int price;
        
        System.out.println("1. " + option1Price + "원 어치. 2. " + option2Price + "원 어치. 3. " + 
        option3Price + "원 어치");
        
        int optionForPrice = input.nextInt();

        if (optionForPrice == 1) {
            price = option1Price;
        } else if (optionForPrice == 2) {
            price = option2Price;
        } else {
            price = option3Price;
        }

        return price;
    }

    // 양을 선택하는 역할
    public static int chooseAmount(String itemName, int price) {
        Scanner input = new Scanner(System.in);
        System.out.println(itemName + " " + price + "원 어치를 선택하셨습니다. 몇 개를 주문하시겠습니까?");

        return input.nextInt();
    }
}
```

위 코드도 나름 각 메서드마다 역할을 정해서 작성했다. 

하지만 의존성을 띄는 걸 확인할 수 있다. 어디서 의존성을 확인할 수 있을까?

바로 `chooseOption` 메서드와 `choosePrice` 메서드다. `chooseOption`에 식재료 옵션이 literal로 추가되면 `choosePrice`도 수정해야 한다. 

이와 같은 관계를 '의존성을 띈다'고 하며 '단일 책임 원칙'에 어긋난다.  

### 단일 책임 원칙 적용 코드  

그러면 이제 '단일 책임 원칙'을 적용해보자.

```java
import java.util.Scanner;

public class OrderIngredient {
    public static void main(String[] args) {
        int totalPrice = orderIngredient();
        System.out.print("구매 최종 비용: " + totalPrice);
    }

    // 식재료 주문하기  
    public static int orderIngredient() {
        Scanner input = new Scanner(System.in);

        // 식재료 선택
        System.out.print("1.감자 2.옥수수 3.수박: ");
        int ingredient = input.nextInt();

        // 식재료 선택에 따른 전체 주문 가격 얻기
        if (ingredient == 1) {
            System.out.println(ingredient + "번 감자를 선택하셨습니다.");
            return getTotalCost("감자", 1000, 2000, 3000);
        }
        else if (ingredient == 2) {
            System.out.println(ingredient + "번 옥수수를 선택하셨습니다.");
            return getTotalCost("옥수수", 4000, 5000, 6000);
        }
        else if (ingredient == 3) {
            System.out.println(ingredient + "번 수박을 선택하셨습니다.");
            return getTotalCost("수박", 10000, 20000, 30000);
        }
        return 0;
    }

    // 전체 비용 얻기
    public static int getTotalCost(String ingredient, int price1, int price2, int price3) {
        Scanner input = new Scanner(System.in);

        // 가격 선택
        System.out.print("1." + price1 + " 2." + price2 +" 3." + price3);
        int selectedPrice = input.nextInt();

        // 선택한 구매가격 안내 및 전체 구매 가격 반환
        if (selectedPrice == 1) {
            return informPriceAndCalculateCost(ingredient, price1);
        }
        else if (selectedPrice == 2) {
            return informPriceAndCalculateCost(ingredient, price2);
        }
        else if (selectedPrice == 3) {
            return informPriceAndCalculateCost(ingredient, price3);
        }
        return 0;
    }

    // 비용 계산하기  
    public static int informPriceAndCalculateCost(String ingredient, int price) {
        Scanner input = new Scanner(System.in);
        // 선택한 구매가격 안내
        System.out.println(ingredient +" " + price + "원어치를 선택하셨습니다.");

        // 수량 선택
        System.out.print("몇 개를 주문하시겠습니까?: ");
        int orderAmount = input.nextInt();

        // 전체 구매 가격 반환
        return orderAmount * price;
    }

}

```

단일 책임 원칙을 적용하면서 메서드가 합쳐지면서 메서드 이름도 바뀌었다. 

적용 전 바로 `chooseOption` 메서드와 `choosePrice` 메서드가 `orderIngredient` 메서드로 합쳐졌다. 식재료명을 변수로 전달하여 의존성을 해결했다.  

그 밖에도 `orderIngredient` 를 수정한다고 하여 `getTotalCost` 메서드가 수정되는 일이 없다.  

또한, `orderIngredient`로 합쳐서 불필요한 메서드 분리를 없애서 최소화할 수 있게 되었다. 

### 그러면 단일 책임 원칙이란 무엇일까?

위 사례를 통해서 내가 느낀 '단일 책임 원칙'이란 다음과 같다고 볼 수 있을 것 같다.  

- 이 원칙을 **_메서드 관점_** 에서 보자면 각 메서드마다 명확한 역할과 책임을 정하는 것
    - 이를 코드로 표현하면 어떤 값을 받고, 어떤 값을 반환할지를 결정하는 것, 이는 메서드 이름에서부터 나타나야 한다.  
    - 만약 메서드끼리 '의존성'을 가진다면 각 메서드마다의 역할과 책임을 명확하게 하지 않았기 때문이라 볼 수 있다.  


- 또한, 이를 메서드 관점이 아닌 **_프로그램 전체적인 설계의 관점_** 에서 보자면 다음과 같이 생각할 수 있을 것 같다.
    - 하나의 프로그램은 한 가지 목적을 위해서 만들어졌지만, 이 목적을 위해서 여러 기능들이 포함된다.   
    - 이에 따라 이 기능들이 섞이지 않게 잘 구별하는 것  
    - 그리고, 추후 변경점이 생겼을 때 유연하게 대처가 가능해야한다는 의미  


### 현실 세계와의 공통점

이는 현실 세계에서 업무 분배를 하기 위해 각 부서마다 업무 범위를 정하고, 각 부서 구성원들끼리도 각자 업무를 맡는 것과 동일한 원리 같다.  

효율적으로 일을 처리하고, 진행하기 위해서는 업무가 중복되면 안된다. 업무가 중복된다는 건 책임이 중복되는 것이고, 이는 책임 회피가 일어날 수 있다.

회사에서 일할 때나 프로젝트를 할 때나 느낀 바는 다음과 같다.

'인원 전체가 책임을 진다는 건 책임지는 일이 없다는 것과 똑같다.'

정말 이상적으로는 인원 전체 모두가 한 가지 업무에 대해 책임을 져서 모두가 집중하면 좋지만 현실적으로는 여러 업무들이 있기 때문에 간과될 가능성이 있다. 

메서드를 작성할 때도 똑같다. 동일한 역할을 하는 메서드가 또 있다면 둘 중 하나만 사용하게 되고, 불필요한 코드가 발생된 것이다.   

### 아름다운 객체 지향

'객체 지향 설계'는 정말 매력적이다. 계속 학습해보자. 자바를 학습해가면서 자동적으로 파고 들어지는 것 같다.    

파이썬도 또한 클래스와 메서드라는 개념이 있지만, 객체지향 원칙에 대한 자료는 자바가 훨씬 많은 것 같다. 자바를 통해 이 내용들을 학습하면서 파이썬과 자바를 모두 잘 사용해서 객체 지향 설계를 좋아하고 잘하는 개발자가 되보자.  