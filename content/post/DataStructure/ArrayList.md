---
title: "[Data structure] ArrayList를 설명해줄게!"
date: 2024-09-14T12:56:04+09:00
draft: true
summary: 
tags: ["java", "datastructure"]
categories: "data structure"
---
# Introduction
---

프로그래밍을 하다보면 데이터를 다루는 때가 많고, 이 데이터를 어떠한 구조로 저장해야하는지 고민하는 시간이 많다. 이 데이터를 저장하는 구조를 '자료 구조'라 한다.

자바를 사용해 여러 자료 구조를 구현해보며 해당 자료구조의 기능별 시간 복잡도가 어떻게 그렇게 나오는지 '설명해줄게!' 시리즈를 연재한다. 

1. [[Data structure] ArrayList를 설명해줄게!]()
2. [Data structure] LinkedList를 설명해줄게!
3. [Data structure] List를 설명해줄게!
4. [Data structure] Hash를 설명해줄게!
5. [Data structure] HashSet을 설명해줄게!
6. [Data structure] Set을 설명해줄게! 
7. [Data structure] Map을 설명해줄게! 
8. [Data structure] Stack을 설명해줄게!
9. [Data structure] Queue를 설명해줄게!


&nbsp;


# 배열과 리스트의 차이 

---

배열과 리스트의 차이는 '크기가 변경될 수 있는가' 로 나눌 수 있다. 

|특징| 배열 | 리스트 |
|---|---|---|
|순서| O | O | 
|중복 | O | O | 
| 크기 | 고정 | 가변적|

### 인덱스 

두 자료구조 모두 순서가 존재하고, 인덱스를 사용할 수 있다는 것을 의미한다. 인덱스를 사용하면 시간복잡도 O(1)로 여러 작업을 수행할 수 있다. 원하는 자료를 바로 찾을 수 있고, 수정할 수 있고, 입력할 수 있다. 
인덱스로 편리한 작업이 가능한 이유는 크기와 자료형이 고정적이기 때문이다. 
예를 들어 int형 요소인 배열로 크기가 5라고 하자. int 형은 4byte다. 




