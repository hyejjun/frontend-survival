# usestore-ts

```bash
npm i usestore-ts
```

상태관리를 위해 usestore-ts를 사용해보자.

## 관심사의 분리

이걸 제대로 적용할 수 있도록한다.

아키텍쳐는 UI 와 비지니스 로직으로 나뉘고,

어떻게 상태를 보여줄지가 비지니스 로직으로 가야 한다.

리액트가 할거는 UI임.

리덕스가 대세라서 이거를 많이들 쓰는데 단점도 있다..

### 리덕스 단점

1. 리덕스에서 js plain object의 사용이 강제가 됨

- 상태처리에 대해 순수 함수로 작성을 해야 하기 때문에 난이도가 있다.

- 자연스레 class를 쓰고 싶어짐
	- 객체 지향의 사고방식에 익숙하고 효과적이기 때문

- 현대의 프로그래밍 언어들은 각 좋은 것만 취사 선택
	- js든 ts는 map이나 reduce 등 고차함수 기능을 잘 활용하고 우리한테 익숙한 class도 함께 사용하자는 것

2. 거대해지는 단일 스토어

- 복잡성 증가, 개발 관심사에 대한 관리가 어려워짐

- 여러 대안들.. `Micro Store` (recoil, zustand)들이 나오고 있음. 이런 것들을 앞으로 활용하게 될 것.

---

### 그러면 이런 Micro Store을 어떻게 만들까?

- Store는 리액트 입장에서는 외부에 있는 존재이다.
	- 하지만 S/W 관점에서는 매우 안쪽에 있고 핵심부이다.

- 전역 상태 라는 말이 외부에 있는것 처럼 느끼게 하지만 이 External Store를 보다 신경써서 만들어야 한다.

- UI는 변경이 잦고 테스트가 어려운 반면 External Store는 외부 의존성이 낮은 순수한 js/ts에 가깝기 때문에 테스트를 하기 더 좋다.

- 리액트를 External Store와 연결해보자...
	- 리액트에서 제공하는 useSyncExternalStore 활용한다..

---

## usestore-ts 활용하기

리덕스는 단일 스토어라서 selector를 만들어서 쓸 수 밖에 없었음

근데 이거는 그냥 스토어 여러개 만들어서 쓰자! 이런 것..

(예시)

### 스토어: CounterStore.ts

`stores/CounterStore.ts`

```ts
import { singleton } from 'tsyringe';

import { Action, Store } from 'usestore-ts';

// 순서는 항상 싱글톤 먼저오게 한다.
@singleton()
@Store()
export default class CounterStore {
  count = 0;

  // object를 넣었을때 주의할점
  state = {
    x: 1,
  };

 @Action()
  increase(step = 1) {
    this.count += step;
  }

 @Action()
 decrease(step = 1) {
   this.count -= step;
 }

 // 새 객체를 그려주는 형식으로 써줘야함.
 @Action()
 changeX() {
   this.state = {
     ...this.state,
     x: this.state.x + 1,
   };
 }
}

```

#### 비동기 함수 - 주의할 점

@Action은 발행이다.

@Action은 원래 있는 함수 바로 리턴하게 함. 그래서 Promise 상태가 바로 리턴 되기 때문에
비동기 함수는 @Action을 붙이지 말고 start 와 compelete 에다가 @Action 붙여서 처리해주면 된다.

### 커스텀 Hook 작성 : useCounterStore.ts

`/hooks/useCounterStore.ts`

```ts
import { container } from 'tsyringe';

import { useStore } from 'usestore-ts';

import CounterStore from '../stores/CounterStore';

export default function useCounterStore() {
  const store = container.resolve(CounterStore);

  return useStore(store);
}
```

### 컴포넌트에서는 어떻게 사용하지?

`/components/Counter.tsx`

```tsx
import useCounterStore from '../hooks/useCounterStore';

export default function Counter() {
  // 앞에가 state, 뒤에가 store
  // const [state, store] = useCounterStore();
  /*
  state에서는 안에 있는 상태값을 사용하고
  store에서는 action을 사용한다.
 */

  // state 안에 있는 상태값을 부를때 이렇게 할 수 있다. (이렇게 많이 함)
  const [{ count }] = useCounterStore();

  return (
    <div>
      <div>
        count:
        {count}
      </div>
    </div>
  );
}

```

`components/CounterControl.tsx`

```tsx
import useCounterStore from '../hooks/useCounterStore';

export default function CountControl() {
  // 앞에 state 값은 생략도 가능하다.
  const [, store] = useCounterStore();

  // state.count를 바로 꺼내와서 쓰기
  // const [{ count }, store] = useCounterStore();

  return (
    <div>
      <div>
        count:
        {store.count}
      </div>
      <button type="button" onClick={() => { store.increase(); }}>
        Increase
      </button>
      <button type="button" onClick={() => { store.increase(10); }}>
        Increase 10
      </button>
      <button type="button" onClick={() => { store.decrease(); }}>
        Decrease
      </button>
      <button type="button" onClick={() => { store.decrease(10); }}>
        Decrease 10
      </button>
    </div>
  );
}
```
