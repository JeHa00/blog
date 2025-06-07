---
title: "빈 생명주기와 스코프 - 1"
date: 2025-02-19T21:00:05+09:00
draft: true
summary: 빈이 존재할 수 있는 범위 인 빈 스코프에 대해 알아보자.
tags: ["Spring"]
categories: "Spring"
---

# 1. 빈 생명주기

---

## 1.1 Life cycle

앞서 배운 내용을 통해 프레임워크가 IoC로서 작동하기 위해 스프링 빈이 필요하다는 것을 알았다. 그러면 객체를 스프링 빈으로 등록하는 것이 존재할 것이고, 프로그램이 종료될 때 이 빈이 소멸될 것이다. 하지만 그냥 뚝 프로그램을 끄면 안된다. 보안을 위해서 외부 자원과의 연결을 끊는 과정 같은 것들이 필요하다. 이번 단원에서는 스프링 프레임워크 내부에서 이 빈이 어떻게 생성되고, 어떤 과정을 거쳐서 종료까지 흘러가는지 **_빈의 생명주기(life cycle)_** 에 대해 학습한다.

일반적인 스프링 빈의 생명 주기 실행 순서를 먼저 말하자면 다음과 같다.

> _스프링 컨테이너 생성 -> 스프링 빈 생성 -> 의존 관계 주입 -> 초기화 메서드 실행(초기화 콜백 실행) -> 사용 -> 소멸 전 메서드 실행(소멸 전 콜백) -> 스프링 빈 객체 소멸 -> 컨테이너 종료 -> 스프링 종료_

초기화 콜백과 소멸 전 콜백을 통해서 프레임워크에게 초기화 시 실행할 메서드와 소멸 전 실행할 메서드를 알려주어 안전하게 종료 작업을 할 수 있다.

다만 의존관계를 주입하는 방식에 따라 미묘하게 달라진다. 생성자 주입으로 생성되는 경우, 빈이 생성될 때 의존 관계를 주입 받고 나서 스프링 빈이 생성되고, 이 빈이 필요한 곳에 의존 관계로서 주입된다. 즉, _빈이 완전한 상태로 사용할 수 있는 시점에는 차이가 존재한다._

또한, 소멸 전 콜백은 반드시 컨테이너 종료 전에만 일어나는 것이 아닌 해당 빈의 사용이 다 종료되면 호출된다. 그래서 싱글톤 빈의 경우 컨테이너의 시작과 종료까지 존재하지만 몇몇 빈은 컨테이너가 종료되지 않아도 소멸 전 콜백이 일어난다.

<br>

## 1.2 생명주기 학습을 위한 소스 코드

위 콜백 메서드를 전용하기 전에 Network를 사용하는 클라이언트 코드가 있고 이 코드를 사용해 테스트를 진행해보자. 먼저 Network를 사용하는 클라이언트 코드를 보여주겠다.

```java
public class NetworkClient {
    private String url;

    public NetworkClient() {
        System.out.println("생성자 호출, url = " + url);
        connect();
        call("초기화 연결 메시지");
    }

    public void setUrl(String url) {
        this.url = url;
    }

    //서비스 시작시 호출
    public void connect() {
        System.out.println("connect: " + url);
    }

    public void call(String message) {
        System.out.println("call: " + url + " message = " + message);
    }

    //서비스 종료시 호출
    public void disconnect() {
        System.out.println("close: " + url);
    }
}
```

다음으로 위 코드에 대한 테스트 코드다.

```java
package SpringBasic.core.lifecycle;

import org.junit.jupiter.api.Test;
import org.springframework.context.ConfigurableApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

public class BeanLifeCycleTest {

    @Test
    public void lifeCycleTest() {
        AnnotationConfigApplicationContext ac = new
                AnnotationConfigApplicationContext(LifeCycleConfig.class);
        NetworkClient client = ac.getBean(NetworkClient.class);
        ac.close();
    }

    @Configuration
    static class LifeCycleConfig {
        @Bean
        public NetworkClient networkClient() {
            NetworkClient networkClient = new NetworkClient();
            networkClient.setUrl("http://hello-spring.dev");
            return networkClient;
        }
    }
}
```

위 테스트 코드를 실행하면 결과는 다음과 같다. 아래 실행 결과를 보면 당연하게도 url의 값이 null로 나온다.

```text
생성자 호출, url = null
connect: null
call: null message: 초기화 연결 메시지
```

