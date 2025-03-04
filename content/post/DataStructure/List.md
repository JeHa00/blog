---
title: "[Data structure] List에 대해 설명해 줄게!"
date: 2024-09-30T08:24:59+09:00
draft: false
summary: ArrayList와 LinkedList를 비교해 성능 차이를 확인한다.
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

# 1. 인터페이스 List

---
자료 구조는 다형성과 OCP 원칙을 잘 활용할 수 있다. 어떻게 적용되는지 알아보자.


**_자바에서 ArrayList와 LinkedList는 List 라는 인터페이스를 구현한다. 역할이 List이고, 구현이 ArrayList와 LinkedList다. 그리고 List는 Collection이라는 인터페이스를 상속 받는다._**


리스트는 [[Data structure] ArrayList를 설명해 줄게!](https://jeha00.github.io/post/datastructure/arraylist/)에서 언급한 것처럼 순서가 있고, 중복이 가능한 자료구조라 했다. 내가 직접 만든 자료 구조 ArrayList와 LinkedList는 내부 구현만 다를 뿐 동일한 기능을 제공하는 리스트다. 이 둘의 공통 기능을 인터페이스로 뽑아서 추상화하면 다형성을 사용할 수 있다. 인터페이스로 뽑아보자.

```java
public interface MyList<E> {
    int size();

    void add(E e);

    void add(int index, E e);

    E get(int index);

    E set(int index, E e);

    E remove(int index);

    boolean remove(E e);

    int indexOf(E e);

    void clear();
}
```

그리고 위 인터페이스를 여태 학습한 MyArrayList와 MyLinkedList가 MyList 인터페이스를 구현하도록 만들자.


- MyArrayList
    ```java
    public class MyArrayList<E> implements MyList<E>{
        //...
    }
    ```

- MyLinkedList
    ```java
    public class MyLinkedList <E> implements MyList<E>{
        //...
    }
    ```

재정의한 메서드에는 `@Override` 애노테이션도 추가한다.

한 번에 많은 데이터를 뒤에서 추가하는 객체를 개발하고 있다고 하자. 다형성을 사용하지 않으면 다음과 같이 타입을 MyArrayList 또는 MyLinkedList로 지정해야 한다. 

처음 구현할 때 MyLinkedList로 한 후, 데이터를 앞에 추가할 일이 많아진다면 MyLinkedList로 지정한 타입을 MyArrayList로 바꿔야 한다.

- 변경 전

    ```java
    public class BatchProcessor {
        // 다형성 미사용 시, 타입 지정
        private final MyLinkedList<Integer> list = new MyLinkedList<>();

        public void add(int size) {
            for (int i = 0; i < size; i++) {
                list.add(0, i);
            }
        }
    }
    ```

- 변경 후

    ```java
    public class BatchProcessor {
        // 다형성 미사용 시, 타입 지정
        private final MyArrayList<Integer> list = new MyArrayList<>();

        public void add(int size) {
            for (int i = 0; i < size; i++) {
                list.add(0, i);
            }
        }
    }
    ```

이렇게 다형성을 사용하지 않고 '구체적인 클래스'에 의존한다면 직접 코드도 수정해야 한다. 하지만 다형성을 사용하기 위해 'interface'를 사용한다면 코드를 수정할 필요 없이 '생성자 의존관계 주입'으로 사용되는 객체만 바꿔끼면 된다. 역할과 구현으로 나눠서 역할을 그대로 두고 구현체만 바꾼다는 것이다. 

```java
public class BatchProcessor {
    // 수정
    private final MyList<Integer> list; 
    
    // 추가
    public BatchProcessor(MyList<Integer> list) {
        this.list = list;
    }

    // 작업을 위해 사용되는 메서드는 그대로
    public void add(int size) {
        for (int i = 0; i < size; i++) {
            list.add(i);
        }
    }
}
```

그러면 두 리스트로 할 때 각각 시간을 비교해보자. 시간 측정을 위해 add 메서드를 수정한다.

```java
public void add(int size) {
    long startTime = System.currentTimeMillis();
    for (int i = 0; i < size; i++) {
        list.add(0, i);
    }
    long endTime = System.currentTimeMillis();
    System.out.println("크기: " + size + ", 계산 시간: " + (endTime - startTime) + "ms");
}
```

- MyArrayList인 경우: 크기: 50000, 계산 시간: 2324ms
    ```java
    MyArrayList<Integer> list = new MyArrayList<>();
    BatchProcessor processor = new BatchProcessor(list);
    processor.add(50_000);
    ```


- MyLinkedList인 경우: 크기: 50000, 계산 시간: 6ms 
    ```java
    MyLinkedList<Integer> list = new MyLinkedList<>();
    BatchProcessor processor = new BatchProcessor(list);
    processor.add(50_000);
    ```

LinkedList를 사용할 때가 시간이 훨씬 빠른 것을 알 수 있다! 

다형성과 의존관계 주입을 사용해서 코드의 변경 없이 확장성 있게 사용할 수 있다! 만약 데이터가 뒤에서 추가할 일이 많다면 반대로 MyArrayList를 사용하면 성능이 더 빠를 것이다.

그러면 두 자료구조의 성능을 비교해보자.


&nbsp;

# 2. 직접 구현한 리스트의 성능 비교 
---

맨 하단에 있는 '성능 비교를 위한 코드 전체본'을 실행했을 때 다음과 같은 결과를 확인할 수 있다. 

## 성능 비교 

추가하는 데이터의 수는 50,000개이고 반복 횟수는 10,000번일 때 각 list 실행 시 표로 정리하면 결과는 다음과 같다.

- 추가: 
    - 배열 리스트는 앞에 추가할수록 시간이 더 걸리는 것을 알 수 있다.
    - 연결 리스트는 뒤에 추가할수록 시간이 더 걸리는 것을 알 수 있다.

    | 기능 | 배열 리스트 | 연결 리스트 |
    | --- | --- | --- |
    | 앞에 추가 | 2245ms | 5ms |
    | 평균 추가 | 6ms |  3273ms|
    | 뒤에 추가 | 2ms |3183ms |

- 삭제

    - 배열 리스트는 앞에서 삭제할수록 시간이 더 걸리는 것을 알 수 있다.
    - 연결 리스트는 뒤에서 삭제할 수록 시간이 더 걸리는 것을 알 수 있다.

    | 기능 | 배열 리스트 | 연결 리스트 |
    | --- | --- | --- |
    | 앞에서 삭제 | 877ms | 2ms |
    | 평균에서 삭제 | 391ms |  1033ms|
    | 뒤에서 삭제 | 0ms |2229ms |


- 조회

    - 배열 리스트는 어디서 조회하든 매우 빠르다. 
    - 연결 리스트는 뒤에서 조회할 수록 시간이 더 걸리는 것을 알 수 있다. 

    | 기능 | 배열 리스트 | 연결 리스트 |
    | --- | --- | --- |
    | 앞에서 조회 | 0ms | 1ms |
    | 중간에서 조회 | 0ms | 563ms |
    | 뒤에서 조회 | 1ms | 1133ms |


- 검색 
    - 배열 리스트와 연결 리스트 모두 뒤에서 검색할수록 시간이 더 걸리는 것을 알 수 있다. 

    | 기능 | 배열 리스트 | 연결 리스트 |
    | --- | --- | --- |
    | 맨 앞 값 검색 | 1ms | 1ms |
    | 중간 값 검색 | 156ms | 666ms |
    | 맨 뒤 값 검색  | 298ms | 1331ms |


### 추가, 삭제
- 배열 리스트는 인덱스를 통해 추가나 삭제할 위치를 O(1)로 빠르게 찾지만, 추가나 삭제 이후에 데이터를 한 칸씩 밀어야 하므로 O(n)이 걸리므로 총 O(n)이 걸린다. 

- 연결 리스트는 인덱스를 통해 추가나 삭제할 위치를 찾는데 O(n)이 걸리지만, 추가나 삭제할 때는 간단한 참조 변경으로 O(1)이 걸리므로 총 O(n)이 걸린다.

### 앞에 추가(삭제)
- 배열 리스트는 추가나 삭제할 위치를 찾는데 O(1), 데이터를 한 칸씩 이동 O(n) -> O(n)
- 연결 리스트는 추가나 삭제할 위치를 찾는데 O(1), 노드를 변경하는데 O(1) -> **O(1)**


### 중간에 추가(삭제)
- 배열 리스트는 추가나 삭제할 위치를 찾는데 O(1), 데이터를 한 칸씩 이동 O(n/2) -> O(n)
- 연결 리스트는 추가나 삭제할 위치를 찾는데 O(n/2), 노드를 변경하는데 O(1) -> O(n)


### 뒤에 추가(삭제)
- 배열 리스트는 추가나 삭제할 위치를 찾는데 O(1), 이동할 데이터가 없으므로 -> **O(1)**
- 연결 리스트는 추가나 삭제할 위치를 찾는데 O(n), 노드를 변경하는데 O(1) -> O(n)


### 인덱스 조회
- 배열 리스트는 배열의 인덱스를 사용해서 값을 O(1)로 찾을 수 있다. 
- 연결 리스트는 노드를 인덱스 수만큼 이동해야 하므로 O(n)이 걸린다. 

### 검색
배열 리스트와 연결 리스트 모두 데이터를 찾을 때까지 배열과 노드를 순회해야 하므로 O(n)이 걸린다.



## 추가로 고려해야할 부분 

ArrayList는 배열 기반이라서 메모리 상에 연속적으로 위치하므로 다음 장점이 있다. 
- CPU 캐시 효율이 좋다: 메모리에서 읽어서 가져올 때 필요한 데이터의 주변 데이터까지 가져오므로 캐시로 가져올 확률이 높다 
- 메모리 접근 속도가 빠르다

하지만 LinkedList는 산발적으로 존재해서 위와 같은 장점을 얻기가 어렵다. 

ArrayList의 CAPCITY를 넘으면 배열을 다시 만들고 복사하는 과정은 한 번에 2배씩 늘어나므로 가끔 발생해 성능에 큰 영향을 주지 않는다. 

그래서 결론을 내리자면 **_데이터를 앞에서 자주 추가하거나 삭제할 일이 아니면 기본으로 배열 리스트를 사용하는 것이 훨씬 효율적이다._**



&nbsp;

# 3. 자바 Collection 인터페이스 리스트의 성능 비교 
---

## 직접 만든 List와 차이점

- ArrayList
    - 기본 CAPACITY는 10이다.
    - 기본 크기 값을 넘어가면 배열을 1.5배 증가시킨다.
        - 10 -> 15 -> 22 -> 33 -> 49 
    - 메모리 고속 복사 연산을 사용한다. 배열은 값을 추가 및 삭제 시 모든 요소를 한 칸씩 뒤로 이동시켜야 한다. 
    - 이 부분을 최적화하기 위해 `System.arraycopy()`를 사용해 시스템 레벨에서 메모리 고속 복사 연산을 수행한다.

- LinkedList

    - last 필드가 존재해서 첫 번째 노드와 마지막 노드 둘 다 참조할 수 있다. 
    - 이중 연결 리스트로 prev 뿐만 아니라 next도 있어서 다음 노드와 이전 노드 모두 이동할 수 있다.  


## 성능 비교 

그러면 이번에는 자바에서 제공하는 ArrayList와 LinkedList를 사용해 성능 비교를 해보자. 

직접 만든 MyList, MyArrayList, MyLinkedList 대신에 아래 콜렉션을 import 해서 사용하면 된다.

```java
import java.util.ArrayList;
import java.util.LinkedList;
import java.util.List;
```

다음은 위 콜렉션을 가져와 사용한 성능 결과다. 


- 추가: 
    - 배열 리스트가 직접 만든 리스트보다 앞에 추가 시 빠른 이유는 '고속 복사'를 사용하기 때문이다. 하지만 데이터가 많아지면 느려진다.
    - **_이론적으로 중간에 추가하는 것이 연결 리스트가 빠를 것 같지만, 실제는 배열 리스트가 더 빠르다. 고속 복사를 사용하고 메모리가 연속적이서, CPU 캐시 효율이 좋고 메모리 접근 속도가 빠르기 때문이다._**

    | 기능 | 배열 리스트 | 연결 리스트 |
    | --- | --- | --- |
    | 앞에 추가 | 153ms | 12ms |
    | 중간에 추가 | **_74ms_** |  **_1647ms_**|
    | 뒤에 추가 | 4ms |4ms |

- 삭제
    | 기능 | 배열 리스트 | 연결 리스트 |
    | --- | --- | --- |
    | 앞에서 삭제 | 56ms | 1ms |
    | 평균에서 삭제 | 24ms |  505ms|
    | 뒤에서 삭제 | 3ms |1ms |


- 조회

    | 기능 | 배열 리스트 | 연결 리스트 |
    | --- | --- | --- |
    | 앞에서 조회 | 2ms | 2ms |
    | 중간에서 조회 | 0ms | 569ms |
    | 뒤에서 조회 | 1ms | 0ms |


- 검색 
    | 기능 | 배열 리스트 | 연결 리스트 |
    | --- | --- | --- |
    | 맨 앞 값 검색 | 3ms | 0ms |
    | 중간 값 검색 | 166ms | 703ms |
    | 맨 뒤 값 검색  | 314ms | 1399ms |


그래서 앞에서 추가, 삭제가 엄청 많은 경우가 아니라면 ArrayList를 사용하는 것이 훨씬 효율적이다.


&nbsp;

# 4. 전체 코드
---

## MyArrayList
```java
import java.util.Arrays;

public class MyArrayList<E> implements MyList<E>{
    private static final int DEFAULT_CAPACITY = 10;
    private Object[] elementData;
    private int size;

    public MyArrayList() {
        elementData = new Object[DEFAULT_CAPACITY];
    };

    public MyArrayList(int initialCapacity) {
        elementData = new Object[initialCapacity];
    }

    private void grow() {
        elementData = Arrays.copyOf(elementData, elementData.length * 2);
    }


    @Override
    public void add(E e) {
        if (size == elementData.length) {
            grow();
        }

        elementData[size] = e;
        size++;
    }

    @Override
    public void add(int index, E e) {
        if (size == elementData.length) {
            grow();
        }

        shiftFromLeftToRight(index);
        elementData[index] = e;
        size++;
    }

    public void shiftFromLeftToRight(int index) {
        for (int i = size; i > index; i--) {
            elementData[i] = elementData[i - 1];
        }
    }

    @Override
    public E remove(int index) {
        E oldValue = get(index);

        shiftFromRightToLeft(index);
        elementData[--size] = null;
        return oldValue;
    }

    @Override
    public boolean remove(E e) {
        int index  = indexOf(e);

        if (index == -1) return false;

        remove(index);

        return true;

    }

    public void shiftFromRightToLeft(int index) {
        for (int i = index; i < size; i++) {
            elementData[i] = elementData[i + 1];
        }
    }

    @Override
    public int indexOf(E e) {
        for (int i = 0; i < size; i++) {
            if (e.equals(elementData[i])) {
                return i;
            }
        }
        return -1;
    }

    @Override
    public E get(int index) {
        return (E) elementData[index];
    }

    @Override
    public E set(int index, E e) {
        E oldValue = get(index);
        elementData[index] = e;
        return oldValue;
    }

    @Override
    public int size(){
        return size;
    }

    public boolean isEmpty() {
        return size == 0;
    }

    public boolean contains(E e) {
        return indexOf(e) >= 0 ? true : false;
    }

    @Override
    public void clear() {
        elementData = new Object[DEFAULT_CAPACITY];
        size = 0;
    }

    public Object[] toArray() {return Arrays.copyOf(elementData, size);}

    @Override
    public String toString() {
        return Arrays.toString(Arrays.copyOf(elementData, size)) + " size=" + size + ", capacity=" + elementData.length;
    }
}
```

## MyLinkedList

```java
import java.util.LinkedList;

public class MyLinkedList <E> implements MyList<E>{

    private Node<E> first;
    private int size;

    @Override
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

    @Override
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

    @Override
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

    @Override
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

    @Override
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

    @Override
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

    @Override
    public int size() {
        return size;
    }

    public E set(int index, E item) {
        Node<E> node = getNode(index);
        E oldValue = node.item;
        node.item = item;
        return oldValue;
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

    @Override
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

    private static class Node<E> {
        E item;
        Node<E> next;

        Node(E item) {
            this.item = item;
        }
    }
}
```



&nbsp;

# 5. 성능 비교를 위한 코드 전체
---

```java
import MyArrayList;
import MyLinkedList;
import MyList;

public class PerformanceTest {

    public static void main(String[] args) {
        int size = 50_000;
        int loop = 10_000;

        System.out.println("----- MyArrayList 추가 -----");
        addFirst(new MyArrayList<>(), size);
        addMid(new MyArrayList<>(), size);
        MyArrayList<Integer> arrayList = new MyArrayList<>();
        addLast(arrayList, size);

        System.out.println("\n----- MyLinkedList 추가 -----");
        addFirst(new MyLinkedList<>(), size);
        addMid(new MyLinkedList<>(), size);
        MyLinkedList<Integer> linkedList = new MyLinkedList<>();
        addLast(linkedList, size);

        System.out.println("\n----- MyArrayList 조회 -----");
        get(arrayList, loop, 0); // 맨 앞에서 가져올 때
        get(arrayList, loop, size / 2); // 중간에서 가져올 때
        get(arrayList, loop, size - 1); // 맨 마지막에서 가져올 때

        System.out.println("\n----- MyLinkedList 조회 -----");
        get(linkedList, loop, 0); // 맨 앞에서 가져올 때
        get(linkedList, loop, size / 2); // 중간에서 가져올 때
        get(linkedList, loop, size - 1); // 맨 마지막에서 가져올 때

        System.out.println("\n----- MyArrayList 검색 -----");
        search(arrayList, loop, 0);
        search(arrayList, loop, size / 2);
        search(arrayList, loop, size - 1);

        System.out.println("\n----- MyLinkedList 검색 -----");
        search(linkedList, loop, 0);
        search(linkedList, loop, size / 2);
        search(linkedList, loop, size - 1);


        System.out.println("\n----- ArrayList 삭제 -----");
        removeFirst(arrayList, loop); // 맨 앞의 값을 삭제할 때

        arrayList.clear();
        add(arrayList, size); // 데이터 재추가
        removeMid(arrayList, loop); // 중간 값을 삭제할 때

        arrayList.clear();
        add(arrayList, size); // 데이터 재추가
        removeLast(arrayList, loop); // 맨 뒷 값을 삭제할 때

        System.out.println("\n----- LinkedList 삭제 -----");
        linkedList.clear();
        add(linkedList, size); // 데이터 재추가
        removeFirst(linkedList, loop); // 맨 앞의 값을 삭제할 때

        linkedList.clear();
        add(linkedList, size); // 데이터 재추가
        removeMid(linkedList, loop); // 중간 값을 삭제할 때

        linkedList.clear();
        add(linkedList, size); // 데이터 재추가
        removeLast(linkedList, loop); // 맨 뒷 값을 삭제할 때
    }

    private static void addFirst(MyList<Integer> list,  int size) {
        long startTime = System.currentTimeMillis();
        for (int i = 0; i < size; i++) {
            list.add(0, i);
        }
        long endTime = System.currentTimeMillis();
        System.out.println("앞에 추가 - 크기: " + size + " 시간: " + (endTime - startTime) + "ms");
    }

    private static void addLast(MyList<Integer> list,  int size) {
        long startTime = System.currentTimeMillis();
        for (int i = 0; i < size; i++) {
            list.add(i);
        }
        long endTime = System.currentTimeMillis();
        System.out.println("마지막에 추가 - 크기: " + size + " 시간: " + (endTime - startTime) + "ms");
    }


    private static void addMid(MyList<Integer> list,  int size) {
        long startTime = System.currentTimeMillis();
        for (int i = 0; i < size; i++) {
            list.add(i / 2, i);
        }
        long endTime = System.currentTimeMillis();
        System.out.println("중간에 추가 - 크기: " + size + " 시간: " + (endTime - startTime) + "ms");
    }


    private static void add(MyList<Integer> list,  int size) {
        for (int i = 0; i < size; i++) {
            list.add(0);
        }
    }

    private static void get(MyList<Integer> list, int loop,  int index) {
        long startTime = System.currentTimeMillis();
        for (int i = 0; i < loop; i++) {
            list.get(index);
        }
        long endTime = System.currentTimeMillis();
        System.out.println("조회 - index: " + index + ", 반복 횟수: " + loop + ", 시간: " + (endTime - startTime) + "ms");
    }

    private static void search(MyList<Integer> list, int loop, int valueToFind) {
        long startTime = System.currentTimeMillis();
        for (int i = 0; i < loop; i++) {
            list.indexOf(valueToFind);
        }
        long endTime = System.currentTimeMillis();
        System.out.println("검색 - valueToFind: " + valueToFind + ", 반복 횟수: " + loop + ", 시간: " + (endTime - startTime) + "ms");
    }

      private static void removeFirst(List<Integer> list, int loop) {
        long startTime = System.currentTimeMillis();
        for (int i = 0; i < loop; i++) {
            list.remove(0);
        }
        long endTime = System.currentTimeMillis();
        System.out.println("첫 번째 삭제 - 반복 횟수: " + loop + ", 시간: " + (endTime - startTime) + "ms");
    }

    private static void removeMid(List<Integer> list, int loop) {
        long startTime = System.currentTimeMillis();
        for (int i = 0; i < loop; i++) {
            list.remove(list.size() / 2 - 1);
        }
        long endTime = System.currentTimeMillis();
        System.out.println("중간 삭제 - 반복 횟수: " + loop + ", 시간: " + (endTime - startTime) + "ms");
    }

    private static void removeLast(List<Integer> list, int loop) {
        long startTime = System.currentTimeMillis();
        for (int i = 0; i < loop; i++) {
            list.remove(list.size() - 1);
        }
        long endTime = System.currentTimeMillis();
        System.out.println("마지막 삭제 - 반복 횟수: " + loop + ", 시간: " + (endTime - startTime) + "ms");
    }
}
```


