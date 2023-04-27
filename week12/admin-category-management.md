# 카테고리 관리

카테고리 데이터를 확인, 등록, 수정 할 수 있게 한다.

카테고리명과 공개여부를 설정할 수 있다.

</br>

## 1. 카테고리 목록

- 목록 페이지 생성
- useFetchCategories hook 으로 API 호출
  - 목록 API 호출
  - [ Bound Mutate ] createCategory 호출시 목록 refetch 요청 (mutate 함수를 호출)

> [SWR -  Bound Mutate](https://swr.vercel.app/ko/docs/mutation#bound-mutate)
>

</br>

## 2. 카테고리 등록

: 처음 등록시에는 카테고리 이름만 작성해서 API로 요청한다.

이때 공개여부는 설정하지 못한다. 자동으로 비공개로 등록됨.

- 등록 페이지 생성
- `여기서 React Hook Form 의 useForm hook & Controller 컴포넌트를 사용`

```tsx
import { Controller, useForm } from 'react-hook-form';
```

__useForm hook__

```tsx
type FormValues = {
  name: string;
};

const { handleSubmit, control } = useForm<FormValues>();
```

__Controller 컴포넌트__

```tsx
<form onSubmit={handleSubmit(onSubmit)}>
  <Controller
    control={control}
    name="name"
    defaultValue=""
    render={({ field: { onChange, value } }) => (
      <div>
        <label htmlFor="input-name">이름</label>
        <input
          id="input-name"
          value={value}
          onChange={onChange}
        />
      </div>
    )}
  />
  <Button type="submit">
    등록
  </Button>
</form>
```

__useFetchCategories 훅이 createCategory 함수를 제공한다.__

등록페이지에서 useFetchCategories 훅을 불러와서 등록 API를 호출 할 수 있게 한다.

등록 API를 호출하는 경우, 목록 API를 refetch 하게 됨

// TODO :: SWR mutate
=> 이거 도대체 뭐임???? 등록 API를 위한 hook이 따로 있는게 아니라 목록에서 제공하는 이거 뭐임...???? SWR mutate 관련해서 더 찾아봐야 할 듯

-> 등록 API를 호출하는 createCategory 함수를 hook에서 제공하는 것

등록 버튼 누르면 createCategory 함수 호출함. apiService.createCategory 로 API 호출 하고 mutate() 로 목록을 refetch 하는 형태

</br>

## 3. 카테고리 수정

```tsx
  const {
    category, loading, error, updateCategory,
  } = useFetchCategory({ categoryId });
```

수정할때는 categoryId 별로 정보를 조회하고 거기서 리턴하는 updateCategory 함수를 사용해서 update API 를 호출한다.

수정 페이지 역시 React Hook Form 의 useForm hook & Controller 컴포넌트를 사용해서 입력 값을 처리한다.

__카테고리 상세, 수정 API 호출을 위한 hook 생성__

`useFetchCategory.ts` hook

목록, 등록과 비슷한 형태로 mutate를 포함한 updateCategory 함수를 리턴하면 된다.

---

### 🏷️ 프로젝트에서는 어떻게 처리했는지?

프로젝트에서는 react-query를 사용했다.

axios를 이용해서 API를 처리했다.

CRUD API를 각각 hooks로 만들어서 원하는 곳에서 호출할 수 있게 했다.

### 🌟 새롭게 알게된 점

이번에 SWC를 새로 학습하면서 Bound Mutation에 관해서 알게 되었다.

__낙관적인 UI__

사용자가 어떤 데이터를 변경하고 API를 통해 서버에 반영하는 시간동안 보다 나은 UX경험을 위해 가데이터? 변경된척? 하고 미리 보여주는 것.

그렇게 하고 mutate를 통해 __데이터를 최신화__ 한다.

당연히... react-query에도 캐시 업데이트 하는 부분이 있는데 이 부분을 간과했다.

기능이 있으면 가져다 써야 하는데 내가 명령형으로 데이터 최신화 하는 부분을 구현했음..

데이터 최신화 하는 부분을 좀 더 자세히 봐야함...

[SWR 알아보기](https://velog.io/@code-bebop/SWR-%EC%8B%AC%EC%B8%B5%ED%83%90%EA%B5%AC)

[SWR, React-Query 에서 데이터 최신으로 유지하기](https://fe-developers.kakaoent.com/2022/220224-data-fetching-libs/)


