---
title: "[Data structure] Singly LinkedList 설명해 줄게!"
date: 2024-09-20T23:31:24+09:00
draft: false
summary: 이번에는 LinkedList에 대해 학습한다!
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
4. [Data structure] Hash를 설명해 줄게!
5. [Data structure] HashSet을 설명해 줄게!
6. [Data structure] Set을 설명해 줄게! 
7. [Data structure] Map을 설명해 줄게! 
8. [Data structure] Stack을 설명해 줄게!
9. [Data structure] Queue를 설명해 줄게!


&nbsp;

# 1. 연결 리스트 (LinkedList)

---

## ArrayList와의 차이점

### 첫 번째, 메모리가 할당되는 방식이 다르다.

ArrayList는 **배열을 기반**으로 하기 때문에 **메모리가 연속적으로** 할당된다. 반대로 LinkedList는 **노드를 기반으로 하기 때문에 참조를 사용한 연결 방식**이므로 **메모리가 산발적으로** 할당된다. 그래서 ArrayList는 배열의 크기를 동적으로 조절하는 방식이므로 메모리 공간에 있어서 낭비되는 것을 피할 수 없다. LinkedList는 참조 형식으로 필요한 만큼만 메모리를 사용하는 장점이 있다.


### 두 번째, 시간복잡도 관점

> **_두 자료 구조의 시간 복잡도 차이는 배열 기반으로 한 임의 접근과 노드 참조 기반으로 한 순차 접근으로 인해 발생된다._**

먼저 LinkedList 중 Singly LinkedList를 기반으로 시간 복잡도를 비교한다.


| 기능 | 배열 리스트 | 연결 리스트 |
|---| ---| ---|
|인덱스 조회 |O(1)|O(n)|
|검색|O(n)|O(n)|
|앞에 추가(삭제)|O(n)|O(1)|
|뒤에 추가(삭제)|O(1)|O(n)|
|중간 추가(삭제)|O(n)|O(n)|


- 인덱스 조회
    - 배열 리스트는 **배열 기반으로 임의 접근(_random access_)으로** 인덱스를 사용해 바로 접근할 수 있어 O(1)로 매우 빠르다. 
    - 연결 리스트는 **노드 기반으로 임의 접근이 불가능한 순차 접근(_sequential access_)으로** 반드시 순차적으로 접근해야지만 n번째 객체에 접근할 수 있어 O(n)이다. 
- 검색: 배열 리스트와 연결 리스트 모두 처음부터 모든 요소에 접근해야 하므로 O(n)이다. 
- 앞에 추가(삭제)
    - 배열 리스트는 인덱스를 사용해 추가(삭제)할 자리를 바로 찾을 수 있으므로 여기까지는 O(1)이다. 하지만, 앞 또는 중간에 데이터를 추가(삭제)하려면 해당 위치에 공간을 확보해야 한다. 이를 위해 추가할 때는 인덱스가 커지는 방향으로 자리를 확보해야 하므로 O(n)이 걸린다. 삭제할 때는 삭제한 후 빈 공간을 메꿔야 하므로 인덱스가 작아지는 방향으로 값을 다 옮겨야 하므로 O(n)이 걸린다.
    - 연결 리스트는 참조 방식이므로 배열 리스트처럼 자리를 확보한다든가, 자리를 다시 메꾸는 것이 불필요하다. 참조값만 변경하면 추가 또는 삭제가 바로 가능하므로 O(1)이다. 하지만 추가(삭제)하기 위한 자리를 찾기까지 순차 접근을 해야 하므로 O(n)이 걸린다. 만약 추가(삭제)해야 하는 자리가 맨 앞이면 O(1)이므로 총 O(1)이 걸린다.
- 중간에 추가(삭제) 
    - 배열 리스트는 '앞에 추가(삭제)하는 경우'와 동일하게 총 O(n)이 걸린다.
    - 연결 리스트는 중간 자리까지 탐색해야 하므로 O(n)이 걸리므로 총 O(n)이 걸린다. 
