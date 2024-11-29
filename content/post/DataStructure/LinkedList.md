---
title: "Array와 LinkedList의 차이, LinkedList의 여러 기능들을 Java로 구현하기"
date: 2024-02-13T22:20:21+09:00
draft: true
summary: Array와 Linked list의 차이, Linked list의 종류에 대해 알아본다.  
tags: [""]
categories: "data structure"
---


이번 포스팅에서는 linked list에 대해서 학습해본다.



# Array와 Linked list의 차이

### 물리적 메모리 주소 관점에서  

제목에 있는 두 자료구조 모두 데이터를 저장할 수 있는 공간이 있으면 아래 2가지를 저장하고 있는 자료구조를 말한다.

- 현재 위치의 값
- 다음 데이터의 위치 

이러면 배열과 차이를 못 느낄 것이다. 

**_그래서 물리적 메모리 주소의 관점에서 봐야 명확히 이해할 수 있다. 배열은 물리적 메모리 주소가 연속적으로 데이터 저장이 되고, linked list는 물리적 메모리 주소가 산발적으로 데이터 저장이 된다._**

위 의미를 풀어서 설명하면 다음과 같다. 

### 삭제 및 추가 

크기의 제한이 없는 linked list와 달리 배열은 한 번 사이즈를 정해서 저장되는 자료구조인데, 늘리거나 줄일 수가 없다. 이 배열에 데이터를 추가 또는 삭제를 한다면 다른 크기의 배열을 만든 다음 기존 배열에 있는 값들을 복사해서 가져와야 한다. 데이터를 추가한다면 크기를 늘린 상태로 새로운 배열을 만들어야 하고, 삭제한다면 반대가 된다.  

하지만 linked list는 중간에 데이터를 삽입하거나, 삭제하려면 삽입 또는 삭제하려는 데이터의 앞선 위치의 데이터가 가지고 있는 위치 정보만을 수정하면 손쉽게 저장할 수 있다. 

그리고, 이 linked list의 삭제의 경우, 삭제하려는 데이터가 있다면 이 데이터의 앞선 데이터 주소 정보를 수정해서 삭제하려는 데이터가 참조되지 않도록 만들어 GC에 의해서 삭제된다.    

이렇게만 들으면 linked list가 배열보다 모든 부분에서 좋은 자료구조라고 판단될 수 있다.

### 탐색

하지만, linked list는 원하는 데이터를 탐색할 때 각 데이터의 다음 데이터 위치 정보를 알기 위해서 하나씩 하나씩만 접근이 가능하기 때문에 '조회'에 있어서는 좋지 못한 자료구조다. 이를 **_'Sequential Access'_** 라 한다. **_어떤 요소에 접근할 때 처음부터 하나씩 하나씩 접근하는 방식_** 을 말한다.

배열의 경우, 연속적으로 저장이 되어 있고 크기가 정해져 있기 때문에 '인덱스'라는 개념이 있어서 **_인덱스를 통해 원하는 데이터에 바로 접근_** 할 수 있다. 이를 **_'Random access'_** 를 지원한다고 한다.    


### 두 자료구조의 장단점과 시간복잡도   

그래서 두 자료구조가 언제 좋은지를 정리하면 다음과 같다. 

- 탐색: 배열
- 추가, 삭제 (O(1)): 링크드 리스트  

그리고 두 자료구조의 시간복잡도는 다음과 같다.  

- linked list
    - 처음(마지막) 삭제 또는 추가: O(1)
    - 특정 요소에 접근: O(n)

- array
    - 인덱스로 접근 후 삭제 또는 삽입한 후 전체적으로 shift: O(1) + O(n)
    - 특정 요소에 접근: O(1)


&nbsp;

---

# Linked List의 단/양방향

각 노드는 다음 위치 정보만을 가지고 있기 때문에 _'단방향'_ 이다. 위에서 언급한 linked list는 단방향을 가정으로 설명했다.  

하지만 첫 번째 노드를 제외한 그 중간에 있는 노드들이 앞 뒤 데이터 위치를 모두 가지고 있는 경우 _'양방향'_ 이다. 양방향의 경우에도 데이터를 추가 및 삭제한다면 양방향이기 때문에 추가 및 삭제하려는 데이터의 전후 노드가 가지고 있는 위치 데이터를 각각 다 수정하면 된다. 이 때 위에서 학습한 것처럼 참조되지 않는다면 gc에 의해 데이터는 삭제된다.   

