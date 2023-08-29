---
title: "Memory_management"
date: 2023-08-28T21:36:54+09:00
draft: true
summary: 
tags: ["python"]
categories: ["Python"]
---

python의 interpreter는 cpython으로 작성되어 있다.

program이 memory에 올라와 process 상태가 되면 이 process의 virtual memory(=address space=주소 공간)은 code, data, stack, heap으로 나눠진다.

낮은 메모리 주소 영역 <= code(text) - data - heap - stack => 높은 메모리 주소 영역

순서로 구성되며 프로세스의 주소 공간은 시작점과 끝점이 정해져있다. stack이 커지면 heap 영역이 줄어든다.

stack은 stack이라는 명칭처럼 연속적인 메모리 할당을 담당하기 때문에 데이터의 사이즈가 변하지 않은 변수(원시 타입)가 저장되고, heap은 연속적이지 않은 산발적인 메모리 할당을 담당하여 사이즈가 정해져있지 않은 데이터 타입(비원시 타입)은 heap에 저장된다.

(만약 프로세스가 할당된 주소 공간을 벗어나는 주소로 요청을 보내면 보안 상의 이유로 os가 강제로 종료시킨다.)

그러면 파이썬 언어에서는 이 주소 공간에 어떻게 코드가 저장되는지 알아보자.

code에는 프로세스의 코드들이 저장되어 있어서 cpu가 이 code 영역의 instruction들을 보고 실행한다.

heap에는 파이썬의 모든 객체 정보들이 저장된다. 파이썬의 모든 값들은 객체임을 먼저 상기시키자. 즉 모든 실제 값은 heap에 저장된다. 그래서 data 영역에 저장되는 건 없다.

stack에는 객체의 reference인 변수들과 reference counting인 참조 횟수 정보가 저장된다. 그리고, 호출된 함수 또는 메서드의 위치를 기억하여 함수 또는 메서드 실행 종료 후 돌아갈 위치를 기억한다.

C와 달리 파이썬은 메모리를 직접 할당하지 못 한다. 대신 내부적으로 python memory manager가 존재하여 관리해준다.

# Reference

아래 3개의 링크는 반드시 다 읽어보도록 하자.

- [python - interpreting, 실행되기 까지와 메모리 관리, memory optimization (1)](https://velog.io/@qlgks1/python-%EB%A9%94%EB%AA%A8%EB%A6%AC-%EA%B4%80%EB%A6%AC-memory-optimization)
- [python - memory optimization (2) : GC, reference counter and GIL](https://velog.io/@qlgks1/python-memory-optimization-2-GC-reference-counter-and-GIL)  
- [어느날 신입 개발자가 나에게 물었다..."python에서 staticmethod를 사용하는 것에 있어서 메모리 이슈가 없는 것일까요?"](https://jay-ji.tistory.com/105)
- [Memory Management in Python — Concept of Heap, Stack, Garbage Collector, Reference Counter, Tracing, Reference Cycles, and GIL](https://www.linkedin.com/pulse/memory-management-python-concept-heap-stack-garbage-collector-usman/)
