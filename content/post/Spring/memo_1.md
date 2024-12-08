---
title: "Memo_1"
date: 2024-11-30T04:28:22+09:00
draft: true
summary: 
tags: [""]
categories: ""
---

Spring 학습 일지 

프로젝트를 생성한 후 실행하니 다음 에러가 발생했다.


```java
Caused by: java.lang.ClassNotFoundException: jakarta.servlet.annotation.WebServlet
	at java.base/jdk.internal.loader.BuiltinClassLoader.loadClass(BuiltinClassLoader.java:641) ~[na:na]
	at java.base/jdk.internal.loader.ClassLoaders$AppClassLoader.loadClass(ClassLoaders.java:188) ~[na:na]
	at java.base/java.lang.ClassLoader.loadClass(ClassLoader.java:520) ~[na:na]
```

해결책은 두 가지다. 
- IntelliJ가 무료버전이라면 'Build, Execution, Deployment' > 'Build Tools' > Gradle > Grade Project에서 'Build and run using' 과 'Run test using' 옵션을 Gradle로 한다.
- build.grade에 있는 providedRuntime 'org.springframework.boot:spring-boot-starter-tomcat' 을 제거하고, gradle을 refresh하는 것 

providedRuntime 의미는 런타임 환경에서 라이브러리가 제공된다는 의미인데, 이 부분에 tomcat이 입력되어 있으므로 프로젝트가 컴파일될 때 해당 라이브러리를 사용하지 않아서 발생하는 문제입니다. 그래서 위 에러를 보면 ClassLoader에 의해서 발생된 것을 알 수 있고, 이 부분의 에러 소스 코드를 보면 JVM과 class loader가 언급된다. 즉, 컴파일 시 해당 클래스를 찾을 수 없다는 내용이다. 그래서 providedRuntime이 지워지면 컴파일 시 제공되므로 문제가 해결된다.
위 코드가 지워지면 스프링 부트의 내장 톰캣 코드가 컴파일 시점에 포함된다.