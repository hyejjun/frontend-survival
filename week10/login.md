# 로그인

로그인 페이지를 만들고

LoginForm 컴포넌트를 만든다.

__로그인 페이지 input value값 스토어에서 관리하기__

./ui/TextBox 컴포넌트를 활용하여 id, pw 의 value 와 onChange를 다루고
이 값들은 LoginFormStore 에서 관리한다.

`./ui/TextBox.tsx`

```tsx
const handleChange = (event: React.ChangeEvent<HTMLInputElement>) => {
    onChange(event.target.value);
  };
```

`LoginForm.tsx`

```tsx
  // 스토어 사용할 수 있는 hooks로 스토어 접근
  const [{ email }, store] = useSignupFormStore();

  // input value값 바뀔때마다 store에 저장
  const handleChangeEmail = (value: string) => {
    store.changeEmail(value);
  };

...

<TextBox
  label="E-mail"
  placeholder="tester@example.com"
  value={email}
  onChange={handleChangeEmail}
/>
```

`LoginFormStore.ts`

```ts
  // email value 값 바뀔때마다 스토어에 저장할 수 있게 Action으로 선언
  @Action()
  changeEmail(email: string) {
    this.email = email;
  }
```

컴포넌트에서 useState로 관리하지 않고 스토어로 빼줬다.

---

## localStorage 활용하기

### 언제 어디서 localStorage에 로그인 토큰 값을 넣는지?

localStorage를 활용하여 새로고침시 유지, 전역적으로 동기화 할 수 있게 하자.

로그인 토큰을 localStorage에 넣을 것이다.

___usehooks-ts___ 를 사용한다.

로그인 토큰 관리하는걸 감추기 위해 hooks로 만든다.

`useAccessToken.ts`

-> 로그인 토큰이 필요한 컴포넌트에서 불러다가 사용할 예정이다.

```ts
export default function useAccessToken() {
  // usehooks-ts 로 이렇게 사용한다. 처음에는 빈 값으로 초기화
  const [accessToken, setAccessToken] = useLocalStorage('accessToken', '');

  // return 값으로 accessToken value값과
  // accessToken을 설정할 수 있는 setAccessToken 함수를 넘겨준다. (로그인 성공시 활용함)
  return { accessToken, setAccessToken };
}
```

`apiService.ts`

로그인 API를 호출하고 응답으로 토큰이 오면 accessToken으로 리턴한다.

```ts
async login({ email, password }: {
  email: string;
  password: string;
}): Promise<string> {
  const { data } = await this.instance.post('/session', { email, password });
  const { accessToken } = data;
  return accessToken;
}
```

`LoginFormStore.ts`

```ts
@singleton()
@Store()
export default class LoginFormStore {
  email = '';

  password = '';

  error = false;

  accessToken = '';

  // 로그인 성공시 호출될 Action - 스토어에 accessToken 을 저장
  @Action()
  setAccessToken(accessToken: string) {
    this.accessToken = accessToken;
  }

  // 로그인 실패시 호출될 Action
  @Action()
  setError() {
    this.error = true;
  }

  async login() {
    try {
      const accessToken = await apiService.login({
        email: this.email,
        password: this.password,
      });

      this.setAccessToken(accessToken);
    } catch (e) {
      this.setError();
    }
  }
}
```

`LoginForm.tsx` 에서 스토어의 로그인 함수 호출

```tsx
 const handleSubmit = (event: React.FormEvent<HTMLFormElement>) => {
    event.preventDefault();
    store.login();
  };
```

`LoginForm.tsx`

useAccessToken hooks의 setAccessToken 기능을 가져옴.

스토어의 로그인 함수 호출

```tsx
  const { setAccessToken } = useAccessToken();

  // 1. 스토어에서 API를 통해 응답 받은 accessToken를 가져옴
  const [{ accessToken }] = useLoginFormStore();

  // 2. accessToken 값이 변경 될 때마다 
  // hooks의 setAccessToken을 사용하여 localStorage 값을 업데이트한다.
  useEffect(() => {
    if (accessToken) {
      setAccessToken(accessToken);
    }
  }, [accessToken]);
```

---

## 로그인 토큰 활용하기

### localStorage에 저장된 로그인 토큰을 활용하여 로그인 여부에 따른 UI 처리하기

필요한 곳에서 `useAccessToken` hooks를 통해 accessToken 값을 불러와서 처리를 해주면 된다.

```tsx
// localStorage 에 저장된 accessToken 가져오기 - hooks 사용
const { accessToken } = useAccessToken();
```

Header 에서 로그인 여부에 따른 GNB 구성을 달리하거나

상품 상세 페이지에서 장바구니에 담으려면 로그인 하라는 문구를 띄울 수 있다.

```tsx
 return(
  <>
   { accessToken ? ( 
    <div>
      환영합니다!
    </div>
   ) : (
     <div>
      로그인 해주세요.
    </div>
   )
  </>
 )
```

---

## API 호출시 헤더에 AccessToken (로그인 토큰) 넣어서 보내기

- 회원임을 증명해야 하는 경우 사용한다.

내 정보, 장바구니 확인 (해당 회원이 뭘 담았는지) 등등...

로그아웃 시 (토큰 무효화 처리를 위해)

`ApiService.ts`

// TODO :: 이 부분 다시 보자. 잘 이해 안감.

```ts
private accessToken = '';

setAccessToken(accessToken: string) {
  if (accessToken === this.accessToken) {
    return;
  }

  const authorization = accessToken ? `Bearer ${accessToken}` : undefined;

  // API 요청시 헤더에 토큰 값이 들어가게 한다.
  this.instance = axios.create({
    baseURL: API_BASE_URL,
    headers: { Authorization: authorization },
  });

  this.accessToken = accessToken;
}
```

`useAccessToken.ts`

```ts
export default function useAccessToken() {
  const [accessToken, setAccessToken] = useLocalStorage('accessToken', '');

  // localStorage의 accessToken이 변경될 때 마다 
  // ApiService 에서 선언한 메서드를 호출하여 API에 들어갈 로그인 토큰을 업데이트 해준다.
  useEffect(() => {
    apiService.setAccessToken(accessToken);
  }, [accessToken]);

  return { accessToken, setAccessToken };
}
```