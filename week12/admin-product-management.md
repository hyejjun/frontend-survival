# 관리자 > 상품관리

상품관리의 폼을 관리하는 부분은 usestore-ts 를 사용하여 스토어에서 관리한다.

## 1. 상품 목록 페이지

- GET /admin/products

- 특이 사항 없음. 일반적인 list fetch

## 2. 상품 상세 페이지

- GET /admin/products/{productId}

- useParams로 productId 받아옴

[API - mutate 할 때]

- API 를 fetch 할 hook을 만들때 mutate() 를 직접 호출하지 않고 refresh라는 함수 안에서 호출 할 수 있게 한다.

- mutate 가 뭔데..?? refresh가 더 명확하게 의미를 나타냄

## 3. 상품 추가 / 수정

여기서 데이터 구조를 동적으로 조정해야함. -> 스토어를 사용한다.

스토어는 추가 / 수정에서 공통으로 사용할 예정

-> 이러면 useState 로 각자 관리 or react hook form으로 각자 관리 하지 않아도 되서 좋다.

### `스토어가 핵심!`

- 추가(create) / 수정(update)하는 경우에 스토어에서 직접 apiService에 있는 API를 호출한다.

```ts
await store.create();
```

이런식으로 사용함.

API 호출이 성공하면 SWR - mutate 로 캐시를 초기화 한다.

### 재사용할 utils 함수 작성

항목을 추가하고 수정할때 자주 쓸 utils 함수들을 만들어서 따로 빼준다.

액션에서 계산으로 빼고... 계산은 어디서나 가져다 쓸 수 있게 함수명 작성하고 명시적 입출력으로 선언했다.

- 배열에 항목 추가

```ts
export function append<T>(items: T[], item: T) {
  return [...items, item];
}
```

배열과 해당 배열에 추가할 item을 받아서 배열에 추가한다.

(배열 복사해서 사용 - copy on write)

</br>

- 배열에서 특정 값 제거

```ts
export function remove<T>(items: T[], index: number) {
  return [
    ...items.slice(0, index),
    ...items.slice(index + 1),
  ];
}
```

slice를 이용

- 0번째 부터 ~ 해당 index 전 까지 잘라내서 복사

- 해당 index 이후로 복사

새로운 배열로 리턴한다.

* unique id가 있는 경우에는 filter 로 처리 가능함

```ts
arr.filter((item) => item.uniq_id !== uniq_id)
```

</br>

- 배열에서 특정 값 업데이트

```ts
export function update<T>(items: T[], index: number, f: (value: T) => T) {
  return items.map((item, i) => (i === index ? f(item) : item));
}
```

인자값으로 배열, 수정할 아이템의 인덱스, 어떻게 수정할지가 명시된 함수

이렇게 전달하면 인덱스가 일치하는 경우에 적용된다.
