---
title: "[Data structure] Set에 대해 설명해 줄게!"
date: 2024-12-13T10:46:27+09:00
draft: true
summary: 
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


# 1. 인터페이스 Set

---

## 1.1 상속 및 구현 관계

셋은 중복을 허용하지 않고, 순서를 보장하지 않는 자료 구조다. 그래서 순서를 보장하지 않으며, 특정 요소가 집합에 있는지 여부를 확인하는데 최적화되어 있다.


다음과 같은 상속 및 구현 관계를 가진다.

- interface Collection
    - interface Set
        - HashSet
            - LinkedHashSet
        - TreeSet

Collection 인터페이스는 List 인터페이스도 상속하는 것처럼 Set 인터페이스도 상속한다.

Set 인터페이스를 구현하는 것이 HashSet과 TreeSet이다. 마지막으로 LinkedHashSet은 HashSet을 상속받는다.


## 1.2 자료구조 차이 

### HashSet

해시를 사용해서 요소를 저장한다. 요소들은 특정 순서 없이 저장되기 때문에, 요소를 추가한 순서를 보장하지 않는다. 

시간복잡도는 추가, 삭제, 검색은 O(1)이 걸린다.  

용도는 데이터의 유일성만 중요하고, 순서는 중요하지 않는 곳에 사용한다.

### LinkedHashSet

HashSet에 LinkedList를 추가해 요소들의 순서를 유지한다. 요소들이 추가된 순서대로 유지된다. 반환할 때도 요소들이 추가된 순서대로 반환된다.

시간 복잡도는 HashSet과 동일하다.  

용도는 데이터의 유일성과 삽입 순서를 유지해야할 때 사용된다.  

참조를 유지해야 하므로 HashSet보다 무겁다.  


### 이진 탐색 트리와 TreeSet

이진 탐색 트리(binary search tree)를 개선한 '레드-블랙 트리'를 내부에서 사용한다. 요소들은 정렬된 순서로 저장된다. 순서의 기준은 Comparator로 변경될 수 있다. 

시간 복잡도는 주요 연산의 경우 O(log n)이 걸린다.  


트리(tree)란 노드를 사용해서 나무가 뒤집어진 것처럼 상위 노드에서 하위 노드로 갈수록 점차 노드가 많아지는 자료 구조를 말한다. 가장 높은 상위 노드를 루트(root)라 한다. 여기서 하나의 상위 노드당 하위 노드가 최대 두 개만 올 수 있는 자료 구조를 **_'이진 트리'_** 라 한다. 

그리고 하나의 상위 노드에 있는 두 개의 하위 노드를 둘 때, 상위 노드의 값을 기준으로 더 작은 값은 왼쪽 하위 노드에 둔다. 상위 노드의 값보다 큰 값은 오른쪽 하위 노드가 가지는 것을 **_이진 탐색 트리_** 라 한다.

이러한 특성으로 이진 탐색 트리는 값을 비교할 때마다 탐색 범위가 절반씩 줄어든다는 점이다. 16개의 데이터가 있고, 데이터가 확실하게 존재한다고 가정할 때 다음과 같은 데이터 수로 줄어든다.
- 16개 -> 8개 -> 4개 -> 2개 -> 1개 

그래서 시간 복잡도가 O(log n)이 된다.  

하지만 계속 상위 노드보다 큰 값이 추가될 수도 있다. 예를 들어 3 -> 5 -> 7 -> 9 -> 11 -> 13 이면 이 때 시간 복잡도는 O(n)이 된다. 이러한 문제점을 개선하기 위해서 균형을 다시 맞추는 것이 필요하다. '레드 블랙 트리'는 이러한 쏠림 문제를 해결해 지속적으로 균형을 유지한다. 그래서 시간 복잡도 O(n)을 유지한다. 위 예시라면 7이나 9를 기준으로 다시 균형을 맞추면 해결된다.  


**_이진 탐색 트리인 TreeSet은 입력 순서가 아니라 데이터의 값을 기준으로 정렬해서 보관한다.따라서 정렬된 순서로 데이터를 차례대로 조회 및 순회할 수 있다._**


&nbsp;


# 2. Set 인터페이스의 메서드   

---

Set은 한국어로는 '집합'을 의미한다. 중고등학교를 다닐 때 집합하면 항상 함께 배운 개념이 있다.

바로 '합집합', '교집합', '차집합'이다.  

Set 인터페이스에서는 위 3가지 개념을 아래 3가지 메서드로 정의하고 있다. 

- 합집합: `addAll(Collection<? extends E c)`
    - c에 있는 모든 요소를 추가한다.
- 교집합: `retainAll(Collection<?> c)`
    - c에 있는 요소만을 유지하고 나머지는 제거한다.
