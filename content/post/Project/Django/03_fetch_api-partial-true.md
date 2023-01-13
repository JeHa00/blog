---
title: "Project: fetch를 사용하여 즐겨찾기 값 PUT으로 업데이트 과정에서 문제점과 해결과정"
date: 2022-11-17T16:23:16+09:00
draft: true
summary: Update serializer를 작성하면서 알게된 유효성 검증 흐름(자세히 적기)
tags: [""]
categories: ["Project"]
---
# 0. Introduction

- 이 repository는 현재 [러닝스푼즈 나노디그리 - django backend 부트캠프](https://learningspoons.com/course/detail/django-backend/)에서 팀 프로젝트를 진행하면서 다음과 같은 내용들에 대해 정리하고자 만들었습니다. 
    - 팀 정책을 이것으로 정한 이유 
    - 개발하면서 부딪힌 문제들에 대한 원인, 해결방안, 해결과정, 그리고 그 이유들

- 이번 포스팅에서는 저장한 사이트의 즐겨찾기 값을 UPDATE 하면서 front와 backend 양측에서 겪은 문제를 정리했습니다.

&nbsp;

---
# 1. 발생된 issue와 원인 코드

### active 문자열 추가 시 문제점
또 한 가지 문제 active를 문자열 연산으로 추가한 후, 파이썬 처럼 동일하게 제거될 거라 생각했다. 
하지만 그 결과 지정된 태그의 class가 NaN으로 바꼈다. 
NaN 은 Not-A-Number(숫자가 아님) 를 의미한다. 

자바스크립트에서 + 는 문자열 연산으로 사용할 수 있지만, - 는 문자열 연산으로 사용하지 못하고 오직 숫자 연산으로만 사용한다는 걸 알 수 있다. 

그래서 클래스에 더할 때는  맨 처음 클래스를 입력할 때는 className을 사용하고, 그 이후에 class 값을 변경하고자할 때는 classList.add(), classList.remove()를 하는 걸 알 수 있다. 


### fetch put 

```js
  // PUT: favorite 값 True로 수정
        fetch(`/api/sites/${post.id}/`, {
            method: 'PUT',
            headers: {
                'content-type': 'application/json'
            },
            body: JSON.stringify({
                'favorite': true,
            })
        })
        .then(response => response.json())
        .then(data => console.log("Success:", data))
        .catch(err => console.error('Error:', err))
```

위 코드의 결과, 403 Forbidden이 떴고, 위 코드의 맨 마지막 줄에 의해서 구체적인 error 내용은 다음과 같다.

`'CSRF Failed: CSRF token missing or incorrect.'}`




### js는 적용 o + put method accept 완료 + db에 저장 x

아래 코드를 실행하니 위 소제목과 같은 결과가 발생했다. 

js는 적용이 되었지만, db에 반영되지 않았다.  

즉 아래 로직에 의하면 유효성 검증 결과 'True'가 아니기 때문에 생긴 것으로 판단된다. 

```python
class SiteDetailAPIView(APIView):
    """
    세부 항목 조회, 수정, 삭제 api
    """

    def get_object(self, pk):
        
        return get_object_or_404(Site, pk=pk)


    ...
    

    def put(self, request, pk):
        print(1)
        
        site = self.get_object(pk)
        print(2)

        serializer = SiteSerializer(site, data=request.data)
        print(3)

        if serializer.is_valid():
            print(4)
           
            serializer.save()
            print(5)
            
            return Response({'msg':'Updated successfully'}, status=status.HTTP_202_ACCEPTED)
      
        return Response({'msg': f'{serializer.errors}'})

```

공식문서를 찾아본 결과, 다음과 같이 raise_exception을 입력하면 유효성 검증에 통과하지 못했을 경우 400 BAD_REQUEST error를 일으켜 중단시킨다.

```python
if serializer.is_valid(raise_exception=True):
```

입력하니, 다음과 같은 결과를 확인했다. 

확실히 유효성 검증을 통과하지 못했기 때문에 생긴 일이었다. 

`PUT http://127.0.0.1:8000/api/sites/1/ 400 (Bad Request)` 이와 같은 에러가 발생했다.  


그래서 `raise_exception=True`를 없애고, Error의 내용을 확인하고자. 맨 마지막 return Response 위에 `print(f'{serializer.errors}')`를 입력하여 내용을 확인했다. 

serializers.errors 내용을 보면 다음과 같다.(에러를 확인하고자 할 때는 예외를 일으키는 걸 사용하지 말자.)

{
'title': [ErrorDetail(string='This field is required.', code='required')], 
'thumbnail_url': [ErrorDetail(string='This field is required.', code='required')], 
'host_name': [ErrorDetail(string='This field is required.', code='required')], 
'content': [ErrorDetail(string='This field is required.', code='required')], 
'category': [ErrorDetail(string='This field is required.', code='required')], 
'user': [ErrorDetail(string='This field is required.', code='required')]
}



이 에러를 해결하기 위해서는 유효성 검증을 해야한다는 걸 알았다.

즉 PUT 을 실행했을 때, 4번이 출력되지 않는다. 
serializer.is_valid(): 실행 시, 다음과 같은 호출 순서를 가진다. 아래 블로그 참고하자.
https://velog.io/@joje/Serializer%EB%A5%BC-%ED%86%B5%ED%95%9C-%EC%9C%A0%ED%9A%A8%EC%84%B1-%EA%B2%80%EC%82%AC-%EB%B0%8F-%EC%A0%80%EC%9E%A5

여기서 나는 validated_data가 없기 떄문에 유효성 검증 단계에 돌입하지 못했다. 

그래서 공식문서를 참고하여 Serializers.py 를 작성해서 실행해봤지만, 똑같이 에러가 발생했다. 

- 그래도 데이터가 저장되는 형태가 이 유효성 검증에 맞게 바뀐 걸 확인했다. 

### partial=True

공식문서의 시리얼라이즈 부분을 읽다가 UPDATE 부분인 https://www.django-rest-framework.org/api-guide/serializers/#partial-updates 을 근거로

`partial=True`를 추가해보니

```python
serializer = SiteSerializer(site, data=request.data, partial=True)
```

DB에 반영이 되었다. 하지만, 아이콘이 바로 활성화되지 않는다. 

새로고침 후에야 적용이 되었다. 



# 3. 유효성 검증 순서

1. `if serializer.is_valid():` 진입

2. `is_valid()`의 내부 코드를 보면 `self.initial_data`에 접근하는 걸 알 수 있다. 이 data는 http message의 body 부분에 있는 데이터로 판단. 

3. 이 데이터를 가지고, `run_validation(self.initial_data)` 를 실행하면 Serializers.py의 Field를 거친다.  

4. 거친 데이터는 validated_data로 받게 되고, 그 후, serializers.py의 def update를 거친다.

5. 그러면서 instance의 속성을 업데이트한다.  