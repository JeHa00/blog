---
title: "[Data structure] Hash에 대해 설명해 줄게!"
date: 2024-10-11T10:43:21+09:00
draft: false
summary: Set 자료구조에서 Hash가 필요한 이유에 관해 설명한다!
tags: ["java", "datastructure"]
categories: "Data Structure"
---

# Introduction
---

프로그래밍을 하다 보면 데이터를 다루는 때가 많고, 이 데이터를 어떠한 구조로 저장해야 하는지 고민하는 시간이 많다. 이 데이터를 저장하는 구조를 '자료 구조'라 한다.

자바를 사용해 여러 자료 구조를 구현해  보며 해당 자료구조의 기능별 시간 복잡도가 어떻게 그렇게 나오는지 '설명해 줄게!' 시리즈를 연재한다. 

1. [[Data structure] ArrayList를 설명해 줄게!](https://jeha00.github.io/post/datastructure/arraylist/)
2. [[Data structure] Singly LinkedList를 설명해 줄게!](https://jeha00.github.io/post/datastructure/linkedlist/)
3. [[Data structure] List를 설명해 줄게!](https://jeha00.github.io/post/datastructure/list/)
4. [[Data structure] Hash를 설명해 줄게!](https://jeha00.github.io/post/datastructure/hash/)
5. [Data structure] HashSet을 설명해 줄게!
6. [Data structure] Set을 설명해 줄게! 
7. [Data structure] Map을 설명해 줄게! 
8. [Data structure] Stack을 설명해 줄게!
9. [Data structure] Queue를 설명해 줄게!

&nbsp;

# 1. List와 Set의 차이 

----
리스트에 대해서 알아봤으니 Set에 대해서 알아보자. 

리스트는 앞서 **_요소들에 순서가 있고 중복이 있는 자료구조_** 라 했다. 순서가 있어서 '인덱스'를 사용할 수 있다. 

하지만 셋(Set)은 리스트와 정반대로 **_요소들에 순서가 없고 중복이 없는 자료구조_**다. 순서가 없으므로 인덱스가 없다. 중복이 없다는 건 예를 들어 'a'라는 문자열이 있으면 동일한 값의 'a'가 있지 않다는 의미다. 

그래서 리스트의 용도는 다음과 같다.
1. 리스트에 추가된 순서대로 요소의 순서가 유지해야 할 때  
2. 동일한 값이나 객체의 중복 허용이 필요할 때  
3. 리스트의 각 요소에 인덱스 접근이 필요할 때  

반대로 셋의 용도는 다음과 같다. 
1. 값이나 객체가 유일하게 존재해야 할 때  
2. 입력한 순서대로 보장할 필요가 없을 때  
3. 빠르게 검색할 필요가 있을 때  

그래서 리스트는 장바구니 목록, 셋은 회원 목록 등에 사용할 수 있다.  

이 셋의 특징 핵심에는 **_해시 알고리즘_** 이 있는데 이에 대해 알아보자!




&nbsp;

# 2. 해시 알고리즘: 해시 인덱스

----

## 해시 인덱스란?


기존 리스트에 정수형 값 1부터 10까지 추가한 후 원하는 값을 찾으려면 모든 값을 탐색해야 한다. 이는 이전에 학습한 대로 O(n)이 걸린다. 하지만 집합은 값을 찾는 데 배열 인덱스를 사용하지 않아도 O(1)이 걸려야 한다. 이를 어떻게 구현할 수 있을까? 

### 데이터의 값을 배열 인덱스와 맞추어 저장하기

만약 값의 크기를 인덱스로 사용한다면 크기를 통해 O(1)로 바로 접근할 수 있을 것이다.

```java
Integer array[] = new Integer[10];
array[1] = 1;
array[3] = 3;
array[5] = 5;
array[9] = 9;
System.out.println(Arrays.toString(array));

// 값 9 찾기
System.out.println(array[9]);
```

- 실행 결과

    ```java
    [null, 1, null, 3, null, 5, null, null, null, 9]
    9
    ```


**_값을 인덱스로 사용해서 저장한 결과 O(1)로 바로 값을 찾을 수 있다. 하지만 전체 배열 출력에서 알 수 있듯이 사용하지 않는 공간이 많다. 만약 입력받으려는 값의 범위가 훨씬 넓다면 그만큼 배열의 크기가 커야 하고 배열에 낭비되는 공간이 많이 발생한다._**

- 위 문제 예시 코드

    ```java
    Integer array[] = new Integer[50];
    array[1] = 1;
    array[3] = 3;
    array[9] = 9;
    array[25] = 25;
    array[40] = 40;
    System.out.println(Arrays.toString(array));
    ```

- 실행 결과

    ```java
    [null, 1, null, 3, null, null, null, null, null, 9, null, null, null, null, null, null, null, null, null, null, null, null, null, null, null, 25, null, null, null, null, null, null, null, null, null, null, null, null, null, null, 40, null, null, null, null, null, null, null, null, null]
    ```

이렇게 데이터의 값을 사용해 저장하려는 위치의 인덱스로 사용할 때 문제점을 파악했다.

### 나머지 연산으로 해결하기  

메모리가 낭비되는 문제를 해결하기 위해 예를 들어 '나머지 연산'을 사용해 인덱스 위치를 구할 수 있다. 나머지 연산을 통해 나온 값을 인덱스로 사용하는 것이다.

- 나누는 수를 10이라 가정 
- 0 ~ 99까지의 수를 배열에 저장한다고 가정

위 가정으로 나올 수 있는 인덱스의 범위는 0부터 9다. 
- `1 % 10 = 1` -> 인덱스 1
- `3 % 10 = 3` -> 인덱스 3
- `22 % 10 = 5` -> 인덱스 2
- `40 % 10 = 0` -> 인덱스 0
- `94 % 10 = 4` -> 인덱스 4 

이런 식으로 인덱스를 구한다면 배열의 크기를 많이 크게 잡지 않아도 된다.

```java
Integer array[] = new Integer[50];
int[] values = {1, 3, 22, 40, 94};
for (int value : values) {
    array[value % 10] = value;
}
System.out.println(Arrays.toString(array));
```

- 실행 결과 

    ```java
    [40, 1, 22, 3, 94, null, null, null, null, null, null, null, null, null, null, null, null, null, null, null, null, null, null, null, null, null, null, null, null, null, null, null, null, null, null, null, null, null, null, null, null, null, null, null, null, null, null, null, null, null]
    ```


이처럼 **_배열의 인덱스로 사용할 수 있도록 저장하려는 요소의 값을 계산해 얻은 인덱스를 '해시 인덱스(hash index)_** 라 한다.


### hash index를 사용해서 얻은 장점
1. 입력값의 범위가 넓어도 제한된 배열의 크기에서 값을 저장할 수 있으므로 메모리가 낭비되는 문제를 해결할 수 있다.
2. O(1) 성능으로 데이터를 저장할 수 있다.
3. O(1) 성능으로 데이터를 조회할 수 있다. 



## 해시 인덱스의 한계: 해시 충돌

그런데 지금까지 설명한 해시 인덱스에는 **_저장할 위치가 충돌할 수 있다_** 는 한계가 있다. 

예를 들어 다음 연산은 동일한 해시 인덱스를 가진다.
- `99 % 10 = 9`
- `9 % 10 = 9` 

어떤 값을 나중에 하냐에 따라서 인덱스 9에서 얻는 값이 달라진다. 

이 한계를 해결하기 위해 **_중첩 배열을 사용해 같은 해시 인덱스의 값을 다 저장한다. 이에 따라 해당 해시 인덱스의 배열에서 추가하려는 값이 존재하는지도 탐색해야 한다._** 

예를 들어 9, 19, 29, 39, 99 를 통해 나머지 연산 방식의 해시 인덱스를 얻으면 다 '9'를 얻는다. 그러면 이 값을 저장할 때마다 인덱스 9의 배열에 값이 이미 있는지 탐색해야 하므로 O(n)이 걸린다. 

```java
LinkedList<Integer>[] buckets = new LinkedList[10];
for (int i = 0; i < buckets.length; i++) {
    buckets[i] = new LinkedList<>();
}

int[] values = {1, 3, 22, 40, 9, 19, 99};
for (int value : values) {
    int hashIndex = value % 10;
    LinkedList<Integer> bucket = buckets[hashIndex];
    if (!bucket.contains(value)) {
        buckets[hashIndex].add(value);
    }
}

System.out.println(Arrays.toString(buckets));
```

- 출력 결과
    ```java
    [[40], [1], [22], [3], [], [], [], [], [], [9, 19, 99]]
    ```


**_중첩 배열을 사용해 해시 충돌이 일어나도 값은 저장할 수 있지만, 저장하기 위해 해당 해시 인덱스의 배열을 모두 탐색해 O(n)이 걸리므로 해시 충돌 발생을 막는 게 좋다. Set의 특징을 구현하려면 해시 인덱스가 필수이므로 해시 충돌을 완전히 피할 수 없다. 그래서 발생 확률을 줄일 수 있는 방법을 찾아야 한다._**

해시 충돌이 발생하는 확률은 배열의 크기와 관련 있다. 입력한 데이터 수보다 배열의 크기가 클수록 충돌 확률은 낮아진다. 그래서 크기 기준을 입력한 데이터의 수가 배열의 크기에서 75%를 채우면 크기를 대략 두 배 증가시킨다. 

`HashMap` 자료 구조의 설명 부분을 보면 알 수 있다. Set 자료구조의 한 종류인 HashSet은 HashMap의 인스턴스라서 `HashMap`의 설명 부분을 봤다.


> An instance of HashMap has two parameters that affect its performance: initial capacity and load factor. The capacity is the number of buckets in the hash table, and the initial capacity is simply the capacity at the time the hash table is created. The load factor is a measure of how full the hash table is allowed to get before its capacity is automatically increased. **_When the number of entries in the hash table exceeds the product of the load factor and the current capacity, the hash table is rehashed (that is, internal data structures are rebuilt) so that the hash table has approximately twice the number of buckets_**

그리고 클래스 변수에서 크기와 관련된 부분이 있다.

```java
    /**
     * The default initial capacity - MUST be a power of two.
     */
    static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; // aka 16

    /**
     * The maximum capacity, used if a higher value is implicitly specified
     * by either of the constructors with arguments.
     * MUST be a power of two <= 1<<30.
     */
    static final int MAXIMUM_CAPACITY = 1 << 30;

    /**
     * The load factor used when none specified in constructor.
     */
    static final float DEFAULT_LOAD_FACTOR = 0.75f;
```

기본값은 2의 4승인 16이고, 최대 크기는 2의 30승이다. 로드 팩터는 배열을 증가시키는 기준 값이다. 여기서 보면 0.75로 75%임을 알 수 있다. 

너무 낮아도 안되고 너무 커도 안된다고 설명한다.

> This implementation provides constant-time performance for the basic operations (get and put), assuming the hash function disperses the elements properly among the buckets. Iteration over collection views requires time proportional to the "capacity" of the HashMap instance (the number of buckets) plus its size (the number of key-value mappings). **_Thus, it's very important not to set the initial capacity too high (or the load factor too low) if iteration performance is important._**



이처럼 Set 자료 구조에서 hash는 매우 중요한 핵심이다. 이제 Set의 종류 중 하나인 HashSet을 구현해 보자. 