- 차집합: `removeAll(Collection<?> c)`
    - c에 있는 요소를 제거한다.  


사실 위 메서드는 Collection 인터페이스에 속해있으므로 Collection 인터페이스를 구현하는 모든 자료 구조는 다 존재한다. 집합에 사용한다면 위와 같은 개념으로 사용할 수 있기 때문에 언급한다.  


위 연산의 시간 복잡도를 생각해보자. 

### 합집합

현재 집합 객체(N개)와 매개변수로 전달한 집합 객체(M개)의 모든 요소를 가지고 있는 새로운 집합을 생성한다면 O(N + M)이 걸린다. 

현재 내가 구현한 Set을 기준으로 코드로 표현하자면 다음과 같다.

```java
    public void addAll(MyHashSet<E> set) {
        boolean modified = false;
        for (LinkedList<E> bucket : set.buckets) {
            for (E value : bucket) {
                if (add(value)) modified = true;
            }
        }
        return modified;
    }
```

자바 소스 코드(AbstractCollection.java)의 `addAll`은 다음과 같다.

```java
    public boolean addAll(Collection<? extends E> c) {
        boolean modified = false;
        for (E e : c)
            if (add(e))
                modified = true;
        return modified;
    }
```

### 교집합

현재 집합 객체(N개)와 매개변수로 전달한 집합 객체(M개)의 요소 중 공통 요소만 가져야 하므로 O(n)이다. 
- 현재 집합 객체를 N개 순회를 진행한다 (O(n)). 
- 각 객체마다 매개변수로 전달한 집합 객체에 존재하는 지 확인한다 (O(1)).
- 존재하면 추가한다 (O(1)).

현재 내가 구현한 Set을 기준으로 코드로 표현하자면 다음과 같다.

```java
    public boolean retainAll(MyHashSet<E> other) {
        boolean modified = false;
        for (LinkedList<E> bucket : buckets) {
            for (E value : bucket) {
                if (!other.contains(value)) {
                    remove(value);
                    modified = true;
                }
            }
        }
        return modified;
    }
```


자바 소스 코드(AbstractCollection.java)의 `retainAll`은 다음과 같다.

```java
    public boolean retainAll(Collection<?> c) {
        Objects.requireNonNull(c);
        boolean modified = false;
        Iterator<E> it = iterator();
        while (it.hasNext()) {
            if (!c.contains(it.next())) {
                it.remove();
                modified = true;
            }
        }
        return modified;
    }

    public Iterator<E> iterator() {
        return map.keySet().iterator();
    }
```



### 차집합  

현재 집합 객체(N개)와 매개변수로 전달한 집합 객체(M개)의 요소 중 매개변수로 전달한 집합의 요소는 없어야 하므로 O(M)이다. 
- M개를 순회한다 (O(M)).
- M개의 각 요소가 N개에 있는지 확인한다 (O(1)).
- 있으면 삭제한다 (O(1)).


현재 내가 구현한 Set을 기준으로 코드로 표현하자면 다음과 같다.

```java
    public boolean removeAll(MyHashSet<E> other) {
        boolean modified = false;
        for (LinkedList<E> bucket : buckets) {
            for (E value : bucket) {
                if (other.contains(value)) { // 교집합과의 차이점
                    remove(value);
                    modified = true;
                }
            }
        }
        return modified;
    }
```
교집합은 other에 value가 포함되어 있지 않으면 value를 삭제하지만, 차집합은 other에 value가 포함되면 value를 삭제한다.

```java
    public boolean removeAll(Collection<?> c) {
        Objects.requireNonNull(c);
        boolean modified = false;
        Iterator<?> it = iterator();
        while (it.hasNext()) {
            if (c.contains(it.next())) {
                it.remove();
                modified = true;
            }
        }
        return modified;
    }

    public Iterator<E> iterator() {
        return map.keySet().iterator();
    }
```



&nbsp;


# 3. MyHashSet 전체 코드

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

    public void addAll(MyHashSet<E> set) {
        boolean modified = false;
        for (LinkedList<E> bucket : set.buckets) {
            for (E value : bucket) {
                if (add(value)) modified = true;
            }
        }
        return modified;
    }

    public boolean retainAll(MyHashSet<E> other) {
        boolean modified = false;
        for (LinkedList<E> bucket : buckets) {
            for (E value : bucket) {
                if (!other.contains(value)) {
                    remove(value);
                    modified = true;
                }
            }
        }
        return modified;
    }

    public boolean removeAll(MyHashSet<E> other) {
        boolean modified = false;
        for (LinkedList<E> bucket : buckets) {
            for (E value : bucket) {
                if (other.contains(value)) { // 교집합과의 차이점
                    remove(value);
                    modified = true;
                }
            }
        }
        return modified;
    }
}
```