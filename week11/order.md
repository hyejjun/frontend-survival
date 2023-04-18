# 주문하기

## ***사전 작업***

_**routes 추가**_

주문하기 페이지 : /order

주문 완료 페이지 : /order/complete

---

## 주문 페이지

주문 페이지에서는 할 일이 크게 두 가지이다.

### 1. 배송지 입력

### 2. 결제

---

### 배송지 입력

#### 주문하기 폼 컴포넌트

`OrderForm.tsx`

주문하기 폼 컴포넌트를 생성한다.

수신자의 이름, 주소, 전화번호를 입력받는 폼이다.

따로 컴포넌트로 빼주었다.

1. OrderFormStore 스토어를 만든다.

    - input value 관리 (name, address1, address2, postalCode, phoneNumber)

    - input의 valid check

    - input change 핸들러

2. 스토어를 사용할 수 있도록 Hook을 만든다.

</br>

#### 배송지 입력 폼 컴포넌트

`ShippingForm.tsx`

배송지 입력 폼 컴포넌트를 생성한다.

이 컴포넌트에서 주소 관련 정보를 다룬다.

주문하기 폼을 간단하게 하기 위해서 이렇게 나눴다.

배송지는 daum의 우편 검색을 통해 얻어낼 값이기 때문에 readOnly로 해줘야한다.

-> (TextBox의 readOnly 속성을 추가, onChange를 옵셔널하게 변경)

### Daum 우편번호 서비스 사용하여 표준화된 주소 얻기

