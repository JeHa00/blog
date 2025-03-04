---
title: "[Data structure] HashSet에 대해 설명해 줄게!"
date: 2024-11-10T10:45:35+09:00
draft: false
summary: 해시를 사용해 만든 자료구조 HashSet을 알아보자.
tags: ["java", "datastructure", "algorithum"]
categories: "Data Structure & Algorithum"
---

# Introduction
---

프로그래밍을 하다 보면 데이터를 다루는 때가 많고, 이 데이터를 어떠한 구조로 저장해야 하는지 고민하는 시간이 많다. 이 데이터를 저장하는 구조를 '자료 구조'라 한다.

자바를 사용해 여러 자료 구조를 구현해  보며 해당 자료구조의 기능별 시간 복잡도가 어떻게 그렇게 나오는지 '설명해 줄게!' 시리즈를 연재한다. 

1. [[Data structure] ArrayList를 설명해 줄게!](https://jeha00.github.io/post/datastructure/arraylist/)
2. [[Data structure] Singly LinkedList를 설명해 줄게!](https://jeha00.github.io/post/datastructure/linkedlist/)
3. [[Data structure] List를 설명해 줄게!](https://jeha00.github.io/post/datastructure/list/)
4. [[Data structure] Hash를 설명해 줄게!](https://jeha00.github.io/post/datastructure/list/hash)
5. [[Data structure] HashSet을 설명해 줄게!](https://jeha00.github.io/post/datastructure/list/hashset)
6. [[Data structure] Set을 설명해 줄게!](https://jeha00.github.io/post/datastructure/list/set)
7. [Data structure] Map을 설명해 줄게! 
8. [Data structure] Stack을 설명해 줄게!
9. [Data structure] Queue를 설명해 줄게!

&nbsp;

# 1. HashSet 구현하기 

---

## 1.1 HashSet은 사실 HashMap을 사용해 구현한 자료구조

> ❗️ 만약 Map 자료 구조에 대해 잘 모르시는 분이라면 1.1은 생략하셔도 됩니다!

Set을 구현한 자료구조는 실제로 HashMap을 상속받는다. Map은 key, value로 이뤄진 자료 구조인데, Set을 구현한 자료구조는 이 value에 기본값으로 Object를 추가한다. 

아래 코드를 확인해보자. 아래 코드는 HashSet의 소스 코드다. 

```java
public class HashSet<E>
    extends AbstractSet<E>
    implements Set<E>, Cloneable, java.io.Serializable
{
    static final long serialVersionUID = -5024744406713321676L;

    private transient HashMap<E,Object> map;

    // Dummy value to associate with an Object in the backing Map
    private static final Object PRESENT = new Object();

    public boolean add(E e) {
            return map.put(e, PRESENT)==null;
    }

    ...
}
```

- map 변수를 보면 `<E, Object>` 로 value의 자료형이 Object인 것을 알 수 있다.
- add() 메서드를 보면 key로 `E e`를, value로 `PRESENT`를 넣는 것을 알 수 있고, 이 `PRESENT`는 `new Object`로 dummay value라고 주석으로 설명한다.

즉, HashSet은 value가 Object인 key로만 이뤄진 HashMap이다. HashMap은 value의 값을 수정할 수 있지만 관련된 메서드를 HashSet에 구현하지 않음으로 Set을 구현했다. 

Set 인터페이스를 구현한 메서드들도 사실 HashSet의 메서드를 사용해 구현한 것이다. 

- add(): HashSet의 put() 메서드를 사용
- remove(): HashSet의 remove() 메서드 사용
- contains(): HashSet의 containKey() 메서드를 사용
- iterator(): HashSet의 keySet().iterator() 메서드를 사용

등등 HashMap 부터 설명하는 게 코드 구현 순서상 맞지만 Set 자료구조 자체에 대해 알아보기 위해 직접 구현해보며 어떻게 시간 복잡도가 이렇게 나오는지 이해하려 한다. 


## 1.2 HashSet 구현하기 

### 기본 필드 

```java
    static final int DEFAULT_INITIAL_CAPACITY = 16;
    private LinkedList<E>[] buckets;
    private int size = 0;
    private int capacity = DEFAULT_INITIAL_CAPACITY;
```

- DEFAULT_INITIAL_CAPACITY: 기본 용량 값이 16이다. 왜냐하면 HashMap의 기본 크기가 2의 4승인 16이기 때문이다. 
- buckets: Set은 중복 없이 유일한 값만 보관하기 때문에 미리 메모리를 확보할 필요가 없으므로 LinkedList의 참조 방식을 사용한다.
    - 실제 HashMap에서는 값을 노드 방식으로 관리한다. 
        ```java
        Node<K, V>[] tables
        ```
        - tables는 ArrayList의 elementData처럼 값을 보관하는 장소라고 생각하자.
        - 그래서 초기 크기 16에서 75% 정도 차면 이 tables의 크기를 2배로 늘린 후, 재해싱(rehashing)한다.
- size: 현재 가지고 있는 요소의 개수  
- capacity: DEFAULT_INITIAL_CAPACITY가 기본값이지만 생성자를 통해 원하는 값으로 변경할 수 있다. 

### 생성자

```java
    public MyHashSet() {
        initBuckets();
    }

    public MyHashSet(int capacity) {
        this.capacity = capacity;
        initBuckets();
    }
```

capacity를 직접 입력하는 방식과 기본값을 사용하는 방식으로 나눠진다. 실제 자바 소스 코드에서 생성자를 사용해 내부 필드를 수정하는 방식으로 만들어져 있다.

내부 필드 buckets이 초기화가 안되어 있으므로 initBuckets() 메서드를 반드시 실행해야 한다. 


### 초기자 

```java
    private void initBuckets() {
        buckets = new LinkedList[capacity];
        for (int i = 0; i < capacity; i++) {
            buckets[i] = new LinkedList<>();
        }
    }
```

지정한 capacity만큼의 LinkedList 배열을 생성해 buckets에 할당한다. 
capaicty만큼 순회하며 생성된 새로운 LinkedList를 buckets의 각 요소에 할당한다.

### hashIndex: 비트 연산자와 나머지 연산자

```java
    private int hashIndex(Object value) {
        return Math.abs(value.hashCode()) % capacity;
    }
```

hashCode() 를 사용해 value을 해시 코드를 계산한다. 해시 코드가 음수일 때가 있으므로 절대값으로 환산하고 capacity를 사용해 해시 인덱스를 계산한다. 

실제 자바 소스 코드에서도 hashCode()를 사용하고 나머지 연산자를 사용한 나머지 연산이 아닌 비트 연산자를 사용해서 구한다. 

```java
// 실제 소스 코드
// tab은 해시 테이블을 의미한다. 
// hash는 해시 코드를 의미한다. 
// n은 해시 테이블의 길이를 의미한다.
tab[index = (n - 1) & hash]
```

- 나머지 연산자를 사용하는 방식과 비트 연산자를 사용한 방식
    ```java

    int capacity = 16;
    long time = System.currentTimeMillis();
    for (int i = 0; i < 3_000_000; i++) {
        int c = i % capacity;
    }
    System.out.println(System.currentTimeMillis() - time + "ms");


    time = System.currentTimeMillis();
    for (int i = 0; i < 3_000_000; i++) {
        // 비트 연산자로 나머지를 계산할 때는 나눠지는 숫자를 1 빼야 한다. 
        int c = (capacity - 1) & i;
    }
    System.out.println(System.currentTimeMillis() - time + "ms");
    ```

- 실행 결과

    ```java
    5ms
    3ms
    ```


비트 연산자를 구한 방식이 훨씬 빠르다는 것을 알 수 있다. 


### add

```java
    public boolean add(E value) {
        int hashIndex = hashIndex(value);
        LinkedList<E> bucket = buckets[hashIndex];
        if (bucket.contains(value)) {
            return false;
        }
        bucket.add(value);
        size++;
        return true;
    }
```

- hashIndex() 메서드를 사용해 value에 해당하는 해시 인덱스를 구한다. 
- 해시 인덱스를 통해 해당되는 bucket 참조값을 얻는다. 
- contains 메서드를 사용해 해당값의 유무를 확인한다. 
- 있으면 false를 반환하고, 없으면 추가한 후 size의 값을 1 증가시킨다. 


### remove

```java
   public boolean remove(E value) {
        int hashIndex = hashIndex(value);
        LinkedList<E> bucket = buckets[hashIndex];
        if (bucket.remove(value)) {
            size--;
            return true;
        } else {
            return false;
        }
    }
```

- add() 메서드와 구조가 비슷하다. 단지 차이는 LinkedList의 remove() 메서드를 호출한다는 것과 삭제에 성공할 경우 size의 값을 1 감소시킨다는 것이다.

### contains

```java
   public boolean contains(E value) {
        int hashIndex = hashIndex(value);
        LinkedList<E> bucket = buckets[hashIndex];
        return bucket.contains(value);
    }
```

contains도 동일하다.


### getSize & isEmpty()

```java
    public int getSize() {
        return size;
    }

    public boolean isEmpty() {
        return size == 0;
    }
```

- size의 필드가 private이므로 이를 얻으려면 getter를 정의해야 한다.
- 현재 셋이 비어있는지 확인하기 위해 isEmpty()를 정의한다.

### clear

```java
    public void clear() {
        for (int i = 0; i < capacity; i++) {
            buckets[i].clear();
        }
        size = 0;
    }
```

- LinkedList의 clear() 메서드를 사용해 초기화한다. 
- 각 bucket 초기화를 완료하면 size 를 0으로 초기화한다.


&nbsp;

# 2. hashCode()와 equals() 메서드의 영향

---

## 2.1 두 메서드를 각각 재정의하지 않을 때 발생되는 문제

hashCode()와 equals() 메서드의 역할을 코드로 확인해보자. 

위 두 메서드를 테스트하기 위한 객체르 하나 만들자. 

```java
import java.util.Objects;

public class Member {
    private String id;

    public Member(String id) {
        this.id = id;
    }

    public String getId() {
        return id;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof Member)) return false;
        Member member = (Member) o;
        return Objects.equals(id, member.id);
    }

    @Override
    public int hashCode() {
        return Objects.hash(id);
    }


    @Override
    public String toString() {
        return "Member{" +
                "id='" + id + '\'' +
                '}';
    }
}
```

### 두 메서드 모두 구현하지 않은 경우

위 코드에서는 두 메서드가 모두 구현되어 있지만 아래 코드를 실행할 때는 없이 실행했다. 

- 실행 코드

    ```java
    MyHashSet<Member> set = new MyHashSet<>(16);
    Member m1 = new Member("A");
    Member m2 = new Member("A");
    System.out.println("m1.hashCode() = " + m1.hashCode());
    System.out.println("m2.hashCode() = " + m2.hashCode());
    System.out.println("m1.equals(m2) = " + m1.equals(m2));

    set.add(m1);
    set.add(m2);
    System.out.println(set);

    Member searchValue = new Member("A");
    System.out.println("searchValue.hashCode() = " + searchValue.hashCode());
    boolean contains = set.contains(searchValue);
    System.out.println("contains 결과 = " + contains);
    ```

- 실행 결과

    ```
    m1.hashCode() = 288665596
    m2.hashCode() = 762218386
    m1.equals(m2) = false
    MyHashSet{buckets=[[], [], [Member{id='A'}], [], [], [], [], [], [], [], [], [], [Member{id='A'}], [], [], []], size=2, capacity=16}
    searchValue.hashCode() = 1796488937
    contains 결과 = false
    ```


- hashCode()를 재정의하지 않은 결과
    - 해시 코드는 참조값을 기반으로 생성된다. 두 Member 객체의 참조값은 다르기 때문에 서로 다른 해시 코드가 생성된다. 
    - 그래서 논리적으로 동일해도 해시 코드가 달라 해시 인덱스가 다르므로, 다른 bucket에 저장되는 것을 확인할 수 있다. 
    - 서로 다른 버킷에 있으므로 contains의 결과가 false가 나온다. 

- equals()를 재정의하지 않은 결과
    - 검색할 때, 논리적으로 같은 값이 있어도 참조값으로 비교된다.
    - m1과 m2를 비교할 때 false가 나온다.
    - contains() 메서드의 결과도 false가 된다. 


### hashCode()만 구현한 경우

실행 코드는 동일하다.

- 실행 결과

    ```
    m1.hashCode() = 96
    m2.hashCode() = 96
    m1.equals(m2) = false
    MyHashSet{buckets=[[Member{id='A'}, Member{id='A'}], [], [], [], [], [], [], [], [], [], [], [], [], [], [], []], size=2, capacity=16}
    searchValue.hashCode() = 96
    contains 결과 = false
    ```

- hashCode()를 재정의한 결과
    - 논리적으로 동일하므로 생성된 hashCode의 결과가 같다.
    - 그래서 동일한 해시 인덱스를 가지기 때문에 같은 bucket에 저장되는 것을 확인할 수 있다. 

- equals()를 재정의하지 않은 결과
    - 논리적으로 같아 이미 존재하는 값이어도, equals()를 재정의하지 않았기 때문에 참조값 기준으로 다른 값으로 판단되어 값이 중복으로 들어간다. 


### equals()만 구현한 경우

실행 코드는 동일하다.

- 실행 결과

    ```
    m1.hashCode() = 288665596
    m2.hashCode() = 762218386
    m1.equals(m2) = true
    MyHashSet{buckets=[[], [], [Member{id='A'}], [], [], [], [], [], [], [], [], [], [Member{id='A'}], [], [], []], size=2, capacity=16}
    searchValue.hashCode() = 1796488937
    contains 결과 = false
    ```

- hashCode()를 재정의하지 않은 결과
    - 생성되는 hashCode()가 달라서 해시 인덱스 값이 다르므로 논리적으로 같아도 다른 bucket에 저장된다. 

- equals()를 재정의하지 않은 결과
    - 논리적으로 같아도 참조값으로 비교되어 false로 나온다.


### 모두 구현한 경우  

- 실행 결과

    ```
    m1.hashCode() = 96
    m2.hashCode() = 96
    m1.equals(m2) = true
    MyHashSet{buckets=[[Member{id='A'}], [], [], [], [], [], [], [], [], [], [], [], [], [], [], []], size=1, capacity=16}
    searchValue.hashCode() = 96
    contains 결과 = true
    ```

- hashCode가 같아 해시 인덱스가 같다.
- 해시 인덱스가 같으므로 m1과 m2의 배정되는 버킷은 동일하다. 
- equals()를 재정의해 논리적인 비교가 가능하므로 값이 중복으로 들어가지 않는다.  
- contains의 결과가 true가 나온다. 

## 2.2 두 메서드의 역할 정리 ❗️

- hashCode() 메서드의 역할은 해당 객체의 논리적인 값에 따라 해당 객체가 속한(속할) 버킷을 지정하는 역할입니다.

- equals() 메서드는 두 객체가 동등한지 판단할 수 있도록 비교하는 역할입니다. 재정의하지 않으면 참조값을 기준으로 비교하기 때문에 동등성 비교가 아닌 동일성 비교가 됩니다. 

&nbsp;

# 3. 전체 코드

---

```java
import java.util.Arrays;
import java.util.LinkedList;

public class MyHashSet<E> {
    static final int DEFAULT_INITIAL_CAPACITY = 16;
    private LinkedList<E>[] buckets;
    private int size = 0;
    private int capacity = DEFAULT_INITIAL_CAPACITY;

    public MyHashSet() {
        initBuckets();
    }

    public MyHashSet(int capacity) {
        this.capacity = capacity;
        initBuckets();
    }

    private void initBuckets() {
        buckets = new LinkedList[capacity];
        for (int i = 0; i < capacity; i++) {
            buckets[i] = new LinkedList<>();
        }
    }

    public boolean add(E value) {
        int hashIndex = hashIndex(value);
        LinkedList<E> bucket = buckets[hashIndex];
        if (bucket.contains(value)) {
            return false;
        }
        bucket.add(value);
        size++;
        return true;
    }

    public boolean remove(E value) {
        int hashIndex = hashIndex(value);
        LinkedList<E> bucket = buckets[hashIndex];
        if (bucket.remove(value)) {
            size--;
            return true;
        } else {
            return false;
        }
    }

    public boolean contains(E value) {
        int hashIndex = hashIndex(value);
        LinkedList<E> bucket = buckets[hashIndex];
        return bucket.contains(value);
    }

    private int hashIndex(Object value) {
        return Math.abs(value.hashCode()) % capacity;
    }

    public int getSize() {
        return size;
    }

    public boolean isEmpty() {
        return size == 0;
    }

    public void clear() {
        for (int i = 0; i < capacity; i++) {
            buckets[i].clear();
        }
        size = 0;
    }

    @Override
    public String toString() {
        return "MyHashSet{" +
                "buckets=" + Arrays.toString(buckets) +
                ", size=" + size +
                ", capacity=" + capacity +
                '}';
    }
}
```