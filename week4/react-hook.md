# React의 Hook

[학습 목표]

- React Hook 이란
- Hooks
  - useState
  - useEffect
  - useContext
  - useRef
  - useLayoutEffect
- React StrictMode 란

---

## React Hooks

React 버전 16.8부터 React Hooks가 새로 추가되었다.

Hooks를 이용하여 기존 Class 컴포넌트를 사용할 필요 없이 상태 값과 여러 React의 기능을 사용할 수 있다.

이전에는 상태값을 관리하는 Class 컴포넌트들이 따로 있고 여기서 관리를 했다.

Hooks 도입 이후에는 모든 컴포넌트에서 상태관리가 가능해지며 컴포넌트 끼리 Hook을 공유하기 쉬워졌다.

### 기존 방식의 문제점

#### Wrapper Hell (HoC)

- Higher Order Component
  - 화면에서 재사용 가능한 로직을 분리해 컴포넌트로 만들고, 재사용 불가한 부분은 파라미터로 받아서 처리하는 방법.
    - 예를 들어 API를 통해 userList를 받아오고 이를 state에 저장하는 부분을 HOC 컴포넌트에 빼고 재사용할 수 있다. 그 HOC 컴포넌트로 userList가 필요한 각각의 컴포넌트를 감싸주는 식으로 구성했음.
    - 문제는 너무 많은 Wrapper 컴포넌트가 생길 수 있다는 것...
  - 로직의 재활용을 위해 많은 양의 wrapper componenet가 생겨서 wrapper hell을 초래함.

#### Huge Components

- 복잡한 컴포넌트는 이해하기 어려움
- 각각 라이프 사이클 매서드에 관련없는 로직이 혼합되어 있을 가능성이 있음.
  - componentDidMount 에서 이벤트 리스너를 사용한다던가
  - componenetWillUnmount에서 cleanup 로직을 수행하는 경우

- 각 라이프 사이클에서 수행할 로직이 많아질수록 어떤것을 cleanup 해줘야 하는지 수정해야하는지 어려움

#### Confusing Classes

- class 의 this 키워드
  - 함수 선언시점에 결정되는게 아니라 함수 실행 시점에 결정되는 값
  - 함수가 어떻게 실행되는지에 따라 변경될 수 있음
  - 사이즈가 큰 프로젝트에서는 this를 예상/추적하는 것에서 실수가 발생할 수 있음
- 그래서 class component는 최적화하기 어려움

예시는 대충 알겠는데 어떻게 복잡해진다는걸까...

class component를 깊게 사용해 본적이 없어서 잘 모르겠다..

---

암튼 이제는 함수형 컴포넌트 hooks 사용으로 완전히 바뀌었음.

돌아가는 것은 불가능..!

#### 기존

- 상태를 가진 컴포넌트 : Class Component 에서 관리
- 이외에 props만 사용하는 재사용 가능한 작은 컴포넌트 : Function Component로 작성

- Redux에서도 비슷한 구분이 존재했다. (이 부분은 추가로 보자..)

#### 현재

- Function Component만 사용
- 어느 컴포넌트에서 상태관리 해야하는지? -> 신경 쓰지 않아도 됨
- 복잡한 요소는 전부 Hook으로 뺐기 때문에 이거를 재사용 하면 됨.

---

### `대표적인 Hooks`

- useState
- useEffect
- useContext
- useRef
- useLayoutEffect (useEffect와 조금 다르다.)

---

### useEffect

- 외부 API에서 데이터를 가져오거나
- 브라우저 제목을 업데이트하거나
- DOM을 조작하는 등의 부수적인 효과를 관리하는 데 사용됩니다.
- 컴포넌트가 렌더링된 후에 동작을 수행할 수 있습니다.

렌더링할때마다 실행된다.

의존성 배열을 통해 언제 이펙트를 실행할지 지정이 가능함.

의존성 배열을 넣지 않으면 렌더링 할 때마다 실행된다.

- (의존성 배열이 빈 경우 componentDidMount 와 `비슷`한 거다... 동일 한 건 절대 아님)

함수를 리턴 (clean up)해서 종료할 수 있음.

### 예제)

#### 1. Timer를 document.title 에 넣어보자

```tsx
import { useEffect, useState } from 'react';
import CheckBoxField from './CheckBoxField';

export default function SearchBar() {
  const [search, setSearch] = useState<string>('');
  const handleChange = (event:React.ChangeEvent<HTMLInputElement>) => {
    const { value } = event.target;
    setSearch(value);
  };
  useEffect(() => {
    document.title = `Now: ${new Date().getTime()}`;
  });
  return (
    <div className="search-bar">
      <div>
        <input type="text" placeholder="Search..." value={search} onChange={handleChange} />
      </div>
      <CheckBoxField label="Only show products in stock" />
    </div>
  );
}

```

