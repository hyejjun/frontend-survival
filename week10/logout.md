# 로그아웃

## 토큰의 유효성 확인하기 + 로그아웃

사이트에 접속하거나 새로고침을 하면 AccessToken의 유효성을 확인한다.

API를 통해 로그인 토큰이 유효한지 확인하고 유효하지 않다면 폐기된 jwt token 이거나 변조된 것이므로 클라이언트 단에서 로그아웃 처리해준다.

### 로그인 토큰의 유효성 확인하자

#### 로그인 토큰의 유효성을 검사할 수 있는 API를 연결

`ApiService.ts`

```ts
async fetchCurrentUser(): Promise<{
  id: string;
  name: string;
}> {
  const { data } = await this.instance.get('/users/me');
  const { id, name } = data;
  return { id, name };
}
```

이 API 를 통해 응답이 오면 로그인 토큰 유효함

응답에 에러가 오면 로그인 토큰 유효하지 않음 -> 로그아웃 처리함

#### 이 API를 사용할 수 있게 `useCheckAccessToken.ts` hook을 만든다

```ts
export default function useCheckAccessToken(): void {
  const { accessToken, setAccessToken } = useAccessToken();

  useEffect(() => {
    const fetchCurrentUser = async () => {
      try {
        await apiService.fetchCurrentUser();
      } catch (e) {
        setAccessToken('');
      }
    };

    fetchCurrentUser();
  }, [accessToken, setAccessToken]);
}
```

useEffect를 사용해서 accessToken과 setAccessToken이 변경 될 때마다

로그인 토큰이 유효한지 확인할 수 있는 API를 호출

### 로그인 토큰 체크는 어느 페이지에서 해야되는지?

로그인체크 = 토큰 체크를 사이트 접속할때마다 항상 해줘야한다.

그래서 이거를 Layout에서 해준다.

`Layout.tsx`

```tsx
export default function Layout() {
  useCheckAccessToken();

  return (
    <Container>
      <Header />
      <Outlet />
    </Container>
  );
}
```

만약 유저가 강제로 토큰 조작을 한다면, 레이아웃에서 토큰 체크 API 요청하고 응답이 "유효한 토큰이 아님"으로 오니까 클라이언트단에서 로컬 스토리지를 바로 빈값으로 만들어 준다.

그치만 jwt 토큰 체크 자체를 백엔드에서 하기 때문에 클라이언트단에서 너무 걱정안해도 된다.

---

### 로그아웃 처리

토큰이 만료되었거나 변조 된 경우 `/users/me` API를 통한 응답이 에러로 올 것이다.

그렇게 되면 클라이언트 단에서 강제로 로그아웃 시켜주면 된다.

#### 로그아웃 API 연결하기

`ApiService.ts`

```ts
async logout(): Promise<void> {
  await this.instance.delete('/session');
}
```

DELETE 메서드로 로그아웃을 요청한다.

#### 로그아웃 버튼 -> 로그아웃 API 호출

`Header.tsx`

```tsx
export default function Header() {
  const navigate = useNavigate();

  const { accessToken, setAccessToken } = useAccessToken();

  const handleClickLogout = async () => {
    await apiService.logout();
    setAccessToken('');
    navigate('/');
  };
}
```

logout API 호출하고

setAccessToken으로 localStorage의 값을 빈 값으로 만들고

navigate('/')로 메인페이지로 이동한다.

#### +) 로그아웃 API 호출했는데 에러나면?

-> 흠... 나는 프로젝트 할 때 강제로 로그아웃 처리하고 다시 로그인 하라고 했었다.

계속 호출해도 에러가 나기 때문에... 그러면 영원히 로그아웃을 할 수 없다...

그리고 보통 jwt token의 유효시간을 정해주기 때문에 프론트에서도 localStorage 유효시간을 정해준다.

그 유효시간이 지나면 localStorage 값을 초기화 시켜서 강제로 로그아웃 처리함.

### 🌟 새롭게 알게된 점

#### 내 정보 API

/users/me

- jwt 토큰을 넘겨서 "내 정보"를 받아오는 것
- API를 확인해보면 `users/{userId}` 이렇게 하는 것이 아니라... `users/me` 이렇게 되어있다.
- 로그인한 회원 "나" 를 중심으로 간다는 것
- 강의 들을 때는 프로젝트때 왜 이렇게 안했지?? 하고 생각했는데 다시 확인해보니 이미 이렇게 하고 있었다.
- 어드민 단이랑 헷갈린것 같다. 어드민단에서는 회원 각각의 info를 받아오는 경우가 있어서 `users/{userId}` 이런식으로 요청하기도 한다.
- 하지만 사용자단에서는 `users/me` 이런식으로 요청하고 jwt 토큰을 넘겨서 회원 정보를 받아온다.

### 🔥 중요하게 봐야할 부분

#### 로그인 토큰 체크

- 레이아웃에서 로그인 토큰이 유효한지 체크한다는 점
- 프로젝트때는 레이아웃 useEffectOnce + URL이 이동할때마다 이렇게 체크했다.

---

최근 여러 프로젝트에서 로그인/로그아웃 파트를 맡으면서 로컬 스토리지를 쓰는게 맞는건지 이런식으로 로그인 체크를 하는게 맞는건지 하면서도 의문이 들었는데 강의에서 나온거랑 거의 동일하게 하고 있어서 좀 더 확신을 갖고 작업할 수 있을 것 같다.
