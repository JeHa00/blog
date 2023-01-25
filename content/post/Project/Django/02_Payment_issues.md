---
title: "Project: Payment 개발 과정에서의 고려사항들과 개발 이슈들"
date: 2022-12-06T11:08:59+09:00
draft: false
summary: Payment 1차 기능을 개발하는 과정에서의 고려사항들과 개발 이슈들에 대해 정리해본다.
tags: ["payment"]
categories: ["Project"]
---
# 0. Introduction

- 이 repository는 현재 [러닝스푼즈 나노디그리 - django backend 부트캠프](https://learningspoons.com/course/detail/django-backend/)에서 팀 프로젝트를 진행하면서 다음과 같은 내용들에 대해 정리하고자 만들었습니다. 
    - 팀 정책을 이것으로 정한 이유 
    - 개발하면서 부딪힌 문제들에 대한 원인, 해결방안, 해결과정, 그리고 그 이유들

- 이번 포스팅에서는 payment 결제 1차 기능을 개발하는 과정에서의 고려사항들과 개발 이슈들에 대해 정리해보겠습니다.
    - payment 결제 1차 기능은 내부적으로 정한 상황들에 대해서 결제 자체가 가능하도록 개발하는 것에 목적을 두었습니다.  
    - 이후에는 환불 기능과 정기결제 기능을 추가하여 '구독' 개념을 도입할 예정입니다. 

&nbsp;

---

# 1. Payment model에 대한 고려사항

내가 구상하는 결제 모델은 배달, 장바구니, 할인 같은 모델과 연관이 되어 있지 않는 순수한 결제 자체의 정보만 담는 모델임을 고려하여 구성했다.

이번 경험을 통해서 결제 api를 가져와 연동할 때에는 입력된 기본값의 타입들이 외부 api에서는 무엇인지 확인하고 나서 모델 설계를 들어가야한다는 걸 알게 되었다.

## 1.1 User ForignKey

결제를 할 때는 로그인이 된 후 진행되는 user story로 구성했기 때문에, User model을 foreignKey로 진행한다. 

&nbsp;

## 1.2 merchant_id

### 필요 이유
merchant_id 는 '주문 번호'를 의미한다. 이를 생성한 이유는 다음과 같다.  
- 주문마다 독립적이고, 결제 정보를 보안의 관점에서 안전히 지킬 수 있기 위해서다. 
- 아임포트를 사용할 때, 결제 금액을 위변하여 결제 진행 자체를 막기 위해 아임포트에 독립된 주문 번호를 전달해야하기 때문이다.

&nbsp;

### merchant_id 암호화

위에 언급된 대로 독립적이고, 안전해야하기 때문에 '암호화' 하여 생성하기로 결정했다.

암호화는 `class PaymentManager(models.Manager)`에서 선언한 메서드인 create_new 과정에서 user 정보를 받아 진행된다.

**1) 기존 모듈 vs 외부 모듈 설치**

'암호화' 하는 방법에는 기존 라이브러리를 사용하는 방식과 써드 파트를 설치하는 방식이 있지만, 추가 설치하여 무게를 늘리고 싶지 않기 때문에 기존에 존재하는 모듈인 `hashlib`를 사용하기로 결정했다. 만약 무게가 늘어나면 EC2에 배포 시에 비용이 더 들기 때문이고, 다른 요인들에 영향을 주지 않는다면 비용이 적은 걸 추구하는 게 엔지니어로서 맞다고 생각했다.
- 고려 대상 모듈: `crytography`

#

**2) sha1 vs sha256**  