그러면 이제 생명주기 관련 콜백을 적용하면 위 실행결과가 어떻게 바뀌는지 알아보자.

<br>

## 1.3 생명주기 관련 메서드 콜백 호출 방법

빈의 생명주기 과정에서 사용할 수 있는 콜백 방법은 3가지이지만 권장하는 방식은 `@PostConstruct` 와 `@PreDestroy` 애노테이션을 사용하는 방법이다. 이 방법부터 알아보자.

&nbsp;

### 1.3.1 @PostConstruct 와 @PreDestroy

이 방법은 애노테이션 하나만 붙이면 되므로 매우 편리하다. 초기화 콜백에는 `@PostConstruct` 를, 소멸 전 콜백에는 `@PreDestroy` 를 사용하면 된다.

`NetworkClient` 객체에 초기화 콜백으로 `init()` 메서드를, 소멸 전 콜백으로 `close()`메서드를 추가한다.

```java
@PostConstruct
public void init() {
    ...
}

@PreDestroy
public void close() {
    ...
}
```

그러면 코드를 더 자세히 확인해보자.

1. 생성하는 역할과 초기화 역할을 `init()` 메서드를 추가해서 분리했으므로 기존 생성자에 있던 코드를 `init()` 에 옮긴다.

```java
@PostConstruct
public void init() {
    System.out.println("NetworkClient.init");
    connect();
    call("초기화 연결 메시지");
}
```

2. 생명주기에 관여하는 메서드는 `disConnect()` 가 아니라 `close()` 메서드이므로 `close()` 메서드로 `disConnect()` 메서드를 감싸서 소멸 전 콜백으로 호출한다.

```java
@PreDestroy
public void close() {
    System.out.println("NetworkClient.close");
    disConnect();
}
```

위 코드를 반영해서 실행하면 다음과 같다.

```text
생성자 호출, url = null
NetworkClient.init
connect: http://hello-spring.dev
call: http://hello-spring.dev message: 초기화 연결 메시지
NetworkClient.close
close: http://hello-spring.dev
```

1. 생성자 호출
2. 초기화 콜백 호출 -> connect()와 call() 메서드 실행
3. 소멸 전 콜백 호출 -> disConnect() 호출

테스트 코드에 위 순서로 코드를 호출하지 않아도 자동으로 호출되는 것을 알 수 있다!

만약 스프링 빈을 등록되지 않은 것인 외부 라이브러리를 초기화, 종료해야 한다면 바로 밑에 방법을 사용하면 된다.

&nbsp;

### 1.3.2 @Bean으로 등록하기

이번 방법은 설정 정보에 `@Bean(initMethod, destroyMethod)`을 추가해 사용한다. `initMethod`와 `destroyMethod`에 각각 초기화 콜백 그리고 소멸 전 콜백으로 사용할 메서드 이름을 입력한다.

기존 코드는 다음과 같다.

```java
...

@Configuration
static class LifeCycleConfig {
    @Bean
    public NetworkClient networkClient() {
        NetworkClient networkClient = new NetworkClient();
        networkClient.setUrl("http://hello-spring.dev");
        return networkClient;
    }
}
```

위 코드에서 `@Bean` 대신에 `@Bean(initMethod, destroyMethod)`을 추가한다. NetworkClient에 초기화 콜백이 init이고, 소멸 전 콜백이 close이면 다음과 같이 작성한다.

- `@Bean(initMethod = "init", destroyMethod = "close")`

이처럼 메서드 이름을 자유롭게 줄 수 있고, 스프링 빈이 스프링 코드에 의존하지 않아서 앞서 말한 것처럼 코드를 고칠 수 없는 외부 라이브러리에도 적용할 수 있다.

&nbsp;

### 1.3.3 인터페이스 사용하기

마지막은 스프링 초창기에 인터페이스를 사용해 초기화, 종료하는 방법이다. 이 방법은 `InitializingBean` 과 `DisposableBean` 인터페이스를 구현하도록 클래스를 수정한다. `InitializingBean` 인터페이스에는 `afterPropertiesSet()` 메서드가 있고, `DisposableBean` 인터페이스에는 `destroy()` 메서드를 가지고 있기 때문에 이 두 메서드를 재정의해야 한다.

