# useRef & Custom Hook

[학습 목표]

- useRef
- Hook의 규칙

---

## useRef

> [beta 문서의 useRef](https://beta.reactjs.org/reference/react/useRef)

-> useRef 공부할때는 여기서 확인하는걸 강추!!

useRef는 값을 참조할 수 있는 리액트 훅인데.. 렌더링에 관계없이 뒤에서 움직이는 것

- 컴포넌트 생애주기 전체에 걸쳐서 유지되는 객체 (= 컴포넌트가 없어질 때까지 동일한 객체가 유지됨)
- 객체 자체가 값은 아니고 `참조`하기 위한 객체이다. (= 언제든지 값을 변경할 수 있다.)

```tsx
const ref = {
 value: 1,
}
// 요 안에 있는거를 바꿀 수 있다.

ref = 'asdfasdf' // X

ref.value += 1 // O
```

저 ref 객체를 Component 바깥으로 뺀다면?
이 ref를 포함한 컴포넌트를 n번 호출/사용 하는 경우, +1 이 n번 된다.

그래서 호출할때 마다 각각(각자) ref를 쓰게 하고 싶으면

```jsx
const ref = useRef(1);
ref.current += 1;
```

이런식으로 작성해줘야 한다.

- ref 값(current)이 업데이트 된다고해서 렌더링 되지 않는다.
  - 만약 ref 값을 화면에 표출했다면 값이 변경되어도 화면에 나타나는 변화가 없을 것이다.
  - ref 값 이외에 다른 state 값이 변경되어 화면이 리렌더링 되었다면 그때 변경된 ref 값을 확인 할 수 있다.

### useRef는 언제 사용하나요?

- ref 로 값을 참조할 때
- DOM을 조작할 때
- ref 값을 재생성 하고 싶지 않을 때

> 구체적인 예를 들자면?

#### 1. 컴포넌트가 사라질 때까지 동일한 값을 써야 하는 경우

- input 과 label 을 연결하는 id를 관리하는 등..

TextField 컴포넌트를 여러번 쓰게 되면 id 가 컴포넌트 내부에서 useRef를 통해 선언되어야 관리가 된다.

```jsx
export default function TextField () {
 const id = useRef(`input-${Math.random()}`);
 ...
 return(
  <div>
   <label htmlFor={id.current}>
   <input id={id.current}>
  </div>
 )
}
```

그럼 TextFiled 컴포넌트를 사용할때 마다 저 id가 각각 적용된다.

그리고 state가 변경되어도 id ref 값에 영향을 주지 않는다.

#### 2. useEffect 에서 사용하는 비동기 통신에서 현재 값을 제대로 쓰고 싶은 경우

- 쓸 일이 많지는 않음..

- Closure : 변수를 capture, bind를 깜빡하는 문제가 종종 일어남..
비동기 통신 하는 상황에서..

state를 useEffect 내부에서 비동기 통신 (setTimeout)같은걸로 실행하면 state를 변경해도 변경된 값이 담기지 않고 초기값이 표시된다.

그런데 ref를 사용하면 현재시점의 값을 참조하기 때문에 현재 ref.current 값을 얻을 수 있다.

---

## Custom Hook

자신만의 Hook을 만들어서 로직을 재활용 할 수 있다.

- 함수로 만들어서 사용하면 된다.

- Hook을 만들때는 "use" 로 시작하는 camelCase로 이름을 붙인다.
  - (컴포넌트를 만들때는 PascalCase로 이름을 붙임.)

Hook 안에서 로직 처리를 다 하고 어떠한 값이나 함수만 리턴해주게끔 설계하기 때문에

컴포넌트 코드도 명확해지고 컴포넌트 내부에서 setState 관련 함수가 실수로 쓰일 것을 방지할 수 있다.

```tsx
// Cutom hook - 비동기 통신 하는 부분을 custom hook으로 뺀 것.
// src/hooks 폴더에서 따로 관리해주자.
export default const useFetchProducts = () => {
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
  return products;
};

export default function App() {
  // Custom hook 사용하기
  const products = useFetchProducts();

  return (
    <FilterableProductTable products={products} />
  );
}
```

---

## Hook 사용 규칙

### 1. 최상위 에서만 Hook을 호출해야 한다

- 반복문, 조건문, 중첩된 함수 내에서 Hook을 호출하면 안된다.
- Function Component 바로 안쪽(최상위)에서만 호출 가능.

### 2. 오직 React 함수 내에서 Hook을 호출해야 한다

- Hook을 일반적인 JavaScript 함수에서 호출하면 안된다.
- Function Component 또는 Custum Hook 에서만 호출 가능.
