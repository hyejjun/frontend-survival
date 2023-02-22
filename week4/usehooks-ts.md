# usehooks-ts

> [usehooks-ts](https://usehooks-ts.com/)
>
>> Hooks를 어떻게 만들었는지 코드를 확인 할 수 있으니 참고해서 Custom Hook을 만들 때 사용할 수 있다.

```bash
npm install usehook-ts
```

---

## 자주 사용하는 usehooks-ts의 hook들

### [useBoolean](https://usehooks-ts.com/react-hook/use-boolean)

true/false 의 boolean 값을 toggle 하는 기능을 가진 hook

```jsx
const { value, setValue, setTrue, setFalse, toggle } = useBoolean(false)

// Just an example to use "setValue"
const customToggle = () => setValue((x: boolean) => !x)
```

</br>

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

</br>

### [useEventListener](https://usehooks-ts.com/react-hook/use-event-listener)

### [useLocalStorage](https://usehooks-ts.com/react-hook/use-local-storage)

- localStorage에 값을 저장하고 이게 업데이트 되면 자동으로 반영됨

### [useDarkMode](https://usehooks-ts.com/react-hook/use-dark-mode)