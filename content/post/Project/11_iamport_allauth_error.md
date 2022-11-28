---
title: "11_iamport_allauth_error"
date: 2022-11-26T23:38:57+09:00
draft: true
summary: 
tags: [""]
categories: [""]
---

# iamport.py

```python
import requests

class Iamport():

    response_success            = 0
    iamport_api_url             = 'https://api.iamport.kr'

    def __init__(self, imp_key, imp_secret): 
        
        self.imp_key            = imp_key
        self.imp_secret         = imp_secret


    def get_access_token(self): 
        """
        imp_key(API key) & imp_secret(API secret) 으로 access_token을 발급받기
        """
        print('----- get_access_token -----')

        access_data             = {
                                    'imp_key': self.imp_key, 
                                    'imp_secret': self.imp_secret
                                  }
        url                     = f'{Iamport.iamport_api_url}/users/getToken'
        response                = requests.post(url, data=access_data)
        res_json                = response.json()

        print('get_access_token response: ', res_json)
        
        if res_json['code'] is Iamport.response_success: 
            access_token        = res_json['response']['access_token']
            return access_token
        
        elif response.status_code == 401:
            raise ValueError('imp_key, imp_secret 인증에 실패했습니다.')


    # def get_headers(self):
    #     print('----- get_headers -----')
    #     return {'Authorization': self.get_access_token()}


    def prepare_payments(self, merchant_id, amount):
        """
        인증방식의 결제 진행 과정에서 결제금액 위변 시, 결제 진행 자체를 막기 위해 결제 예정금액을 사전등록하는 과정
        """
        
        access_token            = self.get_access_token()
        print(f"before if: {access_token}")
        url                     = f'{Iamport.iamport_api_url}/payments/prepare'

        if access_token:
            access_data         = {
                                    'merchant_uid': merchant_id, 
                                    'amount': int(amount)
                                  }
            print(f"after if: {access_token}")
            print('----- prepare_payments -----')
            print('access_data: ', access_data)
            
            headers             = {'Authorization': access_token}
            response            = requests.post(url, data=access_data, headers=headers)
            res_json            = response.json()
            print('response: ', response)
            print('res: ', res_json)
            print(res_json['code'])
            print(res_json['code'] == self.response_success)

            if res_json['code'] == self.response_success: 
                print(response)
                print(res_json)
                pass 
            # elif res_json['code'] != Iamport.response_success: 
            #     raise ValueError('인증 토큰이 유효하지 않습니다.')
            
            # elif res_json['response'] is None: 
            #     raise ValueError('결제정보 사전 등록에 실패하였습니다.(이미 등록된 merchant_uid입니다.)')
        
            # elif response.status_code == 401:
            #     print('인증 Token이 유효하지 않습니다.')
            #     raise ValueError('인증 Token이 유효하지 않습니다.')
                
        else: # status_code == 401
            raise ValueError('인증 토큰이 존재하지 않습니다.')


    def find_transaction(self, order_id):
        """
        가맹점지정 고유번호로 결제내역을 확인
        """
        print('----- find_transaction -----')

        access_token            = self.get_access_token()

        if access_token:
            url                 = f"{Iamport.iamport_api_url}/payments/find/{order_id}"
            headers             = {'Authorization': access_token}
            response            = requests.post(url, headers=headers)
            res_json            = response.json()

            if res_json['code'] == Iamport.response_success:
                context         = {
                                    'imp_id': res_json['response']['imp_uid'],
                                    'merchant_order_id': res_json['response']['merchant_uid'],
                                    'amount': res_json['response']['amount'],
                                    'status': res_json['response']['status'],
                                    'type': res_json['response']['pay_method'],
                                    'receipt_url': res_json['response']['receipt_url']
                                  }
                return context
            
            elif response.status_code == 404:
                raise ValueError('거래건이 존재하지 않습니다.') 
                
        else: # status code == 401
            raise ValueError('인증 토큰이 존재하지 않습니다.')

```



# views.py

```python
# views.py

class PaymentCheckoutAjaxView(View):

    def post(self, request):
        print("##### OrderCheckoutAjaxView #####")

        if not request.user.is_authenticated: 
            return JsonResponse({"authenticated": False}, status=401)
        
        user                = request.user 
        amount              = request.POST.get('amount')
        payment_type        = request.POST.get('type')

        try: 
            merchant_id     = Payment.objects.create_new(
                                    user=user, 
                                    amount=amount,
                                    type=payment_type,
                                )
            print('after making merchant_id')
            
        except:
            merchant_id     = None 

        if merchant_id is not None: 
            data            = {
                                "works": True, 
                                "merchant_id": merchant_id
                              }
            return JsonResponse(data)

        else: 
            return JsonResponse({})

```


