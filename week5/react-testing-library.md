# React Testing Library

__학습 키워드__

- React Testing Library
- given - when - then 패턴
- Mocking
- Test fixture

---

React 컴포넌트를 사용자 입장에 가깝게 테스트 할 수 있는 도구.

구현 자체는 해킹 하듯이 되어있는데

web browser가 아닌곳에서는 document 접근이 안되는데 여기서는 된다..

사용자 입장에서 가깝다는 뜻이 이런거...

거의 E2E 테스트에 가깝기는 함. (동일하진 않음)

---

컴포넌트 테스트 파일은 폴더를 만들 수도 있고

컴포넌트와 같은 위치에

```plain
컴포넌트 : A.tsx

테스트 파일: A.test.tsx
```

이런식으로 만들 수 있다.

---

## 실습

`TextField.test.tsx`

```tsx
import { render, screen } from '@testing-library/react';

import TextField from './TextField';

test('TextField', () => {
  // given : 준비과정
  const setFilterText = () => {

  };

  // when: ~ 를 하면
  render((
    <TextField
      placeholder=""
      filterText=""
      setFilterText={setFilterText}
    />
  ));

  // then: ~어떻게 된다.
  screen.getByLabelText('Search');
});

```

### `given` : 사전에 주어지는 부분

여기서는 컴포넌트의 props값인 `setFilterText` 함수를 미리 만들어놔야 한다.

</br>

### `when` : TextField 컴포넌트를 렌더해주면

```tsx
  render((
    <TextField
      placeholder=""
      filterText=""
      setFilterText={setFilterText}
    />
  ));
```

해당 컴포넌트를 렌더한다는 뜻.

App render 할때랑 비슷하게 생각하면 됨

그 역할을 해주는 것.

</br>

### `then` : Search input 이 잡혀야함

```tsx
screen.getByLabelText('Search');
```

getByLabelText 로 하면 label element가 잡히는게 아니라 이거랑 연결된 input element가 잡힌다.

get 어쩌구는 있으면 잘 되고 없으면 뻑남

/Search/로 정규표현식으로 포함 되어 있기만 하면 되도록 바꿀 수도 있음

---

이게 일단 기본

이제 test 해보면서 하나씩 보면 'Search' 가 잡히긴 함

근데 문제는 이 TextField 컴포넌트가 범용성이 있어야 하는데 매번 label 이 Search 로 고정되어있음.

이 부분을 발견했으니 label 을 props로 넘겨주어 범용성 있는 코드로 수정해보자.

+)
text와 setText로 범용성 있게 추가로 수정이 필요하다.

- 인터페이스 측면에서 보면 왜 FilterText임?? text로 변경

`TextField.tsx` 컴포넌트에 와서

```ts
type TextFieldProps = {
 label : string;
  placeholder: string;
 text: string;
 setText :(value: string) => void; // 받는건 string 이고 반환은 없다.
}

export default function TextField({
  label,
  placeholder,
  text,
  setText,
}:TextFieldProps) {
...
}
```

이렇게 수정해준다.

</br>

---

`FilterText.test.tsx`

```ts
import { fireEvent, render, screen } from '@testing-library/react';

import TextField from './TextField';

test('TextField', () => {
  const label = 'Name';
  const text = 'Tester';
  // given : 준비과정
  const setFilterText = () => {

  };

  const setText = () => {

  };

  // when: ~를 하면
  render((
    <TextField
      label={label}
      placeholder="Input your name"
      text={text}
      setText={setText}
    />
  ));

 // then: ~어떻게 된다.
  ...
});

```

given 과 when 이 있는 상태이다.

하나씩 then을 테스트해보자.

#### input과 input의 value 검사하기

```ts
  const input = screen.getByLabelText(label);
  expect(input.value).toBe('Tester');
```

getByLabelText로 input을 잡고 그 input의 value에 'Tester'가 포함되는지 검사한다.

이렇게 하면 value에 `'HTMLElement' 형식에 'value' 속성이 없습니다.` 이런 경고가 뜬다.

다른 방법으로 검사를 해보자.

```ts
  screen.getByDisplayValue(text);
```

이렇게 하는 방법도 있다.

</br>

#### placeholer 검사하기

```ts
  screen.getByPlaceholderText(/name/);
```

---

setText 등 함수가 불렸는지 호출됐는지 테스트로 확인하고 싶다.

input에다가 뭔가를 입력하면 `fireEvent`라는 걸 쓸 수 있다.

입력에는 여러가지 event들이 있는데 이번에는 `change` 이벤트를 써보겠다.

target 이 부분은 외워야함...

```ts
  // when
  fireEvent.change(screen.getByLabelText(label), {
    target: { value: 'New Name' },
  });
```

#### 다음으로 setText 함수가 called 됐는지 확인하는 방법

```js
let called = false
```

해놓고 `setText` 함수 내에서 `called = true` 라고 하는 방법도 있지만 더 좋은 방법이 있다.

#### jest.fn()

```js
const setText = jest.fn();

// 함수내에서 무슨일이 일어났는지를 써주고 싶으면 이렇게...
const setText = jest.fn(()=>{
 //~~
})
```

모킹한다고 하는데 가짜로 setText를 확인할 수 있는걸 만들어준다.

setText가 불렸는지 확인할 수 있음

setText가 called 됐는지 여부를 바로 확인해보자

```ts
  // then: ~어떻게 된다.
  // called 됐는가?
 expect(setText).toBeCalled();
```

