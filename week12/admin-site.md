# 관리자 페이지

관리자 페이지에서 이러한 기능들을 구현할 예정이다.

```.
- 로그인
- 로그아웃
- 회원 관리
    - 회원 목록
- 카테고리 관리
    - 카테고리 목록
    - 카테고리 등록
    - 카테고리 수정
- 주문 관리
    - 주문 목록
    - 주문 상세
    - 주문 상태 변경
- 상품 관리
    - 상품 목록
    - 상품 상세
    - 상품 등록
    - 상품 수정
```

관리자 로그인 한 사람만 접근 할 수 있고 그 안에서 상품, 주문 관련 정보들을 CRUD 할 수 있게 구현할 예정.

__[리액트에서 사용하는 상태]__

- Local State: 리액트 컴포넌트 안에서만 사용되는 state
- Global State: Global Store에 정의되어 프로젝트 어디에서나 접근할 수 있는 state
- Server State: 서버로부터 받아오는 state

이전에는 모든 상태들을 스토어에서 관리했다.

이번에는 데이터 패칭 라이브러리(SWC)를 사용하여 비동기 로직을 분리할 것이다.

그러면 좀 더 관심사가 분리되고 선언적으로 프로그래밍 할 수 있다.

[데이터 패칭 라이브러리 참고한 글](https://fe-developers.kakaoent.com/2022/220224-data-fetching-libs/)

## SWC

서버 상태 관리 라이브러리

> [SWR](https://swr.vercel.app/ko)
>

```bash
npm i swr
```

프론트에서 캐시 처리 & 백엔드와의 동기화에 집중하기 위해 라이브러리 사용.

백엔드의 그림자 같은 느낌으로 데이터, 상태를 관리하기 위해 사용한다.

## React Hook Form

> [React Hook Form](https://react-hook-form.com/)
>

```bash
npm i react-hook-form
```

React에서 form의 validation을 빠르고 쉽게 도와주는 라이브러리

사용자에게서 데이터를 수집하는 경우 제어/비제어 컴포넌트로 데이터를 수집하게 된다.

제어 컴포넌트 사용: useState 로 입력값의 validate를 실시간으로 체크하는 경우

비제어 컴포넌트 사용: useRef 로 입력값을 관리함. 입력 값이 변할때마다 렌더링 되지 않아서 성능면에서 이점이 있음.

그런데 사용자가 입력할 데이터가 너무 많은 경우 비제어 컴포넌트만 사용한다고 해도 성능 이슈가 생길 수 있고 여러군데 흩어진 폼 데이터를 상태관리하는데도 어려움이 있다.

그래서 이러한 문제를 해결하기 위해 React Hook Form 라이브러리를 사용할 수 있다.

- 기본적으로 비제어 컴포넌트 방식으로 구현되어 있음 -> (제어컴포넌트 사용시 발생하던) 렌더링 이슈를 해결
- form의 데이터와 상태관리를 위해 Provider 사용 -> props drilling 없이 사용 가능
- 데이터가 계속 동기화 되며 한 곳에서 관리할 수 있음 -> 데이터 분산에 따른 이슈 해결

[효울적인 From 관리를 위해 React Hook Form 을 도입하게 된 사례](https://tech.inflab.com/202207-rallit-form-refactoring/react-hook-form/)

---

프로젝트로 돌아와서,

### 1. API 명세서를 보고 사용할 데이터들의 Type을 정한다. (types.ts)

### 2. SWC에서는 Read, Apiservice에서는 Create, Update, Delete와 같은 Mutation 처리를 한다

### 3. SWR의 캐시 초기화(refetch 요청)를 위해 mutate 함수를 사용한다
  
- 상세보기 > 수정 API 요청하고 > 상세보기 refetch
- 목록 > 추가 API 요청하고 > 목록 refetch
