# 관리자 > 로그인 & 사용자 관리

## 관리자 로그인

관리자 페이지로 접근하려면 무조건 로그인이 필요하다.

로그인하지 않고 '/'로 접근하면 '/login' 으로 이동하게 한다.

```tsx
  useEffect(() => {
    if (!accessToken) {
      navigate('/login');
    }
  }, [accessToken, navigate]);
```

accessToken 이 있는 경우(로그인이 된 경우)에만 '/' 에 접근 가능하다.

로그인이 안되어 있다면 '/login' 페이지로 리다이렉션하게 만든다.

혹은 ready 상태로 표현할 수 있다.

__로그인 여부에 따른 UI 처리가 필요한가?__

No. 관리자 페이지는 무조건 로그인한 사람만 들어올 수 있기 때문에 로그인 여부에 따른 처리를 해주지 않아도 된다.

- Layout 에서 로그인이 된 경우에만 렌더링하도록 확실히 제약

```tsx
export default function Layout() {
  const ready = useCheckAccessToken();

  if (!ready) {
    return null;
  }

  return (
    <Container>
      <Header />
      <Outlet />
    </Container>
  );
}
```

__🏷️ 프로젝트에서는 어떻게 처리했는지?__

결과적으로는 프로젝트에서도 동일하게 처리함.

[1] App.js 에서 로그인 토큰 여부에 따른 처리

```jsx
  if (!isLogin) {
    return <LoginPage />;
  }
```

- 강의에서 Layout 에 처리한 것과 비슷한 형태

[2] '/' 로 접근시 일단 '/login'으로 이동

로그인 페이지에서 로그인 토큰 여부에 따른 처리

```tsx
<Routes>
  <Route path="/login" exact={true} element={<Login />} />
  <Route path="/" element={<Navigate replace to="/login" />} />
  <Route path="/home" exact={true} element={<Home />} />
  <Route path="*" element={<NoMatch />} />
</Routes>
```

[3] '/' 로 접근시 localStorage에 있는 로그인 토큰이 있는지 확인하고 있다면 유지, 없다면 '/login'으로 리다이렉트

1, 2, 3 순으로 프로젝트를 진행했는데 여러 시행착오를 겪으면서 최근에는 강의와 비슷한 형태로 구현할 수 있게 되었다.

## 사용자 관리

- 실제로 사용자 관리는 상당히 복잡함
- 강의에서는 간단하게 사용자 목록 조회 정도만 할 예정

__🏷️ 프로젝트에서는 어떻게 처리했는지?__

[일반 사용자]

- 사용자 목록 조회
- 사용자 정보 확인
- 사용자 목록 엑셀로 다운로드

[관리자]

- 관리자 정보 수정
- 슈퍼관리자 / 일반관리자 등록
  - 일반정보, 관리자의 (메뉴 접근)권한 정보 설정

### 사용자 목록

SWR 을 사용해서 사용자 목록을 가져온다.

- 사용자 목록을 보여줄 페이지 생성

- apiService에 fetcher를 제공 url을 받아서 'get'으로 요청하게 한다. (async - await 사용)

```ts
fetcher() {
  return async (url: string) => {
    const { data } = await this.instance.get(url);
    return data;
  };
}
```

- `[SWR 공통 hook]` useFetch hook 생성
  - SWR의 `useSWR`을 hook을 통해 쓸 수 있도록 한다.

- `[/users API 호출]` useFetchUsers hook 생성
  - 페이지에서 useFetch를 바로 쓰지 않고 useFetchUsers를 거쳐서 쓴다.
