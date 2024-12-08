---
title: "[Data structure] Singly LinkedList 설명해 줄게!"
date: 2024-12-09T23:31:24+09:00
draft: true
summary: 이번에는 LinkedList에 대해 학습한다!
tags: ["java", "datastructure"]
categories: "data structure"
---

# Introduction
---

프로그래밍을 하다 보면 데이터를 다루는 때가 많고, 이 데이터를 어떠한 구조로 저장해야하는지 고민하는 시간이 많다. 이 데이터를 저장하는 구조를 '자료 구조'라 한다.

자바를 사용해 여러 자료 구조를 구현해  보며 해당 자료구조의 기능별 시간 복잡도가 어떻게 그렇게 나오는지 '설명해 줄게!' 시리즈를 연재한다. 

1. [[Data structure] ArrayList를 설명해 줄게!](https://jeha00.github.io/post/datastructure/arraylist/)
2. [[Data structure] Singly LinkedList를 설명해 줄게!](https://jeha00.github.io/post/datastructure/linkedlist/)
3. [Data structure] List를 설명해 줄게!
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
    - 배열 리스트는 인덱스를 사용해 추가(삭제)할 자리를 바로 찾을 수 있으므로 여기까지는 O(1)이다. 하지만, 앞 또는 중간에 데이터를 추가(삭제)하려면 해당 위치에 공간을 확보해야 한다. 이를 위해 추가할 때는 인덱스가 커지는 방향으로 자리를 확보해야 하므로 O(n)이 걸린다. 삭제할 때는 삭제한 후 비어진 공간을 매꿔야 하므로 인덱스가 작아지는 방향으로 값을 다 옮겨야 하므로 O(n)이 걸린다.
    - 연결 리스트는 참조 방식이므로 배열 리스트처럼 자리를 확보한다든가, 자리를 다시 메꾸는 것이 불필요하다. 참조값만 변경하면 추가 또는 삭제가 바로 가능하므로 O(1)이다. 하지만 추가(삭제)하기 위한 자리를 찾기까지 순차 접근을 해야 하므로 O(n)이 걸린다. 만약 추가(삭제)해야하는 자리가 맨 앞이면 O(1)이므로 총 O(1)이 걸린다.
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


Doubly LinkedList도 존재한다. 이 자료구조의 시간 복잡도는 Singly LinkedList의 코드 구조를 먼저 설명한 후 진행하겠다. 


&nbsp;

# 2. 단일 연결 리스트 구현하기

---





&nbsp;

# 3. 이중 연결 리스트일 때 시간 복잡도

---





&nbsp;

# 4. 단일 연결 리스트 전체 코드 

---

```java
public class MyLinkedList <E> {

    private Node<E> first;
    private Node<E> next;
    private int size;

    public void add(E element) {
        Node<E> newNode = new Node<>(element);
        if (size == 0) {
            first = newNode;
        } else {
            Node<E> x = getLast();
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
            Node<E> prev = get(index - 1);
            Node<E> next = prev.next;

            prev.next = newNode;
            newNode.next = next;
        }
        size++;
    }

    public E remove(int index) {
        Node<E> removedNode = get(index);
        E removedItem = removedNode.getItem();

        if (index == 0) {
            first = removedNode.next;
        } else {
            Node<E> prev = get(index - 1);
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
            if (x.getItem().equals(item)) {
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
            if (x.getItem().equals(element)) {
                return index;
            }
            x = x.next;
            index++;
        }
        return -1;
    }

    public Node<E> get(int index) {
        Node<E> x = first;
        for (int i = 0; i < index; i++) {
            x = x.next;
        }
        return x;
    }

    public Node<E> getLast() {
        Node<E> x = first;
        while (x.next != null) {
            x = x.next;
        }
        return x;
    }

    public Node<E> getFirst() {
        return first;
    }

    public int getSize() {
        return size;
    }

    public Node<E> getNext() {
        return next;
    }

    public void setNext(Node<E> next) {
        this.next = next;
    }

    public E set(int index, E item) {
        Node<E> node = get(index);
        E oldValue = node.getItem();
        node.item = item;
        return oldValue;
    }

    public int size() {
        return size;
    }


    @Override
    public String toString() {
        StringBuilder sb = new StringBuilder();
        sb.append("[");

        Node<E> x = first;
        for (int i = 0; i < size; i++) {
            sb.append(x.getItem());
            if (x.getNext() != null) {
                sb.append(" -> ");
            }
            x = x.getNext();
        }

        sb.append("]");
        return sb.toString();
    }

    private class Node<E> {
        E item;
        Node<E> next;

        Node(E item) {
            this.item = item;
        }

        public E getItem() {
            return item;
        }

        public Node<E> getNext() {
            return next;
        }

        @Override
        public String toString() {
            return "Node{" +
                    "item=" + item +
                    ", next=" + next +
                    '}';
        }
    }
}

```



