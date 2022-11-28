---
title: "10_iamport_payment_process"
date: 2022-11-26T23:39:35+09:00
draft: true
summary: 
tags: [""]
categories: [""]
---
- checkout.js

```javascript
$(function () {
    var IMP = window.IMP;
    IMP.init('imp62676076');
    $('.order').on('click', function (e) {
        var amount = parseFloat($(".monthly .wrapper input[name='amount']").val().replace(',', ''));
        var type = 'CARD';
        var merchant_id = AjaxStoreTransaction(e, amount, type);
        
        // 결제 정보가 만들어졌으면 iamport로 실제 결제 시도
        if (merchant_id !== '') {
            IMP.request_pay({
                merchant_uid: merchant_id,
                name: 'Devket',
                pay_method: 'card',
                amount: amount,
            }, function (rsp) {
                if (rsp.success) {
                    var msg = '결제가 완료되었습니다.';
                    msg += '고유ID : ' + rsp.imp_uid;
                    msg += '상점 거래ID : ' + rsp.merchant_uid;
                    msg += '결제 금액 : ' + rsp.paid_amount;
                    msg += '카드 승인번호 : ' + rsp.apply_num;
                    // 결제가 완료되었으면 비교해서 디비에 반영
                    ImpTransaction(e, rsp.merchant_uid, rsp.imp_uid, rsp.paid_amount);
                } else {
                    var msg = '결제에 실패하였습니다.';
                    msg += '에러내용 : ' + rsp.error_msg;
                    console.log(msg);
                }
            });
        }
        return false;
    });
});


// 결제 정보 생성 -> 팝업 창 생성
// 결제 정보를 저장: OrderTransaction 객체를 생성하는 뷰를 호출하고, 아임포트에 전달할 유니크 주문 번호를 반환받는다. 
function AjaxStoreTransaction(e, amount, type) {
    console.log("##### AjaxStoreTransaction #####")
    e.preventDefault();
    var merchant_id = '';
    var request = $.ajax({
        method: "POST",
        url: checkout,        
        async: false,
        data: {
            amount: amount,
            type: type,
            csrfmiddlewaretoken: csrf_token,
        }
    });

    request.done(function (data) {
        if (data.works) {
            merchant_id = data.merchant_id;
        }
    });
    console.log('merchant_id: ', merchant_id)
    request.fail(function (jqXHR, textStatus) {
        if (jqXHR.status == 404) {
            alert("페이지가 존재하지 않습니다.");
        } else if (jqXHR.status == 403) {
            alert("로그인 해주세요.");
        } else if (jqXHR.status == 401) {
            alert("인증 Token이 유효하지 않습니다.");
        }
    });

    return merchant_id;
}

// iamport에 결제 정보가 있는지 확인 후 결제 완료 페이지로 이동
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
            csrfmiddlewaretoken: '{{ csrf_token }}',
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

- iamport.py

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



- views.py

```python
# views.py

class PaymentCheckoutAjaxView(View):

    def post(self, request):
        print("##### OrderCheckoutAjaxView #####")

        if not request.user.is_authenticated: 
            return JsonResponse({"authenticated": False}, status=403)
        
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


class PaymentImpAjaxView(View):

    def post(self, request):
        print("##### OrderImpAjaxView #####")
        if not request.user.is_authenticated:
            return JsonResponse({"authenticated": False}, status=403)

        user                    = request.user
        merchant_id             = request.POST.get('merchant_id')
        imp_id                  = request.POST.get('imp_id')
        amount                  = request.POST.get('amount')

        try:
            payment             = Payment.objects.get(
                                    user=user, 
                                    merchant_order_id=merchant_id, 
                                    amount=amount,
                                  )
        except:
            payment             = None 
        
        if payment is not None:
            payment.payment_id  = imp_id 
            payment.success     = True 
            payment.save()

            data = {
                'work': True
            }

            return JsonResponse(data)
        else: 
            return JsonResponse({}, status=401)
```


- models.py

```python
from django.db.models.signals import post_save
from .iamport import Iamport
import hashlib
import time

# models.py
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
            print('try')
            new_transaction.save(using=self._db) 
        
        except Exception as e:
            print(f'save error: {e}')
        
        print('before return')
        
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



https://skyseven73.tistory.com/17