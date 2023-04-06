# 카테고리 목록 / 카테고리 별 상품목록

Header.tsx 를 생성하고 거기에 카테고리 목록을 표시할 것이다.

## 카테고리 목록

### Header 컴포넌트 생성

`Header.test.tsx`

___테스트 작성하기___

<details>
<summary>Header.test.tsx</summary>

```tsx
import { screen } from '@testing-library/react';

import { render } from '../test-helpers';

import Header from './Header';

// mocking
// jest.mock('../hooks/useFetchCategories', () => () => ({
//   categories: [],
// }));

test('Header', () => {
  // msw
  render(<Header />);

  screen.getByText(/Shop/);
  screen.getByRole('link', { name: 'Home' });
});
```

처음에는 mock 데이터로 테스트 작성 후 변경

</details>

`Headers.tsx`

```tsx
import { Link } from 'react-router-dom';
import styled from 'styled-components';

import useFetchCategories from '../hooks/useFetchCategories';

const Container = styled.header`
  margin-bottom: 2rem;

  h1 {
    font-size: 4rem;
  }

  nav {
    padding-block: 2rem;

    ul {
      display: flex;
    }

    li {
      margin-right: 2rem;
    }

    .active {
      color: ${(props) => props.theme.colors.primary}
    }
  }
`;

export default function Header() {
  const { categories } = useFetchCategories();

  return (
    <Container>
      <h1>Shop</h1>
      <nav>
        <ul>
          <li>
            <Link to="/">Home</Link>
          </li>
          <li>
            <Link to="/products">Products</Link>
            {categories.length && (
              <ul>
                {categories.map((category) => (
                  <li key={category.id}>
                    <Link to={`/products?categoryId=${category.id}`}>
                      {category.name}
                    </Link>
                  </li>
                ))}
              </ul>
            )}
          </li>
          <li>
            <Link to="/cart">Cart</Link>
          </li>
        </ul>
      </nav>
    </Container>
  );
}
```

### Category Store 생성

`CategoriesStore.ts`

```ts
@singleton()
@Store()
export default class CategoriesStore {
  categories: Category[] = [];

  async fetchCategories() {
    this.setCategories([]);

    const categories = await apiService.fetchCategories();

    this.setCategories(categories);
  }

  @Action()
  setCategories(categories: Category[]) {
    this.categories = categories;
  }
}
```

### Categories 를 받아올 수 있도록 API 연결하기

`/services/ApiService.ts`

fetchCategories 부분을 추가한다.

```ts
  async fetchCategories(): Promise<Category[]> {
    const { data } = await this.instance.get('/categories');
    const { categories } = data;
    return categories;
  }
```

### useFetchCategories hooks 생성

```tsx
export default function useFetchCategories() {
  const store = container.resolve(CategoriesStore);

  const [{ categories }] = useStore(store);

  useEffectOnce(() => {
    store.fetchCategories();
  });

  return { categories };
}
```

hooks 를 사용하면 categories 를 리턴 받을 수 있다.

---

## 카테고리 별 상품 목록

### 카테고리 선택을 위해 categoryId 얻기

-> `ProductListPage` 컴포넌트에서 `categoryId`를 얻는다.

`ProductListPage.tsx`

```tsx
export default function ProductListPage() {
  const [params] = useSearchParams();

  const categoryId = params.get('categoryId') ?? undefined;

  const { products } = useFetchProducts({ categoryId });

  return (
    <div>
      <h2>Products</h2>
      <Products products={products} />
    </div>
  );
}
```

### categoryId 를 쓰도록 훅을 변경한다

```tsx
export default function useFetchProducts({ categoryId }: {
  categoryId: string;
}): {
  products: ProductSummary[];
} {
  const store = container.resolve(ProductsStore);

  const [{ products }] = useStore(store);

  useEffect(() => {
    store.fetchProducts({ categoryId });
  }, [store, categoryId]);

  return { products };
}
```

### Store 도 변경한다

```ts
async fetchProducts({ categoryId }: {
  categoryId?: string;
}) {
  this.setProducts([]);

  const products = await apiService.fetchProducts({ categoryId });

  this.setProducts(products);
}
```

### apiService 에서 내용 변경

```ts
async fetchProducts({ categoryId }: {
  categoryId?: string;
} = {}): Promise<ProductSummary[]> {
  const { data } = await this.instance.get('/products', {
    params: { categoryId },
  });
  const { products } = data;
  return products;
}
```

처음부터 categoryId 를 받도록 만들어도 되고 이처럼 하나씩 추가하면서 고쳐나가도 된다.
이렇게 고쳐나갈때 테스트 코드가 있으면 좀 더 수월하게 작업할 수 있다.