&nbsp;

---

# Singly Linked List 자바로 구현하기 

```java
package practice;

public class Node {
    int data;
    Node next;

    public Node(int data) {
        this.data = data;
    }

    // 추가하기
    public void append(int data) {
        Node newNode = new Node(data);
        Node currentNode = this;

        // 제일 마지막 노드로 이동하기 
        while (currentNode.next != null) {
            currentNode = currentNode.next;
        }

        currentNode.next = newNode;
    }

    // 동일 값 삭제하기
    public void delete(int data) {
        Node currentNode = this;
        
        // 마지막 노드 방향으로 이동하기
        while (currentNode.next != null) {

            // 동일한 값을 가진 노드 찾기 
            if (currentNode.next.data == data) {
                currentNode.next = currentNode.next.next;
            }
            currentNode = currentNode.next;
        }
    }

    // 하나씩 다 조회하기 
    public void retrieve() {
        Node currentNode = this;
        while (currentNode.next != null) {
            System.out.print(currentNode.data + " --> ");
            currentNode = currentNode.next;
        }
        System.out.println(currentNode.data);
    }
}
```

&nbsp;

---

# Linked List Node

위 코드는 첫 번째 노드가 linked list의 헤더이면서 대표다. 이런 경우 만약 헤더를 삭제하면 이 linked list의 데이터 모두를 찾을 수 없다. 또한 위 메서드로는 첫 번째 노드를 삭제할 수 없다.  

그래서 이번에는 위 문제점을 해결하는 방식으로 만든다.  

헤더를 별도로 만들어서 linked list의 시작을 알리는 용도로 사용해보자.  

Node 클래스와 LinkedList 클래스 2개를 만든다.

```java
package example;

public class LinkedList {
    Node header;

    static class Node {
        int data;
        Node next;

    }

    LinkedList() {
        header = new Node();
    }

    void append(int newData) {
        Node newNode = new Node(); // 추가할 새로운 node 생성
        newNode.data = newData;
        
        Node currentNode = header; // 현재 노드를 this가 아닌 header로 지정  
        while (currentNode.next != null) {
            currentNode = currentNode.next;
        } // next가 없는 노드 찾기  
        currentNode.next = newNode;
    }

    void delete(int dataToBeDeleted) {
        Node currentNode = header; // 현재 노드를 this가 아닌 header로 지정  

        while (currentNode.next != null) {
            if (currentNode.next.data == dataToBeDeleted) {
                currentNode.next = currentNode.next.next;
                continue;
            }
            currentNode = currentNode.next; // 다음 노드로 이동하기  
        }
    }

    void retrieve() {
        Node currentNode = header.next; // 현재 노드를 this가 아닌 header로 지정
        while (currentNode.next != null) {
            System.out.print(currentNode.data + " -> ");
            currentNode = currentNode.next;
        }

        System.out.println(currentNode.data);
    }
}
```

이전 코드와 달리 `LinkedList` 클래스를 사용해서 노드들을 연결했다. `header`에 추가하여 노드 정보에 계속 접근할 수 있도록 했다. 이에 따라 `this`를 사용하지 않고 `header` 부터 접근을 시작한다.  

&nbsp;

---

# Linked List의 여러 기능 구현하기  

## 중복값 삭제 구현하기 

중복값을 삭제하는 기능을 추가해보자. 이 기능은 buffer를 사용하는 기능이 있고, 없는 기능이 있다. buffer를 사용하는 기능으로 만든다면 hast set에다가 노드를 진행하면서 발견한 값이 없으면 넣어놓고, 있으면 노드의 값을 삭제하는 방식으로 진행할 수 있다. 하지만, buffer를 사용하지 않으면 다음과 같이 포인터를 사용하여 해결할 수 있다.  

