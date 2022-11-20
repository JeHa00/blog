---
title: "Bottom_toobar_btn"
date: 2022-11-17T16:23:39+09:00
draft: true
summary: 
tags: [""]
categories: ["Project"]
---
makeDeleteInToolBar(parentNode , site) -> makeModalActive(deleteButton, site): 삭제 버튼을 누르면 Modal 창을 활성화시키는 함수

makeModal() -> 창을 닫는 함수 
            -> deleteSite -> makeDeleteInToolBar로부터 입력된 site 데이터가 있어야 지정된 항목 삭제 가능 

makeModal()을 makeDeleteInToolBar() 안에 넣을 수 없는 이유는 모달창은 1개만 필요하기 떄문이다. 

안에 넣어놓으면 article의 갯수만큼 생성된다.

그래서 makdModalActive 안에 deleteSite를 추가했다. 모달 창의 삭제 버튼은 getElement 함수를 사용하여 가져왔다. 

그래서 실행한 결과, 한 항목의 하단 툴바 삭제버튼을 클릭하여 모달 창을 띄우고, 모달 창의 삭제 기능 버튼을 누르면 모든 항목들이 삭제된다...

(삭제버튼을 눌러서 모달 창을 띄우는 이유는 삭제 경고 기능을 하는 것이 보다 유저 편의성 관점에서 좋다고 생각했기 때문이다. )

입력한 코드 로직 상 즐겨찾기 추가한 것과 동일하기 때문에, js 코드 상에는 문제가 없다고 판단했다.

그래서 views.py의 def delete(self, request): 를 보았다. 

아래와 같이 중간 중간 print문을 입력했다. 

```python
    def delete(self, request, pk):

        site = self.get_object(pk)
        
        print(f'before delete site: {site}')

        site.delete()
        print(f'after delete site: {site}')

        return Response({'msg': 'Deleted successfully'}, status=status.HTTP_200_OK)
```

실행한 결과 site에서 
before delete site: ....
before delete site: ....

위와 같이 모든 항목들 출력되는데, 자바스크립트의 배열로 출력되는 게 아니고, 하나씩 하나씩 출력되면서 모든 항목들이 출력되었다. 

그래서 view 상에는 문제가 없고, 자바스크립트 로직에서 문제가 있음을 생각했다. 

왜냐하면 위 출력문을 보니 난 한 항목의 하단 툴바 삭제 기능을 눌러 실행한 것이지만, 각 항목의 하단 툴바 삭제 버튼을 모든 항목에 대해서 클릭하여 일어난 것과 동일한 결과이기 때문이다. 

그래서 `makeDeleteInToolBar`, `makeModalActive`, `deleteSite` 에 각각 `console.log(site)` 를 입력하여 브라우저의 콘솔 창에 확인해보니 삭제 버튼을 실행하지 않았음에도 

위 각 함수에서 모든 항목들에 대해 출력되는 걸 확인했다. 

즉 위 함수들을 통해서 각 site라는 매개변수가 이어져서 전해지고 있는 건 확인했지만, 모든 site들이 모달 창의 삭제 버튼으로 지속적으로 이어지기 때문에, 한 번의 삭제 버튼으로 모든 항목들이 삭제되는 것이다. 

만약 리액트로 이를 구현했다면, 리액트에서는 각 항목들을 구분하는 별도의 값을 자동적으로 만들어주기 때문에 이런 문제에 부딪히지 않는다고 멘토님을 통해 확인했다. 

그래서 각 항목들의 값을 지속적으로 흐르도록 만드는 것이 아니라, 각 항목들을 구별할 수 있는 **식별자** 를 만들어, 이 식별자에 접근하여 삭제하는 방식을 권하셨다. 

그래서 각 항목의 하단 툴바 삭제 버튼을 클릭하면 해당 항목의 class에 'selected'를 추가한다. 

그래서 이 selected까지 포함하여 document.getElementByClassName에 입력하여 해당 요소를 가져온 후, 해당 요소의 id 값을 fetch에 집어넣는다. 

삭제 후, 화면을 다시 불러오기 위해서 window.location.reload()를 사용한다. 