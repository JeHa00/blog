---
title: "[TIL] Python basic 17: try ~ exception"
date: 2022-03-03T01:33:28+09:00
draft: False
summary: Error의 종류와 예외 처리문에 대해 알아보고, 일부러 에러를 일으키는 이유와 방법에 대해 알아보겠다.
tags: ["TIL", "python"]
categories: ["개발-dev Python"]
---

## 1. Exception(예외) and Error(에러)의 차이점

- `Exception(예외)` 와 `Error(에러)`에 대해 설명하겠다.
- 예외는 무엇이고 에러는 무엇이다라고 외우진 말자.
- 예외를 에러의 범주에 포함하기도 하기 때문이다. 다만, 예외와 에러에 대한 틀을 잡기 위해 설명한다.

<br>

- `Exception(예외)`는 우리가 작성한 로직에서 비정상적으로 발생한 사건(event)이다.
  - 예외에는 `예측이 가능한 예외`와 `예측이 불가능한 예외`로 나눠진다.
  - `예측 가능한 예외`의 경우는 `id 입력`을 생각해보자. 영어로 입력해야하는데, 한글을 입력했을 경우 예외가 발생한다.
  - `예측 불가능한 예외`의 경우는 `메모리의 용량`이 부족해서, `OS`에서 발생한 에러, 하드웨어적인 에러 등등이 해당된다.
- 이 예외와 에러를 잘 처리해야 내가 의도한대로 작동하는 어플리케이션을 만들 수 있다.

---

<br>

## 2. Error의 종류

### 2.1 SyntaxError: 문법 오류

```yml
# 조건문의 헤더에 콜론(:)을 하지 않아 발생했다.
> print('error')
> print('error')
> if True
>     pass
SyntaxError: invalid syntax
```

<br>

### 2.2 TypeError: 자료형에 맞지 않는 연산을 수행하여 발생하는 오류

```yml
> x = [1,2]
> y = (1,2)
> z = 'test'

# list와 tuple은 합칠 수 없다.
> print(x + y)
TypeError: can only concatenate list (not "tuple") to list

# string과 list는 합칠 수 없다.
> print(x + z)
TypeError: can only concatenate list (not "str") to list

# tuple과 string은 합칠 수 없다.
> print(y + z)
TypeError: can only concatenate list (not "str") to list

```

<br>

### 2.3 NameError: 참조가 없을 때 발생하는 오류

```yml
> a = 10
> b = 15
> print(c)
NameError: name 'c' is not defined

```

<br>

### 2.4 IndexError: index가 존재하지 않아 발생하는 오류

```yml
> x = [50, 70, 90]
> print(x[1])
70
> print(x[4]
IndexError: list index out of range

# 알고리즘에서 op을 사용할 때 발생할 수 있는 에러
> print(x.pop())
50
> print(x.pop())
70
> print(x.pop())
90
> print(x.pop())
IndexError: pop from empty list
```

<br>

### 2.5 ValueError: 참조값이 없을 때 발생하는 오류

```yml
> x = [10, 50, 90]
> x.remove(50)
> print(x)
[10, 90]

> x.remove(200)
ValueError: list.remove(x): x not in list
```

<br>

### 2.6 KeyError: 해당되는 key가 없을 때 발생하는 오류

```yml

# `key` 값으로 `value` 값을 출력할 때는 `.get()` 함수를 사용한다.
> x = {'A': 'apple', 'B': 'Banana', 'C': 'coffee'}
> print(x['D'])
KeyError: 'D'
> print(x.get('D'))
None
```

<br>

### 2.7 AttributeError: 모듈, 클래스에 있는 잘못된 속성을 사용하여 발생한 오류

```yml
> import time
>  print(time.time2())
AttributeError: module 'time' has no attribute 'time2'
```

<br>

### 2.8 FileNotFoundError: 파일을 찾을 수 없을 때 발생하는 오류

```yml
> f = open('test.txt')
FileNotFoundError: [Errno 2] No such file or directory: 'test.txt'
```

<br>

### 2.9 ZeroDivisionError: 0으로 나눠서 발생하는 오류

```yml
> print(100 / 0)
ZeroDivisionError: division by zero
```

---

<br>

## 3. 예외 처리 (try ~ exception)

- `try`: `에러가 발생할 가능성이 있는 코드` 실행
  - 이 에러는 내 코드가 정확해도, 방문한 사이트 서버나 여러 프로그램이 외부와 연결될 때, 문제가 있을 수 있기 때문이다. 그래서 외부적으로 문제가 발생해도 `try ~ except`로 대비한다.
