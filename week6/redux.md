# Redux 따라하기

-> 강의를 보고 실습한다.

리덕스가 마법이 아니라는 것...

이렇게 직접 (리덕스 형태로) 만들어서 사용이 가능하다는 것을 실습한다.

이런식으로 만들어서 써야하나?? -> 이럴거면 redux 씁니다..

---

1. Base store 만들기
2. Store 만들기

원래 리덕스는 Store 같은건 필요없다.

시린지는 객체만 넘겨주는데 리덕스는 위에서 내려주는건 컨텍스트로 넘겨질 수 있는데

초기값 지정해주는거

---

## Redux 를 따라서 만들어보자

`src/stores/BaseStore.ts`

```ts
import { singleton } from 'tsyringe';

export type Action<Payload> = {
  type : string;
  payload?: Payload
}

type Reducer<State, Payload> = (state: State, action: Action<Payload>) => State;

type Reducers<State> = Record<string, Reducer<State, any>>;

type Listener = () => void;

@singleton()
export default class BaseStore<State> {
  state : State;

  reducer : Reducer<State, any>; // payload를 미리 알 수 없음. action 마다 다를 수 있음

  listeners = new Set<Listener>();

  constructor(initialState: State, reducers: Reducers<State>) {
    this.state = initialState;

    this.reducer = (state: State, action: Action<unknown>) => {
      const reducer = Reflect.get(reducers, action.type);
      if (!reducer) return state;
      return reducer(state, action);
    };
  }

  dispatch(action: Action<any>) {
    this.state = this.reducer(this.state, action);
    this.publish();
  }

  // 발행
  publish() {
    this.listeners.forEach((listener) => {
      listener();
    });
  }

  addListener(listener: Listener) {
    this.listeners.add(listener);
  }

  removeListener(listener: Listener) {
    this.listeners.delete(listener);
  }
}
```

`src/stores/Store.ts`

```ts
import { singleton } from 'tsyringe';
import BaseStore, { Action } from './BaseStore';

const initialState = {
  count: 0,
  name: 'hyejun',
};

export type State = typeof initialState

const reducers = {

  // 리듀서가 하나였는데 여러개의 리듀서로 나누는것
  increase(state: State, action: Action<number>) {
    return {
      ...state,
      count: state.count + (action.payload ?? 1),
    };
  },

  decrease(state: State, action: Action<number>) {
    return {
      ...state,
      count: state.count - (action.payload ?? 1),
      name: `${state.name}.`,
    };
  },

};

// action creator
export function increase(step?: number) {
  return { type: 'increase', payload: step };
}

export function decrease(step?: number) {
  return { type: 'decrease', payload: step };
}

@singleton()
export default class Store extends BaseStore<State> {
  constructor() {
    super(initialState, reducers); // reducers를 통으로 준다
  }
}
```

`src/hooks/useDispatch.ts`

```ts
import { container } from 'tsyringe';

import { Action } from '../stores/BaseStore';

import Store from '../stores/Store';

export default function useDispatch<Payload>() {
  const store = container.resolve(Store);

  return (action: Action<Payload>) => {
    store.dispatch(action);
  };
}
```

`src/hooks/useDispatch.ts`

```ts
import { container } from 'tsyringe';

import { Action } from '../stores/BaseStore';

import Store from '../stores/Store';

export default function useDispatch<Payload>() {
  const store = container.resolve(Store);

  return (action: Action<Payload>) => {
    store.dispatch(action);
  };
}
```

`src/hooks/useSelector.ts`

```ts
import { container } from 'tsyringe';

import { useEffect, useRef, useState } from 'react';

import useForceUpdate from './useForceUpdate';

import Store, { State } from '../stores/Store';

type Selector<T> = (state: State)=> T;

export default function useSelector<T>(selector: Selector<T>): T {
  const store = container.resolve(Store);

  // 하나의 스토어 - 상태변화 영향 미침 - 각각의 상태
  const state = useRef(selector(store.state));

  const forceUpdate = useForceUpdate();

  useEffect(() => {
    const update = () => {
      // 특정조건이 맞으면 forceUpdate
      const newState = selector(store.state);
      // selector 결과가 객체인 경우 처리 필요함
      if (newState !== state.current) {
        forceUpdate();
        state.current = (newState);
      }
    };
    store.addListener(update);

    return () => {
      store.removeListener(update);
    };
  }, [forceUpdate, store]);

  return selector(store.state);
}

```

이렇게 redux 기능을 직접 만들 수 있다...

사용은?

`src/components/CounterControl.tsx`

```tsx
import useDispatch from '../hooks/useDispatch';
import useSelector from '../hooks/useSelector';

import { decrease, increase } from '../stores/Store';

export default function CountControl() {
  // 내가 액션 넘겨주길 원할때
  const dispatch = useDispatch();

  // 내가 상태를 얻기를 원할때
  const count = useSelector((state) => state.count);

  return (
    <div>
      <div>
        count:
        {count}
      </div>
      <button type="button" onClick={() => { dispatch(increase()); }}>Increase</button>
      <button type="button" onClick={() => { dispatch(increase(10)); }}>Increase 10</button>
      <button type="button" onClick={() => { dispatch(decrease()); }}>Decrease</button>
      <button type="button" onClick={() => { dispatch(decrease(10)); }}>Decrease 10</button>
    </div>
  );
}
```

Dispatch 와 Selector를 사용할 수 있다.