```java
    void removeDuplicatesWithPointer() {
        Node startPointer = header;
        while (startPointer != null && startPointer.next != null) {
            Node runner = startPointer;
            while (runner.next != null) {
                if (runner.next.data == startPointer.data) {
                    runner.next = runner.next.next;
                } else {
                    runner = runner.next;
                }
            }
            startPointer = startPointer.next;
        }}
```

첫 번째 `while`문의 조건을 위와 같이 한 이유는 `startPointer` 노드가 뒤에서 두 번째 노드이고, 
`runner.next.data == startPointer.data` 조건을 만족하여 `runner.next`를 삭제했고, 삭제된 `runner.next`가 마지막이였으면 `startPointer.next`는 `null`이 되기 때문에 `null.next` 로 `NullPointerException`이 발생되기 때문이다.  

또한, `while`문의 조건이 `.next` 가 `null`이 아닌 조건으로 한 이유는 만약 현재 노드의 값이 `startPointer.data`와 동일하다면 `runner`의 이전 노드의 `next` 값을 `runner.next` 가 가리키는 노드로 수정해야 하는데, 이전 노드의 값을 참조할 수 없기 때문이다.  

만약 이전 노드를 조회할 수 있다면 다음과 같이 수정할 수 있다. 


```java
    void removeDuplicatesWithPointer() {
        Node startPointer = header;
        while (startPointer != null) { // 수정된 부분
            Node runner = startPointer;
            while (runner != null) { // 수정된 부분

                if (runner.data == startPointer.data) { // 수정된 부분
                    runner.prev = runner.next; // 수정된 부분  
                } else {
                    runner = runner.next;
                }
            }
            startPointer = startPointer.next;
        }
    }
```

위 코드를 실행하는 클래스를 만든 후 실행하면 다음과 같다.  

```java
public class RemoveDumps {
    public static void main(String[] args) {
        LinkedList ll = new LinkedList();
        ll.append(2);
        ll.append(1);
        ll.append(2);
        ll.append(3);
        ll.append(4);
        ll.append(3);
        ll.append(2);
        ll.append(2);
        ll.retrieve();
        ll.removeDuplicatesWithPointer();
        ll.retrieve();
    }
}
```

- 실행 결과

```java
2 -> 1 -> 2 -> 3 -> 4 -> 3 -> 2 -> 2
2 -> 1 -> 3 -> 4
```

위 코드를 공간 복잡도와 시간 복잡도 관점에서 바라보면 버퍼를 사용하지 않기 때문에 공간복잡도는 O(1)이고, 시간복잡도는 `while` 문을 2번 사용하기 때문에 O(n^2) 이라고 볼 수 있다.  

&nbsp;

## k번째 노드 정보 얻기  

이번에는 앞에서 k번째에 있는 노드의 참조값 정보를 얻어보자.

처음부터 진행하면서 `count` 값을 통해 갯수를 세면서 `nth`와 동일한지 계속 비교한다. 동일하면 반복문을 종료하고 해당 node를 반환한다.  

```java
    Node getNode(int nth) {
        Node node = header.next;
        int index = 1;

        while (node.next != null) {
            if (index == nth) {
                break;
            }
            node = node.next;
            index++;
        }

        System.out.println(nth + "번째 노드의 값: " + node.data);
        return node;
    }
```


- 실행 코드

    ```java
        LinkedList ll = new LinkedList();
        for (int i = 1 ; i < 11; i++) {
            ll.append(i);
        }
        ll.retrieve();
        ll.getNode(1);
        ll.getNode(2);
        ll.getNode(3);
        ll.getNode(4);
    ```

- 실행 결과

    ```java
    1 -> 2 -> 3 -> 4 -> 5 -> 6 -> 7 -> 8 -> 9 -> 10
    1번째 노드의 값: 1
    2번째 노드의 값: 2
    3번째 노드의 값: 3
    4번째 노드의 값: 4
    ```

&nbsp;

## 값이 아닌 노드의 참조값으로 노드 값 삭제하기  

위 코드에서 노드 삭제는 값을 매개변수로 전달했으므로, 처음부터 탐색하여 일치하는 값을 삭제했다. 노드에는 이전 노드 참조값은 없다. 하지만, 노드의 참조값을 매개변수로 받을 경우 이 노드의 값을 어떻게 삭제할까?