- 맨 뒤에 추가(삭제)
    - 배열 리스트
        - 인덱스로 마지막 위치를 바로 찾을 수 있다: O(1)
        - 마지막에 데이터를 추가하므로 이동하지 않는다: O(1)
        - 따라서 O(1)의 성능을 제공한다. 
    - 연결 리스트
        - 노드를 마지막까지 순회해야 마지막 노드를 찾을 수 있다: O(n)
        - 데이터를 추가하는 경우 일부 노드의 참조만 변경하면 된다: O(1)
        - 따라서 O(n)의 성능을 제공한다.


Doubly LinkedList도 존재한다. 

이 자료구조의 시간 복잡도는 Singly LinkedList의 코드 구조를 먼저 설명한 후 진행하겠다. 


&nbsp;

# 2. 단일 연결 리스트 구현하기

---

## 2.1 Node 구현하기 

LinkedList는 노드를 사용한 참조 기반 List이므로 노드를 구현해야 한다. 여기서 고려해야 할 점은 다음과 같다.
1. LinkedList만 사용하므로 '중첩 클래스'로 선언한다. 이때 private으로 선언한다. 이 방식이 유지보수를 하기도 편하다.
2. static을 사용한 이유는 다음과 같다. 
- 노드는 LinkedList의 인스턴스를 통해서만 접근해야 하는 객체가 아니다. 즉, 노드를 생성할 때 상위 클래스에 대한 인스턴스 참조 메모리가 불필요하다.
- static을 사용하지 않으면 각 노드는 LinkedList의 인스턴스를 계속 참조하게 되어 메모리 낭비가 발생한다.
3. 여러 타입을 사용할 수 있도록 제네릭을 사용한다.


```java
public class MyLinkedList<E> {
    ...

    private static class Node<E> {
        E item;
        Node<E> next;

        Node(E item) {
            this.item = item;
        }

    }
}
```


한 노드의 값이 다음과 같다고 하자.

| item | next | 
| --- | --- | 
| 25(int) | x002 | 

위 노드의 의미는 다음과 같다.

- item은 int로 25이고,
- next에는 다음 노드의 참조값인 x002를 가지고 있어서 다음 노드로 이동할 수 있다. 

next에는 예를 들어 실제 아파트가 아닌 아파트의 주소가 들어가 있다고 생각하면 된다. 그래서 ArrayList가 Object[] 를 사용하는 방식과 달리 LinkedList는 여러 노드를 체인(chain)처럼 연결한다.


## 2.2 MyLinkedList의 필드 선언

```java
public class MyLinkedList <E> {

    private Node<E> first;
    private int size;
    ...
}
```

- first: 리스트의 가장 첫 노드를 가리키는 포인트로 사용되는 변수를 의미한다.
    - 이 필드가 있어서 맨 앞쪽 데이터에 바로 접근할 수 있어서 O(1)이 걸린다. 
    - 이 필드를 통해서 여러 노드에 접근할 수 있다. 
- size: LinkedList가 가지고 있는 요소의 개수를 의미한다.

❗️last 필드
만약 맨 마지막 노드의 참조값을 가지는 last가 존재한다면 맨 마지막에 값을 추가하는 것도 O(1)이 걸린다. 자바에서 제공하는 LinkedList의 소스 코드를 보면 last 필드를 확인할 수 있다. 
이러한 LinkedList를 이중 연결 리스트(Doubly Linked List)라 한다. 



## 2.3 연결 리스트에 노드 추가하기 

### 요소만을 사용해서 추가하기


```java
    public void add(E element) {
        Node<E> newNode = new Node<>(element);
        if (size == 0) {
            first = newNode;
        } else {
            Node<E> x = getLastNode();
            x.next = newNode;
        }
        size++;
    }
```

