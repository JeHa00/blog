---
title: "14_fetch_problem"
date: 2022-11-30T16:39:25+09:00
draft: true
summary: 
tags: [""]
categories: [""]
---
# vanilla js와 drf로 구축하기로 정했기 때문에, ajax 부분을 js로 바꾸는 작업 중 발생된 Error

## status code 500 error 발생


https://www.codeit.kr/community/threads/38086



```js
import {getElement ,setFetchData} from './common.js';

window.onload = function () {
    const IMP = window.IMP;
    IMP.init('imp62676076');
    const paymentButton = getElement('.order')
    paymentButton.addEventListener('click', (e) => {
        const amount = parseFloat(getElement(".monthly .wrapper input[name='amount']").value.replace(',', ''));
        const type = 'card';
        const merchant_id = passMerchantId(e, amount, type);
        console.log(merchant_id)
        if (merchant_id !== '' && merchant_id !== undefined) {
            IMP.request_pay({
                pg: 'html5_inicis',
                merchant_uid: merchant_id,
                name: 'Devket Premium 서비스',
                pay_method: 'card',
                amount: amount,
            },
            // 결제창 오픈
            function (response) {
                // 결제한 유저의 이름과 이메일 확인하고 결제 버튼 누른 후, 진행되는 로직 
                if (response.success) {
                    let msg = '결제가 완료되었습니다.';
                    msg += '고유ID : ' + response.imp_uid;
                    msg += '상점 거래ID : ' + response.merchant_uid;
                    msg += '결제 금액 : ' + response.paid_amount;
                    storeImpIdInDB(e, response.merchant_uid, response.imp_uid, response.paid_amount, response.status);
                } else {
                    makeStatusFailure(e, response.merchant_uid);
                    let msg = '결제에 실패하였습니다. \n';
                    msg += '에러내용: ' + response.error_msg;
                    alert(msg)
                }
            });
        }
        return false;
    })
};

// IMP.request_pay를 호출하기 위한 merchant_id 반환
function passMerchantId(e, amount, type) {
    e.preventDefault(); 
    let merchant_id = ''
    const data = setFetchData('POST', {
        amount: amount, 
        type: type, 
    })
    const result = fetch(`/api/payment/checkout`, data)
    .then(response => response.json())
    .then(result => {
        console.log(result.merchant_id) //58b9dea713
        merchant_id = result.merchant_id
        return merchant_id
    }).catch(err => alert(`${err}`))
    console.log(result) // ''
    return result
}
```

https://velog.io/@along/fetch%EC%9D%98-%EA%B8%B0%EB%B3%B8%EC%A0%81%EC%9D%B8-%EC%82%AC%EC%9A%A9%EB%B2%95%EA%B3%BC-scope-%EA%B7%B9%EB%B3%B5

https://chanhuiseok.github.io/posts/js-6/




63번째 줄에서 merchant_id 는 '58b9dea713' 로 출력한다. 
하지만 67번째 줄에서 merchant_id는 ''로 출력된다.

값이 이렇게 다르게 출력되는 이유는 fetch는 비동기로서 작동하기 떄문이다. 
순차적으로 실행되는 게 아니라, fetch는 별도로 실행되어 fetch 밖에 정의한 merchant_id에 할당되지 않는다. 

이런 경우 async와 await을 사용한다. 

```js
// IMP.request_pay를 호출하기 위한 merchant_id 반환
async function passMerchantId_01(e, amount, type) {
    e.preventDefault(); 
    let merchant_id = ''
    const data = setFetchData('POST', {
        amount: amount, 
        type: type, 
    })
    const response = await fetch(`/api/payment/checkout`, data)
    const result   = await response.json() 
    if (result.works) {
        merchant_id = result.merchant_id
        console.log('merchant_id after then:', merchant_id)
        return merchant_id 
    } else if (result.status === 401) {
        alert('로그인 해주세요.')
    } else {
        alert('문제가 발생했습니다. 다시 시도해주세요.')
    }
}
;

async function passMerchantId_02(e, amount, type) {
    e.preventDefault(); 
    let merchant_id = ''
    const data = setFetchData('POST', {
        amount: amount, 
        type: type, 
    })
    await fetch(`/api/payment/checkout`, data)
    .then(response => {
        const result = response.json();
        if (response.status === 200) {
            console.log('result.mechant_id: ', result.merchant_id)
            merchant_id = result.merchant_id
        } else if (response.status === 401) {
            alert('로그인 해주세요.')
        } else {
            alert('문제가 발생했습니다. 다시 시도해주세요.')
        }
    })
    return merchant_id
}
```

passMerchantId_01 함수가 해결된 것이다.
02에서 01로 개선하여 해결  
then과 await은 같이 사용하는 게 아니라고 하여, 따로 만듬 


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


