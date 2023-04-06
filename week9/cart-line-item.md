# 장바구니 보기

장바구니에 상품 담기 기능을 만들기 전에, 비교적 쉬운 장바구니 보기 작업을 먼저 끝내보자.

---

## 장바구니 페이지 생성

`pages/CartPage.tsx`

```tsx
import CartView from '../components/cart/CartView';

import useFetchCart from '../hooks/useFetchCart';

export default function CartPage() {
  const { cart } = useFetchCart();

  if (!cart) {
    return null;
  }

  return (
    <div>
      <h2>장바구니</h2>
      <CartView cart={cart} />
    </div>
  );
}

```

페이지에서 API를 통해 응답을 받아오고 응답 값은 컴포넌트로 넘긴다.

---

## 장바구니 확인하는 API - hooks만들기

`useFetchCart.tsx`

```tsx
export default function useFetchCart() {
  const store = container.resolve(CartStore);

  const [{ cart }] = useStore(store);

  useEffectOnce(() => {
    store.fetchCart();
  });

  return { cart };
}
```

---

## 장바구니 Store만들기

```ts
@singleton()
@Store()
export default class CartStore {
  cart: Cart | null = null;

  async fetchCart() {
    this.setCart(null);

    const cart = await apiService.fetchCart();

    this.setCart(cart);
  }

  @Action()
  setCart(cart: Cart | null) {
    this.cart = cart;
  }
}
```

API 응답 값인 cart를 받아와서 해당 값을 리턴해준다.

여기서는 cart가 비어있는 경우에 그냥 null을 썼는데, ProductDetail처럼 Null Object를 만들어서 쓰면 더 좋다.

---

## apiService 에 fetchCart API 추가하기

```ts
async fetchCart(): Promise<Cart> {
  const { data } = await this.instance.get('/cart');
  return data;
}
```

---

## 장바구니 정보 - 컴포넌트 생성하기

컴포넌트 이름을 Cart로 만들고 싶은데 그렇게 되면 type Cart 와 겹치게 된다.

이거는 어떻게 해결할까?

1. Cart 타입을 가져올때 as CartType으로 가져오면서 타입의 이름을 바꾼다.

2. Cart 컴포넌트를 다른 이름으로 바꾼다.

아샬은 1번의 경우를 선호한다.

쉽게 가기 위해서 2번을 택한다. CartView라는 컴포넌트를 생성하자.

`/components/CartView.tsx`

```tsx
const Container = styled.div`
  table {
    width: 100%;
  }

  th, td {
    padding: .5rem;
    text-align: left;
  }
`;

type CartViewProps = {
  cart: Cart;
};

export default function CartView({ cart }: CartViewProps) {
  if (!cart.lineItems.length) {
    return (
      <p>장바구니가 비었습니다</p>
    );
  }

  return (
    <Container>
      <table>
        <thead>
          <tr>
            <th>제품</th>
            <th>단가</th>
            <th>수량</th>
            <th>금액</th>
          </tr>
        </thead>
        <tbody>
          {cart.lineItems.map((lineItem) => (
            <LineItemView
              key={lineItem.id}
              lineItem={lineItem}
            />
          ))}
        </tbody>
        <tfoot>
          <tr>
            <th colSpan={3}>
              합계
            </th>
            <td>
              {numberFormat(cart.totalPrice)}
              원
            </td>
          </tr>
        </tfoot>
      </table>
    </Container>
  );
}
```

이 컴포넌트 안에 들어가는 상품 라인 별 컴포넌트인 `LineItemView.tsx`도 생성해주자.

___테스트 코드___

`/components/LineItemView.test.tsx` 생성

<details>
<summary>LineItemView.test.tsx</summary>

```tsx
import { render, screen } from '@testing-library/react';

import fixtures from '../../../fixtures';

import LineItemView from './LineItemView';

describe('LineItemView', () => {
  it('renders a line item', () => {
    // TODO :: 비구조 할당문인지? 첫번재 배열을 가져오는건지..?
    const [lineItem] = fixtures.cart.lineItems;

    render((
      <table>
        <tbody>
          <LineItemView lineItem={lineItem} />
        </tbody>
      </table>
    ));

    screen.getByText(lineItem.product.name);
  });
});
```

</details>

`/components/LineItemView.tsx` 생성

```tsx
type LineItemViewProps = {
  lineItem: LineItem;
};

export default function LineItemView({ lineItem }: LineItemViewProps) {
  return (
    <tr>
      <td>
        <Link to={`/products/${lineItem.product.id}`}>
          {lineItem.product.name}
        </Link>
        <Options options={lineItem.options} />
      </td>
      <td>
        {numberFormat(lineItem.unitPrice)}
        원
      </td>
      <td>{lineItem.quantity}</td>
      <td>
        {numberFormat(lineItem.totalPrice)}
        원
      </td>
    </tr>
  );
}
```

여기 안에서 `Options` 컴포넌트를 만들어주었다.

___테스트 코드___

`/components/Options.test.tsx` 생성

<details>
<summary>Options.test.tsx</summary>

```tsx
import { render, screen } from '@testing-library/react';

import Options from './Options';

import { OrderOption } from '../../types';
import fixtures from '../../../fixtures';

const context = describe;

describe('Options', () => {
  context('when options is empty', () => {
    const options: OrderOption[] = [];

    it('renders nothing', () => {
      const { container } = render(<Options options={options} />);

      expect(container).toBeEmptyDOMElement();
    });
  });

  context('when options is not empty', () => {
    const [lineItems] = fixtures.cart.lineItems;
    const { options } = lineItems;

    it('renders options text', () => {
      const { container } = render(<Options options={options} />);

      const optionName = options[0].name;
      const itemName = options[0].item.name;

      // 방법 1
      // screen.getByText(new RegExp(optionName));
      // screen.getByText(itemName);

      // 방법 2
      expect(container).toHaveTextContent(`${optionName}: ${itemName}`);
    });
  });
});

```

</details>

`/components/Options.tsx`

```tsx
const Container = styled.div`
  margin-top: .5rem;
  font-size: 1.4rem;
`;

type OptionsProps = {
  options: OrderOption[];
}

export default function Options({ options }: OptionsProps) {
  if (!options.length) {
    return null;
  }

  // join 뭐임?
  const text = options
    .map((option) => `${option.name}: ${option.item.name}`)
    .join(', ');

  return (
    <Container>
      (
      {text}
      )
    </Container>
  );
}
```

// TODO :: join 활용법 찾아보기

---

routes 테스트에 추가

`routes.test.tsx`

```tsx
    context('when the current path is “/cart”', () => {
      it('renders the cart page', async () => {
        renderRouter('/cart');

        await waitFor(() => {
          screen.getByText(/합계/);
        });
      });
    });
```