```java
public class NetworkClient implements InitializingBean, DisposableBean {

    // 첫 번째 방법에서 init() 메서드를 다음과 같이 수정한다.
    @Override
    public void afterPropertiesSet() {
        System.out.println("NetworkClient.init");
        connect();
        call("초기화 연결 메시지");
    }

    // 첫 번째 방법에서 close() 메서드를 다음과 같이 수정한다.
    @Override
    public void destroy() {
        System.out.println("NetworkClient.close");
        disconnect();
    }
}
```

실행 결과는 첫 번째 방법과 동일하다. 이 방법보다 3.2.1과 3.2.2를 사용하자.

<br>

---

# 2. 빈 스코프

스프링 빈에는 생명주기 과정이 있다는 것을 학습했다. 이 과정에서 의존 관계를 주입하는 시점이 빈마다 다르고, 소멸 전 콜백이 빈마다 다르게 호출되게 해 생명주기의 길이가 빈마다 달라질 수 있다는 것을 알았다. 그러면, 굳이 이것을 다르게 할 필요가 있을까? 다 똑같이 하면 안되는 것일까?

생명주기의 길이가 다르다는 건 빈의 입장에서 봤을 때, 빈 자신이 유지되는 범위가 다르다는 것을 의미한다.

스프링에서는 이 빈이 유지되는 범위를 **_빈 스코프(bean scope)_** 라 하며 다음과 같이 스코프 종류가 나눠진다.

스프링에서 기본적으로 제공하는 빈의 종류는 다음과 같다.

- 싱글톤: 스프링의 시작과 종료까지 유지되는 가장 넓은 범위의 스코프로, 기본 스코프다.
- 프로토타입: 스프링 컨테이너가 빈의 생성과 의존관계 주입까지만 관여하는, 매우 짧은 범위의 스코프다.
- 웹 관련 스코프
  - request: 웹 요청이 들어오고 나갈 때까지 유지되는 스코프다.
  - session: 웹 세션이 생성되고 종료될 때까지 유지되는 스코프다.
  - application: 웹의 서블릿 컨텍스트와 같은 범위로 유지되는 스코프다.
  - websocket: 웹 소켓과 동일한 생명주기를 가지는 스코프

위에서 아래 순서로 각 스코프에 대해 알아보자. 싱글톤과 프로토타입을 비교해보자. **_프로토 타입은 매번 사용할 때마다 새로운 객체가 필요한 경우 사용한다. 하지만, 대체로 싱글톤 빈으로 문제를 해결할 수 있기 때문에 프로토타입 빈을 직접적으로 사용하는 일은 드물다고 한다._**

그래도 각 종류에 대해 학습해보자. 싱글톤과 프로토타입 빈이 코드상에서 어떻게 차이가 나타나는지 확인하자.

빈 스코프를 지정하는 방법은 `@Scope("빈 종류 입력")` 애노테이션을 입력한다.
싱글톤을 원한다면 기본값이 싱글톤이므로 위 애노테이션을 사용하지 않아도 된다.

```java
@Scope("prototype")
@Component
public class ExampleBean {
    return new ExampleBean();
}
```

<br>

## 2.1 싱글톤 스코프

그러면 싱글톤과 프로토타입 모두 코드로 확인해보자. 먼저 싱글톤을 확인하자.

```java
package SpringBasic.core.scope;

import jakarta.annotation.PostConstruct;
import jakarta.annotation.PreDestroy;
import org.junit.jupiter.api.Test;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.assertj.core.api.Assertions;

public class SingletonTest {

    @Test
    void singletonBeanFind() {
        AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(SingletonBean.class);

        SingletonBean singletonBean01 = ac.getBean(SingletonBean.class);
        SingletonBean singletonBean02 = ac.getBean(SingletonBean.class);
        System.out.println("singletonBean01 = " + singletonBean01);
        System.out.println("singletonBean02 = " + singletonBean02);
        Assertions.assertThat(singletonBean01).isSameAs(singletonBean02);
        ac.close();
    }

    static class SingletonBean {
        @PostConstruct
        public void init() {
            System.out.println("SingletonBean.init");
        }

        @PreDestroy
        public void destroy() {
            System.out.println("SingletonBean.destroy");
        }
    }
}

```

```
SingletonBean.init
singletonBean01 = SpringBasic.core.scope.SingletonTest$SingletonBean@3e78b6a5
singletonBean02 = SpringBasic.core.scope.SingletonTest$SingletonBean@3e78b6a5
SingletonBean.destroy
```

