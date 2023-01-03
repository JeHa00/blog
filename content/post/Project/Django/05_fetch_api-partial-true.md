---
title: "Fetch_api를 사용하여 즐겨찾기 값 PUT으로 업데이트 과정에서 문제점과 해결과정"
date: 2022-11-17T16:23:16+09:00
draft: true
summary: 
tags: [""]
categories: ["Project"]
---

### 호출 순서
화면 로드 후, console 창에 
className += 'active' 로 추가 가능. 

하지만, import 해서 사용할 때는 bottom-toolbar.js:127 Uncaught TypeError: Cannot read properties of null (reading 'className') 문제가 발생 
즉 지정 태그를 읽어오지 못한다는 것 
이 하단 툴바는 각 site 항목에 마우스를 hover 시, block에서 flex로 바뀌면서 나타난다. 
console 창에 코드를 직접 입력해보니 제대로 활성화되는 것을 확인할 수 있다. 

- DOM이 다 불러오기 전에 javascript가 실행되어 찾지 못하는 문제점이다. 
- 즉 읽는 시점을 HTML이 다 불러오고나서 js가 실행되도록 해야한다.
    - https://valuefactory.tistory.com/548
    - window.onload = () => {} 를 사용하여 모든 DOM이 불러오고 나서 실행될 것을 분리했다. 
    - 이 함수가 작성되는 파일이 A 라고 하면 이 A를 받아서 사용하는 B에서 DOM이 생성되기 때문에 발생한 것이다.  

그래서 아래와 같이 작성했으나, 그럴 경우 전체 for문을 돌려야 하므로 DOM 생성되면서 반복되는데, 또 반복을 하면서 이 기능을 추가하므로 시간복잡도 n^(2)으로 좋지 않다.

```js
window.onload = () => {
    
    const favoriteButton        = getElement('.footer .item-actions .favorite'); 
    const categoryButton        = getElement('.footer .item-actions .category');
    const tagButton             = getElement('.footer .item-actions .tag');
    const deleteButton          = getElement('.footer .item-actions .delete');

    favoriteButton.addEventListener('click', () => {
        favoriteButton.classList.add('active');
        favoriteButton.setAttribute('data-tooltip', '즐겨찾기 해제'); 
    })   
}
```

그래서 각 버튼의 dom을 생성하는 함수 맨 아래에 넣기로 결정했다. 

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



### 유효성 검증 순서

1. `if serializer.is_valid():` 진입

2. `is_valid()`의 내부 코드를 보면 `self.initial_data`에 접근하는 걸 알 수 있다. 이 data는 http message의 body 부분에 있는 데이터로 판단. 

3. 이 데이터를 가지고, `run_validation(self.initial_data)` 를 실행하면 Serializers.py의 Field를 거친다.  

4. 거친 데이터는 validated_data로 받게 되고, 그 후, serializers.py의 def update를 거친다.

5. 그러면서 instance의 속성을 업데이트한다.  