# models.py

```python
from django.db.models.signals import post_save
from .iamport import Iamport
import hashlib
import time

# models.py
class PaymentManager(models.Manager):

    imp = Iamport(IMP_KEY, IMP_SECRET)

    def create_new(self, user, amount, type, success=None, transaction_status=1):
        if not user: 
            raise ValueError("Can't find user")
        
        # merchant_id 암호화하기
        user_hash = hashlib.sha1(str(user.id).encode('utf-8')).hexdigest()[:5]
        time_hash = hashlib.sha1(str(int(time.time())).encode('utf-8')).hexdigest()[-5:]
        merchant_id = hashlib.sha1((user_hash + time_hash).encode('utf-8')).hexdigest()[:10]

        # 아임 포트에 통보
        PaymentManager.imp.prepare_payments(merchant_id, amount)

        print('after prepare_payments')
        
        # 매니저 말고 그대로 사용하기
        new_transaction = self.model(
            user=user, 
            merchant_id=merchant_id,
            amount=int(amount),
            type=type
        )

        if success is not None: 
            new_transaction.success = success
            new_transaction.status = transaction_status

        try:
            new_transaction.save(using=self._db) 
        
        except Exception as e:
            print(f'save error: {e}')
        
        return new_transaction.merchant_id

    
    def get_transaction(self, merchant_order_id):
        result = PaymentManager.imp.find_transaction(merchant_order_id)

        if result['status'] == 'paid':
            return result
        else:
            return None
    
    def all_for_user(self, user):

        return super(PaymentManager, self).filter(user=user)

class Payment(models.Model):
    """ 결제 정보 모델 """

    """ 노경민 : 향후 payment_method의 choice에 결제방식 추가 수정 작업이 필요함  """

    user = models.ForeignKey(User, on_delete=models.CASCADE, verbose_name='유저')
    merchant_id = models.CharField(max_length=120, null=True, blank=True)
    payment_id = models.CharField(max_length=120, unique=True, null=True)
    amount = models.PositiveIntegerField(verbose_name='결제 금액', default=0)
    payment_date = models.DateTimeField(verbose_name='결제갱신일', auto_now_add=True)


    CARD = 1
    PAYMENT_TYPE_CHOICES = [
        {CARD, '신용카드'}
    ]

    # payment_method choices
    type = models.IntegerField(choices=PAYMENT_TYPE_CHOICES, verbose_name='결제방식', default=1)
    
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
    success = models.BooleanField(default=False)

    objects = PaymentManager()

    def __str__(self):
        return f"{self.payment_id} {self.get_status_display()}"

    class Meta:
        verbose_name = '결제'
        verbose_name_plural = '결제 목록'



def payment_validation(sender, instance, created, *args, **kwargs):

    if instance.transaction_id:
        import_transaction = Payment.objects.get_transaction(merchant_id=instance.merchant_id)

        merchant_id = import_transaction['merchant_id']
        imp_id = import_transaction['imp_id']
        amount = import_transaction['amount']

        local_transaction = Payment.objects.filter(merchant_id=merchant_id,
                                                            transaction_id=imp_id,
                                                            amount=amount
                                                            ).exists()
        # 서버 쪽에 없으면 결제가 안된 것
        # 결제는 됬지만 그 금액으로 된 게 없는 것 
        if not import_transaction or not local_transaction:
            raise ValueError("비정상 거래입니다.")


post_save.connect(payment_validation, sender=Payment)
```

# 발생된 Error

js에서 보낸 request를 views.py의 PaymentCheckoutAjaxView class에서 받아 처리하는 과정에서 아래와 같은 error가 발생했다. 

```yml
Unauthorized: /premium/payment/checkout/
POST /premium/payment/checkout/ HTTP/1.1 401
```

로그인 문제라 판단하여 아직 로그인 기능이 개발 완료되지 않았기 때문에, 간단히 구현할 수 있는 방법을 찾다가 django allauth 를 택해서 사용하기로 했다. 

왜냐하면 위 view class의 조건문에서 걸렸다고 생각했기 때문이다. 그래서 allauth를 사용하여 네이버 api key, secret을 가져와 로그인 기능을 추가했다. 

하지만 그럼에도 동일한 error가 발생했다. 

print 문을 중간 중간 입력하여 error가 나는 지점을 구체적으로 잡아가기 시작했다. 

그 결과, models.py 에서 PaymentManager class에서 다음 코드 부분에 에러가 발생된 걸 확인했다.