- 중첩 클래스로 SingletonBean 객체를 생성하고, 초기화 콜백과 소멸자 전 콜백을 생성한다.
- 싱글톤은 Scope을 사용하지 않아도 된다.
- ApplicationContext로 위 객체를 스프링 빈으로 등록한다.
- getBean 메서드를 사용해서 스프링 컨테이너에 요청한다.
- 실행 결과를 보면 요청할 때마다 같은 스프링 빈을 반환하는 것을 알 수 있다. 테스트를 통과한 것으로도 알 수 있다.

즉 **_싱글톤 스코프의 빈을 조회하면 스프링 컨테이너는 항상 같은 인스턴스의 스프링 빈을 반환하는 것을 알 수 있다._**

<br>

## 2.2 프로토타입 스코프

이번에는 프로토타입 스코프를 확인해보자.

```java
package SpringBasic.core.scope;

import jakarta.annotation.PostConstruct;
import jakarta.annotation.PreDestroy;
import org.junit.jupiter.api.Test;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.assertj.core.api.Assertions;
import org.springframework.context.annotation.Scope;

public class PrototypeTest {

    @Test
    void singletonBeanFind() {
        AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(PrototypeBean.class);


        PrototypeBean prototypeBean01 = ac.getBean(PrototypeBean.class);
        PrototypeBean prototypeBean02 = ac.getBean(PrototypeBean.class);
        System.out.println("prototypeBean01 = " + prototypeBean01);
        System.out.println("prototypeBean02 = " + prototypeBean02);
        Assertions.assertThat(prototypeBean01).isNotSameAs(prototypeBean02);
        ac.close();
    }

    @Scope("prototype")
    static class PrototypeBean {
        @PostConstruct
        public void init() {
            System.out.println("PrototypeBean.init");
        }

        @PreDestroy
        public void destroy() {
            System.out.println("PrototypeBean.destroy");
        }
    }
}
```

```
PrototypeBean.init
PrototypeBean.init
prototypeBean01 = SpringBasic.core.scope.PrototypeTest$PrototypeBean@2c78324b
prototypeBean02 = SpringBasic.core.scope.PrototypeTest$PrototypeBean@79defdc
```

- 실행 결과를 보면 스프링 컨테이너에 스프링 빈을 요청할 때마다 다른 빈을 반환하는 것을 알 수 있다.
- 그리고, 생성자 후 콜백이 2번 실행되고, 소멸 전 콜백은 실행되지 않은 것을 알 수 있다.

즉, **_프로토타입 빈은 스프링 컨테이너에 조회하면 항상 새로운 인스턴스를 생성해서 반환한다는 것이다._** 또한, 소멸 전 콜백을 호출하지 않은 것을 봐선 **_스프링 컨테이너는 프로토타입 빈을 생성하고, 의존관계를 주입, 초기화까지만 처리한다는 것을 알 수 있다._** 이는 클라이언트에 빈을 반환하면, 이 빈은 스프링 컨테이너가 관리하지 않는다는 것이기 때문에 프로토타입 스프링 빈은 클라이언트에게 관리할 책임이 있다는 것이다.

## 싱글톤 빈에서 프로토타입을 사용하면..?

싱글톤 빈이 의존관계 주입으로 프로토타입 빈을 주입받아서 사용되는 경우, 의도치 않은 결과를 얻을 수 있어서 이 케이스에 대해 학습해보자.

싱글톤 빈은 스프링 컨테이너 시점에 보통 생성되고, 의존관계 주입이 발생하고, 컨테이너가 종료될 때 소멸된다. 그래서 싱글톤 빈에서 의존관계를 주입받을 때 이 시점의 스프링 빈 객체가 계속 유지된다. 스프링 빈도 모두 다 객체로 참조값을 전달하는 방식이기 때문에 싱글톤 빈의 내부 필드에서 이 참조값을 가지고 있는다. 만약 이 참조값이 프로토타입 빈이라면 프로토타입이여도 싱글톤 빈이 생성될 때 주입받은 참조값을 계속 가지고 있고, 싱글톤은 스프링 컨테이너 소멸하기 전까지 소멸되지 않기 때문에 새로운 프로토타입을 사용하지 못하게 된다.

아래 테스트 코드를 실행하면 프로토타입의 내부 필드 값인 count가 증가된 채로 유지되는 것을 알 수 있다.

