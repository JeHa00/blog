---
title: "Book Study - 클린 코드, 이제는 파이썬이다: 파이썬에서 빠지기 쉬운 함정들(Common Python Gotchas)"
date: 2023-01-06T19:14:21+09:00
draft: false
summary: 파이썬 코드를 작성하면서 빠지기 쉬운 함정들과 이에 대한 해결책을 파이썬스러운 코드를 통해 해결해본다.
tags: ["TIL", "Book Study"]
categories: ["Book Study"]
---
# 0. Introduction

- 아래 book study는 알 스웨이가트가 지었고, 박재호님이 번역하신 [클린 코드, 이제는 파이썬이다.](https://book.interpark.com/product/BookDisplay.do?_method=detail&sc.prdNo=355096830&gclid=Cj0KCQjw166aBhDEARIsAMEyZh4ltxiM-nlGaj3yjPIW82A6l-hPlXjmjBCqtmw6xzqRX8dc8Rk6PFMaAjm9EALw_wcB) 를 읽고 진행한 book study 입니다. 영문 원본으로 온라인 공개된 자료가 있어서 영문으로 학습합니다.

- 기존에 읽었던 Clean Code는 자바 코드로 되어 있어서, 먼저 파이썬 클린 코드를 학습 후 시작할려고 합니다.

- 이번 book study를 진행하면서 code에 대한 철학이 생기고, code를 바라보는 눈이 깊어지고, 넓어지기를 바랍니다.

- 각 chapter를 읽고 내용 정리하는 식으로 진행합니다.

- 이번에 학습하는 chapter의 주제는 **'Chapter 08: Common Python Gotchas - 파이썬에서 빠지기 쉬운 함정들'** 입니다.

<br>

---

# 1. 루프문 진행 중에는 리스트에서 아이템을 추가/삭제하지 말자

> **_루프문 진행 중에는 리스트에서 아이템을 추가/삭제하지 말고, 새로운 리스트를 생성해서 하자._**

list에 'sock'이라는 문자열이 발견될 때마다 이와 일치하는 'sock'을 삽입하여 해당 'sock'의 갯수를 짝수로 만드는 코드다. 

### 아이템 추가

```python
clothes = ['skirt', 'red sock']  
for clothing in clothes: # 리스트를 반복 
    if 'sock' in clothing: # 'sock' 문자열 찾기
        clothes.append(clothing) # 일치하는 'sock' 짝을 추가
        print('Added a sock:', clothing) # 사용자에게 알림

Added a sock: red sock
Added a sock: red sock
Added a sock: red sock
...
Added a sock: red sock
...
```

clothing에 추가된 아이템이 다음 반복에서 참조된다. 아래 이미지와 같이 'red sock'이 추가된다. 컴퓨터의 메모리가 부족해지거나 사용자가 중단해야 멈춘다. 

![image](https://user-images.githubusercontent.com/78094972/210774947-265b78e4-300f-4e49-87f2-479222147d26.png)


이 코드에서 알려주는 교훈은 list를 반복하는 동안 이 리스트에 새 아이템을 추가하면 안된다는 점이다. 의도치 않게 계속 반복되기 때문이다. 

그래서 위 코드를 아래와 같이 변경하는 걸 권장한다. 'newClothes' 같은 새 리스트를 생성하여 사용한다.

```python
clothes = ['skirt', 'red sock', 'blue sock']  
newClothes = []
for clothing in clothes:
    if 'sock' in clothing: 
        print('Appending:', clothing)
        newClothes.append(clothing)
print(newClothes)
clothes.extend(newClothes)

Append: red sock
Append: blue sock
['red sock', 'blue sock']

>> clothes.extend(newClothes)
['skirt', 'red sock', 'blue sock', 'red sock', 'blue sock']  
```

<br>

### 아이템 삭제

for 루프문을 실행하는 과정에서 아이템을 '추가'하면 안되듯 '삭제'해서도 안된다. 삭제를 하여 index가 수정되면 이 과정에서 원소를 조사하지 않고 지나치기 때문이다. 

아래 코드를 보면 'mello'는 삭제되었지만 'yello'는 삭제되지 않았다. 위 설명대로 'mello'가 삭제되는 순간 index가 댕겨지면서 index `[2]`까지는 확인했기 때문에, 삭제된 후 `[3]`을 확인하게 된 것이다.

```python
greetings = ['hello', 'hello', 'mello', 'yello', 'hello']
for i, word in enumerate(greetings):
    if word != 'hello':
        del greetings[i]

print(greetings) # ['hello', 'hello', 'yello', 'hello']
```

![image](https://user-images.githubusercontent.com/78094972/210934440-b99e6c0d-abb4-4f97-896c-88eaba35cd9e.png)


그렇다면 어떻게 코드를 작성하는게 좋을까?

삭제할 아이템을 제외한 모든 아이템을 복사하는 새 리스트를 만든 다음, 원래 리스트를 교체하자. 

```python
greetings = ['hello', 'hello', 'mello', 'yello', 'hello']
newGreetings = []
for word in greetings:
    if word == 'hello':
        newGreetings.append(word)

greetings = newGreetings
print(greetings) # ['hello', 'hello', 'hello']
```

다음으로 이를 list comprehension으로 작성해보자. 훨씬 더 간결하고, 리스트를 변경할 때 발생하는 함정을 피할 수 있다.

```python
greetings = ['hello', 'hello', 'mello', 'yello', 'hello']
newGreetings = [word for word in greetings if word == 'hello']
```

<br>

### 참조, 메모리 사용, sys.getsizeof() method

원본 대신 새로운 리스트를 생성하면 메모리 낭비로 보일 수 있다. 하지만, 변수가 실제 값 대신에 '값에 대한 참조'를 포함하는 것처럼 리스트도 값에 대한 참조를 포함한다. 

`newGreetings.append(word)` 행은 word 변수에 있는 문자열을 복사하는 게 아니라, 문자열의 참조를 복사하기 때문에 훨씬 적은 메모리를 차지한다. `sys.getsizeof()`를 통해 확인해보자.


```python
>> import sys
>> sys.getsizeof('cat') 
52 # 52바이트
>> sys.getsizeof('a much longer string than just "cat"')
85 # 85바이트

>> sys.getsizeof(['cat']) 
64 # 64바이트
>> sys.getsizeof(['a much longer string than just "cat"'])
64 # 64바이트
```

list에 담겨진 문자열을 크기와 상관없이 64바이트를 차지한다. 왜냐하면 실제 문자열을 포함하는 게 아닌, 문자열을 참조할 뿐이다. 

**_🔆 참조는 참조된 데이터의 크기에 관계없이 크기가 동일하다._**

그러므로 원래 리스트를 반복하면서 해당 리스트를 수정하지 않고 새로운 리스트를 생성한다고 해서 메모리를 낭비한다고 생각해서는 안된다. 

리스트를 수정하는 코드가 언뜻 동작하듯이 보여도, 에러 발견과 수정에 오랜 시간이 걸리는 미묘한 버그의 원인이 될 수 있다. 차라리 컴퓨터 메모리를 낭비하는 편이 낫고, 프로그래머의 시간을 허비하는 것은 훨씬 고비용이다.

### 아이템 변경은 가능하다.
루프문에서 리스트를 실행할 때, 아이템을 추가하거나 제거하지 않아야 하지만 리스트의 내용은 수정해도 좋다.

```python
>> numbers = ['1', '2', '3', '4', '5']
>> for i, number in enumerate(numbers):
>>    numbers[i] = int(number)

>> numbers 
[1, 2, 3, 4, 5]
```

### 안전하게 아이템을 삭제, 추가하는 또 다른 방법: 거꾸로 반복하기

아이템을 삭제하거나 추가하기 위한 또 다른 방법은 **_리스트를 끝에서부터 앞으로 거꾸로 반복하는 것_** 이다.

예를 들어 다음 코드를 보자. 리스트에서 루프 반복문이 실행되는 동안 삭제되므로 IndexError가 발생된다

```python
someInts = [1, 7, 4, 5]

for i in range(len(someInts)):
    if someInts[i] % 2 == 0:
        del someInts[i] # IndexError: list index out of range
```

하지만, 이번에는 거꾸로 반복해보자.

```python
someInts = [1, 7, 4, 5]
for i in range(len(someInts)-1, -1, -1):
    if someInts[i] % 2 == 0:
        del someInts[i]

someInts # [1, 7, 5]
```

위 코드가 잘 동작하는 이유는 for 루프 내에서 반복될 아이템은 인덱스가 바뀌는 것이 하나도 없기 때문이다.

삭제뿐만 아니라, 추가도 잘 동작이 되지만, 살짝만 바꿔도 버그가 등장하므로 제대로 하기 까다롭다. 

그렇기 때문에 ❗️ **_원본 리스트 수정보다는 신규 리스트 생성이 훨씬 더 간단하다._**

🔆 그래서 파이썬의 핵심 개발자 레이먼드 헤팅어는 이렇게 말한다.

> Question: 루프문을 돌면서 리스트를 수정하는 가장 좋은 방법은?  
> Answer: 없다. 생각도 하지 마라.


<br>

---

# 2. copy.copy()나 copy.deepcopy() 없이 가변 값을 복사하지 말자

변수는 객체를 포함하는 상자라기보다는 **_객체를 참조하는 레이블 또는 이름 태그_** 로 생각하자. 

### 변수 할당 시

파이썬에서 할당문은 절대로 객체를 복사하는 게 아닌, 객체에 대한 참조를 복사할 뿐이다.

```python
spam = ['cat', 'dog', 'eel']
cheese = spam 
spam # ['cat', 'dog', 'eel']
cheese # ['cat', 'dog', 'eel']
spam[2] = 'MOOSE'
spam # ['cat', 'dog', 'MOOSE']
cheese # ['cat', 'dog', 'MOOSE']
id(cheese), id(spam)# 235696337288
```

분명히 spam만 수정했지만, cheese까지 수정된 걸 확인할 수 있다. 

이렇게 된 이유는 **_할당문은 객체를 복사하지 않고, 객체에 대한 참조만 복사하기 때문이다._**

리스트 객체를 중복으로 만들지 않는다.

### 인자로 전달 시

이는 할당뿐만 아니라, 함수 호출에 인자로 전달된 객체에도 동일한 원리가 전달된다.

```python
def printIdOfParam(theList):
    print(id(theList))

eggs = ['cat', 'dog', 'eel']
print(id(eggs)) # 2356893256136
printIdOfParam(eggs) # 2356893256136
```


❗️ 만약 파이썬이 참조가 아닌 전체 리스트를 복사했다고 생각해보자. 

eggs에는 단 3개가 아닌 10억 개의 아이템이 들어있는 상황에서 인자로 넘기면 이거에 대한 리스트를 전부 복사해야 한다.

따라서 간단한 함수를 호출하는데 메모리는 매우 많이 잡아먹는다. 그래서 파이썬 할당이 참조만 복사하고 객체는 절대 복사하지 않는 이유다.

### 해결책 

이런 함정에서 벗어나는 방법은 `copy.copy()` 메소드로 단순히 참조가 아닌 복사본을 만드는 것이다.

```python
import copy
bacon = [2, 4 ,8, 16]
ham = copy.copy(bacon)
id(bacon) == id(ham) # False
```

그러나 변수가 객체를 포함하는 상자가 아닌 객체에 대한 레이블이나 이름표와 같듯이, 중첩 리스트의 경우에는 리스트 안에 객체를 참조하는 레이블이나 이름표가 포함된다. 그래서 중첩 리스트의 경우에는 copy.copy()를 할지라도 내부 리스트에 대해서는 참조만 복사한다.

```python
import copy
bacon = [[1, 2], [3, 4]]
ham = copy.copy(bacon)
bacon.append('APPENDED')
bacon # [[1, 2], [3, 4], 'APPENDED']
ham # [[1, 2], [3, 4]]
bacon[0][0] = 'CHANGED'
bacon # [['CHANGED', 2], [3, 4], 'APPENDED']
ham # [['CHANGED', 2], [3, 4]]
id(bacon[0]) == id(ham[0]) # False
```

위 코드의 결과 copy.copy()를 사용했음에도 불구하고 두 변수에 모두 반영되었다.  

이런 경우, **_copy.deepcopy()_** 를 사용하면 리스트 객체 내의 모든 리스트 객체를 복사할 수 있다.


## copy.deepcopy를 권장

객체를 복사할 때 2가지 방법이 있음을 알았다. 이 중에서 copy.deepcopy()를 권장한다. 왜냐하면 미묘한 버그까지 예방할 수 있기 때문이다. copy.copy()에 비해서 약간 느리지만 눈치채기 어려운 정도라고 한다.


<br>

---


# 3. 기본 인수에 가변 객체는 사용하지 말자

파이썬에선 정의된 함수에서 파라미터에 대한 기본 인수(default argument)를 설정할 수 있다. 개발자가 파라미터를 명시적으로 사용하지 않으면 기본 인수를 사용해서 함수가 실행된다. 예를 들어서 `'cat dog'.split()`는 `'cat dog'.split(None)`를 호출하는 경우와 동일하다.

그러면 다음으로 기본 인수로 가변 객체를 사용한 경우를 보자.

```python

def addIngredient(ingredient, sandwich=['bread', 'bread']):
    sandwich.insert(1, ingredient)
    return sandwich

mySandwich = addIngredient('avocado')
mySandwich # ['bread', 'avocado', 'bread']
```

위와 같이 작성할 수 있다. 하지만, 기본 인자로 가변 객체를 사용할 경우 다음 코드와 같은 문제점이 발생할 수 있다. 위 코드에 이어서 작성 후 실행했다.

```python
anotherSandwich = addIngredient('lettuce')
anotherSandwich # ['bread', 'lettuce', 'avocado', 'bread']
```

anotherSandwich를 선언한 건 처음인데, 어째서 avocado가 있는 것일까?

addIngredient()가 호출될 때마다 이 기본 인자 리스트를 재사용하기 때문에 이처럼 예상치 못한 동작으로 이어진다.

함수 def문은 매번 함수를 호출할 때마다 실행되는 게 아니라 한 번만 실행되기 때문에, 오직 `['bread', 'bread']` 하나만 생성된다. 

```python
mySandwich = ['bread', 'cheese', 'bread']
mySandwich = addIngredient('butter', mySandwich)
mySandwich # ['bread', 'butter', 'cheese', 'bread']
```

### 해결책

그래서 리스트 또는 딕셔너리 같은 가변 객체를 기본 인수로 사용해야하는 경우, 파이썬다운 해법은 기본 인수를 None으로 설정하는 것이다. 그리고, 이를 확인하고 함수가 호출될 때마다 새로운 리스트나 딕셔너리를 제공하는 코드를 작성한다. 

```python
def addIngredient(ingredient, sandwich=None):
    if sandwich is None:
        sandwich = ['bread', 'bread']
    sandwich.insert(1, ingredient)
    return sandwich

firstSandwich = addIngredient('cranberries')
firstSandwich # ['bread', 'cranberries', 'bread']

secondSandwich = addIngredient('lettuce')
secondSandwich # ['bread', 'lettuce', 'bread']

id(firstSandwich) == id(secondSandwich) # False
```

🔆 가변 데이터 타입에는 리스트, 딕셔너리, 집합, 클래스 문으로 만들어진 객체가 포함된다. 이러한 유형의 객체를 def 문에 기본 인수로 넣어서는 안된다.

<br>

---


# 4. 문자열을 문자열 연결로 생성하지 말자  

<br>

---


# 5. sort()가 알파벳 순으로 정렬하리라 기대하지 말자

<br>

---

# 6. 부동소수가 완벽히 정확할 거라고 가정하지 말자

<br>

---

# 7. 부등 연산자 !=를 연달아 쓰지 말자

<br>

---

# 8. 단일 아이템 튜플에서는 쉼표를 잊지 말자



<br>

---

# Reference

- [클린 코드, 이제는 파이썬이다.](https://book.interpark.com/product/BookDisplay.do?_method=detail&sc.prdNo=355096830&gclid=Cj0KCQjw166aBhDEARIsAMEyZh4ltxiM-nlGaj3yjPIW82A6l-hPlXjmjBCqtmw6xzqRX8dc8Rk6PFMaAjm9EALw_wcB)