기존 모듈로 고려한 대상은 `hashlib`를 선택했으며 이 안에서도 `sha-1`과 `sha-256`을 고민했다. `sha-2`에서 `sha-256`을 선택한 이유는 일반적으로 이 해쉬 알고리즘을 사용한다고 확인했기 때문이다. 일반적으로 사용하는 만큼 안전성과 접근성 등등 여러모로 이유가 있을거라 판단했기 때문이다. 그러면 결국 `sha-1`을 선택한 이유는 이 두 가지를 사용하여 해시값을 생성해보니 `sha-1`이 더 짧았기 때문이다. 현재 프로젝트 규모가 크지 않기 때문에 그리 긴 해시값은 필요없다고 판단했기 때문이다.

❗️ 하지만 프로젝트 발표 후, 프로젝트 진행한 거를 회고하면서 다시 sha-1과 sha-2를 알아보면서 sha-1에 해독 가능성이 제시됭 이를 중단하여, 크롬 브라우저에서는 2019년부터는 SHA-1 인증서를 사용하는 사이트는 접속 못하도록 차단했다고 들어 프로젝트가 다시 착수할 때는 sha-256으로 바꿀 계획이다.

- sha: Secure Hash Algorithm의 약어 
- 참고 문서
    - [hashlib - 보안 해시와 메시지 요약](https://docs.python.org/ko/3/library/hashlib.html)  
    - [namu.wiki - SHA](https://namu.wiki/w/SHA)

#

**3) 암호화의 대상 구체화하기**

이 다음으로는 사용자 id 외에 구체적으로 무엇을 암호화할지를 고민하기 시작했다. 

사용자 id만 하기에는 안전하지 못하다는 생각이 들어, 지속적으로 변하여 그 값이 이전과 독립적인 게 무엇이 있을까 고민했다. 
그건 바로 '시간' 이었다. 그리고, 사용자 한 명이 결제를 여러 번 시도할 때 시간을 지속적으로 흐르기 때문에 사용자 id(user.id)와 시간을 가지고 만든다면 계속해서 독립적인 주문 번호를 생성할 수 있을거라 판단했다.

#

**4) digest vs hexdigest**

digest에 대해 먼저 알아보자. 

원본 데이터가 해시함수를 통과하여 암호화된 데이터를 '다이제스트(digest)'라고 한다.

`hashlib.sha1()`을 사용하는 코드들을 참고하니 digest와 hexdigest가 많이 언급되어 공식문서를 찾아보니 무엇보다 큰 차이는 digest는 해싱한 바이트 문자열을 반환하고, hexdigest는 바이트 문자열을 16진수로 변환한 문자열을 반환한다. `hashlib.sha1(<문자열>.encode()).digest() 또는 .hexdigest()`로 비교해보면 된다.

출력 결과 16진수로 되어있는 hexdigest가 더 짧은 문자열을 가지기 때문에 DB에 저장되는 용량이 더 적어질거라 판단하여 digest를 선택했다.

#

**5) 해시하기 위한 encoding**

파이썬에서의 문자열은 기본적으로 유니코드이므로, 해시하기 위해서는 바이트 형태가 필요하다. 그래서 해시 전에 반드시 인코딩되어야 하므로 `.encode('utf-8')`를 사용한다. 

❗️encode의 기본값은 `'utf-8'`이므로 별도로 입력할 필요는 없다. 프로젝트가 다시 착수될 때 수정할 사항 중 하나다.

#

**6) merchant_id의 자릿수**

merchant_id의 자릿수를 설계할 때 아임포트 API에 string(40)을 확인하지 못하고 다음과 같이 과하게 설계했다.

```python
merchant_id = models.CharField(verbose_name='주문번호' , max_length=120, unique=True)
```

그후 merchant_id를 암호화하여 자릿수를 생성할 때, 40을 20으로 착각했고, 현재 결제 건은 많지 않으니 '10'에 맞춰서 진행하기로 결정했다. 

그후, 프로젝트 발표를 마치고 코드를 보면서 회고하는 과정에서 max_length와 암호화 후, 자릿수를 인덱싱하는 과정에서 설계 상 맞지 않는 걸 확인했고, 이 부분 또한 프로젝트가 다시 착수되면 수정 사항으로 확인되었다. 

