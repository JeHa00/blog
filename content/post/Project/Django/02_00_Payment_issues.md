---
title: "Project: Payment 개발 과정에서의 고려사항들과 개발 이슈들"
date: 2022-12-05T11:08:59+09:00
draft: true
summary: 
tags: ["payment"]
categories: ["Project"]
---


# Payment id 암호화

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




# 결제 정보를 html tag에 노출된 것을 서버 view class로 옮기는 과정에서 발생된 Error

## TypeError: View.__init__() takes 1 positional argument but 2 were given 

MakeStatusFailed class에서 urls.py에 입력할 때 .as_view()를 입력해야하는데 빠트림

출처: https://stackoverflow.com/questions/53572251/django-2-1-3-error-init-takes-1-positional-argument-but-2-were-given 



## ValueError: 거래건이 존재하지 않습니다.

- 아래 views.py 코드에서 payment.save()로부터 에러가 발생했다.

```python
# views.py 

class MakeStatusFailed(APIView):
    """
    사용자 변심으로 결제 취소 시, payment의 status를 failed로 바꾸기
    """

    def post(self, request):
        print('----- MakeStatusFailed -----')
        imp_id                      = request.data['imp_id']
        print('----- imp_id -----', imp_id)
        merchant_id                 = request.data['merchant_id']
        print('----- merchant_id -----', merchant_id)
        payment                     = Payment.objects.get(merchant_id=merchant_id)
        print('----- payment  -----', payment)
        
        if payment is not None: 
            payment.payment_id      = imp_id
            payment.status          = 'failed'
            payment.save()
            data                    = {'works': True}

            return Response(data, status=status.HTTP_200_OK)
        
        else:
            data                    = {'works': False}
            
            return Response(data, {'msg':"Can't find a payment object with merchant_id passed"}, status=status.HTTP_400_BAD_REQUEST)
```


- Error message

```yml
 File "/Users/jehakim/Desktop/Devket/pocket/models.py", line 218, in payment_validation
    iamport_transaction = Payment.objects.get_transaction(merchant_id=instance.merchant_id)
  File "/Users/jehakim/Desktop/Devket/pocket/models.py", line 170, in get_transaction
    result = PaymentManager.imp.find_transaction(merchant_id)
  File "/Users/jehakim/Desktop/Devket/pocket/iamport.py", line 86, in find_transaction
    raise ValueError('거래건이 존재하지 않습니다.')
ValueError: 거래건이 존재하지 않습니다.
```


Error 발생 순서는 다음과 같다.

MakeStatusFailed class -> payment.save() -> Payment model class 실행 -> post_save.connect 실행 -> payment_validation function 실행 -> find_transaction 실행 -> ValueError 발생 

왜 Error가 발생된 것일까? 왜 아임포트에서 해당 merchant_id를 찾지 못한 것일까? 

이는 먼저 prepare_payments 단계에서 아임포트에 정보를 제대로 전달하지 못 했기 때문이다. 

하지만, merchant_id와 amount는 확실하게 생성 및 입력되어 전달했는데 무엇이 문제일까? 

아임포트 API 문서를 보면 이 단계에서 반드시 전해줘야하는 건 merchant_id와 amount 즉, 결제금액이다. 

하지만 더 나아가서 amount의 최고 결제 금액은 100원임을 알 수 있다. 
(출처: https://faq.iamport.kr/7e1f6e5f-5e36-4617-b509-c26602079561)

print 문으로 전달된 amount 값을 확인해보면 '0'으로 확인된다. 

PaymentPassAjaxView class에서 온 request로부터 amount 정보를 받아왔을텐데 왜 값이 다를까?

html tag value로 입력된 100 수치는 클라이언트에게 노출되어 있기 때문에, 보안 상 좋지 않아 서버 단으로 입력되도록 바꾸는 과정에서 amount가 적힌 태그를 삭제했고, 값이 있는 태그를 읽어오는 함수도 js에서 삭제했다.

그런데 왜 '0'이 입력되었을까? 

models.py 의 amount 속성의 default 값이 0이기 때문이다. 

다시 한 번 default 값을 정할 때에도 공식 문서의 안내를 따라 설계를 해야한다는 걸 느꼈다.

그 결과 '0'이 입력되어 결국 거래건이 존재하지 않는다는 결과가 뜬 것이다.

그래서 default = 100으로 수정했고 amount 값을 view class에서 수정할 수 있도록 옮겼다. 

Devket은 유료 무료로만 나눠진 거라 상품과 할인율이 없지만, 구독이란 개념이 들어가게 된다면 할인률에 따라 상품을 나눠서 운영한다면 model 속성으로서 값을 가져와서 입력되도록 설계한다.