add(E element) 메서드는 맨 마지막에 새로운 노드를 추가하는 메서드다. size가 0이면 새로 추가하는 노드는 first이므로 first 필드에 새로운 노드 참조값을 저장한다. 만약 size가 0이 아니면 getLastNode 메서드를 사용해 제일 마지막 노드를 조회한 후, 해당 노드의 next 필드에 저장한다. 

add 메서드에 size에 따라 분기 처리를 한 이유는 이 메서드의 역할의 응집도를 높이기 위해서다. `addFirst`로 나눌 수 있으나, 이런 경우 함수를 호출할 때 size의 값을 조회한 후 사용해야 하므로 위와 같이 메서드 내에서 분기 처리를 하는 게 더 좋다.

마지막으로 노드를 추가했으므로 반드시 size 값을 증가시킨다.

그러면 LinkedList에 노드를 추가해 보자.

- 메인 코드
    ```java
    MyLinkedList list = new MyLinkedList();
    System.out.println(list);
    list.add("a"); // x001이라 하자.
    System.out.println(list);
    list.add("b"); // x002
    list.add("c"); // x003
    list.add("d"); // x004
    System.out.println(list);
    ```
- 결과
    ```java
    []
    [a]
    [a -> b -> c -> d]
    ```

|LinkedList| size: 4 |
|---| --- | --- |--- |--- |--- |--- |--- |--- |
|index| 0(first) ||1 ||2 || 3
| Node | x001 | ----(link)----> | x002 | ----(link)----> | x003 | ----(link)----> |x004 |
| item | a |  | b|  | c |  | d |  |
| next | x002 | |x003 | | x004 | | **null**|



### 위치를 지정해서 추가하기

```java

    public void add(int index, E element) {
        Node<E> newNode = new Node<>(element);
        if (index == 0) {
            newNode.next = first;
            first = newNode;
        } else {
            Node<E> prev = getNode(index - 1);
            Node<E> next = prev.next;

            prev.next = newNode;
            newNode.next = next;
        }
        size++;
    }

```

index가 0이면 first 위치이므로 새로운 노드의 next 값에 기존 first 값을 전달한 후, 기존 first에 새로운 노드의 참조값을 전달한다. 이 순서를 지키지 않으면 새로운 노드의 다음 노드는 자기 자신이 된다.

index가 0이 아니면 중간에 노드를 추가해야 한다. 참조값만 변경하면 되므로 해당 인덱스 직전의 노드(prev)와 prev의 다음 노드, next 참조값을 얻는다. 다음으로 아래 순서대로 참조값을 변경한다.
1. prev의 다음 노드 참조값을 newNode로 변경
2. newNode의 다음 노드 참조값을 Node의 next로 지정한다.

배열 리스트보다 요소를 추가하는 것이 매우 쉬운 것을 알 수 있다! 

하지만, getNode() 메서드를 볼 때 알 수 있지만 index까지 순차 접근하므로 O(n)이 걸린다.

마지막으로 노드를 추가하는 것이기 때문에 size의 값을 증가시킨다.

아래 같은 상황에서 `add(2, "e")`를 실행한다고 하자.


|LinkedList| size: 4 |
|---| --- | --- |--- |--- |--- |--- |--- |--- |
|index| 0(first) ||1 ||2 || 3
| Node | x001 | ----(link)----> | x002 | ----(link)----> | x003 | ----(link)----> |x004 |
| item | a |  | b|  | c |  | d |  |
| next | x002 | |x003 | | x004 | | **null**|


추가되면 다음과 같이 LinkedList는 변경된다.

|LinkedList|  size: 5 | | prev | | newNode |
|---| --- | --- |--- |--- |--- |--- |--- |--- | --- |
|index| 0(first) ||1 ||**2** || 3 || 4| 
| Node | x001 | -----> | x002 | -----> | **x005** | -----> |x003 | -----> |x004 |
| item | a |  | b|  | **e**|  | c |  | d |  |
| next | x002 | |**_x003 -> x005_** 로 변경 | |**x003** | | x004 | | **null**|