🔆 max_length는 DB의 VARCHAR 사이즈를 나타내는데, VARCHAR는 mysql에서 버전 4에서는 bytes 였다가 버전 5에서 글자수로 바뀌었다. 
- a max_length argument which specifies the size of the VARCHAR database field used to store the data. 출처: [Field options](https://docs.djangoproject.com/en/4.1/topics/db/models/#field-options)

#

**7) 두 번 암호화하기**

암호화를 할 때, user의 id와 시간만 합쳐서 암호화를 한 번 하는 것보다 부분적으로 인덱싱하여 가져와서 합친 후, 암호화를 한 번 더 실행하기로 했다. 그 이유는 구글링하여 해시에 대해 알아보다가 여러 번 해시함수를 거쳐서 다이제스트를 생성하는 방식을 보안적으로 사용한다는 글을 확인했기 때문이다.

```python
user_hash = hashlib.sha1(str(user.id).encode('utf-8')).hexdigest()[:5]
time_hash = hashlib.sha1(str(int(time.time())).encode('utf-8')).hexdigest()[-5:]
merchant_id = hashlib.sha1((user_hash + time_hash).encode('utf-8')).hexdigest()[:10]
```

&nbsp;

## 1.3 payment_id

아임포트에 merchant_id와 amount를 전달하고, 결제 이후에 아임포트 내에서 merchant_id를 기준으로 결제내역을 확인할 때 `imp_id`를 받는다. 이 imp_id와 merchant_id로 정상 거래인지 아닌지를 판단한다.

이 아임포트에서 전달한 imp_id가 DB에 payment_id로 저장됩니다. 즉 '결제 번호'를 의미한다. 이 결제 번호는 결제 오류로 아임포트에서 받지 못할 수도 있어 다음과 같이 `blank=True`로 설정한다.

&nbsp;

## 1.4 amount

