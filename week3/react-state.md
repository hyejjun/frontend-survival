# React State 상태 관리

[목표]

- React state란?
- DRY 원칙
- SSOT(Single Source of Truth)
- useState
- 1급 객체(first-class object)란?
- Lifting State Up

---

[thinking in react](https://beta.reactjs.org/learn/thinking-in-react)

STEP

3. 최소한의 것. 하지만 완벽한 UI 상태의 표현을 찾자.
4. 어디있게 할건지 결정한다.
5. 데이터 플로우에 대해서 inverse 하는걸 넣는다...? 이건 뭐냐

---

## React의 State

데이터가 있다고 해서 다 state 가 되는게 아니다.

state는 __변경__ 을 다루기 위한 것.

어떤 컴포넌트의 state가 변경되면 해당 컴포넌트 & 하위 컴포넌트를 리렌더링 한다.

### [state 관리에 대한 원칙]

#### 1. DRY

리액트는 가능하면 DRY 원칙을 따르라고 한다.

- [DRY (Don’t Repeat Yourself)](https://ko.wikipedia.org/wiki/중복배제)

- 중복 배제 (Don't repeat yourself; DRY)

- 모든 형태의 정보 중복을 지양하는 원리

#### 2. SSOT

- [SSOT (Single Source of Truth)](https://ko.wikipedia.org/wiki/단일_진실_공급원)

- 모든 데이터를 한 곳 에서만 관리

</br>

### [state가 될 수 있는 조건]

1. __변경 돼야 함__ : 변경되지 않는 건 state로 다룰 가치가 없다.
2. 부모 컴포넌트에서 전달하는 props는 state가 아니다.
3. 다른 state나 props를 이용해 계산 가능하다면 state가 아님. (제일 중요)

```tsx
function Form() {
  const [firstName, setFirstName] = useState('Taylor');
  const [lastName, setLastName] = useState('Swift');

  // 🔴 Avoid: 불필요한 state와 Effect
  const [fullName, setFullName] = useState('');
  useEffect(() => {
    setFullName(firstName + ' ' + lastName);
  }, [firstName, lastName]);

 // ✅ Good: 렌더링 하는 동안에 계산되기 때문에 이렇게 작성하자.
  const fullName = firstName + ' ' + lastName;
}
```

-> 이렇게 만들면 안된다!!!

[(공식문서) useEffect가 필요하지 않을걸..?](https://beta.reactjs.org/learn/you-might-not-need-an-effect)

</br>

### [상태 관리는 어떻게 하는가?]

상태(state)가 너무 많으면 복잡함.

TypeScript 를 사용해서 관리하는 상태의 수를 줄일 수 있다.

상태를 값을 하나씩 다룰 수 있는데 이걸 묶어 줄 수 있다. ts는 재사용할 때 굉장히 편함.

js는 불안함. object가 있는데 뭐가 들었는지 모르니까 props로 하나씩 전달하는게 오히려 안전함.

</br>

### [상태는 어떤 컴포넌트가 소유해야 하는가?]

상태는 누가 소유/관리 해야 하는가? (어떤 컴포넌트가 소유/관리 해야 하는가?)

기본적으로 이 상태를 사용하는 컴포넌트가 소유한다. 그러니까 이 상태에 의존하는 컴포넌트 중에 최상위 컴포넌트에서 관리하는 것이 좋다.

[컴포넌트 구성도]
>부모
>>자식1
>>
>>자식2

자식1과 자식2에서 모두 필요한 상태인 경우 부모에게서 해당 상태를 얻어야 한다.

그래서 최상위 컴포넌트에서 상태를 관리하는 것이 좋다.

- 상태를 관리하는 컴포넌트를 최상위로 올리는 작업
  - [“Lifting State Up”](https://ko.reactjs.org/docs/lifting-state-up.html)

- 자식 컴포넌트에서 공통적으로 쓰이는 상태가 있을 때 관련 부모 컴포넌트로 상태를 올려서 공통으로 상태를 사용하는 방법
  - [Sharing State Between Components](https://beta.reactjs.org/learn/sharing-state-between-components)

---

## Inverse Data Flow

하위 컴포넌트의 props로 함수를 전달한다.

- 콜백함수라고 부른다.
- props로 변수를 전달했던 것 처럼 함수도 전달이 가능하다.
- setState 기능을 하는 함수도 그대로 전달이 가능하고 아니면 setState를 조작하는 함수를 따로 만들어서 그 함수를 props로 전달 할 수도 있다.
- 강의에서는 setState를 그대로 넘겨준다.

자식 컴포넌트에서 상태를 변경할 수 있게 된다.
부모에서 뭔가를 얻어야 하는 경우

[일급 함수](https://developer.mozilla.org/ko/docs/Glossary/First-class_Function)