## 2.4 연결 리스트에 노드 제거하기

### 인덱스로 삭제하기
```java

    public E remove(int index) {
        Node<E> removedNode = getNode(index);
        E removedItem = removedNode.item;

        if (index == 0) {
            first = removedNode.next;
        } else {
            Node<E> prev = getNode(index - 1);
            prev.next = removedNode.next;
        }

        removedNode.item = null;
        removedNode.next = null;
        size--;
        return removedItem;
    }
```

- 인덱스가 0인 경우, first를 삭제하는 것이기 때문에 first의 다음 노드를 first로 지정하면 원래 first 노드를 참조하는 객체가 없어 GC에 의해 삭제된다. 

- 인덱스가 0이 아닌 경우, 해당 인덱스 직전의 노드(prev)를 조회해 prev의 다음 노드를 해당 인덱스 노드(removedNode)의 다음 노드(next)로 지정한다. 그러면 removedNode를 참조하는 객체는 존재하지 않아 삭제된다. 

- 삭제이므로 size의 값을 감소시킨다. 

LinkedList의 상황이 다음과 같다고 하자.

`remove(2)`를 수행한다면 어떻게 될까?
- `getNode(2)`가 실행되어 `removedNode`에는 x003이 할당된다.
- `removedItem`에는 e가 할당된다.
- index의 값이 0이 아니므로 else 문에 진입한다.
- index가 2이므로 `getNode(1)`이 실행되어 `prev`에는 x005가 할당된다. 
- `prev.next`는 x003 노드의 다음 노드인 x004가 할당된다. 
- x003 노드의 item과 next에는 null이 할당된다. 
- size의 값은 5에서 4로 감소된다.


|LinkedList|  size: 5 | |prev | |removedNode |
|---| --- | --- |--- |--- |--- |--- |--- |--- | --- |
|index| 0(first) ||1 ||**2** || 3 || 4| 
| Node | x001 | -----> | x002 | -----> | **x005** | -----> |x003 | -----> |x004 |
| item | a |  | b|  | **e**|  | c |  | d |  |
| next | x002 | |x005| |**x003** | | x004 | | null|

삭제하면 다음과 같이 변한다.

|LinkedList|  size: 4 |
|---| --- | --- |--- |--- |--- |--- |--- |--- |
|index| 0(first) ||1 || 2 || 3| 
| Node | x001 | -----> | x002 | -----> |x003 | -----> |x004 |
| item | a |  | b|  | c |  | d |  |
| next | x002 | |**_x005 -> x003_** 로 변경| | x004 | | null|


removedNode의 값은 다음과 같이 되고 GC에 의해 삭제된다.

| Node | x003|
| --- | --- | 
| item | null |
| next | null | 

### 요소 값으로 삭제하기

```java
    public boolean remove(E item) {
        Node<E> x = first;
        Node<E> prev = null;

        while (x != null) {
            if (x.item.equals(item)) {
                break;
            }

            prev = x;
            x = x.next;
        }

        if (x == first) {
            remove(0);
            return true;
        }

        if (x == null) {
            return false;
        }

        prev.next = x.next;
        x.next = null;
        x.item = null;
        size--;
        return true;
    }
```

item을 기준으로 Node를 삭제할 수 있다. 
- 만약 item과 동일한 값이 없으면 삭제할 수 없으므로 false를 반환한다. 
- item과 동일한 값의 Node(x)가 존재한다면 prev의 next에 x의 next를 할당한다. 
- 만약 x가 first면 첫 인덱스 값을 삭제하는 것이므로 remove(0)을 사용해 삭제한다.

삭제이므로 size의 값을 감소시킨다.

삭제에 성공했으므로 true를 반환한다. 

### ❗️ Node 삭제 시 item과 next에 null을 할당하는 이유 

