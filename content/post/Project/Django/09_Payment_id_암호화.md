---
title: "09_Payment_id_암호화"
date: 2022-11-25T11:08:59+09:00
draft: true
summary: 
tags: [""]
categories: [""]
---
아임포트 결제 시, 겹쳐지지 않는 주문 번호가 필요하여 암호화 과정을 도입한다.

https://docs.python.org/ko/3/library/hashlib.html

hexdigest 
hash.hexdigest()
digest()와 유사하지만, 요약은 16진수 숫자만 포함하는 두 배 길이의 문자열 객체로 반환됩니다. 전자 메일이나 기타 바이너리가 아닌 환경에서 값을 안전하게 교환하는 데 사용할 수 있습니다.


이외에 https://ddolcat.tistory.com/713 에 참조하면 써드 파트를 설치하는 방식이 있지만, 라이브러리를 추가로 설치하고 싶지 않다. 

기존에 있던 모듈을 사용하고 싶어서 이 방식은 택하지 않는다. 

sha256 과 sha1 중 후자를 택한 건 현재 사용자와 데이터 규모가 작기  때문에, 짧은 것을 선택했다.

.encode('utf-8')을 입력하지 않으면 sha1로 변환된 값을 확인할 수 없다. 

user_hash = hashlib.sha1(str(user.id).encode('utf-8')).hexdigest()[:2]
print(user_hash)

user_hash = hashlib.sha1(str(user.id).encode('utf-8')).hexdigest()
print(user_hash)

time_hash = hashlib.sha1(str(int(time.time())).encode('utf-8')).hexdigest()[-3:]
print(time_hash)

time_hash = hashlib.sha1(str(int(time.time())).encode('utf-8')).hexdigest()
print(time_hash)

자리 수를 몇 으로 해야할까?


# models.Manager

https://docs.djangoproject.com/en/4.0/topics/db/managers/


models.Manager란 특정 모델이 사용하는 쿼리 명령어를 만들 때 사용하는 모델이다. 

이 모델의 모델명은 <이 모델 매니저를 사용할 모델명>Manager 로 작명한다. (예를 들어서 AsManager라고 하자.)

그리고 이 Manager를 사용할 모델의 클래스 변수로 objects 에 할당하거나, 다른 이름으로 할당한다. 

objects 가 아닌 objects_manager 라고 한다면 AsManager.objects 를 입력하면 django에서 제공하는 기본 쿼리 명령어(메서드)를 사용한다. 

하지만, AsManager.objects_manager 라고 한다면 해당 Manager model에서 만든 쿼리 메서드를 사용할 수 있다. 


# 막힌 부분

`PaymentCheckoutAjaxView`는 아임포트 js 파일이 실행될 시, js function인 `AjaxStoreTransaction`가 호출하는 뷰 클래스다. 
`AjaxStoreTransaction`가 보내는 request를 받아서 주문 고유 번호인 `merchant_id`를 반환하는 클래스다.  

즉, `AjaxStoreTransaction`는 `PaymentCheckoutAjaxView`가 보낸 response에 담겨진 `merchant_id` 주문 고유번호를 아임포트에 전달하는 역할을 한다. 

그래서 이 고유 번호와 다른 값들과 함께 정산 확인 및 환불을 진행할 수 있다.  

`AjaxStoreTransaction`와 `merchant_id` 라는 명칭은 아임포트 안내사항에 따라 작명했다.  

```python
# views.py
class PaymentCheckoutAjaxView(View):
    ...
    merchant_id = Payment.objects.create_new(
                    user=user, 
                    amount=amount,
                    type=payment_type,
                )
```

`create_new`는 `PaymentManager` 에서 만든 메서드다. 

이 메서드에서 진행이 안되는 걸 print문을 통해 확인했다.

나는 중간 중간 print 문을 찍어서 어디서 막혔는지를 확인하는 방식으로 디버깅을 한다. 

`create_new` 는 다음과 같이 작성되어 있다.

```python
class PaymentManager(models.Manager):

    imp = Iamport(IMP_KEY, IMP_SECRET)

    def create_new(self, user, amount, type, success=None, transaction_status=1):
        print('##### enter create_new #####')
        if not user: 
            raise ValueError("Can't find user")
        
        # merchant_id 암호화하기
        user_hash = hashlib.sha1(str(user.id).encode('utf-8')).hexdigest()[:5]
        time_hash = hashlib.sha1(str(int(time.time())).encode('utf-8')).hexdigest()[-5:]
        merchant_id = hashlib.sha1((user_hash + time_hash).encode('utf-8')).hexdigest()[:10]

        # 아임 포트에 통보
        PaymentManager.imp.prepare_payments(merchant_id, amount)

        print('after prepare_payments')

        new_transaction = self.model(
            user=user, 
            merchant_id=merchant_id,
            amount=amount,
            type=type
        )

        print(new_transaction)
        ...
```

iamport api와 통신하기 위해서 Iamport class를 만들었다. 

이 클래스의 `prepare_payments` 메서드에서 오류가 발생했다.  

