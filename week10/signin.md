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

## API 명세서에 맞게 `ApiService` signup 메서드를 추가한다.

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