```python
class PaymentManager(models.Manager):

    imp = Iamport(IMP_KEY, IMP_SECRET)

    def create_new(self, user, amount, type, success=None, transaction_status=1):

        ...

        new_transaction = self.model(
            user=user, 
            merchant_id=merchant_id,
            amount=int(amount),
            type=type
        )
        
        ...
```

하지만, 내부적으로 어떻게 에러가 발생된지 확인할 수 없어서 나중에 알게된 VSC debug tool을 사용하여 breakpoint를 사용하여 진행했다.

위 코드 부분에서 에러가 발생하여 다음 코드 부분으로 넘어와 에러가 발생된 것이다.
try - except 문에 있기 때문에, error가 발생되자, except 문으로 넘어왔고, 그 밑에 else 문의 JsonResponse를 거치면서 401 Error가 발생된 것이다.

즉, 401 Error 건이 아니지만 내가 에러처리 http status code를 잘못 입력하여 401로 뜬 것이다. 

```python

class PaymentCheckoutAjaxView(View):

    def post(self, request):
      ...

        try: 
            merchant_id     = Payment.objects.create_new(
                                    user=user, 
                                    amount=amount,
                                    type=payment_type,
                                )


        except:
            merchant_id     = None 

        if merchant_id is not None: 
            data            = {
                                "works": True, 
                                "merchant_id": merchant_id
                              }
            return JsonResponse(data)

        else: 
            return JsonResponse({}, status=401)
```

status=401 을 지우자 다음 에러가 다시 뜨지 않았지만, merchant_id 는 계속해서 None이 할당되기 때문에, 근본적인 원인은 해결되지 않았다.

```yml
Unauthorized: /premium/payment/checkout/
POST /premium/payment/checkout/ HTTP/1.1 401
```

위 문제를 해결하자 다른 특이한 error 안내문이 뜨지 않고, except 로 넘어와 원인을 알 수 없었다. 

그래서 try ~ except 문을 다음과 같이 수정해봤다. error가 반드시 뜬다면 try ~ except 문이 떠도 뜰 것이기 때문이다. 

```python
merchant_id     = Payment.objects.create_new(
                        user=user, 
                        amount=amount,
                        type=payment_type,
                    )
```

그러자 다음과 같은 에러가 발생했다.

```yml
ValueError: Cannot assign "<SimpleLazyObject: <User: rudtls0611>>": "Payment.user" must be a "User" instance.
```

즉, Payment class model의 user가 ForeignKey로 연결한 user가 아니라는 것이다. 

DB를 확인해보니 allauth를 사용하여 다른 user table이 생성되어 다른 곳에서 만들어지고 있었던 것이다.  

https://velog.io/@jinhyungrhee/%EB%AA%A8%EA%B0%81%EC%BD%94210825-Django-allauth%EC%99%80-contrib.auth%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%98%EC%97%AC-User-model-%EA%B5%AC%ED%98%84%ED%95%98%EA%B8%B0

평소와 달리 반대로 검색해보자. 

특정 키워드를 알지 못하기 때문에 전체 구글링으로 키워드를 알아낸 후, 이 키워드를 사용하여 django allauth 공식문서를 들어가서 찾아본다. 

특정 키워드는 `AUTH_USER_MODEL`이고, django allauth 공식 문서에서 찾지 못했기 때문에, 위 블로그 안내에 따라 진행하여 해결했다.  

위 키워드 설정을 settings.py에 추가하기 위해서는 class User 생성 시, `models.Model`을 상속 받는게 아니라 `AbstractUser`를 상속받아 생성한다.

결제 모듈 진행을 위해서 임시로 만드는 것이기 때문에 다음과 같이 작성했다.

```python
class User(AbstractUser):
    pass
```

마이그레이션 시, 위 키워드에 할당한 `User(AbstractUser)`를 참고하여 테이블을 만들어주고, allauth가 이 custom user를 사용한다.  

기존에 만든 User model은 주석처리를 하여 진행한 결과 위 에러 문제를 해결했다! 


# payment_validation 에서 발생된 문제

`save error: 'Payment' object has no attribute 'transaction_id'`

post_save에서 의해서 Payment 실행 후, payment_validation 함수가 실행된다. 

그런데, 이 payment_validation에는 `transaction_id`가 존재하지 않는다. 

```python
def payment_validation(sender, instance, created, *args, **kwargs):

    if instance.transaction_id:
        import_transaction = Payment.objects.get_transaction(merchant_id=instance.merchant_id)

        merchant_id = import_transaction['merchant_id']
        imp_id = import_transaction['imp_id']
        amount = import_transaction['amount']

        local_transaction = Payment.objects.filter(merchant_id=merchant_id,
                                                            transaction_id=imp_id,
                                                            amount=amount
                                                            ).exists()
        # 서버 쪽에 없으면 결제가 안된 것
        # 결제는 됬지만 그 금액으로 된 게 없는 것 
        if not import_transaction or not local_transaction:
            raise ValueError("비정상 거래입니다.")


post_save.connect(payment_validation, sender=Payment)
```

