# TSyringe

__학습 키워드__

- TSyringe
- 의존성 주입(Dependency Injection)
- reflect-metadata
- sington (싱글톤)

---

## TSyringe란?

TypeScript용 `DI 도구`. External Store를 관리하는데 활용 가능하다.

## DI (Dependency Injection) 의존성 주입

의존성 주입이라는게 뭔가...?

- 의존성 : 어떤 함수, 클래스 등이 내부에 다른 함수, 클래스를 사용함
- 주입 : 어떤 함수, 클래스 등이 내부에 사용하는 다른 함수, 클래스를 내부에서 생성하는 것이 아니라 외부에서 생성하여 넣어주는 것을 '주입'이라 함

## TSyringe 활용

앞으로 우리는 class를 사용해서 Store를 만들어 볼 것이다.

React 컴포넌트 입장에서는 `전역` 변수처럼 느껴짐

`"Props Drilling"` 문제를 해결할 수 있는 방법 중 하나다.

=> props가 부모로부터 계속 내려가는 그것...

중간 컴포넌트는 이 props를 사용하지 않아도 받아서 넘겨줘야 하는 불편함이 있다.

그래서 이런걸 해결해야하는데...

Context는 전체를 바꿔야 하기 때문에 조금 비효율적이다..

우리는 간단하게 TSyringe를 이용하게 처리를 할 것.

이걸 사용하려면 TSyringe와 얘가 내부적으로 사용하고 있는 reflect-metadata를 설치한다.

```bash
npm i tsyringe reflect-metadata
```

싱글톤으로 관리할 CounterStore 클래스를 준비하기 위해 파일을 생성한다.

```bash
mkdir src/stores
touch src/stores/CounterStore.ts
```

singleton 은 전역에서 하나! new () 를 해주지 않아도 사용이 가능함. 객체 생성 알아서함

`src/hooks/useForceUpdate.tsx`

```tsx
import { useState, useCallback } from 'react';

export default function useForceUpdate() {
  const [, setState] = useState({});

  const forceUpdate = useCallback(() => {
    setState({});
  }, []);

  return forceUpdate;
}
```

`src/stores/CounerStore.ts`

```tsx
import { singleton } from 'tsyringe';

type Listener = () => void;

@singleton()
export default class CounterStore {
  count = 0;

  listeners = new Set<Listener>();

  // 발행 : listener들한테 바뀌었다고 알려줌.
  publish() {
    this.listeners.forEach((listener) => {
      listener();
    });
  }

 // Store 외부에서 store.listeners.add(forceUpdate) 하던거를 캡슐화 하기 위해서 addListener라는 함수를 생성함
  addListener(listener: Listener) {
    this.listeners.add(listener);
  }

  removeListener(listener: Listener) {
    this.listeners.delete(listener);
  }
}

```

`src/components/Counter.tsx`

```tsx
import { useEffect } from 'react';
import { container } from 'tsyringe';

import useForceUpdate from '../hooks/useForceUpdate';

import CounterStore from '../stores/CounterStore';

export default function Counter() {
  // CounterStore 사용하기
  const store = container.resolve(CounterStore);

  // useForceUpdate hooks를 사용해서 forceUpdate 함수 가져오기
  const forceUpdate = useForceUpdate();

 // forceUpdate를 useCallback 처리를 해놔서 무한정으로 업데이트 되지는 않지만 이렇게 처리를 해준다.
 // forceUpdate || store 가 변화할때 이 부분이 동작함
  useEffect(() => {
    // 처음에 addListener 함수를 통해서 foreUpdate 기능을 추가
    // (구독) 너가 업데이트 되면 나한테 알려줘, 알려주는데 그게 뭐임? forceUpdate. 그럼 나는 리렌더 함
    store.addListener(forceUpdate);

    // 이 컴포넌트가 사라질때 forceUpdate 기능을 제거함
    return () => {
      store.removeListener(forceUpdate);
    };
  }, [forceUpdate, store]);

  return (
    <div>
      <div>
        count:
        {store.count}
      </div>
    </div>
  );
}

```

