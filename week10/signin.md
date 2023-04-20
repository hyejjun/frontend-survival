# 회원가입

- 회원 정보를 담아서 API를 통해 요청
- 중복 회원 아이디에 대한 처리 및 테스트 코드를 확인

---

회원 가입 API

`POST /users`

`요청`

```.
{
  "email": "tester@example.com",
  "name": "tester",
  "password": "password"
}
```

`응답`

```.
HTTP Status Code

201 Created: 회원 가입 성공
422 Unprocessable Content: 회원 정보가 올바르지 않음.

Success 
Body (JSON)

{
  "accessToken": "Header.Payload.Signature"
}
```

## API 명세서에 맞게 `ApiService` signup 메서드를 추가한다

email, name, password 를 넘기면 accessToken을 응답으로 줌

회원 가입하면 즉시 로그인 되게끔 한다.

## `SignupFormStore` 스토어 생성

로그인과 비슷하게 input value를 여기서 관리하고

signup() 메서드를 스토어에서 관리한다.

"가입" 버튼을 누르면 signup() 메서드가 호출되면서 apiService에 있는 회원가입 API를 요청한다.

요청 성공시 응답으로 받은 accessToken을 회원가입 스토어의 setAccessToken Action을 이용해 저장한다.

### SignupFormStore를 사용할 수 있는 hook 생성

```ts
export default function useSignupFormStore() {
  const store = container.resolve(SignupFormStore);
  return useStore(store);
}
```

### SignupForm 컴포넌트 생성

여기서 회원가입을 진행할 수 있고 성공시 응답으로 받은 스토어에 있는 accessToken을 useAccessToken hook을 이용해서 localStorage에 저장한다.

```tsx
export default function SignupForm() {
  const { setAccessToken } = useAccessToken();

  // 1. store에 있는 accessToken 가져오기
  const [{ accessToken }] = useSignupFormStore();

  // 2. accessToken 값이 변경될 때 마다 setAccessToken으로 localStorage에 저장하기
  useEffect(() => {
    if (accessToken) {
      setAccessToken(accessToken);
    }
  }, [accessToken]);
  ...
```

### SignupPage 에서 해당 컴포넌트 사용

```tsx
export default function SignupPage() {
  const navigate = useNavigate();

  const [{ accessToken }, store] = useSignupFormStore();

  useEffect(() => {
    store.reset();
  }, []);

  useEffect(() => {
    if (accessToken) {
      store.reset();
      navigate('/signup/complete');
    }
  }, [accessToken]);

  return (
    <SignupForm />
  );
}
```

맨 처음 이 페이지에 접근하게 되면 store에 저장된 input값들 (email, name, password..) 값들을 reset 한다. (신규 가입자이기 때문)

회원가입을 진행하고, 가입에 성공하면 응답으로 accessToken이 온다.

SignupFormStore 에서 accessToken을 가져온다.

useEffect를 사용해서 accessToken이 있으면 store에 저장된 input값들 (email, name, password..) 값들을 reset 한다.

그리고 가입 완료 페이지로 이동한다.

---

- routes에 추가

```ts
{ path: '/signup', element: <SignupPage /> },
{ path: '/signup/complete', element: <SignupCompletePage /> },
```

가입페이지와 가입 완료 페이지 추가

---

- `LoginForm` 컴포넌트에 "회원 가입" 링크 추가

```tsx
<p>
  <Link to="/signup">
    회원 가입
  </Link>
</p>
```

---

테스트를 위해서 mocks의 handlers.ts 에도 /users (회원가입 API를) 추가해준다.

---

### 🌟 새롭게 알게된 점

#### 회원가입 후 즉시 로그인 처리

어드민 관련 프로젝트를 많이 하다보니 회원가입 처리에 대한 고민을 크게 해보지 않았던것 같다.

이미 인가된 ID, PW를 가지고 작업을 할 때가 많았어서 사용자 편의성을 위해 회원가입 후 즉시 로그인 되게끔 해야 된다는 생각을 하지 못했다.

평소 다른 사이트들을 쓸때 당연하게 생각했던 부분이고, 어떤 사이트에는 이 기능이 되어있지 않아서 "아 뭐야 왜 로그인 직접해야돼?" 했던 부분인데 정작 프로젝트에서는 간과했다..!

__회원가입 후 API 응답으로 AccessToken를 받아오면 로그인 처리 해주기__

### 🔥 중요하게 봐야할 부분

#### 회원가입 페이지를 접근/나갈때 Input value 초기화 하기

간과하면 안되는 부분이 회원가입 페이지에 처음 접근하거나 회원가입 완료후 페이지를 떠날 때 모든 input들을 초기화 해줘야 한다는 점이다.

평소 프로젝트에서는 리액트 useState를 사용해서 input 관리를 했기 때문에 매번 초기 렌더링 될때 state가 초기화 되어서 그 걱정을 안했었다.

하지만 강의에서는 input 관리를 store에서 해주기 때문에 잊지말고 초기화 해줘야 한다.
