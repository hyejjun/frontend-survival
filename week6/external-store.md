# External Store

## 학습 키워드

- 관심사의 분리
- Layered Architecture
- Flux Architecture
- useReducer
- useCallback

---

1. 용어의 정의(정확한 정의)
2. 역사 또는 왜 필요한가/왜 생겼는가
3. 특징 (또는 장/단점)
4. 실제 사용 사례나 경험

---

### 관심사의 분리

1. 용어의 정의

 : 관심사 분리(separation of concerns, SoC)는 컴퓨터 프로그램을 구별된 부분으로 분리시키는 디자인 원칙으로, 각 부문은 개개의 관심사를 해결한다.

2. 왜 필요한가? + 장점

 : 관심사 분리를 통해 프로그램 설계, 배포, 이용에 있어 자유도가 높아진다.
 일반적으로 코드의 단순화, 유지보수, 코드 재사용이 용이해진다는 장점이 있다.

3. 특징

 : Input -> Process -> Output [3단계]로 코드를 적절히 분리하면 코드 이해 + 유지 보수가 쉬워짐.

 하나의 OutPut은 사용자에게 또 다른 하나의 Input을 요청하게 됨. (계속 순환)

4. 실제 사용 사례나 경험

- 작은 컴포넌트들을 합쳐서 하나의 큰 컴포넌트를 만드는 것
- 각자 컴포넌트의 역할은 다름. 각자의 관심사가 다르다는 것
- 이런걸 `관심사를 분리` 한다고 함.

예를 들어서
> App
>> Header
>>
>> SearchBar
>>
>> Posting
>>
>> Footer

이런식으로 컴포넌트가 나뉜다면 Header는 SearchBar가 하는일에는 전혀 관심이 없다는 것이다.

그렇다면 어떤 기준으로 관심사를 분리할 수 있을까?

---

### Layered Architecture

-> Layered Architecture에서는 사용자에게 가깝고 먼 것을 기준으로 잡고 관심사를 분리한다.

1. 용어의 정의

 : 계층이 나뉘어져 있는 아키텍쳐이다. 수평 계층으로 구성되며 각각의 레이어는 하나의 관심사에만 집중할 수 있도록 하는 것이다.

2. 왜 필요한가? + 장점

 : 각 레이어를 loosley coupling 된 형태로 구축하면서, 각자 자신의 관심사에만 집중할 수 있음.

 비지니스 로직을 순수하게 유지함으로써 유지보수와 확장성 측면에 좋음.

 코드 재사용성을 높일 수 있음

3. 특징

 위에서 같이 3계층으로 나눠보자.

- Presentation Layer

  : 사용자가 접근할 수 있는 UI

- Application Layer

  : Front-end
  Presentation Layer에서 사용자와 상호작용하면서 수집된 데이터를 처리하고 API를 통해 Data Layer와 통신하는 계층

- Data Layer

  : Back-end
  Application Layer에서 처리된 데이터를 저장하고 관리하는 계층

4. 실제 사용 사례나 경험

확장성 보다는 일관성을 가져가는 것이 목표인 App / Web에 적합한 경우가 많다.

---

### Flux Architecture

1. 용어의 정의(정확한 정의)

단방향 데이터 플로우를 강조하는 아키텍쳐

2. 역사 또는 왜 필요한가/왜 생겼는가

MVC의 대안으로 나왔고 Model-View의 복잡한 관계를 해결하기 위해 탄생함.

#### MVC 아키텍쳐의 한계

