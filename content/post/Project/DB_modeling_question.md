# Email model 관련 질문

- authentication_check 는 인증 여부에 대한 속성값인데, pocket에서 인증하는게 있는가?

- email의 EmailField에는 max_length 값이 이미 내장되어 있는데, 굳이 max_length를 입력할 필요가 있을까?


# User model 관련 질문

upload_to=f"profile/"에서 f-string을 쓴 이유

# Site model 관련 질문

- thumbnail_url에서 max_length 2000을 한 이유
- url = models.CharField(verbose_name='url', max_length=2000, null=True) 에서 max_length 부분
- host_name 에서 max_length를 500으로 한 부분

(완료)
- favortie 속성이 TextField로 되어 있는데 BooleanField로 수정하기 (완료)

# max_length
a max_length argument which specifies the size of the VARCHAR database field used to store the data.
https://docs.djangoproject.com/en/4.1/topics/db/models/#field-options 

VARCHAR 는 mysql에서 bytes 였다가 글자수로 바뀌었다. 버전 4에서는 bytes였지만, 버전 5부터는 글자수로 바뀌었다. 

# Tags model 관련 질문

- list 속성의 verbose_name을 '목록'으로 바꾸기

# Hightlight model 관련 질문

- list 속성의 verbose_name을 '목록'으로 바꾸기
- content_text, content_location 에서 null=True를 한 이유

# PaymentManager 
- merchant_id 부분의 5를 10으로 수정하기 -> 블로그에 정리하기
- 5를 한 이유는 기존 iamport에서 20이 최대였는데, 주문 번호가 그리 많지 않기 때문에 반으로 줄이도록 결정. 그리고, 아이디와 주문 시간 두 가지를 섞어서 만들기 때문에 10의 반반인 5를 입력한 것이다.

아임포트 API 문서에서는 payments.validation 부분을 보면 merchant_id의 string(40) 을 알 수 있다. 

# Payment
- payment_id와 merchant_id의 max_length를 120으로 한 이유
    - 책을 보고 그대로 따라하다보니 그렇게 된 것 같다.
    - sha1로 생성했을 때, 자리수가 몇 개인지 확인하자. 이는 string(20)과 연결되는 부분이므로 수정해야 한다.
    - PaymentSerializer의 payment_id, merchant_id의 max_length와 안맞는다.

- 결제 상태를 awaiting 을 'ready'로 바꾸자.
    - https://api.iamport.kr/#!/payments/getPaymentsByStatus


# post_save 더 자세히 알아보기