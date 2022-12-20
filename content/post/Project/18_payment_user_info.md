---
title: "18_payment_user_info"
date: 2022-12-14T22:26:51+09:00
draft: true
summary: 
tags: [""]
categories: [""]
---
user                    = User.objects.get(id=kwards['user_id'])
buyer_email     = Email.objects.get(user=user).email



결제창이 뜨고 30분 동안 결제가 지속되지 않으면 자동적으로 취소된다


>>> user = User.objects.get(id=1)
>>> user
<User: 1>
>>> Payment.objects.filter(user=user)
<QuerySet [<Payment: 37374c6584 결제대기>, <Payment: 6de52e9430 failed>, <Payment: 93ef0ad86e 결제대기>, <Payment: b63c73553f 결제대기>, <Payment: 5a433b6a6f 결제대기>, <Payment: 6f76b60d35 결제대기>, <Payment: 3b65952dc6 결제대기>, <Payment: ff8214c669 결제대기>, <Payment: b181b03d23 결제대기>, <Payment: 87bdfa2dba paid>, <Payment: 80844bdfa0 paid>, <Payment: 7d8b569f0e paid>, <Payment: f0c36b009d 결제대기>, <Payment: 0d1605ae9d 결제대기>, <Payment: d14115bca0 결제대기>, <Payment: 0bda5801d1 결제대기>, <Payment: a964fc7b32 paid>, <Payment: 215d52b6e5 결제대기>, <Payment: 0ad691a4a3 결제대기>, <Payment: ac7d35ac86 paid>, '...(remaining elements truncated)...']>


class Payment():
    ...

    def __str__(self):
        return f"{self.merchant_id} {self.get_status_display()}"

    ...


```python
class PaymentSerializer(ModelSerializer):

    PAYMENT_TYPE_CHOICES    = [('card', '신용카드')]
    STATUS_CHOICES          = [
                                ('await', '결제대기'),
                                ('paid', '결제성공'),
                                ('failed', '결제실패'),
                                ('cancelled', '결제취소')
                              ]

    user                    = serializers.CharField(max_length=10, allow_blank=False, trim_whitespace=True)
    amount                  = serializers.IntegerField(max_value=50000, min_value=100)
    payment_id              = serializers.CharField(max_length=20, allow_blank=False, trim_whitespace=True)
    merchant_id             = serializers.CharField(max_length=10, allow_blank=False, trim_whitespace=True)
    payment_data            = serializers.DateTimeField()
    status                  = serializers.ChoiceField(choices=STATUS_CHOICES) 
    type                    = serializers.ChoiceField(choices=PAYMENT_TYPE_CHOICES)


    class Meta: 
        model = Payment
        fields = ['user', 'amount', 'payment_id', 'merchant_id', 'payment_data', 'status', 'type']
```


https://itecnotes.com/server/increasing-size-of-tmp-on-ec2-instances/

Custom user 로 AbstractUser로 바꾸면 last_login으로 충돌된다. 

그래서 기존 User를 잡을 때, 무엇으로 잡을지 정하면 고정하기



# model choices

```python
class Payment(models.Model):
    """ 결제 정보 모델 """

    user                        = models.ForeignKey(User, on_delete=models.CASCADE, verbose_name='유저')
    payment_id                  = models.CharField(verbose_name='결제번호', max_length=120, null=True, blank=True, unique=True)
    merchant_id                 = models.CharField(verbose_name='주문번호' , max_length=120, unique=True)
    amount                      = models.PositiveIntegerField(verbose_name='결제 금액', default=100)
    payment_date                = models.DateTimeField(verbose_name='결제갱신일', auto_now_add=True)
    
    PAYMENT_TYPE_CHOICES        = [{'card', '신용카드'}]
    type                        = models.CharField(verbose_name='결제 수단', max_length=10, choices=PAYMENT_TYPE_CHOICES, default='card')
    
    STATUS_CHOICES              =[
                                  {'await', '결제대기'},
                                  {'paid', '결제성공'},
                                  {'failed', '결제실패'},
                                  {'cancelled', '결제취소'}
                                 ]
    status                      = models.CharField(verbose_name='결제상태', default='await', choices=STATUS_CHOICES, max_length=10)

    objects                     = PaymentManager()

    def __str__(self):
        return f"{self.merchant_id} {self.get_status_display()}"
```


payment.get_status_display() 

현재 STATUS_CHOICES는 set으로 구성된 리스트로 되어있다.

이에 따라서 admin에 들어가 확인해도 human-readable 값으로 나타나지 않았다. 

그래서 쿼리셋으로 불러와서 나타는 것들을 보면 일정하지 않았다. 어떤 거는 '결제 대기' 어떤 거는 'paid'로 떴다.

이에 대해 django 공식문서를 알아보니 [Model field reference - choices](https://docs.djangoproject.com/ko/4.0/ref/models/fields/#choices) 

sequence data type을 사용하라고 했다.

다 같이 보면서 이 부분을 작성했는데, 왜 이를 못 알아봤을까? 

tuple로 바꾸니 원하는 대로 출력이 되었다.