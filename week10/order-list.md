# 주문 목록 확인 & 주문 상세 보기

## 1. 주문 목록 확인

### 주문 목록 확인 API 응답 객체 확인 후 해당 type 작성

`GET /orders`

주문 목록을 얻는다. 주문 목록에서는 주문 간소화된 정보만 포함된다.

응답

```json
{
  "orders": [
    {
      "id": "0BV000ODR0001",
      "title": "맨투맨",
      "totalPrice": 256000,
      "status": "paid",
      "orderedAt": "2023-01-01 00:00:00"
    }
  ]
}
```

API 명세서를 확인하고 이거에 맞게 type 을 미리 선언해주자.

```ts
export type OrderSummary = {
  id: string;
  title: string;
  totalPrice: number;
  status: string;
  orderedAt: string;
}
```

types.ts 선언

### `ApiService.ts` 에 /orders API 추가

```ts
async fetchOrders(): Promise<OrderSummary[]> {
  const { data } = await this.instance.get('/orders');
  const { orders } = data;
  return orders;
}
```

### 주문목록 스토어 생성 - OrderListStore

여기서 해줘야 할 것

- 주문목록 orders 에 API 응답 받아와서 넣기

  - 변수 : orders (주문목록), 타입(OrderSummary) 넣어줘야함

  - 매서드 : fetchOrders (주문목록 API 요청하는 함수)

  - Action : setOrders (주문목록 응답값 변수에 넣기)

### 주문목록 스토어를 사용할 수 있게 hook을 만들어준다

```ts
export default function useFetchOrders() {
  const store = container.resolve(OrderListStore);

  const [{ orders }] = useStore(store);

  useEffect(() => {
    store.fetchOrders(); // 주문목록 API를 요청하는 매서드를 실행
  }, [store]);

  return { orders }; // 주문 목록을 리턴
}
```

### 주문 목록 페이지 생성 (OrderListPage.tsx)

useFetchOrders hook을 이용해서 orders (주문목록)을 받아온다.

```tsx
<Orders orders={orders}>
```

이렇게 컴포넌트에 넘겨준다. 주문 목록 같은 경우는 props로 넘겨주기로 한다.

// TODO ::

음... 왜지?? 다른거는 props driling 때문에 스토어에 저장하고 바로 갖다 썼는데

이거는 강의를 다시 확인!

### routes 에 /orders 추가 (페이지 추가)

```ts
{ path: '/orders', element: <OrderListPage /> },
```

### Header.tsx 에 /orders 링크 추가

```tsx
<li>
  <Link to="/orders">Orders</Link>
</li>
```

주문한 목록을 보여줘야 하니까 로그인 되었을때만 저 링크가 보여야 한다.

### 컴포넌트 작성

#### Orders.tsx

주문목록 (orders)를 페이지에서 props로 받아오고 있음

```tsx
  if (!orders.length) {
    return null;
  }
```

이렇게 orders.length 가 falsy 할때 만 retrun null로 해준다.

orders 가 제대로 들어왔다면

```tsx
return (
    <Container>
      <ul>
        {orders.map((order) => (
          <li key={order.id}>
            <Link to={`/orders/${order.id}`}>
              <Order order={order} />
            </Link>
          </li>
        ))}
      </ul>
    </Container>
  );
```

이렇게 order를 한 줄 씩 뿌려주고 링크로 해당 상품이 있는 페이지를 연결한다.

여기에서 사용된 `Order.tsx` 컴포넌트는

order 에 대한 값을 받아서

주문일시, 주문코드, 상품, 결제 금액 등을 뿌려주면 된다.

---

### 🌟 강의에서 배운 점

1. API 명세서 보고 type 설정하기

- typescript를 사용하고 있기 때문에 type을 미리 정해줘야 한다.
- mock 데이터 만들때도 유용하게 사용할 수 있다.

2. 개별 주문 항목을 컴포넌트로 따로 뺀다는 점

- 프로젝트에서는 따로 빼지 않고 리스트 컴포넌트 안에서 전부 처리해줬다.
- map으로 돌려서 그 안에서 바로 뿌려줬는데 이렇게 나눠야 관리가 편해질 것 같다.
