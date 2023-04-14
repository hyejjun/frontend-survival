# 주문 상세

## 2. 주문 상세 보기

### 주문 상세 확인 API 응답 객체 확인 후 해당 type 작성

`GET /orders/{id}`

주문 상세 정보를 얻는다.

API 응답 데이터의 형태를 보고 type을 정의한다.

```ts
export type OrderDetail = {
  id: string;
  title: string;
  lineItems: LineItem[];
  totalPrice: number;
  status: string;
  orderedAt: string;
}
```

`nullOrderDetail type 정의하기`

```ts
export const nullOrderDetail: OrderDetail = {
  id: '',
  title: '',
  status: '',
  lineItems: [],
  totalPrice: 0,
  orderedAt: '',
};
```

### `ApiService.ts` 에 /orders/{id} API 추가

```ts
async fetchOrder({ orderId }: {
  orderId: string;
}): Promise<OrderDetail> {
  const { data } = await this.instance.get(`/orders/${orderId}`);
  return data;
}
```

이렇게 API 연결하는 부분을 추가한다.

### 주문상세 스토어 생성 - OrderDetailStore

여기서 해줘야 할 것

- 주문상세 API 응답 받아와서 넣기

  - 변수
    - order (주문상세), 타입(OrderDetail) 넣어줘야함. 초기값은 nullOrderDetail
    - loading = true;
    - error = false;

  - 매서드 : fetchOrder (주문상세 API 요청하는 함수)

  - Action

    (order / loading / error) 에 대한 값을 각 Action에서 정의한다.
    - startLoading
    - setOrder - 인자값 (order: OrderDetail)
    - setError

</br>

### 주문목록 스토어를 사용할 수 있게 hook을 만들어준다

- orderId 가 바뀔 때 마다 API 요청을 날릴 수 있게 작성

```ts
export default function useFetchOrder({ orderId }: {
  orderId: string;
}) {
  const store = container.resolve(OrderDetailStore);

  const [{ order, loading, error }] = useStore(store);

  useEffect(() => {
    store.fetchOrder({ orderId });
  }, [store]);

  return { order, loading, error };
}
```

### 페이지 생성 - OrderDetailPage

- 여기서 useFetchOrder hook을 이용해서 API 요청하고 그 응답을 받아온다.

```tsx
  const params = useParams();

  const { order, loading, error } = useFetchOrder({
    orderId: String(params.id),
  });
```

useParams로 url 에 있는 파라메터를 가져온다.

그 값을 API 요청 인자값으로 넣어준다.

// TODO : useParams 알아보기

- loading, error 에 대한 처리도 해주고

- order 데이터는 Order 컴포넌트에 props로 내려준다

### 컴포넌트 생성 - Order

```tsx
type OrderProps = {
  order: OrderDetail;
};

export default function Order({ order }: OrderProps) {
  if (!order.lineItems.length) {
    return null;
  }

  return (
    <div>
      <dl>
        <dt>주문 일시</dt>
        <dd>{order.orderedAt}</dd>
        <dt>주문 코드</dt>
        <dd>{order.id}</dd>
      </dl>
      <Table
        lineItems={order.lineItems}
        totalPrice={order.totalPrice}
      />
    </div>
  );
}
```

#### 컴포넌트 재활용을 위해 재구성

1. `LineItem` 컴포넌트 (장바구니)

상품 목록을 보여주는 부분

`LineItemView`와 `Options` 컴포넌트를 재사용

`CartView` 컴포넌트에서 `Table` 컴포넌트를 추출한다.

CartView 컴포넌트는 Table 컴포넌트 그 자체를 사용한다.
