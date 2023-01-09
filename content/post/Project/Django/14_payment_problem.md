---
title: "14_fetch_problem"
date: 2022-11-30T16:39:25+09:00
draft: true
summary: 
tags: [""]
categories: [""]
---
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


