---
title: "[Data structure] ArrayList를 설명해 줄게!"
date: 2024-08-14T12:56:04+09:00
draft: false
summary: 배열과 리스트의 차이를 학습한 후 배열기반 리스트인 ArrayList를 학습한다.
tags: ["java", "datastructure"]
categories: "Data Structure"
---
# Introduction
---

프로그래밍을 하다 보면 데이터를 다루는 때가 많고, 이 데이터를 어떠한 구조로 저장해야하는지 고민하는 시간이 많다. 이 데이터를 저장하는 구조를 '자료 구조'라 한다.

자바를 사용해 여러 자료 구조를 구현해  보며 해당 자료구조의 기능별 시간 복잡도가 어떻게 그렇게 나오는지 '설명해 줄게!' 시리즈를 연재한다. 

1. [[Data structure] ArrayList를 설명해 줄게!](https://jeha00.github.io/post/datastructure/arraylist/)
2. [Data structure] LinkedList를 설명해 줄게!
3. [Data structure] List를 설명해 줄게!
4. [Data structure] Hash를 설명해 줄게!
5. [Data structure] HashSet을 설명해 줄게!
6. [Data structure] Set을 설명해 줄게! 
7. [Data structure] Map을 설명해 줄게! 
8. [Data structure] Stack을 설명해 줄게!
9. [Data structure] Queue를 설명해 줄게!


&nbsp;


# 1. 배열(array)의 특징
---


## 1.1 인덱스 

배열의 가장 큰 특징을 꼽자면 '인덱스'라 생각한다. 배열은 인덱스를 사용할 수 있다. 인덱스를 사용하면 시간복잡도 O(1)로 여러 작업을 수행할 수 있다. 원하는 자료를 바로 찾을 수 있고, 찾아서 수정할 수 있고, 입력할 수 있다. 

> **_인덱스로 편리한 작업이 가능한 이유는 자료형이 고정적이고 생성되는 메모리 위치가 연속적이므로 참조값을 바탕으로 위치를 계산할 수가 있기 때문이다._**

그러면 인덱스를 통해서 어떻게 바로 접근할 수 있을까?

예를 들어 요소의 자료형이 int형인 배열이 있고 크기가 5라고 하자. int 형은 4byte다. 
그러면 이 배열이 메모리에 위치하는 크기는 총 20 byte다. 

||||||
|----|----|----|----|----|
| 4 byte | 4 byte | 4 byte | 4 byte  | 4 byte |

그러면 각 인덱스에 해당하는 메모리 위치를 다음과 같이 계산할 수 있다.

- 인덱스 0의 위치: 참조값(x100) + (4 byte * 0) = x100
- 인덱스 1의 위치: 참조값(x100) + (4 byte * 1) = x104
- 인덱스 2의 위치: 참조값(x100) + (4 byte * 2) = x108
- 인덱스 3의 위치: 참조값(x100) + (4 byte * 3) = x112
- 인덱스 4의 위치: 참조값(x100) + (4 byte * 4) = x116

이런 식으로 **한 번의 연산을 통해 바로 필요한 위치를 구할 수 있어서** 시간 복잡도 O(1)이다.


## 1.2 배열의 검색 

하지만, 인덱스가 있어도 배열에서 필요한 값을 찾을 때는 각 위치에 접근해 데이터를 확인해야 한다. 배열의 크기에 비례해서 오래 걸리므로 시간 복잡도 O(n)이다.



## 1.3 배열의 데이터 추가 

또한, 인덱스가 있어서 데이터를 추가할 때 오래 걸리기도 한다. 배열에서 데이터를 추가하는 경우는 아래 3가지로 분류할 수 있다. 

- 맨 뒤에 추가 
- 중간에 추가
- 맨 앞에 추가

아래와 같은 배열이 있다고 하자. 
int 형 배열은 값이 0으로 초기화된다.

||||||
|---|---|---|---|---|
| 0 |0 |0 |0 |0 |

위 값을 1, 2, 3, 4, 0으로 초기화하자.

||||||
|---|---|---|---|---|
| 1 |2 |3 |4 |0 |


### 맨 뒤에 추가하는 경우

배열의 크기에 여유가 있다면 맨 마지막에 바로 추가할 수 있어서 O(1)이다.
배열의 크기가 정해져 있으므로 1을 뺀 값이 맨 마지막 인덱스다. 
인덱스를 사용해 맨 뒤에 5를 추가하자.

추가한 배열의 상태는 다음과 같다.
||||||
|---|---|---|---|---|
| 1 |2 |3 |4 | 5|

코드로 구현하면 다음과 같다.

```java
void addLast(int[] arr, int newValue) {
    arr[arr.length - 1] = newValue;
}
```


### 중간에 추가하는 경우

배열의 위치를 찾는 데 O(1)이 걸린다. 인덱스 2에 값 6을 추가한다면 인덱스 2부터의 값들을 뒤로 한 칸씩 밀어야 한다. 맨 마지막 5 값은 배열의 크기를 넘기 때문에 배열에서 삭제된다.

||||||
|---|---|---|---|---|
| 1 |2 |3 |4 -> 3 | 5 -> 4|

인덱스를 사용해 6을 추가한다.


||||||
|---|---|---|---|---|
| 1 | 2 | 6 | 3 | 4 |

이 경우에 시간복잡도는 다음과 같다.
- 배열의 위치를 찾는 데는 O(1)이 걸린다. 
- 하지만 해당 인덱스의 오른쪽에 있는 데이터를 모두 한 칸씩 이동해야 하므로 O(n/2)이 된다. 
- 총 O(1 + n/2) 이지만, 빅오 계산법은 상수를 무시하므로 O(n)이 된다. 

만약 중간 인덱스에 추가가 아닌 덮어씌우는 거라면 배열의 위치를 찾고 바로 삽입하면 되므로 O(1)이 된다. 


코드로 구현하면 다음과 같다.

```java
void addAtIndex(int[] arr, int index, int newValue) {
    for (int i = arr.length - 1; i > index; i--){
        arr[i] = arr[i - 1];
    }
    arr[index] = newValue;
}
```



### 맨 앞에 추가하는 경우
이번에는 맨 앞에 데이터를 추가해 보자.

맨 앞에 3를 추가하기 위해 모든 데이터를 한 칸씩 이동한다.


||||||
|---|---|---|---|---|
| 1 | 2 - > 1 | 6 - > 2 | 3 -> 6 | 4 -> 3 |

그 후, 인덱스 0을 사용해 값 3를 추가한다.


||||||
|---|---|---|---|---|
| 3 | 1 | 2 | 6 | 3 |

이 경우, 시간 복잡도는 다음과 같다.

- 배열의 위치를 찾는 데 O(1)이 걸린다.
- 배열의 크기만큼 데이터를 한 칸씩 뒤로 옮기므로 O(n)이 걸린다.
- 총 O(1 + n)이지만, 빅오이므로 O(n)으로 표기한다.


이처럼 배열은 어디에 데이터를 추가하냐에 따라서 시간 복잡도가 많이 다르다.


코드로 구현하면 다음과 같다.

```java
void addFirst(int[] arr, int newValue) {
    for (int i = arr.length - 1; i > 0; i--) {
        arr[i] = arr[i - 1];
    }
    arr[0] = newValue;
}
```



## 1.4 배열의 제일 큰 단점 

무엇보다 배열의 큰 단점은 크기를 바꿀 수 없다는 점이다. 미리 여유 있게 큰 크기로 지정할 수 있지만, 더 데이터가 필요하면 이후 새로 생기는 데이터를 추가할 수 없다는 문제점이 생긴다. 그렇다고 너무 큰 배열을 처음부터 확보하면 메모리가 많이 낭비된다. 

그래서 배열처럼 처음부터 정적으로 크기가 정해져 있는 것이 아니라, 언제든지 길이를 늘일 수 있는 자료구조인 리스트(list)가 필요하다.


## 1.5 배열 정리
- 배열의 장점
    - 순서가 있어서 인덱스를 사용할 수 있다. 
    - 값을 중복으로 저장할 수 있다.  
    - 맨 뒤에 데이터를 추가하는 경우 O(1)이 걸린다.
- 배열의 단점
    - 크기가 고정적이다. 
    - 맨 앞, 중간에 데이터를 추가하는 경우 O(n)이 걸린다.



&nbsp;

# 2. 배열리스트(ArrayList)

---

## 2.1 배열과 리스트

### 배열과 리스트의 차이

이번 섹션에서는 배열리스트에 대해 알아볼 것이다. 배열은 앞서 언급한 대로 순서가 있고, 중복을 허용하지만 크기가 정적으로 고정되어 있다. 리스트도 순서가 있고 중복을 허용하지만 배열과의 차이점은 크기가 동적으로 변할 수 있다.

### 리스트의 종류와 메모리 할당 방식의 차이점

이 배열의 단점을 해결하는 리스트 자료 구조를 만들어보자. 리스트도 두 종류: ArrayList와 LinkedList가 있다. ArrayList는 배열을 기반으로 만들어진 리스트다. LinkedList는 배열이 아닌 노드(Node) 기반으로 만들어진 리스트다. 

기반이 되는 구조가 달라서 메모리가 할당되는 방식이 다르다. ArrayList는 배열을 기반으로 하는 만큼 메모리가 연속적으로 할당된다. 하지만 LinkedList는 노드를 기반으로 해서 메모리가 산발적으로 할당된다. 둘 다 객체에 대한 참조값을 요소로 가지지만, 연속적이냐 산발적이냐로 차이가 있다. 그리고 둘 다 heap 영역에 생성된다. 

### ArrayList 자료구조 특징 요약

- 연속적인 데이터 리스트 
- 내부적으로 `Object[]` 배열을 이용해 요소를 저장  
- 배열을 사용하므로 인덱스로 '요소에 빠르게 접근'할 수 있다. 
- 크기가 고정된 배열과 달리 데이터양에 따라 동적으로 늘릴 수 있다. 이때, 현재 배열보다 더 큰 크기의 배열에 복사하기 대문에 지연이 발생할 수 있다. 
- **시간 복잡도: 데이터를 맨 뒤에 추가 및 삭제할 때 성능이 좋고, 앞이나 중간에 데이터를 추가 삭제할 때 비효율적이다.** 
    - 데이터 추가
        - 마지막 추가: 제일 마지막으로 인덱스를 통한 접근이 가능해 O(1)
        - 앞, 중간 삭제: 데이터 추가를 위해 요소를 뒤로 밀어내기 때문에 O(n)
    - 데이터 삭제
        - 마지막 삭제: 제일 마지막으로 인덱스를 통한 접근이 가능해 O(1)
        - 앞, 중간 삭제: 삭제 후, 요소를 앞으로 댕기기 때문에 O(n)
    - 인덱스 조회: 인덱스를 사용하므로 O(1)
    - 데이터 검색: 모든 배열을 순회하며 일치 여부를 확인하기 때문에 O(n)

&nbsp;

# 3. 배열리스트 구현해 보기
---

그러면 배열리스트를 직접 간략히 구현해 보자. 직접 만드는 배열리스트를 `MyArrayList` 라고 명명하겠다. 

`MyArrayList`의 전체 코드는 이 포스팅 제일 마지막에 둔다.


## 3.1 필드 속성 정의하기 

```java
private static final int DEFAULT_CAPACITY = 5;
private Object[] elementData;
private static final Object[] EMPTY_ELEMENTDATA = {};
private int size;
```

1. 내부 속성에 외부에서 접근하지 못하도록 모두 `private`으로 지정한다. 
2. `DEFAULT_CAPACITY`: 리스트 생성 시, 기본 할당 용량
    - 10으로 지정한다. 
    - 기본 생성자를 통해 객체 생성 시 위 변수에 접근할 수 있도록 `static` 으로 클래스 변수로 선언한다.
    - 변경되지 않도록 `final`로 선언한다. 
3. `elementData`: 실제 MyArrayList에 추가하는 데이터를 담는 배열 객체  
    - 다양한 타입의 데이터를 보관하기 위해 `Object`배열을 사용한다. 
    - 제네릭을 사용한 `E[]`로 선언이 불가능하다. 
4. `size`: 현재 `elementData` 저장된 요소의 총개수

여기서 주목할 것은 `size` 변수다. size는 실제 elementData의 크기와 다르다는 것이다. 다음과 같은 `Object[] elementData`가 있다고 하자.


||||||
|---|---|---|---|---|---|---|---|---|---|
|index| 0 | 1 | 2 | 3 | 4 | 5| 6 | 7 | 8 |
| value | x100 | x104 | x108 | x112 | null| null | null | null | null |

위 배열의 크기는 9이지만, 실제로 값이 있는 개수는 4개다. 그래서 `size`의 값은 4다. 현재 보유하고 있는 요소의 개수 정보를 알면 좋은 이유는 리스트는 여유 있게 배열 크기를 증가시키기 때문에, 정확한 수량을 알 수 없다는 문제가 있는데 이 변수를 사용해 실제 요소 개수를 알 수 있다.
- 이 `size` 값으로 배열의 크기를 늘릴지를 이 값을 기준으로 판단한다.
- null 값이 있는 위치를 피해서 접근할 수 있으므로 `NullPointerException` 발생을 막을 수 있다는 것이다. 값을 추가할 때나 삭제할 때 배열은 한 방향으로 밀어내는 작업이 필요한데, 이 `size`를 사용해서 작업을 수행할 수 있다.
- 새로 추가할 값의 인덱스를 의미하기도 하다.


## 3.2 생성자

```java
public MyArrayList() {
    elementData = new Object[DEFAULT_CAPACITY];
};

public MyArrayList(int initialCapacity) {
    if (initialCapacity > 0) {
        this.elementData = new Object[initialCapacity];
    } else if (initialCapacity == 0) {
        this.elementData = EMPTY_ELEMENTDATA;
    } else {
        throw new IllegalArgumentException("Illegal Capacity: "+
                                            initialCapacity);
    }
}
```

자바의 ArrayList를 보면 오버로딩을 사용해 생성자에 인자가 있는 경우와 없는 경우 모두 존재한다. 인자가 있으면 정수형 인자 값만큼 배열의 크기를 결정하지만, 없으면 기본으로 설정된 크기(DEFAULT_CAPACITY)로 결정된다. 만약 음수면 예외를 발생시키도록 설계한다.

### 유의사항: 제네릭 기반 배열 생성을 하지 않는 이유 

여기서 유의해야 할 사항은 `new Object[]` 다. `new E[]`를 하는 게 나중에 타입 캐스팅을 하지 않아도 된다' 라고 생각할 수 있지만, 제네릭에는 `new` 예약어를 사용할 수 없다. 그 이유는 _제네릭은 런타임 시, 이레이저에 의해 타입 정보가 사라지므로 런타임에 타입 정보가 필요한 생성자에는 사용할 수 없다._ 만약 제네릭 기반으로 생성하는 코드를 작동하면 컴파일 오류가 발생한다. 그래서 모든 데이터를 담을 수 있는 `Object`를 그대로 사용해야 한다.


## 3.3 grow() 메서드 

```java
private void grow() {
    elementData = Arrays.copyOf(elementData, elementData.length * 2);
}
```

이 메서드가 배열과 리스트의 가장 큰 차이점인 동적 크기 변화를 만든다. 이 메서드는 다음과 같은 조건일 때 실행된다.

```java
if (elementData == size) {
    grow()
}
```

현재 보유하고 있는 요소 개수가 배열 크기와 동일하면 실행해 크기를 동적으로 늘린다. 


## 3.4 add 구현하기
add는 자료를 추가하는 데 사용하는데 `add(E e)`와 `add(int index, E e)` 메서드가 존재한다.

```java
public void add(E e) {
    add(size, e)
}


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
```

### add(int index, E e) 메서드 
- index 위치에 e를 추가하는 메서드다.
- `shiftFromLeftToRight()` 메서드
    - index 위치에 빈 공간을 만들어 값을 추가하려면 요소를 뒤쪽으로 이동시켜야 한다. 
    - 그림으로 보면 왼쪽에서 오른쪽으로 이동시켜야 할 때 이 메서드를 사용한다.
    - 옮기기 전 요소의 최대 인덱스는 'size - 1'이고 오른쪽으로 이동하므로 `int i = size`에서 시작한다.
- size의 값이 elementData의 길이와 동일하면 grow() 메서드를 실행한다. 
- 여기서 주목할 점은 size를 인덱스로 사용해서 새로 추가하는 데이터가 들어갈 인덱스를 의미한다.
- 데이터가 새로 추가되어 size의 값을 증가시키면 이후 다시 새로 추가되는 데이터의 인덱스를 의미한다.
- 아래 리스트 상태에서 인덱스 1에 값을 추가한다고 하자. (length: 5, size: 3)
    ||||||
    |---|---|---|---|---|---|
    |index| 0 | 1 | 2 | 3 | 4 |
    | value | 'A' | 'B' | 'C' | null | null|
    - `shiftFromLeftToRight()`에 의해서 값이 오른쪽으로 밀린다.
    ||||||
    |---|---|---|---|---|---|
    |index| 0 | 1 | 2 | 3 | 4 |
    | value | 'A' | 'B' | 'C' -> 'B' | null -> 'C' | null|
    - 그 다음 값을 추가한다. (length:5, size: 4)
    ||||||
    |---|---|---|---|---|---|
    |index| 0 | 1 | 2 | 3 | 4 |
    | value | 'A' | 'D' | 'B' | 'C' | null|

### add(E e) 메서드 
index를 입력받지 않으면 제일 마지막에 데이터를 넣는다.

위 상태에서 add(E e) 메서드를 실행하면 size가 3을 의미하므로 인덱스 3에 새로운 요소가 추가된다.
(length: 5, size: 4)
||||||
|---|---|---|---|---|---|
|index| 0 | 1 | 2 | 3 | 4 |
| value | 'A' | 'B' | 'C' | null -> 'D' | null|



## 3.5 remove 구현하기 

```java
public E remove(int index) {
    E oldValue = get(index);

    shiftFromRightToLeft(index);
    elementData[--size] = null;
    return oldValue;
}

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

public int indexOf(E e) {
    for (int i = 0; i < size; i++) {
        if (e.equals(elementData[i])) {
            return i;
        }
    }
    return -1;
}
```

remove는 요소를 삭제하는 데 사용하며, `remove(int index)` 와 `remove(E e)` 가 있다. 

### remove(int index) 메서드

이 메서드는 특정 인덱스의 요소를 삭제합니다. add() 메서드 방식을 거꾸로 구현하면 된다.

삭제의 경우, 삭제한 인덱스 이후 요소들을 앞으로 댕겨야 하므로 `shiftFromRightToLeft()` 메서드를 사용한다. 이 메서드도 size 변수를 사용한다. 단 `shiftFromLeftToRight()`와 달리 인덱스가 커지는 방향으로 진행한다.

사용 예시를 보자.

||||||
|---|---|---|---|---|---|
|index| 0 | 1 | 2 | 3 | 4 |
| value | 'A' | 'B' | 'C' | 'D' | null|

- 위 상태(크기: 5, size: 4)에서 remove(1)을 실행한다.

    ||||||
    |---|---|---|---|---|---|
    |index| 0 | 1 | 2 | 3 | 4 |
    | value | 'A' | 'B' -> 'C' | 'C' -> 'D' | 'D' -> null | null|


- 결과 (크기: 5, size: 3)

    ||||||
    |---|---|---|---|---|---|
    |index| 0 | 1 | 2 | 3 | 4 |
    | value | 'A' | 'C' | 'D' | null | null|



### remove(E e) 메서드

이 메서드는 특정 요소의 값을 기준으로 삭제한다. 하지만 내부 코드를 보면 다음 순서로 진행된다.
- `indexOf()` 메서드를 사용해 `E e`가 위치한 인덱스 값을 얻는다.
- 얻은 인덱스 값을 `remove(int index)`에 전달해 삭제하고 `true`를 반환한다.
- 만약 얻은 인덱스 값이 -1이면 `false`를 반환한다.


### indexOf(E e) 메서드 

- indexOf() 메서드는 전달한 인자와 제일 첫 번째로 일치하는 요소의 인덱스를 반환한다. 
- 일치 여부는 `equals()` 메서드를 사용하는데, 이 메서드의 기준은 `e` 객체 내부에서 오버라이딩한 것을 기준으로 사용한다. 
- 여기서 `equals()`를 사용하는 이유는 논리적으로 같은 것을 찾기 때문이다(동등성). 논리적으로 같아도 동일하지 않을 수 있기 때문이다. 바로 동일한 것을 찾고 싶다면 `==`을 사용한다.

## get / set 구현하기

```java
public E get(int index) {
    return (E) elementData[index];
}

public E set(int index, E e) {
    E oldValue = get(index);
    elementData[index] = e; 
    return oldValue;
}
```

- get() 메서드로 가져올 때는 Object로 저장했기 때문에 E로 형 변환을 한 후 가져 와야 한다. 
- set() 메서드는 get() 메서드로 이전 값을 가져온 후 설정한다. 


## 기타 요소 구현하기 

```java
public void clear() {
    elementData = new Object[DEFAULT_CAPACITY];
    size = 0;
}

public Object[] toArray() {return Arrays.copyOf(elementData, size);}

@Override
public String toString() {
    return Arrays.toString(Arrays.copyOf(elementData, size)) + " size=" + size + ", capacity=" + elementData.length;
}
```
### size 구현하기

```java
public int size(){
    return size;
}
```

size 필드는 private이므로 별도의 메서드를 만들어야 클라이언트에서 접근이 가능하다. size가 public이면 외부에서 수정할 경우, 내부 데이터 수와 일치하지 않을 수 있어서 MyArrayList 동작이 이상해버릴 수 있다.


### isEmpty 구현하기

```java
public boolean isEmpty() {
    return size == 0;
}
```

size의 값이 0과 일치하는지 확인해서 리스트가 비어있는지, 아닌지를 확인하는 메서드다. 

### contains 구현하기

```java
public boolean contains(E e) {
    return indexOf(e) >= 0 ? true : false;
}
```

특정 요소의 존재 유무를 확인하는 메서드다. indexOf는 특정 요소의 인덱스 값을 확인하는 것이므로 다르다. 하지만,indexOf 메서드를 사용해 존재 유무를 확인할 수 있다. indexOf는 값이 존재하면 0 이상의 인덱스를 반환하므로 이에 따라 삼항 연산자를 사용해 true를, 0 미만은 false를 반환하면 된다.



### clear 구현하기

```java
public void clear() {
    elementData = new Object[DEFAULT_CAPACITY];
    size = 0;
}
```

모든 요소들을 지우기 위해 반복문으로 순회하며 null을 대입할 수도 있지만, 새로운 배열을 넣어주면 훨씬 간단하다. size도 0으로 수정한다.


### toString 구현하기

```java
@Override
public String toString() {
    return Arrays.toString(Arrays.copyOf(elementData, size)) + " size=" + size + ", capacity=" + elementData.length;
}
```

toString 메서드를 만들지 않으면 객체를 출력할 때 리스트와 관련된 내부 값 정보를 확인할 수 없다. 다만, elementData의 모든 값을 보여줄 필요 없이 실제 있는 값만 보여주면 되기 때문에 `Arrays.copyOf(elementData, size)`를 실행하면 된다. 



&nbsp;

# 배열리스트 전체 코드
---

```java
public class MyArrayList<E> {
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


    public void add(E e) {
        if (size == elementData.length) {
            grow();
        }

        elementData[size] = e;
        size++;
    }

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

    public E remove(int index) {
        E oldValue = get(index);

        shiftFromRightToLeft(index);
        elementData[--size] = null;
        return oldValue;
    }

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

    public int indexOf(E e) {
        for (int i = 0; i < size; i++) {
            if (e.equals(elementData[i])) {
                return i;
            }
        }
        return -1;
    }

    public E get(int index) {
        return (E) elementData[index];
    }

    public E set(int index, E e) {
        E oldValue = get(index);
        elementData[index] = e; 
        return oldValue;
    }

    public int size(){
        return size;
    }

    public boolean isEmpty() {
        return size == 0;
    }

    public boolean contains(E e) {
        return indexOf(e) >= 0 ? true : false;
    }

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