# 관리자 > 주문관리

원래 주문 관리는 상당히 복잡함...

강의에서는 주문 상태를 변경하는 정도로 연습한다.

## 주문 목록

GET /admin/orders

요청하면 전체 주문 목록을 받게 된다.

궁금한건... 실제로 서비스를 운영할때는 어떻게 할까??

무신사 같은 경우 주문이 어마무시하게 많을텐데...

당연히 page 별로 나눌거고...

## 주문 상세 정보, 주문 정보 수정

GET /admin/orders/{id}

주문 번호를 기준으로 주문 상세 정보를 받아온다.

* 페이지를 만들때 error 인 경우, order가 falsy한 경우를 함께 해준다.

안그러면 optional chaining으로 처리해줘야 하기때문에..!

```tsx
if(error | !order) {
  return <p>Error!</p>
}
```

PATCH /admin/orders/{id}

해당 API를 호출하는 hooks를 만들때 SWR을 사용하는데 여기서 주문 수정 하는 함수를 하나 만든다. 그리고 수정 api 호출 후 상세 정보 api를 mutate로 reload 한다.

---

만약 특정 회원의 주문만 확인하고 싶다면???

-> 서비스 페이지에서는 모든 요청에 로그인 토큰을 넣어 보냈기 때문에 해당 회원의 주문 목록 조회가 가능했다.

-> 관리자가 확인하려면 요청 params로 userid를 보내줘야 할 듯

- 그러면 API를 수정하거나 추가하게 될 것 같다.
- /admin/orders 에서 optinal params 로 userid를 받게끔 해야할듯
