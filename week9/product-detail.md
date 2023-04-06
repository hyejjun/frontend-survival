# 상품 상세

상품 상세 정보를 얻어서 보여주는 페이지를 준비한다.

API로 데이터를 얻어서 뿌려준다.

## 상품 상세 페이지

```tsx
export default function ProductDetailPage() {
  const params = useParams();

  const { loading, error } = useFetchProduct({
    productId: String(params.id),
  });

  if (loading) {
    return (
      <p>Loading...</p>
    );
  }

  if (error) {
    return (
      <p>Error!</p>
    );
  }

  return (
    <ProductDetail />
  );
}
```

useFetchProduct 에 성공하면 `<ProductDetail />` 컴포넌트를 리턴한다.

지금은 간단하게 처리하기 위해 error인 경우 이렇게만 리턴된다.

```html
<p>Error!</p>
```

하지만 실제로 구현할 때 정확히 에러처리를 해줘야한다.

## 상품 상세 컴포넌트

장바구니 담기 기능 때문에 prop drilling 문제가 발생할 수 있어서, Page에서 product를 내려주지 않게 했다. ProductDetail 컴포넌트에서 product만 얻어서 활용하자.

// TODO :: 다시 확인

--> 이건 무슨 얘기지?? 장바구니 담기 기능이랑 상품 상세의 props drilling 이랑 뭔 상관일까??

다시 확인해보자...

___테스트 코드 작성___

`ProductDetail.test.tsx`

<details>
<summary>ProductDetail.test.tsx</summary>

```tsx
import { screen } from '@testing-library/react';

import { container } from 'tsyringe';

import { render } from '../../test-helpers';

import ProductDetail from './ProductDetail';

import fixtures from '../../../fixtures';

import ProductDetailStore from '../../store/ProductDetailStore';

const [product] = fixtures.products;

// 1. mocking 하는 방법

const { options } = product;

jest.mock('../../hooks/useProductDetailStore', () => () => [{ product }]);

// 2. msw 를 하는 방법
test('ProductDetail', async () => {
  const store = container.resolve(ProductDetailStore);

  await store.fetchProduct({ productId: product.id });

  render(<ProductDetail />);

  screen.getByText(product.name);
});

```

mocking으로 하다가 msw로 변경

</details>


`ProductDetail.tsx`

```tsx
const Container = styled.div`
  display: flex;
  justify-content: space-between;

  aside {
    width: 38%;
  }

  article {
    width: 60%;
  }
`;

export default function ProductDetail() {
  // Store 에서 product 값을 가져옴
  const [{ product }] = useProductDetailStore();

  return (
    <Container>
      <aside>
        <Images images={product.images} />
      </aside>
      <article>
        <h2>{product.name}</h2>
        <AddToCartForm />
        <Description value={product.description} />
      </article>
    </Container>
  );
}
```

### 상품 상세 Store 만들기

#### 1. `useProductDetailStore` hooks 만들기

상세보기 컴포넌트에서 Store 에 접근을 해서 product 값을 가져왔다.

useProductDetailStore hook을 만들자.

`.src/hooks/useProductDetailStore.ts`

```ts
import { container } from 'tsyringe';

import { useStore } from 'usestore-ts';

import ProductDetailStore from '../store/ProductDetailStore';

export default function useProductDetailStore() {
  const store = container.resolve(ProductDetailStore);

  return useStore(store);
}
```

#### 2. `src/store/ProductDetailStore.ts` Store 만들기

```ts
import { singleton } from 'tsyringe';

import { Action, Store } from 'usestore-ts';

import { ProductDetail, nullProductDetail } from '../types';

import { apiService } from '../services/ApiService';

@singleton()
@Store()
export default class ProductDetailStore {
  product: ProductDetail = nullProductDetail;

  loading = true;

  error = false;

  async fetchProduct({ productId }: {
    productId: string;
  }) {
    // 1. 로딩 시작 -> loading = true, error = false
    this.startLoading();

    // 2. apiService.fetchProduct({ productId })
    // api 를 통해 상품 상세 정보를 받아온다.
    try {
      const product = await apiService.fetchProduct({ productId });
      this.setProduct(product);
    } catch {
      this.setError();
    }
  }

  // 3. 각 상태 별 Action 을 명시해준다.

  // 1) 상품 상세 정보 API 요청 
  // -> 상품 상태 = null, loading = true, error = false
  @Action()
  private startLoading() {
    this.product = nullProductDetail;
    this.loading = true;
    this.error = false;
  }

  // 2) 상품 상세 정보 API 정상 응답 
  // -> 상품 상태 = product, loading = false, error = false
  @Action()
  private setProduct(product: ProductDetail) {
    this.product = product;
    this.loading = false;
    this.error = false;
  }

  // 3) 상품 상세 정보 API 응답 에러 
  // -> 상품 상태 = null, loading = false, error = true
  @Action()
  private setError() {
    this.product = nullProductDetail;
    this.loading = false;
    this.error = true;
  }
}
```

