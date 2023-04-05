# 개발환경 구축하기

## 용어 정의

개발 진행시 작업자들이 공통적으로 사용할 용어를 먼저 정의한다.

개발을 진행하며 추가/수정이 가능하다.

공통된 언어를 사용하는 것...!

용어 지정 시에 접두사를 사용하는 것은 좋지 않은 신호이기 때문에 되도록이면 접두사를 제외하고 의미를 명확히 해줘야 한다.

```.
- Product: 상품
  - Summary: 상품에 대한 요약 정보
  - Detail: 상품에 대한 상세 정보
  - Image: 상품 이미지
  - Option: 상품에 대한 상세 옵션 종류 (색상, 크기 등)
    - OptionItem: 옵션에 대한 상세 옵션 값 (옵션이 색상이라면 이건 Blue, Red 같은 걸 의미함)

- Category: 상품에 대한 분류

- Cart: 장바구니
  - LineItem: 장바구니에 담긴 것 (상품, 옵션, 수량 등을 동시에 다룸)
    - 여기서도 Option과 OptionItem을 사용한다.
    - 용어는 동일하지만 Product와 다른 구성을 갖기 때문에, 여기서는 Product와 Order라는 접두어를 활용한다.
    - 시스템을 분리할 수 있다면, 근본적으로 나누는 걸 추천(상품 정보 확인 / 장바구니 / 주문).

- Order: 주문
  - 여기서도 동일한 LineItem 활용.

- User: 사용자
```

</br>

## 기능 정의

어떤 것 부터 구현할 것인지?

고민이 될 것이다..

어디까지 만들면 이러이러한 서비스가 만들 수 있는데 구매의향이 있는지 물어볼 수 있을까?

프로젝트 마다 다르겠지만 이번 주차에서는 `상품 목록 확인, 상세 정보 확인` 이 두가지만 있어도 이러한 서비스가 나올 것이다~~ 라고 설명할 수 있다. 카탈로그의 형태겠지?

그래서 저 2개를 우선순위를 줘서 구현해보자는 것!

```.
<기능>

1. 상품 목록 확인
2. 상품 상세 정보 확인
3. 장바구니에 상품 담기
4. 주문하기 → 배송지 입력, 결제
5. 주문 목록 확인
6. 주문 상세 확인
7. 로그인
8. 회원 가입
```

</br>

## 화면 정의

페이지 및 링크를 어떻게 뽑을 것인가...?

```.
1. 홈 페이지 - `/`
2. 상품 목록 페이지 - `/products`
3. 상품 상세 페이지 - `/products/{id}`
4. 장바구니 페이지 - `/cart`
5. 주문 페이지 - `/order`
6. 주문 완료 페이지 - `/order/complete`
7. 주문 목록 페이지 - `/orders`
8. 주문 상세 페이지 - `/orders/{id}`
9. 로그인 페이지 - `/login`
10. 회원 가입 페이지 - `/signup`
11. 회원 가입 완료 페이지 - `/signup/complete`
```

이렇게 구현할 예정이다..

</br>

## REST API

