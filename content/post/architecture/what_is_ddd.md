---
title: "DDD basic concept"
date: 2023-03-01T20:04:32+09:00
draft: true
summary: DDD란 무엇이고, layer 간 의존성은 어떻게 띄고 있고, 어떻게 구성하는지에 대해 정리해본다. 또한 이를 class를 사용하여 추상화하는 이유도 정리해본다.  
tags: [""]
categories: "architecture"
---


# 1. What is DDD and its advantages?

이번 프로젝트의 구조를 DDD로 잡아가기로 결정되어 이에 대한 기본적인 내용을 정리해본다.

## 1.1 DDD란?

> DDD(Domain-Driven Development)는 2003년 에릭 에반스가 Domain-Driven Design이라는 책을 처음 출간하면서 소개한 개발 방법론으로, "훌륭한 소프트웨어를 개발하고 싶다면 서비스 도메인에 귀를 기울여라"라는 슬로건으로부터 시작되었고, 현재는 서비스 개발에 가장 큰 주류를 이루고 있는 개발 방법론이다.

### 1. 도메인 레벨과 코드 레벨의 용어와 구조를 일치시키는 것

- 용어 일치: domain model과 software entity의 이름을 일치시키기  
- 구조 일치: domain logic에 code logic을 일치시키기  

#

### 2. 코드를 계층별로 분리하고 추상화 수준을 구별하는 것  

4개의 계층으로 분리하여 추상화 수준을 구별한다.  

- Presentation layer
- Application layer  
- Domain layer
- Infrastructure layer  

각 layer가 무슨 역할을 하고, 무엇을 의미하는지는 다음 챕터에서 알아본다.  

#

## 1.2 DDD의 장점

### 첫 번째, 소통이 편리하다

- 도메인 전문가(기획자)와 개발자 간의 소통을 도와준다.  
- 개발자들도 다른 사람의 코드를 이해하기가 쉬워진다.  

#

### 두 번째, 유지보수가 편리하다  

의존성을 제어하기 때문에 유지보수가 편리하다  

- Loose Coupling (느슨한 결합)과 High Cohesion(높은 응집)
  - domain 간에는 Loose Coupling
  - domain 내에서는 High Cohesion

- 이를 위해서 class로 만들어 추상화한다.  

&nbsp;

---

# 2. Layers of DDD

DDD의 layer는 각 도메인 당 Presentation, Application, Infrastructure, Domain layer로 총 4개로 구성된다.

그러면 각 레이어가 무슨 역할을 하는 지 정리해보겠다.  

## 2.1 Each layer of DDD

### Presentation Layer

> **_request를 받아서 response를 하는 layer로 코드 레벨에서는 "controller"가 이에 해당된다._**

위 요약문처럼 request를 받아서 response를 하는 레이어지만, 이를 보다 자세히 말하자면 다음 3가지만을 드러내야한다.

- API url 설정  
- request model, response model  
- 상세 비지니스 로직 수행을 하기 위한 service를 하위 layer인 Application layer에서 호출하여 실행

#

### Application Layer

> **_비지니스 로직을 정의하는 layer로 코드 레벨에서는 "service"가 이에 해당된다._**  

이 레이어에서는 비지니스 로직을 정의한다.  

#

### Domain Layer

> **_domain model과 비즈니스 룰을 정의하는 layer로, 코드 레벨에서는 "model"이 이에 해당된다._**

model entity를 정의하고, model structure와 state를 관리한다.  

#

### Infrastructure Layer

> **_실제 구체적인 처리를 담당하는 곳으로, 주로 외부와의 통신을 담당한다._**  
> **_코드 레벨에서는 "repository"가 이에 해당된다._**

예를 들어 RDBMS, NoSQL 그리고 email 발송을 위해 Email client와 접촉하는 부분이 이 레이어가 된다.  

해당 앱 외 부분들은 외부에 해당된다.  

#

### 각 layer의 code level에서의 명칭 정리  

각 레이어와 각 레이어가 코드 레벨에서 구현된 명칭은 다음과 같다.

| DDD Layer | code level name |
|---- |---- |
| Presentation|controller |
| Application | service |
| Domain| model |
| Infrastructure| repository |

&nbsp;

## 2.2 Dependency between layers

각 layer는 다음과 같이 의존성을 띈다.  

**Presentation Layer → Application Layer → Infrastructure Layer + Domain Layer**

Presentation Layer는 바로 밑에 Application Layer를 의지하고, Application Layer는 Infrastructure와 Domain Layer를 의지한다.

이를 코드 수준으로 표현하자면 다음과 같다.

**controller -> service -> repository 순서로 의존성을 가진다.**

- controller는 service 만 호출한다.
- service 는 repository 만 호출한다.  

#

이를 도메인 간으로 생각하면 다음과 같다.

여러 도메인이 결합된 문제를 해결할 때는 의존성이 어떻게 띄게 될까?

- A domain의 controller 에서 B domain의 service 를 호출하여 여러 도메인이 결합된 문제를 해결한다.  
- A domain의 service 에서 B domain의 repository 를 호출하여 여러 도메인이 결합된 문제를 해결한다.  
- A domain 의 repository 는 B domain의 repository 를 의지하지 않는다.
- A domain 의 service 는 B domain의 service 를 의지하지 않는다.  

위 2가지의 이유는 다음과 같다.  

- circular import error를 방지하기 위함이다.  
- domain 간 coupling이 많이 생기기 때문인데, 이를 보다 구체적으로 말하자면 각 layer마다 가지고 있는 역할들이 존재하는데 이 역할들이 명확해지지 않아진다. 그러면 DDD 아키텍처가 시간이 지날수록 유지하기 힘든 시스템 구조가 된다고 한다.  

#

## 2.3 그리고 class를 사용하여 추상화한다

어째서 class를 사용하는가?

### 결합도에 따른 코드 관리

클래스는 기본적으로 강하게 결합된 요소는 모아두고, 약결합된 요소를 분리하거나 의존성 주입(dependency injection) 이라는 형태를 통해 가져다가 사용한다. 이러한 결합도에 따른 코드 분리는 "무엇이 무엇과 연결되어 있는가" 를 신경쓰는지 않도록 하여 '인지 비용'을 줄일 수 있다.  

### 상태 관리

클래스는 함수와 달리 상태(state)를 속성으로 관리한다. 특정 상태를 여러 함수에서 사용해야 한다면 함수에서는 다른 함수를 부를 때마다 그 값을 반환해야하지만, 클래스는 해당 정보를 선언하면 클래스 내의 메서드에서는 자유롭게 가져와서 사용하거나 다른 메서드를 호출하면 된다.  

### 코드 재사용  

도메인 간에 특정 layer에 중복된 형태의 로직이 발견된다면 해당 중복 코드를 상위 클래스로 만들어 분리할 수 있다.