> [Daum 우편번호 서비스](https://postcode.map.daum.net/guide)
>

표준화된 주소를 얻기 위해서 해당 서비스를 사용한다.

[특징]

- 카카오 map 과는 달리 Key를 발급 받을 필요가 없어서 편리하고 간단하게 구현이 가능하다.

- 기업용 / 상업용 모두 무료로 사용 가능

사이트에 들어가보면 여러가지 방법으로 사용 예시가 나와있다.

[사용하기]

__타입 정의하기__

전역으로 얻는 daum 객체의 타입을 알 수 있도록 `daum.postcode.d.ts` 파일을 만들어서 타입을 정의한다.

__컴포넌트에서 사용하기__

Daum 우편번호 서비스를 이용하려면,
우편번호 검색 팝업창을 띄우고자 하는 페이지에 아래와 같은 방식으로 코드를 넣으면 됩니다.

`index.html`에 스크립트 태그를 추가한다.

js는 작성한 순서대로 동작하므로 daum 우편번호 스크립트 태그 먼저 작성하고 main.ts 스크립트 태그를 추가해야한다.

```html
<script src="//t1.daumcdn.net/mapjsapi/bundle/postcode/prod/postcode.v2.js"></script>
```

리액트에서 사용 예시

```js
  const refElement = useRef<HTMLDivElement>(null);

  useEffectOnce(() => {
    new daum.Postcode({
      oncomplete(data) {
        const { address, zonecode: postalCode } = data;
        changeAddress({ address, postalCode });
        close();
      },
      width: '100%',
      height: '100%',
    }).embed(refElement.current);
  });
```

useRef로 특정 돔에 접근해서 그 위치에 embed 함수를 사용하여 구현한다.

changeAddress 는 스토어에 작성한 handleChangeAddress1 함수를 받아서 props로 내려준다.

close 는 usehooks-ts에 useBoolean 을 이용한다.

- useBoolean 활용하기
  - usesetFalse: closeSearch
- props로 내리기
  - close={closeSearch}

화면에 표현할때는

useBoolean의 value 값이 true 일때만 주소 검색 컴포넌트를 보여주게끔 하면 된다.

---

### 🌟 새롭게 알게된 점

#### Daum 우편번호 서비스

프로젝트에서는 항상 카카오 맵 API 를 이용하여 주소 관련 부분을 구현했다. (위경도 활용하는 프로젝트)

API Key 없이 사용할 수 있는 서비스가 있는지 몰랐다.

Daum 우편주소 서비스에서는 위경도가 제공되지 않는 것 같다. 위경도를 활용이 필요한 프로젝트는 카카오 맵 API를 활용하는 것이 좋겠다.

하지만 강의처럼 간단하게 주소만 받아오는 정도라면 Daum 우편주소 서비스를 이용하자.

js 스크립트 태그만 추가하면 되기 때문에 편리하다.

</br>

### 🔥 중요하게 봐야할 부분

#### useRef의 활용

[useRef Beta 문서](https://react.dev/reference/react/useRef)

1. 정의

useRef는 렌더링에 필요하지 않은 값을 참조할 수 있는 React Hook이다.

2. 특징

- useRef의 current가 값을 참조하는 점을 활용하여 id 값 같은 상수를 관리할 수 있다.

- useRef를 선언할 때 초기값을 받는다.
  - 초기값은 어떤 타입이든 상관없음
  - useState 상태값은 렌더링 될때마다 값이 초기화 되지만 ref.current 값은 항상 유지됨

- ref.current 프로퍼티를 변경 가능함 but 값이 변경되어도 리렌더 하지 않음.
  - 변경을 하더라도 리액트는 컴포넌트를 리렌더하지 않는다.
    - ref.current 값을 변경하더라도 바로 화면에 나타나지 않는다.
    - state가 변경되어서 화면이 리렌더 되면 그때 변경된 값을 확인할 수 있다.
  - 참조는 일반 js 객체이기 때문에 사용자가 언제 값을 변경했는지 리액트가 알 수 없기 때문이다.

- Do not write or read ref.current during rendering, except for initialization. This makes your component’s behavior unpredictable. (이건 뭔소린지 모르겠음- 렌더링 중에는 ref값을 바꾸지 말라는게..?)

- useRef 로 DOM 조작

  .currnet 에 접근하면 해당 DOM 에 접근할 수 있다.

  리액트에서는 querySelector 나 getElementById 등... js api 로 돔에 접근하지 않고 useRef로 접근한다.

3. 활용

Ref의 바람직한 사용 사례

- 포커스, 텍스트 선택영역, 혹은 미디어의 재생을 관리할 때
- 애니메이션을 직접적으로 실행시킬 때
- 서드 파티 DOM 라이브러리를 React와 같이 사용할 때

4. 추가적으로...

#### useRef / Variable / global Variable

컴포넌트 내부에 varaible 변수를 선언할 수 있고

컴포넌트 외부에 global variable 변수를 선언할 수 있다.

___렌더링에 영향을 받지 않는 useRef를 활용하는 것과 일반 변수를 활용하는것에는 어떤 차이가 있는걸까?___

1. useRef 와 varaible (컴포넌트 내부에 선언됨)

- 컴포넌트 내부에 선언된 변수는 리렌더링 될때마다 값이 초기화 된다. (컴포넌트 생애주기 동안 같은 값 유지 못함)
- 반면 ref.current 는 리렌더의 영향을 받지 않기 때문에 컴포넌트 생애주기 동안 같은 값을 유지할 수 있다.
- 렌더링에 영향을 받지 않는 값을 다룰때 useRef를 사용함

2. useRef 와 global variable (컴포넌트 외부에 선언됨)

- 컴포넌트 외부에 선언된 global 변수는 리렌더링 될 때마다 "초기화 되지 않는다".
- 그럼 useRef를 사용하는 것과 같지 않은지?
  - 해당 컴포넌트를 단 한번만 사용할 때는 문제 없이 사용할 수 있다.

하지만, 중요한 것은 리액트의 컴포넌트는 `재사용`하는 경우가 많다는 점이다.

재사용할 때 global variable은 사용하는 모든 컴포넌트에서 그 값을 공유하게 된다.

(컴포넌트가 사라졌다가 다시 등장한다고 해도 global 변수는 유지됨)

- 어느 컴포넌트에서 global 변수를 변경하면 해당 컴포넌트를 사용하고 있는 모든 부분에서 변경된 값을 공유한다.

- `컴포넌트 별로 다른 변수 값을 갖고 싶은 경우에는 useRef를 사용해야 한다.`
  - useRef를 사용하면 컴포넌트 재사용시마다 변수도 각 컴포넌트에 맞게 새롭게 초기화되기 때문이다.

[useRef / global variable 참고 블로그](https://programming119.tistory.com/266)