내가 생성한 모델에는 transaction_id가 아닌 payment_id가 존재한다. 이를 수정하자 해결되었다.


https://github.com/iamport/iamport-react-native/issues/21 



DB 싱크가 안맞을 때, https://lemonlemon.tistory.com/182 이 문서를 참고하여 진행


# 또 다시 발생된 Error

PG사를 지정하지 않았다는 에러 안내문이 발생했다.  

처음 아이포트를 진행할 때 https://classic-admin.iamport.kr/settings 이 사이트로 진행했으나, 기존 PG 사 설정을 할 수 없어서, 안해도 되는 걸로 판단했다. 

하지만, 다시 아임포트 docs https://docs.iamport.kr/implementation/payment 를 읽어보면서 정할 수 있는 걸로 판단했다. 

구글링을 하다가 https://github.com/iamport/iamport-react-native/issues/21 이 사이트에 들어오니, 가맹점 코드를 입력해야한다고 한다. 

그리고, '구 메뉴얼' 과 '신 메뉴얼' 간에 차이가 존재한다는 걸 인지했고, 기존 pg 사 설정이 옛날 admin 사이트로만 다들 설명되어 있어서, 새로운 admin 사이트인 https://admin.iamport.kr/integration 에서 결제 연동 탭에서 웹 표준 모델인 KG이니시스를 선택하여 진행했다. 

그리고 https://docs.iamport.kr/sdk/javascript-sdk?lang=ko#request_pay 을 참고하여 아임포트에서 제공하는 js 파일에 `pg: 'html5_inicis'` 를 입력했다. 이 페이지를 보면 무슨 값들이 입력되어야 하고, 각 값들의 기본 타입은 무엇인지 알 수 있다. 

처음에 type을 choice 하기 위해서 사용된 field가 IntegerField였는데, 그래서 js에 'card'로 입력하여 필드 Error가 발생하여 우리 측에는 Integer로 발생했으니 1로 입력했으나, 이는 아임포트 측에서 받아들일 수 없던 값이었다. 그래서 CharField로 바꾼 후, choices 속성 값인 `PAYMENT_TYPE_CHOICES` 를 수정했다. 

결제 모듈을 가져와 사용할 때는 입력된 기본값의 타입들이 무엇인지 확인하고 나서 모델 설계를 들어가야한다는 걸 알게 되었다.


# 뜬 모달 결제창, 하지만 결제 완료 단계에서 발생된 문제

```js
function ImpTransaction(e, merchant_id, imp_id, amount) {
    console.log('##### ImpTransaction #####')
    e.preventDefault();
    var request = $.ajax({
        method: "POST",
        url: validation,
        async: false,
        data: {
            merchant_id: merchant_id,
            imp_id: imp_id,
            amount: amount,
            csrfmiddlewaretoken: csrf_token,
        }
    });
    console.log(`request: ${request}`)
    request.done(function (data) {
        if (data.works) {
            $(location).attr('href', location.origin+redirect)
        }
    });
    request.fail(function (jqXHR, textStatus) {
        if (jqXHR.status == 404) {
            alert("페이지가 존재하지 않습니다.");
        } else if (jqXHR.status == 403) {
            alert("로그인 해주세요.");
        } else {
            alert("문제가 발생했습니다. 다시 시도해주세요.");
        }
    });
}
```

위 함수에서 request.fail로 들어가서 403이 발생된 문제다.  
분명 로그인을 했는데, 왜 이 에러가 뜬 것일까? 저 코드가 403으로 입력해서 발생했을 수도 있다. 
브라우저 디버깅 기능을 사용했을 때, `request`는 다음과 같은 값을 가졌다.



PaymentImpAjaxView class 의 다음 부분에서 Error가 발생하여 500 Error가 발생했다.

```python
payment             = Payment.objects.get(
                                user=user, 
                                merchant_order_id=merchant_id, 
                                amount=amount,
                                )
```

get에 들어가는 매개변수 명칭이 model이 가지고 있는 속성이 아니어서 발생된 문제였다. 
merchant_order_id 는 Payment에서 존재하지 않는다. 
**속성 값을 입력할 때도 정확하게 model이 가지고 있는 속성 값을 입력해야 한다.**


🔆 위 코드를 작성하기 위해 참고했던 코드의 모델을 보니 User model을 따로 만들지 않았으며 foreignKey로 User랑 연결하지 않고, allauth만으로 했기 때문에,
위 에러가 발생되지 않았다. 