```java
package SpringBasic.core.scope;

import jakarta.annotation.PostConstruct;
import jakarta.annotation.PreDestroy;
import org.junit.jupiter.api.Test;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.annotation.Scope;

import static org.assertj.core.api.Assertions.*;

public class SingletonWithPrototypeTest1 {

    @Test
    void singletonClientUsePrototype() {
        AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(ClientBean.class, PrototypeBean.class);

        ClientBean clientBean01 = ac.getBean(ClientBean.class);
        int count1 = clientBean01.logic();
        assertThat(count1).isEqualTo(1);

        ClientBean clientBean02 = ac.getBean(ClientBean.class);
        int count2 = clientBean02.logic();
        assertThat(count2).isEqualTo(2);

    }

    static class ClientBean {
        private final PrototypeBean prototypeBean; // 생성 시점에 주입


        public ClientBean(PrototypeBean prototypeBean) {
            this.prototypeBean = prototypeBean;
        }

        public int logic() {
            prototypeBean.addCount();
            int count = prototypeBean.getCount();
            return count;
        }
    }

    @Scope("prototype")
    static class PrototypeBean {
        private int count = 0 ;

        public void addCount() {
            count++;
        }

        public int getCount() {
            return count;
        }

        @PostConstruct
        public void init() {
            System.out.println("PrototypeBean.init" + this);
        }

        @PreDestroy
        public void destroy() {
            System.out.println("PrototypeBean.destroy");
        }
    }
}

```

그러면 다음과 같이 ApplicationContext를 필드로 저장한 후, logic() 메서드를 수행할 때마다 Prototype 빈을 조회하면 새롭게 생성할 수 있다.

```java
package SpringBasic.core.scope;

import jakarta.annotation.PostConstruct;
import jakarta.annotation.PreDestroy;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.annotation.Scope;

import static org.assertj.core.api.Assertions.*;

public class SingletonWithPrototypeTest1 {

    @Test
    void singletonClientUsePrototype() {
        AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(ClientBean.class, PrototypeBean.class);

        ClientBean clientBean01 = ac.getBean(ClientBean.class);
        int count1 = clientBean01.logic();
        assertThat(count1).isEqualTo(1);

        ClientBean clientBean02 = ac.getBean(ClientBean.class);
        int count2 = clientBean02.logic();
        assertThat(count2).isEqualTo(2);

    }

    static class ClientBean {

        // 새로 추가된 부분
        @Autowired
        private ApplicationContext ac;


        public int logic() {
            PrototypeBean prototypeBean = ac.getBean(PrototypeBean.class);
            prototypeBean.addCount();
            int count = prototypeBean.getCount();
            return count;
        }
    }

    @Scope("prototype")
    static class PrototypeBean {
        private int count = 0 ;

        public void addCount() {
            count++;
        }

        public int getCount() {
            return count;
        }

        @PostConstruct
        public void init() {
            System.out.println("PrototypeBean.init" + this);
        }

        @PreDestroy
        public void destroy() {
            System.out.println("PrototypeBean.destroy");
        }
    }
}

```

위 코드처럼 의존관계를 주입받는 것이 아닌 get() 메서드같은 것을 사용해 코드상에서 직접 찾는 것을 Dependency Lookup(DL, 의존관계 조회, 탐색)이라 한다. 하지만 이는 스프링 애플리케이션 컨텍스트를 주입받으면 스프링 컨테이너에 의존적인 코드가 되어 테스트가 어렵다.

그래서 컨테이너에 의존적이지 않으면서 의존관계를 조회할 수 있는 방법으로 ObjectProvider를 상요하면 된다. 과거에는 ObjectFactory가 있었는데 편의기능이 추가되면서 ObjectProvider가 만들어졌다고 한다. ObjectProvider를 사용해서 새로운 프로토타입 빈을 항상 생성해보자. ClientBean 객체만 수정해보자.

```java
    static class ClientBean {

        // 수정된 부분
        @Autowired
        private ObjectProvider<PrototypeBean> prototypeBeanProvider;


        public int logic() {
            // 수정된 부분
            PrototypeBean prototypeBean = prototypeBeanProvider.getObject();
            prototypeBean.addCount();
            int count = prototypeBean.getCount();
            return count;
        }
    }
```

스프링 컨테이너 전체에 의존하지 않으면서 DL을 수행하므로 테스트를 작성하기 훨씬 쉬워진다.

나머지 웹과 관련된 스코프는 다음 포스팅에서 알아보자.

---

# Reference

- [스프링 핵심 원리 - 기본편
  ](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8/dashboard)
