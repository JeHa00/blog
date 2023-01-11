---
title: "Project: Iamport를 선택한 이유, 사용 시 결제 과정과 장점, 그리고 아임포트 javascript SDK를 사용한 결제 흐름"
date: 2022-12-04T14:56:16+09:00
draft: false
summary: 기존 결제 과정과 iamport를 사용 시 변한 결제 과정을 정리하고, iamport 사용 시 얻는 장점들에 대해 정리해본다. 마지막으로 프로젝트에서 아임포트 javascript SDK를 사용한 결제 흐름을 정리해본다.
tags: ["iamport"]
categories: ["Project"]
---

# 0. Introduction

- 이 repository는 현재 [러닝스푼즈 나노디그리 - django backend 부트캠프](https://learningspoons.com/course/detail/django-backend/)에서 팀 프로젝트를 진행하면서 다음과 같은 내용들에 대해 정리하고자 만들었습니다. 
    - 팀 정책을 이것으로 정한 이유 
    - 개발하면서 부딪힌 문제들에 대한 원인, 해결방안, 해결과정, 그리고 그 이유들

- 이번 포스팅에서는 제가 맡은 결제 기능의 전체적인 프로세스와 아임포트를 사용하면서 이 과정이 어떻게 변하는 지에 대해 설명하겠습니다.

&nbsp;

---

# 1. 기존 결제 과정

- 결제의 기본 진행 과정: `인증 -> 결제` 순서로 진행

- 하지만, 한국에서는 이 인증 부분에서 차이가 있다. 한국에서 카드 결제 과정은 다음과 같다. 왜냐하면 **한국에서는 카드 정보(카드 번호 / 유효기간 / cvc )를 일부 가맹점 또는 PG 사를 제외하고는 저장할 수 없기 때문이다. 카드 정보는 오로지 카드사에서만 저장 가능하다.**

- 카드사 서버가 구매자 브라우저로부터 직접 카드 정보를 전달받아 인증처리하는 게 다른 나라와 가장 큰 차이이다. 

- 이 카드 인증된 결과를 바탕으로 결제 프로세스가 진행된다. 결제 프로세스는 3번부터 8번까지의 과정을 말한다.

![image](https://docs.iamport.kr/static/images/tech/webhook/pg-flow.svg)
출처: [아임포트 공식문서 - webhook](https://docs.iamport.kr/tech/webhook)

&nbsp;

### PG 사에서 저장할 수 없도록 만든 방법

![image](https://github.com/iamport/iamport-manual/raw/master/%EC%9D%B8%EC%A6%9D%EA%B2%B0%EC%A0%9C/screenshot/background/authentication.png)
출처: [아임포트 공식문서 - webhook](https://docs.iamport.kr/tech/webhook)

###

그래서 결제를 진행할 때 PG 사의 결제 모달 창이 뜬 후, 카드사를 클릭하면 또 다른 모달 창이 뜬다. 

이것이 PG 사에서 카드 정보를 저장하지 못하도록 하기 위함이다.  

그래서 PG 결제 모듈 창을 띄우고 나서 결제 과정의 첫 시작인 '인증' 과정이 시작될 수 있다.  


&nbsp;

---

# 2. Iamport 선택 이유와 장점
### Iamport 사용 시 결제 과정

![image](https://docs.iamport.kr/static/images/tech/webhook/iamport-flow.svg)
출처: [아임포트 공식문서 - webhook](https://docs.iamport.kr/tech/webhook)



## 아임 포트를 선택한 이유

### PG 사와의 복잡한 연동 과정 해소

> **_직접 api로 만들어야하는 많은 부분을 아임포트가 대신 해주기 때문에, 짧은 기간 안에 결제 기능을 추가하기 좋은 라이브러리로서, 경험이 없는 학습자가 사용하기에 보다 퀄리티에 집중할 수 있다._**      

직접 PG 서버와 통신해야 했던 가맹점 서버의 역할을 대신한다. PG 서버와 직접 통신하기 위해서 PG가 지원하는 제한적인 개발환경을 사용해야하고, PG 모듈을 설치하는 등 절차가 복잡했지만, 아임포트를 사용하면서 간결하고  REST API를 통해 쉽게 결제를 연동할 수 있다. 

위 이미지의 1, 2, 5, 7번 과정만 신경쓰면 된다.  카드정보가 잘못되었다면 카드사 모듈 창에서 입력 시, 인증 오류가 날 것이다. 결제 정보가 잘못되었다던가, 카드 한드가 초과되었다던가, 사용자 변심으로 결제가 취소했다면 결제 단계에서 에러가 난다.  


### 결제 기능 확장성

토스에서도 결제 API를 제공한다. 하지만, 나중에 '구독'이란 정기 결제를 도입할 상황에서 아임포트는 정기 결제 기능을 제공하고 토스에서는 그렇지 않기 때문에, 추후 결제 기능 확장성을 고려할 때 아임포트를 선택했다.


### 편리한 복수 PG 사용  

연동되는 PG 선택도 [iamport admin](https://admin.iamport.kr/auth/signin)에서 로그인하여 테스트 버전으로 자유롭게 선택할 수 있다.  

### 잘 정리된 api 문서

[아임포트 api 문서](https://api.iamport.kr/) 를 보면 아임포트 API가 체계적으로 정리되어 있다.

적절한 api url에 필요한 정보만 보내면 결제 정보를 조회할 수 있다.  



&nbsp;

---

# 3. 아임포트 Javascript SDK를 사용한 결제 흐름


아임포트 결제 연동 서비스를 사용하기 위해서는 아임포트 Javascript SDK(Software Development Kit)를 사용해야 한다. 이 SDK를 통해서 내가 생성한 주문번호와 금액을 아임포트에 전달하고, 아임포트는 결제 번호를 나에게 전달한다.

그래서 이 SDK를 통해 결제 각 단계에서의 데이터 처리를 js function을 통해서 해당 api로 데이터를 전달하기 위해서 fetch를 사용했다. 그리고, 이 view에서 model에 접근하여 고유 주문 번호, 결제 상태, 결제 금액 등의 정보를 저장한다. model에 접근할 때는 ModelManager를 사용하여 해당 Model 객체만의 ORM을 만들었다.

## 3.1 결제 프로세스 과정 순서

해당 프로젝트에서 설계한 결제 프로세스의 전체 흐름은 다음 이미지와 같다. 

![image](https://user-images.githubusercontent.com/78094972/211764083-805f1ddb-8622-45c4-90f2-3ef80d5d9a70.png)

&nbsp;

### 1), 2) 결제 버튼 클릭 및 call-payment.js 실행

아래 이미지의 '이 옵션 선택' 태그를 클릭하여 `call-payment.js`를 실행한다. 

❗️ 원래 사이트에서는 월간 멤버쉽과 연간 멤버쉽 둘 다 존재했다. 하지만, 월간 멤버쉽만 고려한 이유는 1차로 결제 자체 기능만 구현한 후, 그 다음으로 월간 멤버쉽만을 추가로 구현할 계획이기 때문이다.

![image](https://user-images.githubusercontent.com/78094972/204946352-7f8cb13c-11f5-4cb6-bec3-8b37daa64504.png)


call-payment.js의 전체 소스 코드를 보고 싶다면 [이 링크](https://github.com/backendnanodegree/Devket/blob/main/static/js/call-payment.js)를 클릭한다.

위 이미지의 버튼은 `.order`이라는 클래스 명을 가지고 있기 때문에 이를 통해서 DOM을 인식하여 event를 건다.

```js
import {getElement ,setFetchData} from './common.js';

window.onload = function () {
    const IMP = window.IMP;
    IMP.init('imp62676076');
    const paymentButton = getElement('.order')
    paymentButton.addEventListener('click', async (e) => {
        const type              = 'card';
        let buyer_name, buyer_email, merchant_id, amount, payment_info;
        payment_info            = await passPaymentInfo(e, type);
        [buyer_name, buyer_email, merchant_id, amount] = payment_info
        if (merchant_id !== '' && merchant_id !== undefined) {
           ...
        }
        return false;
    })
};
```

&nbsp;


### 3) 가맹점 식별코드로 IMP 객체 초기화

'call-payment.js'가 실행되면서 제일 먼저 하는 건 '가맹점 식별코드'로 인한 초기화 작업이다.
'가맹점 식별코드'는 아임포트를 가입할 때 IMP_KEY와, IMP_SECRET 그리고 가맹점 식별코드인 MERCHANT_ID가 제공된다. 이 MERCHANT_ID를 입력해야 한다. IMP_KEY와 IMP_SECRET은 아임포트에 접근하기 위한 토큰을 받기 위해서 필요하다.

&nbsp;

### 4) passPaymentInfo 함수 실행

> **_IMP.request_pay()에 전달하기 위한 merchant_id(고유 주문 번호), buyer_name, buyer_email을 backend에서 받아내는 함수_**  

![image](https://user-images.githubusercontent.com/78094972/211742667-8b37833c-0dbc-42f0-a6d5-6e2c17eb8848.png)

이벤트가 발생 시, call-payment.js에서 가장 먼저 실행되는 함수다. 그 이유는 아임포트 api에 전달할 merchant_id (고유 주문 번호)를 생성하고, amount(결제 금액)와 함께 전달하기 위해서다.

argument(or parameter)로 받은 type은 결제 방식을 말한다. 현재 개발 단계에서는 카드 결제만 고려하고 있기 때문에 `const type = 'card'`로 정해져있다. 

- **[4-1, 4-2] 과정**  
    - merchant_id를 생성하기 위해서 정해놓은 `/api/payment/checkout` 으로 fetch를 사용하여 data를 보낸다. 그러면 urls.py 에 의해서 이 api url과 mapping된 django view class로 request를 보낸다. 이 api url과 연결된 class는 `PaymentPassView` 다. 
- **[4-5] 과정**
    - `PaymentPassView`에서 보낸 response에서 json형태로 데이터를 뽑아낸다. `.works`는 아임포트 api에서 응답 결과를 알려주는 key 값이다. 아임포트에서 응답이 성공했으면 `"works": True`로, 실패하면 `False`로 값이 온다.

그래서 works가 True이면 view class에서 원하는 값을 받은 것이기 때문에, 이 경우 unpacking을 사용하고자 배열의 형태로 결과값을 반환했다.

```js
// IMP.request_pay를 호출하기 위한 merchant_id 반환
async function passPaymentInfo(e, type) {
    e.preventDefault(); 
    let merchant_id = ''
    let amount = 0
    let buyer_name = ''
    let buyer_email = ''
    const data = setFetchData('POST', {
        amount: amount, 
        type: type
    })
    const response = await fetch(`/api/payment/checkout`, data)
    const result   = await response.json() 
    if (result.works) {
        buyer_name = result.buyer_name 
        buyer_email = result.buyer_email
        merchant_id = result.merchant_id
        amount = result.amount
        return [buyer_name, buyer_email, merchant_id, amount]
    } else if (response.status === 401) {
        alert('로그인 해주세요.')
    } else {
        alert('문제가 발생했습니다. 다시 시도해주세요.')
    }};
```

그러면 PaymentPassView class에서 어떻게 merchant_id를 생성하여 반환하는지 알아보자.

로그인 후, 결제를 시도하면 다음 ORM으로 user과 email를 조회한다.

```python
user = User.objects.get(id=kwards['user_id'])
buyer_email = Email.objects.get(user=user).email 
# Email model에서 email은 회원가입 시 사용한 이메일을 말한다.
```

그 후, 이 객체 정보를 사용하여 merchant_id를 생성한다.

```python
merchant_id = Payment.objects.create_new(
                user=user,
                amount=amount,
                type=payment_type,
                )
```



- **[4-3 ~ 4-5 과정]**
    - **(4-3) class PaymentManager**  
        - Payment만의 orm을 사용하기 위해 만든 PaymentManager를 통해서 merchant_id를 생성한다.

    - **(4-4) import.py & Payment object 생성**  
        - 그후, 이 merchant_id와 amount를 아임포트에 전달한다.
        - PaymentManager의 클래스 변수로 선언한 imp를 사용하여 `prepare_payments` method를 통해 생성한 merchant_id를 amount와 함께 아임포트에 전달한다.
        - 이 때 전달하는 아임포트 api url은 `{아임포트 기본 api url}/payments/prepare` 이다.
        - 그 다음에 payment 객체를 새롭게 생성한다.

        ```python
        class PaymentManager(models.Manager):
            imp = Iamport()

            def create_new(self, user, amount, type):
                ...
                # 아임포트에 전달
                PaymentManager.imp.prepare_payments(merchant_id, amount)
                
                # 새로운 payment 객체 생성
                new_payment = self.model(user=user, merchant_id=merchant_id,
                                        amount=int(amount), type=type)
        ```

    - **(4-5) class PaymentPassView**  
        - PaymentManager의 create_new에서 반환한 merchant_id와 함께 필요한 정보들을 call-payments.js의 passPaymentInfo function에 response를 보낸다.

&nbsp;

### 5) IMP.request_pay()에 결제 정보 전달 

이전 단계에서 보낸 amount, merchant_id, pay_method, buyer_name, buyer_email와 함께 어느 pg사를 호출할 것인지 pg key 값에 value로 입력해야 한다. 이는 아임포트 공식문서를 참고한다. 

pg사로는 KG 이니시스를 선택했는데, 그 이유로는 웹표준으로서 브라우저 제약 없이 결제가 가능하기 때문이다.

```js
if (merchant_id !== '' && merchant_id !== undefined) {
            IMP.request_pay({
                pg: 'html5_inicis', // KG 이니시스
                merchant_uid: merchant_id,
                name: 'Devket Premium 서비스',
                pay_method: 'card',
                amount: amount,
                buyer_name : buyer_name,
                buyer_email : buyer_email
            },
            // 결제창 오픈
            function (response) {
                if (response.success) {
                   ...
                } else {
                   ...
                }
            });
        }
```
&nbsp;

### 6) PG사의 결제창 오픈 및 카드사 선택하여 결제 진행

다음과 같은 KG 이니시스의 결제창이 떠서 결제가 진행된다.

![image](https://user-images.githubusercontent.com/78094972/204946409-f7df613c-15af-43df-a5b1-4c1633f7deb0.png)

&nbsp;

### 7), 8) 결제 성공: storeImpIdInDB 함수 실행 -> redirect 

> **_결제가 성공하여 아임포트에서 받은 imp_uid를, 생성했던 payment 객체에 imp_id로서 결제 상태를 저장하고, User 객체의 결제 상태를 변경하는 함수_**

![image](https://user-images.githubusercontent.com/78094972/211764278-c8cfba87-185a-49db-9604-58da610fe634.png)

- **(7-1) urls.py**
    - fetch를 사용하여 '/api/payment/validation' api url에 data를 보내면 urls.py에서 이 api url에 mapping된 PaymentImpStoreView로 request가 가도록 한다.
- **(7-2, 7-3, 7-4, 7-5) class PaymentImpStoreView**
    - request로부터 imp_id, amount, merchant_id, status를 가져와 이에 해당하는 payment 정보와 User 정보를 조회한다.
    ```python
    class PaymentImpStoreView:
        ...
        user = User.objects.get(id=kwards['user_id'])
        payment = Payment.objects.get(user, merchant_id, amount)
    ```
    - 조회 후, payment 객체에 imp_id, status 정보를 저장한다. user 객체에는 기존에 미결제 상태인 것을 '결제' 상태로 변경한다. 

    ```python
    user.payment_status = User.PAYMENT_ON
    user.save()
    payment_data = {'payment_id': imp_id, 'status': payment_status}
    self.check_validation(payment, data=payment_data)
    ...
    ```

    - 그리고, 변경 결과에 따라서 `works`의 값이 변경되고, 이를 storeImpIdInDB에 전달한다.


- **(7-5 ~ 7-7) 결제 검증하기: payment_validation & get_transaction**
    - post_save에 의해서 Payment 객체가 저장되면 결제가 유효한지 검증하는  `payment_validation` function이 실행된다.

    ```python
    post_save.connect(payment_validation, sender=Payment)
    ```
    - payment_validation 내부에는 PaymentManager로 정의한 `get_transaction` 메서드가 있어서, 이를 통해서 iamport 내에 결제 내역이 있는지 확인하여 아임포트에도 존재하고, db에도 다 존재하는지를 확인합니다.
    - 다 존재해야 결제가 올바르게 된 것입니다.


- **[8] 과정- redirect** : PG 결제 창에서 결제가 완료되면 다른 화면으로 리다이렉트합니다.

&nbsp;

### 9) 결제 실패: makeStatusFailure 함수 실행
![image](https://user-images.githubusercontent.com/78094972/211742647-d5694554-81bc-4483-9351-016ca109380f.png)

> **_사용자가 결제 정보를 다 입력하고, 완료버튼만 누르면 되는 상황에서 '사용자 변심'으로 결제를 취소할 경우 결제 상태를 반영하는 함수_**

- **(9-1) urls.py**
    - fetch를 사용하여 '/api/payment/failure' api url에 data를 보내면 urls.py에서 이 api url에 mapping된 PaymentFailedView로 request가 간다.
- **(9-2 ~ 9-5) class PaymentFailedView**
    - request로부터 imp_id 와 merchant_id를 가져와 아래아 같이 객체 조회 및 수정에 사용한다.
    - Payment 객체 조회 및 수정

    ```python
    imp_id = request.data['imp_id']
    merchant_id = request.data['merchant_id']

    # Payment 객체 조회
    payment = Payment.objects.get(merchant_id= merchant_id)
    
    # Payment 객체 정보 수정
    payment_data = {'payment_id': 'imp_id', 'status': 'failed'}
    serializer = PaymentSerializer(payment, data=payment_data, partial=True)
    ```

    - 처리 결과에 따라서 `"works"`의 값을 True, False로 바꿔서 Response를 보낸다.


&nbsp;

### 10) 결제 실패 안내문

9)의 경우처럼 사용자 변심으로 결제 실패한 경우와 그 외의 실패했을 때 다음과 같이 alert를 사용하여 안내한다.

```js
else {
    if (response.imp_uid) {
        // 결제한 유저의 이름과 이메일 확인 후, 사용자 변심으로 결제 창을 닫아 결제 취소되는 로직
        makeStatusFailure(e, response.merchant_uid, response.imp_uid);
    }
    let msg = '결제에 실패하였습니다. \n';
    msg += '에러내용: ' + response.error_msg;
    alert(msg)
}
```

&nbsp;

## 3.2 결제 프로세스에서 고려한 발생할 수 있는 상황들

결제 기능에서 중요하고 어려운 것은 다음 3가지라고 생각한다.
- 내부 정책에 따라서 무엇보다 여러 상황에 대해서 고려해보고 이에 대해 어떻게 코드를 짰는지
- 배달을 하는 e-commerce처럼 장바구니, 배달, 쿠폰 할인 같은 여러 모델들이 엮어져야 복잡해지는 상황 
- 대규모 트래픽 상황에서 많은 사용자들이 동시에 결제 요청들이 들어올 때 이를 얼마나 빠르고 정확하게 처리하는지

하지만, 현재 프로젝트는 단지 구독 같은 개념이고, e-commerce처럼 복잡하지 않다. 또한, 트래픽이 거의 없는 상황이다. 그래서 내가 최대한 고려할 것은 첫 번째 경우라고 생각한다.

이런 상황에서 최대한 내가 생각해낼 수 있는 결제 상황들에 대해 정리해보려고 한다.

### 고려한 결제 상황

|merchant_id|imp_id|status| |
|----|----|----|----|
|o|x|await|(1)|
|o|x|failed|(2)|
|o|o|failed|(3)|
|o|o|paid|(4)|

- 로그인 전 =>
    - 로그인 전 결제를 시도할려고 한다면 `alert()`를 사용하여 결제 후 시도하라는 안내문이 뜬다.

- 로그인 후 =>
    - (1) PG 결제 창을 띄웠지만 카드사를 선택하지 않고, 사용자 변심으로 취소했을 경우 => 결제 실패
        - _merchant id는 존재하지만, imp id는 존재하지 않고, 결제 상태는 await_
    - (1) 카드 정보 입력 실패 => 결제 실패
        - 카드 정보 불일치, 한도 초과, 잔액 부족 등의 사유로 결제가 중단
            - _merchant id는 존재하지만, imp id는 존재하지 않고, 결제 상태는 await_
    - (2) 주문 번호가 이미 존재할 경우 => 결제 실패  
        - 주문 번호는 시간에 따라 다른 주문 번호를 생성하도록 했기 때문에, 이미 존재하는 주문 번호가 들어왔다는 건 에러가 발생한 상황을 의미
        - _merchant id는 존재하지만, imp id는 존재하지 않고, 결제 상태는 failed_
    - 카드 정보 입력 완료 =>
        - (3) '취소' 버튼 클릭 => 사용자 변심으로 인한 결제 실패 
            - _merchant id, imp id는 존재하지만 결제 상태는 failed_
        - (4) '완료' 버튼 클릭 => 결제 성공 
            - _merchant id, imp id는 다 존재하면서 결제 상태는 paid_


위의 상황들은 1차적인 결제 기능 구현 시 고려한 상황이다. 계속해서 환불과 정기 결제를 추가하려고 한다. 

그렇다면 환불 시에는 사용 기간에 따른 환불 정책을 어떻게 세울 것인지, 정기 결제를 도입한다면 결제 기간 몇 일 전에 알림을 주는 것이 사용자 편의성에 좋을텐데 이를 기술적으로 어떻게 구현할지가 고려사항으로 올라온다.


1차적인 결제 과정에서의 고려사항은 [Project: Payment 개발 과정에서의 고려사항들과 개발 이슈들](https://jeha00.github.io/post/project/django/02_payment_issues/) 문서를 참고하도록 한다.


&nbsp;

---

# Reference 

- [한국 전자결제 서비스 흐름](https://github.com/iamport/iamport-manual/blob/master/%EC%9D%B8%EC%A6%9D%EA%B2%B0%EC%A0%9C/background.md)  
- [Django에서 아임포트를 통해 결제 구현하기 - (1) 아임포트 세팅 및 결제 구조](https://skyseven73.tistory.com/17)  
- [iamport admin](https://admin.iamport.kr/auth/signin)
- [아임포트 api 문서](https://api.iamport.kr/)   