첫 번째, A 객체가 다른 객체(B)를 참조하고 있으면 GC(Garbage Collector)는 A가 다른 객체를 강하게 참조한다고 간주해서 메모리 회수를 조금 늦게 한다. 그래서 만약 item이나 next의 값에 null을 할당하면 다른 객체 참조를 하지 않기 때문에 메모리 회수를 더 빠르게 할 수 있다. 

두 번째, LinkedList 같이 노드 기반인 자료 구조는 노드를 삭제해도 삭제된 노드를 다른 노드나 외부에서 참조될 수 있는 가능성이 존재한다. 그러면 계속 메모리에 남아 메모리 누수(Memory Leak)가 발생할 수 있으므로 삭제 시 참조를 끊고 불필요한 객체를 메모리에서 안전하게 제거하기 위해 할당한다. 

세 번째, 데이터 유출 방지 목적도 있다. 메모리에 남으면 삭제되어야 하는 데이터가 유출될 수 있기 때문이다.

## 2.5 연결 리스트에서 노드 조회하기 

### 인덱스로 해당 노드의 값 조회하기

```java
    public E get(int index) {
        Node<E> x = first;
        for (int i = 0; i < index; i++) {
            x = x.next;
        }
        return x.item;
    }
```

for 문을 사용해 index만큼 반복을 수행한다. 반복을 수행해 나온 x값이 index 번째에 위치한 Node의 item 값이다.

### 마지막 노드 조회하기

```java
    public Node<E> getLast() {
        Node<E> x = first;
        while (x.next != null) {
            x = x.next;
        }
        return x;
    }
```

이 메서드는 getNode() 메서드와 size를 사용해 대신할 수 있지만 이와 같이 마지막 노드를 위한 메서드를 만들면 굳이 size 조회를 위한 메서드를 사용할 필요 없이 바로 알 수 있다.

### 첫 번째 노드 조회하기 

```java
    public Node<E> getFirstNode() {
        return first;
    }
```

first 필드를 사용해 LinkedList의 첫 번째 노드를 조회한다.

## 2.6 기타 메서드 

### indexOf

```java
    public int indexOf(E element) {
        Node<E> x = first;
        int index = 0;
        while (x != null) {
            if (x.item.equals(element)) {
                return index;
            }
            x = x.next;
            index++;
        }
        return -1;
    }
```

first의 참조값을 x에 복사하는 이유는 x의 값을 다음 노드로 자체 수정하기 위함이다. 

while 반복문을 사용해도 되고 for 반복문을 사용해도 된다. 반복문을 진행하면서 x를 다음 노드로 수정하고, index의 값을 증가시킨다. 만일 x의 item이 element와 동일하면 그때 index를 반환한다. 

동일한 값의 노드를 찾지 못하면 -1을 반환한다.

### set

```java
    public E set(int index, E item) {
        Node<E> node = get(index);
        E oldValue = node.item;
        node.item = item;
        return oldValue;
    }
```

특정 인덱스의 노드 item 값을 수정할 때 사용한다. 특정 index의 노드를 조회할 때는 get() 메서드를 사용한다. 그리고 이 노드의 item 속성에 직접 접근해 매개변수로 받은 item을 할당한다.

### size

```java
    public int size() {
        return size;
    }
```

LinkedList의 size 값을 알기 위해서 필요하다. 필드는 private으로 선언했기 때문이다. 보안을 위해 이 size를 변경하기 위한 별도의 메서드는 존재하지 않는다. 

### clear

```java
    public void clear() {
        for (Node<E> x = first; x != null; ) {
            Node<E> next = x.next;
            x.item = null;
            x.next = null;
            x = next;
        }
        first = null;
        size = 0;
    }
```

LinkedList 인스턴스에 속한 모든 노드를 삭제한다. 삭제하려면 GC에 의해서 삭제되어야 하므로 각 노드에 접근해 null로 초기화한다. 마지막으로 size도 0으로 초기화한다.

### toString