노드 정보를 얻기 위해서 먼저 k번째 노드의 정보를 얻은 후, k번째 노드의 다음 노드 정보 가지고 있는 참조 변수를 만든다. 
이 참조 변수를 사용해서 k번째 노드 정보의 값과 다음 노드 정보를 덮어씌우면 손쉽게 해결된다.

```java
    boolean deleteNode(Node node) {
        if (node == null ) {
            return false;
        }

        Node next = node.next;
        node.data = next.data;
        node.next = next.next;

        return true;
    }
```

&nbsp;


## 뒤에서 k번째에 해당하는 노드의 값을 찾기 구현하기

### 첫 번째 방법: 처음부터 진행하기

위 예제에서 사용한 `runner`를 응용해보자. 

```java
    int findKthNodeFromBack(int k) {
        Node startPoint = header;
        int indexFromBack;
        while (startPoint != null && startPoint.next != null) {
            Node runner = startPoint;
            indexFromBack = 1;
            while (runner.next != null) {
                runner = runner.next;
                indexFromBack++;
            }
            if (indexFromBack == k) {
                break;
            }
            startPoint = startPoint.next;
        }
        return startPoint.data;
    }
```

뒤에서 몇 번째에 해당하는지는 앞에서 진행하면서 깊이 정보를 측정하면 된다. 이진 트리처럼 하나의 노드씩만 연결되어 있기 때문에 깊이 정보만을 생각한다. 

- 실행 코드

    ```java
    public class findKthNodeFromBack {
        public static void main(String[] args) {
            LinkedList ll = new LinkedList();
            for (int i = 1 ; i < 11; i++) {
                ll.append(i);
            }
            ll.retrieve();
            System.out.println(ll.findKthNodeFromBack(3));
            System.out.println(ll.findKthNodeFromBack(1));
            System.out.println(ll.findKthNodeFromBack(4)); 
        }
    }
    ```


- 결과

    ```java
    1 -> 2 -> 3 -> 4 -> 5 -> 6 -> 7 -> 8 -> 9 -> 10
    8
    10
    7
    ```

이번에는 `while`문이 아닌 재귀함수를 사용하여 구현해보자.

```java
    int findKthNodeFromBackWithRecursion(Node node, int k) {
        Node startPoint = node;

        int indexFromBack = 1;

        Node runner = startPoint;
        while (runner.next != null) {
            runner = runner.next;
            indexFromBack++;
        }

        if (indexFromBack == k) {
            return startPoint.data;
        }

        return findKthNodeFromBackWithRecursion(startPoint.next, k);
    }
```

- 실행 코드
    ```java
    ...
        System.out.println(ll.findKthNodeFromBackWithRecursion(ll.header, 3));
    ```

- 실행 결과

    ```java
    ...
    8
    ```


이번에는 O(n^2)이 아닌 O(n)으로 시간 복잡도를 줄여보자. 

```java
int findKthNodeFromBack(Node node, int k) {
    Node currentNode = node;

    int totalNodeCount = 1;

    while (currentNode.next != null) {
        currentNode = currentNode.next;
        totalNodeCount++; // 전체 노드 갯수 세기
    }

    currentNode = node; // currentNode를 다시 처음 지점으로 초기화

    // k번째 노드로 이동 
    for (int i = 1; i <= totalNodeCount - k; i++) {
        currentNode = currentNode.next;
    }

    return currentNode
}

```

동일한 실행 코드로 결과를 하면 동일한 결과를 확인할 수 있다.  

전체 노드 갯수를 센 후, `k` 값을 빼서 노드를 이동시키면 n^2을 하지 않아도 된다.  

하지만 끝까지 갔다가, 최악의 경우로 다시 처음까지 돌아오기 때문에 2n으로 O(n)이다.  


### 두 번째 방법: 재귀 호출  

위 마지막 방법은 전체 노드를 파악한 후, 초기화하여 k번째로 이동하는 과정을 거친다. 이번에는 재귀 호출을 사용해서 해결해보자.

계속 다음 노드를 호출하여 제일 마지막 노드까지 접근한다. 

```java
    int getKthNodeFromBackWithRecursion(Node node, int k) {
        if (node == null) {
            return 0;
        }

        int index = getKthNodeFromBackWithRecursion(node.next, k) + 1;

        if (index == k) {
            System.out.println(k + "th to last node is " + node.data);
        }

        return index;
    }
```
 
