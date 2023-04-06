# 상품 목록

상품 목록 페이지를 만들어보자.

1. 상품 목록 얻기

    - API 통신을 통해 얻는다
    - useFetchProducts hooks을 작성하자

2. 상품 목록 보여주기

    - 화면을 통해 보여준다
    - Products `컴포넌트`에 구현한다

이 두가지를 합친 것이 `페이지` (ProductListPage)에 들어간다.

역할을 나누는것...

`/pages/ProductListPage.tsx`

```tsx
import Products from '../components/product-list/Products';

import useFetchProducts from '../hooks/useFetchProducts';

export default function ProductListPage() {
  const { products } = useFetchProducts();

  return (
    <div>
      <h2>Products</h2>
      <Products products={products} />
    </div>
  );
}
```

왜 컴포넌트에서 직접 hooks을 사용하여 API 통신하지 않고 페이지에서 부르는걸까?

이유가 있음..

@!!!이건 다시 확인

일단 API 통신을 위한 hook을 만들어준다.

`hooks/useFetchProducts.ts`

```ts
import ProductDetailStore from '../store/ProductDetailStore';

const apiBaseUrl = 'https://shop-demo-api-01.fly.dev';

export default function useFetchProducts() {
  type Data = {
    products: ProductSummary[];
  };

  const { data } = useFetch<Data>(`${apiBaseUrl}/products`);

  return {
    products: data?.products ?? [],
  };
}
```

리스트를 뿌려주는 Products 컴포넌트 생성 후

그 안에 개별로 들어가는 Product 컴포넌틀를 생성한다.

- Products 컴포넌트 생성

`Products.tsx`

```tsx
const Container = styled.div`
  ul {
    display: flex;
    flex-wrap: wrap;
  }

  li {
    width: 20%;
    padding: 1rem;
  }

  a {
    display: block;
    text-decoration: none;
  }
`;

type ProductsProps = {
  products: ProductSummary[];
}

export default function Products({ products }: ProductsProps) {
  if (!products.length) {
    return null;
  }

  return (
    <Container>
      <ul>
        {products.map((product) => (
          <li key={product.id}>
            <Link to={`/products/${product.id}`}>
              <Product product={product} />
            </Link>
          </li>
        ))}
      </ul>
    </Container>
  );
}

```

- Product 컴포넌트 생성

`Product.tsx`

```tsx
const Thumbnail = styled.img.attrs({
  alt: 'Thumbnail',
})`
  display: block;
  width: 100%;
  aspect-ratio: 1/1;
`;

type ProductProps = {
  product: ProductSummary;
}

export default function Product({ product }: ProductProps) {
  return (
    <div>
      <Thumbnail src={product.thumbnail.url} />
      <div>{product.name}</div>
      <div>
        {numberFormat(product.price)}
        원
      </div>
    </div>
  );
}
```

숫자 콤마 적용 등... 포맷을 맞춰주기 위해서

numberFormat utils 함수를 준비한다.

```ts
export default function numberFormat(value: number) {
  return new Intl.NumberFormat().format(value);
}
```

상품 목록을 Store로 관리하자

```ts
@singleton()
@Store()
export default class ProductsStore {
  products: ProductSummary[] = [];

  async fetchProducts({ categoryId }: {
    categoryId?: string;
  }) {
    this.setProducts([]);

    const products = await apiService.fetchProducts({ categoryId });

    this.setProducts(products);
  }

  @Action()
  setProducts(products: ProductSummary[]) {
    this.products = products;
  }
}

```

원래는 Store에서 바로 API hooks를 호출하게 했는데 모든 Store에서 매번 반복되기도 하고

Store 가 axios를 갖고 있을 필요 없게 하기 위해서

`apiService` 로 따로 빼주었다.

```ts
import axios from 'axios';

import { ProductDetail } from '../types';

const API_BASE_URL = process.env.API_BASE_URL
                     || 'https://shop-demo-api-01.fly.dev';

export default class ApiService {
  private instance = axios.create({
    baseURL: API_BASE_URL,
  });

  async fetchProduct({ productId }:{
    productId?: string;
  }): Promise<ProductDetail> {
    const { data } = await this.instance.get(`/products/${productId}`);
    return data;
  }
}

export const apiService = new ApiService();
```

여기서 API 관련된 것들을 모두 관리할 예정이다.

다른 API들도 여기서 다 관리할 것.

---

## (구현 전) 테스트 파일 작성

테스트를 항상 먼저 작성한다.

이러한 내용으로 구현할 것이라는 것을 명시하고 통과되게끔 만들고 리팩토링을 계속하면 된다.

`/test/product_list_test.ts`

```ts
Feature('Product list');

Scenario('All products', ({ I }) => {
  I.amOnPage('/');

  I.click('Products');

  I.see('CBCL 하트자수맨투맨');
});

Scenario('Category', ({ I }) => {
  I.amOnPage('/');

  I.click('top');

  I.see('CBCL 하트자수맨투맨');
  I.dontSee('CBCL 핀턱자수후드');

  I.click('outer');

  I.dontSee('CBCL 하트자수맨투맨');
  I.see('CBCL 핀턱자수후드');

  I.click('bottom');

  I.dontSee('CBCL 하트자수맨투맨');
  I.dontSee('CBCL 핀턱자수후드');
});

Scenario('Visit the product page', ({ I }) => {
  I.amOnPage('/');

  I.click('Products');

  I.click('CBCL 하트자수맨투맨');

  I.see('편하게 입을 수 있는 맨투맨');
});
```