```java
    @Override
    public String toString() {
        StringBuilder sb = new StringBuilder();
        sb.append("[");

        Node<E> x = first;
        for (int i = 0; i < size; i++) {
            sb.append(x.item);
            if (x.next != null) {
                sb.append(" -> ");
            }
            x = x.next;
        }

        sb.append("]");
        return sb.toString();
    }
```

- LinkedList를 출력할 때 호출되는 메서드다.
- StringBuilder를 사용하는 이유는 가변형으로 String으로 계속 연산하는 것보다 훨씬 빠르다. String은 하나의 값이 추가될 때 기존 객체에 추가되는 것이 아닌 새로운 객체가 생성되는 불변형이라 연산 속도가 느리다. 문자열 연산을 할 때는 StringBuilder를 사용한 후 toString() 메서드를 사용하는 게 훨씬 빠르다. 
- size 수 만큼 반복문을 수행해 노드의 item 값을 sb에 추가한다.


&nbsp;

# 3. 단일 연결 리스트 전체 코드 

---
```java
public class MyLinkedList <E> {

    private Node<E> first;
    private int size;

    public void add(E element) {
        Node<E> newNode = new Node<>(element);
        if (size == 0) {
            first = newNode;
        } else {
            Node<E> x = getLastNode();
            x.next = newNode;
        }
        size++;
    }

    public void add(int index, E element) {
        Node<E> newNode = new Node<>(element);
        if (index == 0) {
            newNode.next = first;
            first = newNode;
        } else {
            Node<E> prev = getNode(index - 1);
            Node<E> next = prev.next;

            prev.next = newNode;
            newNode.next = next;
        }
        size++;
    }

    public E remove(int index) {
        Node<E> removedNode = getNode(index);
        E removedItem = removedNode.item;

        if (index == 0) {
            first = removedNode.next;
        } else {
            Node<E> prev = getNode(index - 1);
            prev.next = removedNode.next;
        }

        removedNode.item = null;
        removedNode.next = null;
        size--;
        return removedItem;
    }

    public boolean remove(E item) {
        Node<E> x = first;
        Node<E> prev = null;

        while (x != null) {
            if (x.item.equals(item)) {
                break;
            }

            prev = x;
            x = x.next;
        }

        if (x == first) {
            remove(0);
            return true;
        }

        if (x == null) {
            return false;
        }

        prev.next = x.next;
        x.next = null;
        x.item = null;
        size--;
        return true;
    }

    public int indexOf(E element) {
        Node<E> x = first;
        int index = 0;
        while (x != null) {
            if (x.item.equals(element)) {
                return index;
            }
            x = x.next;
            index++;
        }
        return -1;
    }

    public E get(int index) {
        Node<E> x = first;
        for (int i = 0; i < index; i++) {
            x = x.next;
        }
        return x.item;
    }

    public Node<E> getLastNode() {
        Node<E> x = first;
        while (x.next != null) {
            x = x.next;
        }
        return x;
    }

    public Node<E> getNode(int index) {
        Node<E> x = first;
        for (int i = 0; i < index; i++) {
            x = x.next;
        }
        return x;
    }

    public Node<E> getFirstNode() {
        return first;
    }

    public int size() {
        return size;
    }

    public E set(int index, E item) {
        Node<E> node = getNode(index);
        E oldValue = node.item;
        node.item = item;
        return oldValue;
    }


    public void clear() {
        for (Node<E> x = first; x != null; ) {
            Node<E> next = x.next;
            x.item = null;
            x.next = null;
            x = next;
        }
        first = null;
        size = 0;
    }

    @Override
    public String toString() {
        StringBuilder sb = new StringBuilder();
        sb.append("[");

        Node<E> x = first;
        for (int i = 0; i < size; i++) {
            sb.append(x.item);
            if (x.next != null) {
                sb.append(" -> ");
            }
            x = x.next;
        }

        sb.append("]");
        return sb.toString();
    }

    private static class Node<E> {
        E item;
        Node<E> next;

        Node(E item) {
            this.item = item;
        }
    }
}

```



