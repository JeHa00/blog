---
title: "Array와 LinkedList의 차이, LinkedList의 종류 그리고 코드로 구현하기"
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

**_그래서 물리적 메모리 주소의 관점에서 봐야 보다 확실히 이해할 수 있다. 배열은 물리적 메모리 주소가 연속적으로 데이터 저장이 되고, linked list는 물리적 메모리 주소가 산발적으로 데이터 저장이 된다._**

위 의미를 풀어서 설명하면 다음과 같다. 

### 삭제 및 추가 

크기의 제한이 없는 linked list와 달리 배열은 한 번 사이즈를 정해서 저장되는 자료구조인데, 늘리거나 줄일 수가 없다. 이 배열에 데이터를 추가 또는 삭제를 한다면 다른 크기의 배열을 만든 다음 기존 배열에 있는 값들을 복사해서 가져와야 한다. 데이터를 추가한다면 크기를 늘린 상태로 새로운 배열을 만들어야 하고, 삭제한다면 반대가 된다.  

하지만 linked list는 중간에 데이터를 삽입하거나, 삭제하려면 삽입 또는 삭제하려는 데이터의 앞선 위치의 데이터가 가지고 있는 위치 정보만을 수정하면 손쉽게 저장할 수 있다. 

그리고, 이 linked list의 삭제의 경우, 삭제하려는 데이터가 있다면 이 데이터의 앞선 데이터 주소 정보를 수정해서 삭제하려는 데이터가 참조되지 않도록 만들어 GC에 의해서 삭제된다.    

이렇게만 들으면 linked list가 배열보다 모든 부분에서 좋은 자료구조라고 판단될 수 생각할 수 있다.

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

---

# Linked List Node

위 코드는 첫 번째 노드가 linked list의 헤더이면서 대표다. 이런 경우 만약 헤더를 삭제하면 이 헤더를 참조하는 데이터들부터 다 찾을 수 없다.  

그래서 이번에는 위 문제점을 해결하는 방식으로 만든다.  


----

# Reference

- [Linked List 개념](https://www.youtube.com/watch?v=DzGnME1jIwY)
- [단방향/양방향 Linked List 개념](https://www.youtube.com/watch?v=G4IIDyfoHeY)
- [[자료구조 알고리즘] 단방향 Linked List 구현 in Java](https://www.youtube.com/watch?v=C1SDkdPvQPA)