> [REST API](https://docs.google.com/document/d/1bGYl3IDoX53cNBbZHNlsRhPLZQ3Qiu-Jm3gpqyu_xI0/view)
>

API 명세서 이다.

참고해서 `mock data`와 `types`를 작성할 수 있다.

</br>

## 개발 환경 세팅

의존성 문제로 테스트가 잘 작동하지 않는 경우가 있기 때문에 해당 `package.json`을 참고하자.

<details>
<summary>package.json</summary>

```json
{
  "name": "week09-test",
  "version": "0.0.1",
  "description": "week09-test",
  "source": "index.html",
  "scripts": {
    "start": "parcel --port 8080",
    "build": "parcel build",
    "server": "cd express-app && ts-node app.ts",
    "serve": "servor dist index.html 8080 --reload",
    "check": "tsc --noEmit",
    "lint": "eslint --fix --ext .js,.jsx,.ts,.tsx .",
    "test": "jest",
    "coverage": "jest --coverage --coverage-reporters html",
    "watch:test": "jest --watchAll",
    "codeceptjs": "codeceptjs run --steps",
    "codeceptjs:headless": "HEADLESS=true codeceptjs run --steps",
    "codeceptjs:ui": "codecept-ui --app",
    "ci": "concurrently -s first -k 'npm run server' 'npm run serve' 'sleep 3 && npm run codeceptjs'"
  },
  "author": "Seedwhale",
  "license": "ISC",
  "dependencies": {
    "axios": "^1.3.4",
    "lodash": "^4.17.21",
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "react-router": "^6.9.0",
    "react-router-dom": "^6.9.0",
    "reflect-metadata": "^0.1.13",
    "styled-components": "^5.3.9",
    "styled-reset": "^4.4.5",
    "tsyringe": "^4.7.0",
    "usehooks-ts": "^2.9.1",
    "usestore-ts": "^0.0.3"
  },
  "devDependencies": {
    "@codeceptjs/configure": "^0.10.0",
    "@codeceptjs/ui": "^0.5.0",
    "@swc/core": "^1.3.42",
    "@swc/jest": "^0.2.24",
    "@swc/plugin-styled-components": "^1.5.49",
    "@testing-library/jest-dom": "^5.16.5",
    "@testing-library/react": "^13.4.0",
    "@types/jest": "^29.5.0",
    "@types/lodash": "^4.14.191",
    "@types/node": "^18.15.10",
    "@types/react": "^18.0.27",
    "@types/react-dom": "^18.0.10",
    "@types/styled-components": "^5.1.26",
    "@typescript-eslint/eslint-plugin": "^5.49.0",
    "@typescript-eslint/parser": "^5.49.0",
    "codeceptjs": "^3.4.1",
    "concurrently": "^7.6.0",
    "eslint": "^8.32.0",
    "eslint-config-airbnb": "^19.0.4",
    "eslint-plugin-codeceptjs": "^1.3.0",
    "eslint-plugin-import": "^2.27.5",
    "eslint-plugin-jsx-a11y": "^6.7.1",
    "eslint-plugin-react": "^7.32.1",
    "eslint-plugin-react-hooks": "^4.6.0",
    "jest": "^29.5.0",
    "jest-environment-jsdom": "^29.5.0",
    "msw": "^1.2.1",
    "parcel": "^2.8.3",
    "parcel-reporter-static-files-copy": "^1.5.0",
    "playwright": "^1.32.1",
    "process": "^0.11.10",
    "servor": "^4.0.2",
    "ts-node": "^10.9.1",
    "typescript": "^4.9.5",
    "whatwg-fetch": "^3.6.2"
  }
}
```

</details>

</br>

## E2E Test

테스트 코드를 먼저 작성하고 이에 맞춰서 구현하는 것이다!

***어떤 기능을 구현하고 싶은지 테스트코드로 먼저 작성 후 구현***

CodeceptJS로 E2E 테스트 준비하고, 여기 있는 기능 테스트를 모두 통과시키는 걸 목표로 삼는다.

1. 상품 목록
    1. 모든 상품 보기
    2. 특정 카테고리의 상품 보기
2. 상품 상세
3. 장바구니
    1. 장바구니가 비어있는 경우
    2. 장바구니에 상품을 담은 경우
4. …

</br>

## Test Helper

테스트 코드에서 styled-components의 Theme과 React Router의 Link 등을 사용할 때 문제가 발생하지 않도록, React Testing Library의 render를 한번 감싼 테스트용 헬퍼 함수를 준비.

`./src/test-helper.ts`

```tsx
/* eslint-disable import/prefer-default-export */
/* eslint-disable import/no-extraneous-dependencies */
import { render as originalRender } from '@testing-library/react';

import React from 'react';

import { MemoryRouter } from 'react-router';

import { ThemeProvider } from 'styled-components';

import defaultTheme from './styles/defaultTheme';

export function render(element: React.ReactElement) {
  return originalRender((
    <MemoryRouter initialEntries={['/']}>
      <ThemeProvider theme={defaultTheme}>
        {element}
      </ThemeProvider>
    </MemoryRouter>
  ));
}

```

-> 이건 내용 다시 봐야 할 듯... 뭔지..?

</br>

## Types

기본적인 타입을 준비한다.

__미리 정한 용어집과 REST API 스펙에 맞춰서 작성함.__

```ts
export type Category = {
  id: string;
  name: string;
}

export type Image = {
  url: string;
}

export type ProductSummary = {
  id: string;
  category: Category;
  thumbnail: Image;
  name: string;
  price: number;
}

export type ProductOptionItem = {
  id: string;
  name: string;
};

export type ProductOption = {
  id: string;
  name: string;
  items: ProductOptionItem[];
};

export type ProductDetail = {
  id: string;
  category: Category;
  images: Image[];
  name: string;
  price: number;
  options: ProductOption[];
  description: string;
}

export type OrderOptionItem = {
  name: string;
};

export type OrderOption = {
  name: string;
  item: OrderOptionItem;
};

export type LineItem = {
  id: string;
  product: {
    id: string;
    name: string;
  };
  options: OrderOption[];
  unitPrice: number;
  quantity: number;
  totalPrice: number;
}

export type Cart = {
  lineItems: LineItem[];
  totalPrice: number;
}
```

</br>

## fixtures 세팅

`./fixtures/`

<details>
<summary>products.ts</summary>

```ts
import { ProductDetail } from '../src/types';

const products: ProductDetail[] = [
  {
    id: 'product-01',
    category: {
      id: 'category-01',
      name: 'Top',
    },
    images: [
      { url: 'http://example.com/01.jpg' },
    ],
    name: 'Product #1',
    price: 128_000,
    options: [
      {
        id: 'option-01',
        name: 'Color',
        items: [
          { id: 'option-item-01', name: 'Black' },
          { id: 'option-item-02', name: 'White' },
        ],
      },
      {
        id: 'option-02',
        name: 'Size',
        items: [
          { id: 'option-item-03', name: 'S' },
          { id: 'option-item-04', name: 'M' },
          { id: 'option-item-05', name: 'L' },
        ],
      },
    ],
    description: 'item description',
  },
];

export default products;

```

</details>

<details>
<summary>categories.ts</summary>

```ts
import { Category } from '../src/types';

const categories: Category[] = [
  { id: 'category-01', name: 'Category #1' },
  { id: 'category-02', name: 'Category #2' },
];

export default categories;
```

</details>

<details>
<summary>cart.ts</summary>

```ts
import { Cart } from '../src/types';

const cart: Cart = {
  lineItems: [
    {
      id: 'line-item-01',
      product: {
        id: 'product-01',
        name: '맨투맨',
      },
      options: [
        { name: '컬러', item: { name: 'black' } },
      ],
      unitPrice: 10_000,
      quantity: 1,
      totalPrice: 10_000,
    },
  ],
  totalPrice: 10_000,
};

export default cart;
```

</details>

`index.ts`

```ts
import categories from './categories';
import products from './products';
import cart from './cart';

export default {
  categories,
  products,
  cart,
};
```

</br>

## MSW 세팅

`./src/mocks/handlers.ts`

REST API 스펙에 맞춰서 MSW 핸들러를 준비한다.

이때 fixtures를 활용하는 것이 좋다.

```ts
import { rest } from 'msw';

import { ProductSummary } from '../types';

import fixtures from '../../fixtures';

const BASE_URL = 'https://shop-demo-api-01.fly.dev';

const productSummaries: ProductSummary[] = fixtures.products
  .map((product) => ({
    id: product.id,
    category: product.category,
    thumbnail: { url: product.images[0].url },
    name: product.name,
    price: product.price,
  }));

const handlers = [
  rest.get(`${BASE_URL}/categories`, (req, res, ctx) => (
    res(ctx.json({ categories: fixtures.categories }))
  )),
  rest.get(`${BASE_URL}/products`, (req, res, ctx) => (
    res(ctx.json({ products: productSummaries }))
  )),
  rest.get(`${BASE_URL}/products/:id`, (req, res, ctx) => {
    const product = fixtures.products.find((i) => i.id === req.params.id);
    if (!product) {
      return res(ctx.status(404));
    }
    return res(ctx.json(product));
  }),
  rest.get(`${BASE_URL}/cart`, (req, res, ctx) => (
    res(ctx.json(fixtures.cart))
  )),
  rest.post(`${BASE_URL}/cart/line-items`, (req, res, ctx) => (
    res(ctx.status(201))
  )),
];

export default handlers;
```

`./src/mocks/server.ts`

```ts
// eslint-disable-next-line import/no-extraneous-dependencies
import { setupServer } from 'msw/node';

import handlers from './handlers';

const server = setupServer(...handlers);

export default server;
```