called 됐으니 통과.

</br>

setText가 뭐로 called 됐는가? 인자값.

```js
  const setText = jest.fn();

  // when: ~를 하면
  render((
    <TextField
      label={label}
      placeholder="Input your name"
      text={"New Name"}
      setText={setText}
    />
  ));

 // 실패
 expect(setText).toBeCalledWith(1, 3);
 // 성공
 expect(setText).toBeCalledWith("New Name"));
```

테스트 통과 못함.
왜냐면 value에 "New Name"을 넣어서 render 해줬기 때문.
근데 테스트 기대값은 1, 3 ???

실패 메시지

```.
TextField

    expect(jest.fn()).toBeCalledWith(...expected)

    Expected: 1, 3
    Received: "New Name"
```

---

### context

~ 했을 때

지금 하다 보니 두가지 경우가 있음.

이거는 context로 분리를 해줘야함

```ts
import { fireEvent, render, screen } from '@testing-library/react';

import TextField from './TextField';

const context = describe;

describe('TextField', () => {
  const label = 'Name';
  const text = 'Tester';

  // given : 준비과정
  const setText = jest.fn();
  beforeEach(() => {
    jest.clearAllMocks();// 잡아놨던거 다 클리어
  });

  // 크게 겹치는 렌더 하는 부분을 따로 빼준다.
  function renderTextField() {
    render((
      <TextField
        label={label}
        placeholder="Input your name"
        text={text}
        setText={setText}
      />
    ));
  }

  it('renders elements', () => {
    // when: ~를 하면
    renderTextField();
    // then: ~어떻게 된다.
    screen.getAllByDisplayValue(text);
    screen.getByPlaceholderText(/name/);
  });

  // context : 입력했을 때
  context('when user enters name', () => {
    it('calls setText handler', () => {
      // given
      renderTextField();
      // when
      fireEvent.change(screen.getByLabelText(label), {
        target: { value: 'New Name' },
      });

      // then
      expect(setText).toBeCalledWith('New Name');
    });
  });
});

```

### BeforeEach - 테스트 하나하나 실행할 때마다 먼저 실행되는 것

1. 초기화

```ts
  const setText = jest.fn();

  beforeEach(() => {
    setText.mockClear(); // setText만 call 됐던것들 기록하고 있던것들 다 지워준다
    jest.clearAllMocks(); // 잡아놨던거 다 클리어
  });
```

setText 처럼 여러번 쓰는 함수는 어디서든지 한번이라도 call 하게 되면 어디서든 call 한거로 인식한다.

그러면 안되기 때문에 한번 불리고 나서 beforeEach로 초기화 해준다.

2. context 에서 given이 it 안에 있는게 싫어..

```ts
 // context : 입력했을 때
  context('when user enters name', () => {
    beforeEach(() => {
      // given
      renderTextField();
    });
    it('calls setText handler', () => {
      // when
      fireEvent.change(screen.getByLabelText(label), {
        target: { value: 'New Name' },
      });

      // then
      expect(setText).toBeCalledWith('New Name');
    });
  });
```

이렇게 beforeEach로 빼줄 수 있다.

반복되는 코드 분리하고 fireEvent를 통해 인터렉션만 검증한다.

비지니스 로직에 가까운 것들 (숫자만 입력하게 하는 그런 기능..)

-> 이거는 setText에서 하면 된다. 그러면 컴포넌트로부터 해당 로직이 분리되는 것.

---

swc 가 type을 검증을 안하는데 이걸 검증하게 하려면

```bash
npx tsc --noEmit
```

원래는 컴파일한 결과가 js로 생기는데 안생기게함.

그럼 잘못된걸 찾을 수 있음 type 관련해서..

---

### 외부 의존성이 큰 코드 - API CALL 검증하는 방법

백엔드 서버가 없는경우...

이런식으로 목데이터를 만들어서 쓸 수 있다.

`App.test.tsx`

```tsx
import { render, screen } from '@testing-library/react';
import App from './App';

jest.mock('./hooks/useFetchProducts', () => () => [
  {
    category: 'Fruits', price: '$1', stocked: true, name: 'Apple',
  },
]);

test('App', () => {
  render(<App />);

  screen.getByText('Apple');
});

```

만약 저 목데이터가 여러곳에서 쓰인다면?

`src > fixtures > products.ts` 만들고

```ts
const products = [
  {
    category: 'Fruits', price: '$1', stocked: true, name: 'Apple',
  },
]

export default products;
```

`src > fixtures > index.ts` 만들고

```ts
import products from './products'

export default {
 products
}
```

`App.test.tsx`

```tsx
import fixtures from '../fixtures';

jest.mock('./hooks/useFetchProducts', () => () => fixtures.products);
```

이렇게 바꿔줄 수 있다.

---

다른 방법은

hooks > `__mocks__` > useFetchProducts.ts

```ts
import fixtures from '../../../fixtures';

const useFetchProducts = jest.fn(() => fixtures.products);

export default useFEtchProducts;
```

`App.test.tsx`

```tsx
jest.mock('./hooks/useFetchProducts');
```

이렇게 쓸 수 도 있다.

근데 부모 컴포넌트인 경우 많은 useFetch어쩌구를 쓸 수 있다.

이런 경우 너무 복잡해지는데 그런 부분을 효율적으로 처리하는 방법을 `MSW`를 쓸 수 있다.