amount는 '결제 금액'을 의미한다. 이 결제는 테스트 결제이기 때문에, 테스트 결제를 위한 최소 결제 금액을 알고 있는 것이 중요했다. 최소 결제 금액은 [최소,최대 결제금액이 얼만지 궁금해요!](https://faq.iamport.kr/7e1f6e5f-5e36-4617-b509-c26602079561)에서 KG 이니시스를 확인하면 '신용카드'는 100원이라는 걸 알 수 있다. 그래서 다음과 같이 default 값으로 100을 입력해야 한다.

```python
amount = models.PositiveIntegerField(verbose_name='결제 금액', default=100)
```

만약 이 100원보다 낮은 금액으로 merchant_id와 amount를 아임포트에 전달할 경우, 아임포트에서는 받아들여지지 않아서 다음과 같은 에러가 발생된다.

- 발생된 error: `거래건이 존재하지 않습니다.`

그렇기 때문에 결제 외부 API를 사용한다면 반드시 테스트를 위한 최소 결제 금액을 확인해야 한다.


&nbsp;

## 1.5 type

type은 결제 방식을 의미한다. 아임포트에서는 결제 수단을 'method'라 했지만 이는 다른 의미로 받아들여질 확률이 크다고 판단되어 type이란 명칭을 선택했다. 

또한, 프로젝트의 클론 대상이 되는 사이트에서는 카드 결제만을 고려했기 때문에, 이번 프로젝트 또한 카드 결제만을 고려했다.  

type의 choices 속성 값에 해당되는 PAYMENT_TYPE_CHOICES가 처음에는 IntegerField에 입력되기 위해서 
다음과 같이 before case로 적혔지만, 이런 경우 type 정보를 아임포트에 전달하면 못 받아들였기 때문에 아임포트와의 호환을 위해서 after case로 수정했다.

```python
# before case
PAYMENT_TYPE_CHOICES = [(1, '신용카드')]

# after case
PAYMENT_TYPE_CHOICES = [('card', '신용카드')]
```

❗️ type이란 명칭은 파이썬의 내장 함수 type()에 사용되는 예약어이기 때문에 수정사항에 해당된다.

#


## 1.6 status

'결제 상태'를 말한다. 결제 객체를 생성 후 중간 중간 과정을 저장하는 게 중요하기 때문이다. 이 저장된 값에 따라서 결제가 어떻게 중단되었고, 어디서 중단되었는지 알 수 있기 때문이다. 또한, 결제가 정확하게 이뤄졌는지 판단할 수 있다. 

처음에는 이 또한 IntegerField를 사용하여 choices를 다음과 같이 만들었다.

```python
# status choices
IS_DONE = 2
IS_AWAITING = 1
IS_CANCELLED = 0

STATUS_CHOICES =[
        {IS_DONE, '결제완료'},
        {IS_AWAITING, '결제대기'},
        {IS_CANCELLED, '결제취소'}
    ]

status = models.IntegerField(choices=STATUS_CHOICES, default=IS_AWAITING, verbose_name='결제상태')
```

하지만 이럴 경우, 아임포트와 연동하는 과정에서 혼란스러웠기 때문에 아임포트에서 전달하는 상태 값을 최대한 그대로 사용하는 게 낫다는 판단을 하여 CharField를 사용했다.


```python
STATUS_CHOICES =[
                 {'await', '결제대기'},
                 {'paid', '결제성공'},
                 {'failed', '결제실패'},
                 {'cancelled', '결제취소'}
                ]
                
status = models.CharField(verbose_name='결제상태', default='await', 
choices=STATUS_CHOICES, max_length=10)
```

max_length는 `cancelled` 를 고려하여 10으로 설정했다.

&nbsp;

---
# 2. Iamport.py 작성 시 고려사항 

iamport.py 는 아임포트 github에서 제공하는 함수를 그대로 가져온 게 아니라, 이를 참고로 나만의 방식으로 작성했다. 

### IMP_KEY와 IMP_SECRET

`IMP_KEY`와 `IMP_SECRET` 값을 가리키는 인스턴스 변수들을 생성자에 포함시켜서 해당 클래스를 가져와서 변수를 인스턴스화할 때, 별도의 KEY와 SECRET 값을 받지 않도록 설계했다. 


### 아임포트 api url을 클래스 변수로 만들기
아임포트 api url에서 protocol + host 부분은 클래스 변수로 만들어서 복잡한 url을 보다 가독성 좋고 알아보기 쉽게 만들었다.


### 아임포트 응답 성공일 때, 'code'의 값 
아임포트에서 응답을 보낼 때 'code'라는 key의 대응되는 값이 0이므로 단지 0으로 두기보다는 `response_success`라는 변수가 가리키도록 하여 가독성 부분도 고려했다. 


### request module
아임포트에 url에 데이터를 전달할 때는 `request` module을 사용했다. 

❗️ 위 부분이 반복되기 때문에 프로젝트를 다시 착수할 때 별도의 메서드로 함수화할 예정이다.


### status code

status code 부분은 400번대가 존재하는데 아임포트 api 문서에서 실패 시 반환하는 status code를 그대로 가져왔기 때문이다.


&nbsp;

---
# 3. models.Manager 작성 시 고려사항

### models.Manager를 사용한 이유

models.Manager를 사용한 이유는 로직을 view에서 처리하면 너무 길어지기 때문에, 이보다는 해당 Manager model에서 만든 쿼리 메서드를 만들어서 사용하는 게 관리의 관점에서 낫다고 판단했기 때문이다.

- [models.Manager 관련 django docs](https://docs.djangoproject.com/en/4.0/topics/db/managers/)

### Manager name
 
공식문서를 참고하여 Manager의 이름은 `<이 모델 매니저를 사용할 모델명>Manager`로 작명한다. 그리고 이 Manager를 사용할 모델에 objects에 할당한다.

```python
class Payments(models.Model):
    ...
    objects = PaymentsManager()
    ...
```

&nbsp;


---
# 4. DB에 저장되는 결제 정보 시간대 설정

결제 시간을 저장하기 위해서 DateTimeField를 사용했다.

저장된 결제 시간을 보니 실제 결제 시간과 DB에 저장된 시간이 다른 issue가 발생했다. 

[Django docs - TIME_ZONE](https://docs.djangoproject.com/el/1.10/ref/settings/#std:setting-TIME_ZONE)를 보면 settings.py에 `TIME_ZONE`이 정의되어 있지않으면,  default 값으로 `America/Chicago` 로 설정되는 걸 알 수 있다. 

그래서 다음 두 가지 설정을 추가해본다.

```python
# settings.py

TIME_ZONE = 'Asia/Seoul
USE_TZ = False
```

`USE_TZ` 가 True이면 default 값으로 time zone이 인식된다. 그래서 이 부분을 False로 변경한다.

그리고, `TIME_ZONE`에 무슨 값을 세팅할지 알지 못해서 구글링을 통해 확인했다. 나중에 위 문서를 보니 
[list of time zones](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)에 목록들이 나와있는 걸 확인했다. 

&nbsp;

---
# 5. post_save.connect()


## post_save.connect를 택한 이유와 역할

### 이유
post_save.connect()를 사용한 이유는 2가지다.
- 첫 번째: Payment 모델 객체에 관한 것이기 때문에, models.py 에서 처리하길 원했다.
- 두 번째: Payment 모델 save()가 실행되면 자동적으로 payment_validation을 체크하여 아임포트와 로컬 DB에 존재하는지 자동적으로 체크하길 원했다.

```python
from django.db.models.signals import post_save
...
post_save.connect(payment_validation, sender=Payment)
```

### 역할
[docs Django - Signals](https://docs.djangoproject.com/en/4.1/ref/signals/)를 참고하면 post_save는 model 객체에 대해 save() method가 실행된 후, method가 실행되도록 신호를 보낸다.

Payment model이 저장되면 위 코드를 사용하여 payment_validation 함수를 실행한다. 만약 payment_id가 존재할 경우, 아임포트 내에서 찾은 결제 내역이 실제 모델에도 존재하는지를 확인한다. 한 곳이라도 없으면 '비정상 거래' 임을 알리는 역할을 수행한다.

&nbsp;

## post_save.connect의 내부 원리


### ModelSignal class and partial class

> **_model 객체인 sender 그리고, receiver를 받아서 partial class를 통해 Signal.connect에 전달해주는 역할_**

post_save는 다음과 같이 ModelSignal class의 인스턴스다. 그래서 이 인스턴스에 접근하여 인스턴스 메서드인 connect가 실행된다. connect에서 받은 인자들을 _lazy_method에 전달한다. 


```python
# signals.py

class ModelSignal(Signal):
   
    ...

    def _lazy_method(self, method, apps, receiver, sender, **kwargs):
        ...
        partial_method = partial(method, receiver, **kwargs)
        if isinstance(sender, str):
            ...
        else:
            return partial_method(sender)

    def connect(self, receiver, sender=None, weak=True, dispatch_uid=None, apps=None):
        self._lazy_method(
            super().connect, apps, receiver, sender,
            weak=weak, dispatch_uid=dispatch_uid,
        )
    ...
...

post_save = ModelSignal(use_caching=True)
```

받은 인자는 아래 코드대로 self를 사용하여 같은 클래스의 메서드를 호출하여 전달된다. 아래에서 super()는 Signal 상위 클래스를 말한다.

```python
self._lazy_method(method=super().connect, apps=None, receiver=payment_validation, sender=Payment, ...)
```

그러면 partial_method 인스턴스 객체를 만든다. 이 때 partial class 내부의 `__new__` method가 실행되서 다음과 같이 func과 args 속성이 각각 method와 receiver를 가리킨다.

```python
partial_method = partial(method=super().connect, receiver=payment_validation)
partial_method.func = super().connect
partial_method.args = payment_validation
```

그 다음으로 `_lazy_method` method는 객체가 문자열 유무에 따라 분기가 되는데, Payment의 type()은 str이 아니고, `<class 'django.db.models.base.ModelBase'>` 이므로, `partial_method(sender=Payment)`를 실행한다. 

`partial_method(sender=Payment)` 실행되면 'partial class'의 `__call__` method가 호출된다.

```python
def __call__(self, /, *args, **keywords):
    keywords = {**self.keywords, **keywords}
    return self.func(*self.args, *args, **keywords)
```

위의 각 매개변수는 다음을 의미한다.
- self.func: super().connect -> Signal.connect
- *self.args: payment_validation
- *args: Payment


### Signal.connect

그러면 `self.func(*self.args, *args, **keywords)`가 어떻게 실행되는지 알아보자. 

```python
self.lock = threading.Lock()
self.sender_receivers_cache = weakref.WeakKeyDictionary() if use_caching else {}

def connect(self, receiver, sender=None, weak=True, ...):

    if weak:
        ...
        weakref.finalize(receiver, self._remove_receiver)

    with self.lock:
        self._clear_dead_receivers()
        if not any(r_key == lookup_key for r_key, _ in self.receivers):
            self.receivers.append((lookup_key, receiver))
        self.sender_receivers_cache.clear()

# threading.py
_allocate_lock = _thread.allocate_lock
Lock = _allocate_lock
```

### weakref.finalize() class
Signal class는 weak라는 변수로 약한 참조와 강한 참조를 구분한다. weak가 True이면 약한 참조를 의미하여, weakref.finalize() class를 통해 receiver가 garbage collector에 의해서 수거될 때 실행할 콜백함수를 등록한다. 여기서 콜백함수는 self._remove_receiver다.

### dead_receivers
receiver가 실행 및 완료되면 `self._remove_receiver`가 실행되어 `self._dead_receivers = True` 값으로 변경되어 self.receivers에 dead_receiver가 존재하는 걸 알린다. 

이 dead_receiver는 self.lock이 유지되는 동안 _clear_dead_receivers에 의해서 
- 1) 플래그 변수인 self._dead_receivers를 존재하지 않는다는 의미로 False로 바꾼 후,
- 2) dead_receivers를 self.receivers에서 제외시킨다.

### sender_receivers_cache


&nbsp;

## 🔆 참고: 약한 참조 vs 강한 참조

- 약한 참조(Weak reference): 참조수(reference count)를 증가시키지 않는 reference 객체
- 강한 참조(Strong reference): 참조수(reference count)를 증가시키는 reference 객체  

- Garbage collector는 reference count가 0인 경우, 해당 reference 객체를 삭제하고 사용하지 않는 메모리라고 판단하여 해당 메모리를 반환한다. 
    - 그렇다면 약한 참조는 언제든지 GC에 의해서 언제든지 제거될 수 있다. 
    - 강한 참조는 reference count가 0이 되거나 메모리에서 해제될 때 제거된다.

- Reference count 확인하기
    - reference count는 `sys.getrefcount(value)`를 사용하면 value의 참조 수를 알 수 있다.


&nbsp;

---

# Reference 

- [hashlib - 보안 해시와 메시지 요약](https://docs.python.org/ko/3/library/hashlib.html)  
- [namu.wiki - SHA](https://namu.wiki/w/SHA)  
- [Field options](https://docs.djangoproject.com/en/4.1/topics/db/models/#field-options)  
- [최소,최대 결제금액이 얼만지 궁금해요!](https://faq.iamport.kr/7e1f6e5f-5e36-4617-b509-c26602079561)  
- [models.Manager 관련 django docs](https://docs.djangoproject.com/en/4.0/topics/db/managers/)  
- [list of time zones](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)  