![Alt text](https://velog.velcdn.com/images/alskt0419/post/35a7a12e-4f0d-416c-889c-92bdeca47dbb/image%20%281%29.png)

Controller :  Model의 데이터를 조회 / 업데이트 하는 역할

Model : 이런 데이터를 View에 반영함

View : 사용자로부터 데이터를 입력받고 그 입력이 Model에 영향을 줌.

=> 거대한 어플리케이션을 대상으로 한 프로젝트에서는 복잡하다는 것.

3. 특징 (또는 장/단점)

`unidirectional data flow`를 강조함.

![Alt text](https://haruair.github.io/flux/img/flux-simple-f8-diagram-with-client-action-1300w.png)

Action : 이벤트나 메시지 같은 객체이다.

Dispatcher : 여러 Store로 Action을 전달한다. 메시지 브로커와 유사함.

Store (여러개) : 받은 Action에 따라서 상태를 변경하고 그리고 이 Store를 구독하고 있는 (변화가 있는지 없는지) View에게 상태 변경을 알린다.

View : Store의 상태를 반영한다. 그리고 View에서 어떤 Action이 있으면 그게 전달되서 또 상태가 변경된다.

새로운 데이터를 넣으면 처음부터 다시 시작되는 방식으로 설계됨

4. 실제 사용 사례나 경험

#### Redux

-> Flux와 비슷하지만 Reducer 개념을 추가해서 상태 변경 개념 자체를 바꿔버림.

단일 스토어를 사용하게 되어 좀 더 단순한 그림을 제안함.

__Action__

__Store__

-> dispatch를 통해 Action을 전달받음. 사용자가 정의한 Reducer를 통해 상태를 새걸로 (통채로) 변경함.

```js
const state = {
 name : 'test'
}

const nextState = {
 ...state,
 name : 'New name'
}
```

__View__

-> Store에서 상태를 얻어서 반영함.

Action을 어떻게 표현하느냐가 사용성에 큰 차이를 만들지만 상태를 UI에 반영하는 방법(React를 사용)은 모두 동일함.

- Input → Action + dispatch
- Process → reducer
- Output → View(React)

이런 느낌이다.

---

### External Store란?

원래 우리는 상태를 useState 같은 Hook을 이용해서 처리했는데

이제는 이거를 외부에서 처리하는걸로 해보자..

일반적인 아키텍쳐에서 보면 UI가 가장 바깥이다.

여기서 말하는 External은 React 안에 있지 않다는 의미이다.

더이상 useState로 관리를 안할거라는 뜻!!

근데 상태가 변경이 안되면 state가 코드 내부상에서 변경이 되어도 화면에 반영이 안된다.

그래서 외부에서 상태를 관리하고 강제 업데이트 (forceUpdate)를 사용해서 상태가 변경되었음을 알릴 수 있다.

- React : UI를 담당
- 순수 TypeScript(JavaScript)가 비지니스 로직 담당

=> 관심사의 분리가 가능해짐

__테스트 코드는 자주 바뀌는 UI 대신 오래 유지되는 비지니스 로직에 대해 작성하면 유지보수에 도움이 된다.__

---

### React - UI와 비지니스 로직 분리하기

1. forceUpdate 함수를 만든다.

모든 것들을 useState로 관리하는 대신 내가 사용할 것들은 전역변수로 설정해둔다. 이 값을 업데이트 할때는 ts 나 js를 활용하여 업데이트 한다. (당연히 뒤에서는 업데이트 되는걸 확인할 수 있다. console.log 같은거로) 근데 이 변수의 변화를 화면에도 표출하고 싶다.

화면이 리렌더링 되는 것은 상태가 변화할때이다.

하지만 이 변수들은 상태 값이 아니기 때문에 변수가 변화한다고 해도 화면에 표출되지 않는다.

그래서 forceUpdate 를 만들어서 호출하면 상태 값을 변하게 해서 강제로 업데이트 하게 만들어보는것으로 간단하게 로직 분리를 통한 상태 관리 예제를 살펴 볼 수 있다.

```tsx
import { useState } from 'react';

let count = 0;

export default function App() {
  const [, forceUpdate] = useState({});

  const handleClick = () => {
    count += 1;
    forceUpdate({});
  };

  return (
    <div>
      <div>
        count: {count}
      </div>
      <button type="button" onClick={handleClick}>Increase</button>
    </div>
  );
}
```

`forceUpdate({});`

이렇게 하면 값이 이전하고 똑같은데 왜 업데이트가 되는거지?

바로바로 객체를 새걸로 다시 그려주기 때문이다.

그래서 상태가 변한것으로 인식하고 화면이 리렌더 된다.

forceUpdate의 상태값 자체는 우리가 사용할 일이 없어서 저렇게 생략해주었다.

</br>

2. 이제 진짜 UI(컴포넌트) 와 비지니스(forceUpdate 함수) 로직을 분리한다.

`/src/hooks/useForceUpdate.tsx`

```tsx
import { useState } from 'react';

export default function useForceUpdate() {
  const [, setState] = useState({});

  const forceUpdate = () => setState({});

  return forceUpdate;
}
```

`src/components/Counter.tsx`

```tsx
import useForceUpdate from './hooks/useForceUpdate';

let count = 0;

function increase() {
  count += 1;
}

export default function App() {
  const forceUpdate = useForceUpdate();

  const handleClick = () => {
    increase();
    forceUpdate();
  };

  return (
    <div>
      <div>
        count:
        {count}
      </div>
      <button type="button" onClick={handleClick}>Increase</button>
    </div>
  );
}
```

비지니스 로직을 hooks형태로 만들어서 분리한다.

그러면 아래 컴포넌트에서는 useState를 사용 하지 않아도 되고

테스트 코드를 작성할때 증가에 대한 테스트는 비지니스 로직에서만 테스트 하면 된다.

화면단에서는 increase 함수가 호출 되는지만 확인하면 된다.