하지만 이 코드의 경우는 처음까지 오기 때문에 무조건 시간복잡도가 2n이다.  

### 세 번째 방법: 포인터  

첫 번째, 두 번째 방법은 뒤에서부터 새는 방법이다. 그래서 맨 뒤까지 무조건 이동한 후, 뒤에서 몇 번째인지를 확인해야 한다. 하지만 포인터를 사용하는 방식은 앞에서부터 진행되어 k번째임을 확인되면 바로 중단된다.  

그래서 포인터 2개(p1, p2)를 사용한다. p1은 맨 마지막 노드임을 가리키는 포인터, p2는 뒤에서 k번째 노드임을 가리키는 포인터다.  p1이 맨 마지막 노드일 때, p2는 k번째 노드여야 한다. 이를 위해서 p1을 먼저 k만큼 앞에서 k번째 노드로 이동시킨다.  그 다음 p1과 p2를 p1이 null일 때까지 계속 다음 노드로 이동시킨다. 그러면 p1이 null로 제일 마지막 노드를 지났을 때, p2는 k번째 노드가 된다.  

예를 들어 총 1, 2, 3, 4, 5 의 각 값을 가지고 있는 노드가 5개 있다고 하자. p1과 p2는 모두 1에서 시작한다. 그리고 p1을 k만큼 다음 노드로 이동시킨다. k가 3인 경우 p1은 4 값을 가지고 있는 노드로 이동된다. 그러면 p1이 null이 될 때까지 p1과 p2를 다음 노드로 이동시킨다. p1이 null이 될 때 즉, 5 다음으로 이동될 때 p2는 3이 값인 노드에 위치한다. 

```java
    Node getKthNodeFromBackWithPointer(Node first, int k) {
        Node p1 = first;
        Node p2 = first;

        for (int i = 0; i < k; i++) {
            if (p1 == null) {
                return null;
            }
            p1 = p1.next;
        }

        while (p1 != null) {
            p1 = p1.next;
            p2 = p2.next;
        }

        return p2;
    }
```

위 알고리즘은 p1이 null이 될 때까지 움직이므로 시간 복잡도는 O(n)이다.  


&nbsp;

## Linked List 노드들을 x 값에 따라 나누기

Linked List에 있는 모든 노드들을 x 값을 기준으로 값이 작은 것들은 왼쪽, 큰 것들은 오른쪽으로 총 두 파트로 나누는 기능을 만들어보자. 단 x는 오른쪽 파트 어디에 놔도 상관없다.  

예를 들어 구현하려는 기능을 이해해보자. Linked List는 8 -> 5 -> 2 -> 7 -> 3 -> 4 로 구성되어 있다. x가 5라고할 때 5보다 작은 것은 5 값을 가지고 있는 노드의 왼쪽에, 5보다 큰 것은 5 값을 가지고 있는 노드의 오른쪽에 위치시킨다는 의미다.



---

# Reference

- [Linked List 개념](https://www.youtube.com/watch?v=DzGnME1jIwY)
- [단방향/양방향 Linked List 개념](https://www.youtube.com/watch?v=G4IIDyfoHeY)
- [[자료구조 알고리즘] 단방향 Linked List 구현 in Java](https://www.youtube.com/watch?v=C1SDkdPvQPA)
- [[자료구조 알고리즘] LinkedListNode의 구현 in Java](https://www.youtube.com/watch?v=IrXYr7T8u_s)
- [[자료구조 알고리즘] Linked List 중복값 삭제 in Java](https://www.youtube.com/watch?v=Ce4baygLMz0)
- [[자료구조 알고리즘] 단방향 Linked List 뒤부터 세기 in Java](https://www.youtube.com/watch?v=Vb24scNDAVg&t=1s)
- [[자료구조 알고리즘] 단방향 Linked List 중간노드 삭제 in Java](https://www.youtube.com/watch?v=xI4iPEmkHlc)
- [[자료구조 알고리즘] Linked List 값에 따라 나누기 in Java](https://www.youtube.com/watch?v=xufv1LUy42E)