`src/components/CounterControl.tsx`

```tsx
import { container } from 'tsyringe';

import CounterStore from '../stores/CounterStore';

export default function CountControl() {
  // CounterStore 사용하기
  const store = container.resolve(CounterStore);

  const handleClickIncrease = () => {
    store.count += 1;
    store.publish(); // 바뀌었다고 알려줌 (발행)
  };

  const handleClickDecrease = () => {
    store.count -= 1;
    store.publish();
  };

  return (
    <div>
      <button type="button" onClick={handleClickIncrease}>Increase</button>
      <button type="button" onClick={handleClickDecrease}>Decrease</button>
    </div>
  );
}

```

이렇게 기본이 된다.

---

### 테스트 코드 작성하기

`App.tsx`

```tsx
import Counter from './components/Counter';
import CountControl from './components/CountControl';

export default function App() {
  return (
    <div>
      <Counter />
      <Counter />
      <CountControl />
    </div>
  );
}
```

`App.test.tsx`

```tsx
import { render, screen, fireEvent } from '@testing-library/react';
import { container } from 'tsyringe';

import App from './App';

const context = describe;

test('App', () => {
  render(<App />);
});

describe('App test', () => {
  beforeEach(() => {
    // 테스트는 독립적이여야 하므로 Store에 있는 값들이 매번 초기화 되게 해준다.
    container.clearInstances();
  });

  context('when press increase button once', () => {
    test('counter', () => {
      render(<App />);

      fireEvent.click(screen.getByText('Increase'));

      expect(screen.getAllByText('count:1')).toHaveLength(2);
    });
  });

  context('when press increase button twice', () => {
    test('counter', () => {
      render(<App />);

      fireEvent.click(screen.getByText('Increase'));
      fireEvent.click(screen.getByText('Increase'));

      expect(screen.getAllByText('count:2')).toHaveLength(2);
    });
  });
});

```

---

`Counter.tsx`

```tsx
import { container } from 'tsyringe';

import { useEffect } from 'react';

import useForceUpdate from '../hooks/useForceUpdate';

import CounterStore from '../stores/CounterStore';

// 사실 얘네는 한 묶음이다. useStore로 묶어준다.
function useCounterStore() {
  const store = container.resolve(CounterStore);

  const forceUpdate = useForceUpdate();

  useEffect(() => {
    store.addListener(forceUpdate);

    return () => {
      store.removeListener(forceUpdate);
    };
  }, [forceUpdate, store]);
  return store;
}

export default function Counter() {
  const store = useCounterStore();

  return (
    <div>
      <div>
        count:
        {store.count}
      </div>
    </div>
  );
}
```

여기서 useCounterStore로 묶어준 것을 다른곳에서도 사용할 수 있게 useConterStore.tsx로 파일을 분리한다.

그리고 export default 처리함

그러면 다른 파일에서도 이렇게 사용이 가능하다.

```tsx
import useCounterStore from '../hooks/useCounterStore';

export default function CountControl() {
  // Store 사용하기
  const store = useCounterStore();

  const handleClickIncrease = () => {
    store.count += 1;
    store.publish();
  };

  const handleClickDecrease = () => {
    store.count -= 1;
    store.publish();
  };

  return (
    <div>
      <div>
        count:
        {store.count}
      </div>
      <button type="button" onClick={handleClickIncrease}>Increase</button>
      <button type="button" onClick={handleClickDecrease}>Decrease</button>
    </div>
  );
}
```

여기에 있는 store.count += 1; 이런것도 캡슐화 하고 싶다면??

`CounterStore.ts`

```ts
  increase() {
    this.count += 1;
    this.publish();
  }
```

스토어에 와서 캡슐화 하고

사용할때는

```ts
  const handleClickIncrease = () => {
    store.increase();
  };
```

이렇게 한다.