```tsx
  useEffect(() => {
    document.title = `Now: ${new Date().getTime()}`;
  });
```

이 예제에서는 useEffect에 의존성 배열을 넣지 않고 렌더링 될때마다

document.title 에 현재시각을 반영하도록 한다.

좀 더 명확하게 확인하기 위해 state를 사용하는 컴포넌트에서 useEffect를 사용해 보았다.

input에 값을 입력할때마다 title이 변한다.

state가 변경 될때 마다 리렌더 되고 useEffect가 실행되기 때문이다.

</br>

#### 2. Timer를 on off 하는 기능 넣어보자

```tsx
import { useEffect, useState } from 'react';

function Timer() {
  useEffect(() => {
    // 종료 처리를 위해 원래 title 값 저장함
    const before = document.title;

    const id = setInterval(() => {
      document.title = `Now : ${new Date().getTime()}`;
    }, 100);

    // 종료 처리 함수
    return () => {
      // 원래 title 값으로 변경
      document.title = before;

      // setInterval 종료 처리
      clearInterval(id);
    };
  });

  return <div>go</div>;
}

export default function TimerControl() {
  const [playing, setPlaying] = useState<boolean>(false);

  const handleClick = () => {
    setPlaying(!playing);
  };

  return (
    <div>
      {
        playing
          ? <Timer />
          : <div>stop</div>
      }
      <button type="button" onClick={handleClick}>
        Toggle
      </button>
    </div>
  );
}

```

useEffect 내부에서 종료 처리 함수를 만들지 않았더니 stop 이 되어도 title 이 이전으로 바뀌지 않았다.

흠.. 왜그럴까?

#### 3. useEffect 처음에 한번만 실행하기

useEffect 의존성 배열 없이 사용

주로 API 호출해서 데이터를 얻을 때 사용한다.

App.tsx에서 products를 API를 통해 받아오자.

-> 이 경우에는 useEffect를 사용한다.

```tsx
...
import Product from './types/Product';

export default function App() {
  const [products, setProducts] = useState<Product[]>([]);

  useEffect(() => {
    const fetchProducts = async () => {
      const url = 'http://localhost:3500/products';
      const response = await fetch(url);

      const data = await response.json();

      setProducts(data.products);
    };

    fetchProducts();
  }, []);

  ...
}
```

#### 1. API 통신을 통해 얻은 데이터를 담을 상태값을 만든다

- 그 상태는 products 인데 type은 이전에 정의한 Product[] 타입을 따른다.

#### 2. 빈 의존성 배열을 갖는 useEffect를 선언한다

#### 3. useEffect `내부`에서 API 통신 함수를 구현하고 호출한다

- 왜?? 내부에서 호출하는지...?
- 외부에 함수 구현하고 호출하면 안되는지?
  - 동작은 됨.
  - 내부에서 호출해야 하는 이유를 알아보자.

왜 내부에서 호출해야 하냐면...

함수가 외부에 있는 상태에서

나중에 fetchProducts 함수가 엄청 길어지고 이 함수가 state 나 props를 사용한다고 생각해보자.

이 함수에서 사용하는 state나 props를 useEffect deps 에서 빼먹는다면...?

- Effect는 props / state의 변화를 동기화 하지 못한다.

까먹으면 골치아파지니까 useEffect 내부에서 함수를 선언하자.

deps에서 빼먹은게 있다면 useEffect에서 뭐가 빠졌다고 알려주기 때문이다.

[useEffect - 함수를 Effect 안으로 옮기자.](https://overreacted.io/ko/a-complete-guide-to-useeffect/#%ED%95%A8%EC%88%98%EB%A5%BC-%EC%9D%B4%ED%8E%99%ED%8A%B8-%EC%95%88%EC%9C%BC%EB%A1%9C-%EC%98%AE%EA%B8%B0%EA%B8%B0)

---

## React.StrictMode

### Effect가 두 번 실행 되는 문제

<React.StrictMode>로 컴포넌트 전체를 감쌀 경우 Effect가 두 번 실행된다.

예상치 못한 Side Effect를 찾으려고 하기 때문.

평소에는 큰 문제가 없지만, API 등을 사용하면 이상하다고 느낄 수 있다.

개발할때만 적용되는 것이기 때문에 너무 신경쓰지 말고, 불편하다고 없애면 안된다.

제대로 되는건지 확인하고 싶다면 개발 중간에 잠시 <React.StrictMode>를 지우고 확인한 뒤 다시 붙이자.

- html 강의가 있음.. 이것도 들어보자...

- **[예상치 못한 부작용 검사](https://www.notion.so/HTML-0bf5a58a76714573946be792e38f53d5)**