- `except 에러명 1~n`:
  - error가 발생했을 때 잡아내어 다음 코드로 넘어가도록 해준다.
  - 여러 개 가능하다.
  - 어떤 Error만 잡을지 정할 수 있다.
  - 모든 error 잡아내기
    - `except Exception:`은 모든 예외의 부모격이라 모든 error를 잡아낸다.
    - `except:` 또한, 모든 error을 잡아낸다.
    - 하지만, 정확히 어떤 error가 발생했는지 알 수 없다.
    - 정확히 어떤 error가 발생했는지 알아야 로그를 남길 때 정확히 남길 수 있다.
  - 예제 3에서처럼 예제 2에서 `alias`를 줘서 `except Exception as e` 와 `print(e)` 을 사용하여, 대략적인 error 내용을 확인하여 출력하도록 할 수 있다.
- `else`: `try block`에 `에러`가 `없을 경우` 실행한다.
  - 정상적으로 흘러갈 때 실행된다.
  - `for ~ else` 에서도 `for`문에 `break`를 만나지 않으면 `else`가 실행했듯이, `except`를 만나지 않으면 실행된다.
- `finally`: 에러발생 유무와 상관없이 `무조건` 실행된다.
  - error 발생 유무에 상관없이 항상 실행해줘야 하는 구문으로 `finally`를 사용한다.
  - 예를 들어 error가 발생했을 경우, 연결된 메모리를 끊어줘야 메모리가 새지 않기 때문에 `finally`를 통해서 실행한다.

```yml
## 예제 1
## error 발생하지 않는 코드
> name = ['Kim', 'Lee', 'Park']
> print(name.index('Kim'))
> try :
>      z = 'Kim'
>      x = name.index(z)
>      print('{} Found it! {} in name'.format(z, x + 1))

# error 중 ValueError 만 잡는다.
# error 처리 시, 이렇게 error의 정확한 유형대로 잡아내느 것이 제일 좋은 case다.
> except ValueError:
>      print('Not found it! - Occurred ValueError!')

# error가 발생되지 않았으므로 else 구문이 작동된다.
> else:
>      print('Ok! else.')
> print(' ' )
0
Kim Found it! 1 in name
Ok! else.

## 예제 2
# error 발생 코드
# error가 발생했어도 except로 잡아냈기 때문에 else 구문을 출력된다.
# 그리고, 그 다음 코드를 계속해서 실행한다. print('pass')가 출력된 걸 확인할 수 있다.
# 또한, 예제 1에서의 ValueError를 지웠기 때문에 모든 Error를 잡아낸다.
# 하지만, 그래서 어떤 Error를 잡았는지 정확히 알 수 없다.
> name = ['Kim', 'Lee', 'Park']
> try:
>      z = 'Cho'
>      x = name.index(z)
>      print('{} Found it! {} in name'.format(z, x + 1))
#  except Exception:
>  except:
>      print('Not found it! - Occurred Error!')
>  else:
>      print('Ok! else.')
>
> print('pass')
Not found it! - Occurred Error!
pass

## 예제 3
# 예제 2를 보완한 코드

> name = ['Kim', 'Lee', 'Park']
> try:
>     z = 'Cho'
>     x = name.index(z)
>     print('{} Found it! {} in name'.format(z, x + 1))
# 예제 2와 달리 추가된 부분
> except Exception as e:
>     print(e)
#
>     print('Not found it! - Occurred Error!')
> else:
>     print('Ok! else.')
# 예외 유무에 상관없이 finally는 실행된다.
> finally:
>     print('Ok! finally')
'Cho' is not in list
Not found it! - Occurred Error!
Ok! finally
```

<br>

- `error`를 일부러 발생시킨 경우를 생각해보자.
- 언제 일부러 `error`를 발생시킬까??
- 여기서 `error`란 Python에서 발생시킨 게 아니라, 설계자가 회사에서 요구하는 논리상 알기 위해서 `일부러` 발생시킨 `error`를 말한다.
- 아래 코드로 설명을 하자면 `a`가 `Kim`이 아니라면 이는 파이썬 내에 `ValueError`가 발생된 것이 아니다. 하지만 회사에서 `Kim`이 아닌 경우에 대해 알기 위해서 error를 발생시켰다. 이를 토대로 언제 `Kim`이 아닌지 역으로 분석할 수 있다.

```yml
## error 발생 X
> name = ['Kim', 'Lee', 'Park']
>  try:
>      a = 'Park'
>      if a == 'Park':
>          print('OK! Pass!')
>      else:
>          raise ValueError
>  except ValueError:
>      print('Occurred! Exception!')
>  else:
>      print('Ok! else!')
OK! Pass!
Ok! else!


## error 발생 O
> name = ['Kim', 'Lee', 'Park']
>  try:
>      a = 'Park'
>      if a == 'Kim':
>          print('OK! Pass!')
>      else:
>          raise ValueError
>  except ValueError:
>      print('Occurred! Exception!')
>  else:
>      print('Ok! else!')
Occurred! Exception!
```

- a에는 'Park'가 할당되었다. 'Kim'이 아니기 때문에, 일부러 `ValueError`를 발생시켰다. 그래서 `except ValueError` 문이 실행이 되어 print문이 출력되었다.
- 일부러 Error를 일으키고, ErrorType도 정할 수 있다는 걸 알고 있자.
