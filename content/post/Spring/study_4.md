---
title: "빈의 생명주기 과정에 대해 알아보자."
date: 2025-02-10T20:59:31+09:00
draft: true
summary: 
tags: ["Spring"]
categories: "Spring"
---

데이터베이스 커넥션 풀이나, 네트워크 소켓처럼 애플리케이션 시작 지점에 필요한 연결을 미리 해두고, 애플리케이션 종료 시점에 연결을 모두 종료하는 작업을 하려면 객체의 초기화와 종료 작업이 필요하다. 파이썬에서는 with 문을 사용해 외부 자원과의 연결을 자동으로 초기화하고 종료한다. 스프링에서는 어떻게 하는지 알아보자.