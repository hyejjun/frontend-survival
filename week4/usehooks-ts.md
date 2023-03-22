# usehooks-ts

> [usehooks-ts](https://usehooks-ts.com/)
>
>> Hooks를 어떻게 만들었는지 코드를 확인 할 수 있으니 참고해서 Custom Hook을 만들 때 사용할 수 있다.

```bash
npm install usehooks-ts
```

---

## 자주 사용하는 usehooks-ts의 hook들

### [useBoolean](https://usehooks-ts.com/react-hook/use-boolean)

true/false 의 boolean 값을 toggle 하는 기능을 가진 hook

```jsx
import { useBoolean } from 'usehooks-ts';

export default function TimerControl() {
  const { value: playing, toggle: togglePlaying } = useBoolean(false);

  return (
    <div>
      {
        playing
          ? <Timer />
          : <div>stop</div>
      }
      <button type="button" onClick={togglePlaying}>
        Toggle
      </button>
    </div>
  );
}

```

코드 작성하다가 return 값들이 뭐가 있는지 잘 모르겠으면 `cmd + useBoolean` 들어가서 상세 내용 확인 하면 된다.

</br>

### [useCounter]

```tsx
import { useCounter } from 'usehooks-ts';

export default function TimerControl() {
  const { count, increment, decrement } = useCounter(1);

  return (
    <div>
      <div>
        {count}
      </div>
      <button type="button" onClick={increment}>+</button>
      <button type="button" onClick={decrement}>-</button>
    </div>
  );
}

```

### [useEffectOnce](https://usehooks-ts.com/react-hook/use-effect-once)

useEffect에서 의존성 배열이 비어있는 상태
-> 명확하게 하기 위해 사용한다.

```jsx
useEffectOnce(()=>{
 console.log('처음 한번만 실행됨');
})
```

```jsx
useEffect(()=>{
 console.log('처음 한번만 실행됨');
},[])
```

</br>

### [useFetch](https://usehooks-ts.com/react-hook/use-fetch)

fetch 비동기 통신을 사용할 수 있는 hook
</br> -> 정말 간단히 쓸 때 좋음.

조금 더 복잡하지만 캐시 이슈까지 신경쓴 대안들...!

- [SWR](https://swr.vercel.app/ko)
- [TanStack Query](https://tanstack.com/query)

TanStack Query가 react-query 이다.

나는 react-query를 주로 사용..

SWR을 추가로 살펴보자.

</br>

### [useInterval](https://usehooks-ts.com/react-hook/use-interval)

React에서 setInterval 쓸 때 왠만하면 이 hook을 사용하자.

실수/오류를 줄여줌...

#### `setInterval` 이란?

- 일정한 시간이 지난 후에 원하는 함수를 호출하는 매서드.
- 주기적으로 실행해서 일정한 간격을 만들고 `clearInterval` 을 이용해서 함수 호출을 중단해야 한다.

#### `setInterval` 의 문제점

- 지연간격(delay)을 보장하지 못함.
- 3초 간격으로 지연간격을 설정해 놓아도 setInterval은 3초 간격 함수 호출을 하지 못할 가능성이 크다.
(뭔 소리징?? - 함수를 실행하는데 1.5초가 걸리면 setInterval은 1.5초만에 실행된다고 한다.)
- 이러한 특성 때문에 React에서 setInterval을 사용할 때 `클로저 / 트러블 슈팅` 오류를 만나게 된다.
  - 값이 바뀌어 갱신된 state 값을 가져오지 못하는 경우
  - [참고블로그](https://velog.io/@goldbear2022/%ED%81%B4%EB%A1%9C%EC%A0%80Closure%EA%B0%80-%EB%90%98%EB%B2%84%EB%A6%B0-%EB%82%B4%EB%B6%80%ED%95%A8%EC%88%98%EA%B0%80-%EC%9D%BC%EC%9C%BC%ED%82%A4%EB%8A%94-%EC%98%A4%EB%A5%98-feat.-setInterval)
  - 이 문제는 아직은 잘 모르겠음...

#### `=> 결론적으로 이러한 클로저 / 트러블 슈팅 오류를 피하기 위해 useInterval hook을 사용해야 함`

[참고블로그](https://velog.io/@goldbear2022/setInterval-%ED%8A%B9%EC%A7%95%EA%B3%BC-useInterval)

</br>

### [useEventListener](https://usehooks-ts.com/react-hook/use-event-listener)

- A가 업데이트 되면 커스텀 이벤트 B를 발생시키고 여기에 대한 핸들러를 붙여서 사용할 수 있다.
- window, Element, Document, 커스텀 이벤트에 사용할 수 있음.. addEventListener 같은 이벤트처럼 사용하는듯..?

- 여기에서는 useLocalStorage랑 엮어서 사용함
  - [참고 블로그](https://velog.io/@taetae-5/React-18-%EC%97%90%EC%84%9C-useLocalStorage-%EA%B5%AC%ED%98%84)

### [useLocalStorage](https://usehooks-ts.com/react-hook/use-local-storage)

- localStorage 에 저장된 값을 마치 리액트의 useState 처럼 훅 기반으로 읽고 쓸 수 있게 한다.

- [localStorage 값을 react-hook으로 안전하게 관리하자](https://velog.io/@ktthee/localStorage-%EA%B0%92%EC%9D%84-%EB%A6%AC%EC%95%A1%ED%8A%B8-%ED%9B%85%EC%9C%BC%EB%A1%9C-%EC%95%88%EC%A0%84%ED%95%98%EA%B2%8C-%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0)

</br>

### [useDarkMode](https://usehooks-ts.com/react-hook/use-dark-mode)

- 다크모드 관리
- 활용 예시
  - 현재 테마(theme)와 테마를 변경하는 함수(toggleTheme)를 리턴해주는 hook을 생성
  - localStorage에 기본적으로 라이트테마를 저장해두고, 토글이 클릭될 때마다 테마가 변경된다.
  - 이후에 유저가 페이지를 이동하거나 새로고침을 했을 때에도 localStorage를 참조해 테마를 유지한다.