react query 로 하면 편한데... 로딩이랑 에러 처리까지 해주니까...

암튼 스토어에서 API 요청에 대한 처리를 해주었다.

여기서 null object를 사용하고 있는데 이거는 간단하게 만들어주면 된다.

type 파일에 추가해주자.

```ts
export const nullProductDetail: ProductDetail = {
  id: '',
  category: { id: '', name: '' },
  images: [],
  name: '',
  price: 0,
  options: [],
  description: '',
};
```

### API 응답을 가져올 수 있게 - useFetchProduct hooks 만들기

```tsx
export default function useFetchProduct({ productId }:{
  productId: string;
}) {
  const [{ loading, error }, store] = useProductDetailStore();

  useEffect(() => {
    store.fetchProduct({ productId });
  }, [store, productId]);

  return {
    loading, error,
  };
}
```

### 상품 상세 이미지를 넣을 Images 컴포넌트 생성

___테스트 코드 작성___
<details>
<summary>Images.test.tsx</summary>

```tsx
import { screen } from '@testing-library/react';

import { render } from '../../test-helpers';

import Images from './Images';

import { Image } from '../../types';

const context = describe;

describe('Images', () => {
  context('when images is empty', () => {
    const images: Image[] = [];

    it('renders nothing', () => {
      const { container } = render(<Images images={images} />);

      expect(container).toBeEmptyDOMElement();
    });
  });

  context('when images is not empty', () => {
    const images: Image[] = [{ url: 'http://example.com/test.png' }];

    it('renders image', () => {
      render(<Images images={images} />);

      screen.getByRole('img');
    });
  });
});

```

</details>

`Images.tsx`

```tsx
const Thumbnail = styled.img.attrs({
  alt: 'Product Image',
})`
  display: block;
  width: 100%;
  aspect-ratio: 1/1;
`;

type ImagesProps = {
  images: Image[];
}

export default function Images({ images }: ImagesProps) {
  // 이 문법은 도대체 뭘까????
  const [image] = images;

  if (!image) {
    return null;
  }

  return (
    <Thumbnail src={image.url} />
  );
}
```

// TODO :: 비구조 할당문인지? 첫번재 배열을 가져오는건지..?

### 상품 상세 정보글을 넣을 Description 컴포넌트 생성

___테스트 코드 작성___
<details>
<summary>Description.test.tsx</summary>

```tsx
import { screen } from '@testing-library/react';

import { render } from '../../test-helpers';

import Description from './Description';

const context = describe;

describe('Description', () => {
  context('when text is empty', () => {
    it('renders nothing', () => {
      const text = '';

      // container를 사용하는것을 추천하지는 않음. screen을 쓰는것을 추천하는데 이거는 이렇게 처리한다.
      const { container } = render(<Description value={text} />);

      expect(container).toBeEmptyDOMElement();
    });
  });

  context('when text is not empty', () => {
    const lines = ['1st line', '2nd line', '3rd line'];
    const text = lines.join('\n');
    it('renders multi-line text', () => {
      render(<Description value={text} />);

      lines.forEach((line) => {
        screen.getByText(line);
      });
    });
  });
});

```

</details>

`Description.tsx`

```tsx
// key 값에 index 값을 넣으면 안된다는 eslint 경고가 뜨기 때문에 이렇게 꼼수를 썼다.
function key(value: string, index: number) {
  return `${index}-${value}`;
}

const Container = styled.div`
  li {
    min-height: 1rem;
    line-height: 1.4;
  }
`;

type DescriptionProps = {
  value: string;
}

export default function Description({ value }: DescriptionProps) {
  if (!value.trim()) {
    return null;
  }

  const lines = value.split('\n');

  return (
    <Container>
      <ul>
        {lines.map((line, index) => (
          <li key={key(line, index)}>
            {line}
          </li>
        ))}
      </ul>
    </Container>
  );
}
```
