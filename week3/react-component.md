# Thinking in React

[thinking in react](https://beta.reactjs.org/learn/thinking-in-react)

-> 단계별로 꼭 봐야만 하는 자료 !!!!!

STEP

1. 컴포넌트 계층구조로 UI 쪼개기
2. 리액트로 정적인 버전을 만들자

html css 로 퍼블리싱하던 작업과 크게 다르지 않음.

리액트의 기초가 되는 것...

이거 다음에는 상태관리-> 동적인 관리를 배워보자.

---

## JSON 데이터

>__[주요 내용]__ </br>
> __REST API 와 GraphQL__
>>
>> 1) REST API 란 무엇인가 </br>
>> 2) GraphQL은 왜 등장했는가? </br>
>> 3) REST API vs GraphQL </br>

</br>

JSON 목업(mockup) 데이터를 가지고 테스트 해보자.

백엔드에서 JSON형태로 데이터를 전달해주는 API를 제공한다고 가정한다.

### 1. REST API

- GET, POST, PUT/PATCH, DELETE (CRUD)
- 리소스 중심으로 뭔가를 조작한다.

예를 들어서 내가 어떤 게시물에 대한 데이터를 얻는다. 그러면 게시물에 딸린 댓글까지 같이 얻는 구조

4가지 액션을 하나의 리소스에 대해서 해줌

### 2. GraphQL

- Graph라는 자료구조를 이용한다.
- Query에서 얻고자 하는 걸 스키마 처럼 넣어준다. (쿼리 자체에서 지정해준다는 것)
- Query(Read), Mutation (Command : Create, Update, Delete), Subscription (Event)

근데 GraphQL은 Query를 통해 내가 원하는 특정 데이터만 얻을 수 있는 구조이다.

쿼리 랭귀지라서 SQL이랑 비슷함. 쿼리는 얻는것 + 고치는 것을 포함.

Subscription : 이벤트를 인지한는 용도. Query에 대해서 인지하지는 않고 Command에 대해서 인지한다. 누가 뭘 만들었다. 댓글을 달았다는것을 알게 해주는 용도.

</br>

얘네 둘 다 JSON 형태로 데이터를 반환한다.

### JSON

- FE 와 BE 가 데이터를 주고 받을 수 있도록 하는 데이터의 형태.
- 기본적으로 string 형태이다.

- JSON.stringify(객체형태) : JSON to String
- JSON.parse(문자열) : String to JSON
  - 컨버터가 js에 있음

</br>

지금 예시에서는 key 값으로 쓸 만 한게 없어서 일단 적절히 섞어서 사용해주자.

---

### DSL (Domain-Specific Language)

프론트엔드는 이 데이터를 사용자가 볼 수 있도록 UI를 구성한다.
React는 선언형(HTML과 유사한 모양의 DSL[도메인 특화 언어]을 사용)으로 UI를 구성 할 수 있다.

DSL

- 특정한 도메인을 적용하는데 특화된 컴퓨터 언어.
- 예를 들어서 HTM과 같은 __웹페이지 분야__ 에서 널리 쓰이는 언어를 DSL이라고 할 수 있다.

범용언어

- 반대로 여러 문제를 광범위 하게 해결하기 위해 고안된 프로그래밍 언어가 있다.
- C, JAVA

---

### 명령형 프로그래밍 & 선언형 프로그래밍

리액트에서는 선언형으로 구성할 수 있다.

#### 명령형 프로그래밍

- 컴퓨터가 수행할 명령들을 순서대로 써 놓은 것.
- how to solve it. __어떻게 (How)__ 그것을 해결할 것인가에 관심이 있다.
- 거의 대부분의 컴퓨터 하드웨어는 명령형으로 구현된다.
- C언어...등등
- 명령형 방식 : "여기서 쭉 직진하시다가 롯데리아가 보이시면 우회전 하세요. 직진하다가 두 번째 신호등에서 좌회전하세요. OO호텔이 보일 겁니다"

#### 선언형 프로그래밍

- 프로그램이 __무엇을 (What)__ 해야 할지를 나타내는 경우
- 웹 페이지를 나타낼 때 "어떤 방법"으로 나타내야 하는지 보다 제목, 본문, 그림 등과 같이 "무엇"을 화면에 나타내야 할지를 고민한다.
- SQL, HTML 등등...
- 선언형 접근 방식은 명령형으로 구현된 것들이 추상화되어 있어야 한다.
- 선언형 방식 : "OO 호텔은 xxx동 xxx로 xx길 10에 있습니다.

```js
// 배열을 파라미터로 받고 각 요소들의 값에 2를 곱하는 함수

// 명령형 방식
function double (arr) {
  let results = [];
  for (let i = 0; i < arr.length; i++){
    results.push(arr[i] * 2);
  }
  return results;
}

// 선언형 방식
function double(arr) {
  return arr.map((item) => item * 2);
}
```

두 함수는 배열을 파라미터로 받아 각 요소들의 값에 2를 곱하는 같은 기능을 한다.

명령형 방식을 보면 어떻게 배열의 요소들에 2를 곱할지 설명하는 식으로 작성됐다.

하지만 선연형 방식은 어떻게 보다 무엇을 할지만 작성됐다.

즉, JavaScript에 내장된 map을 이용해 같은 기능을 수행했다. 개발자는 map의 내부가 어떻게 되어있는지 모르지만, 그것을 신경 쓸 필요가 없다.

[참고 블로그](https://code-lab1.tistory.com/244)

---

## 컴포넌트 기반 (컴포넌트 계층 구조)

리액트의 강력한 특징

- 컴포넌트 기반이다.
- 캡슐화가 가능하다.
  - 컴포넌트로 잘게 쪼개서 이것들을 잘 조합하면 복잡한 UI들을 만들 수 있다.

간단한 컴포넌트를 모아서 복잡한 UI들을 만들 수 있다.

컴포넌트 하나하나가 복잡하다면 리액트를 잘 활용하지 못하는 것..

</br>

### 심플한 컴포넌트란? 컴포넌트를 나누는 기준은?

#### 1. SRP (Single Responsibility Principle)

단일 책임 원칙

- 모든 컴포넌트는 하나의 책임만 가진다.
- 하나의 컴포넌트가 변경되는건 하나의 이유여야 한다.
- 사소한 변경이 있으면 거기만 고칠 수 있게.
- 컴포넌트가 너무 길어지면... 쪼개줘야 한다.

</br>

#### Extract Function

> [Extract Function](https://refactoring.com/catalog/extractFunction.html)
>
> [Inline Function](https://refactoring.com/catalog/inlineFunction.html)
>

변화의 크기를 제약하는 방법으로 SRP를 위한 수단이다.

1. 우선은 코드를 길게 작성한다.
2. 적당히 자를 수 있는 부분이 보일때 __함수로 추출__ 한다.
3. 바로 다른 파일을 만들어야 한다고 생각하지 말자!
4. 컴포넌트를 나누는 기준이 애매하다면?

- 우선 하나의 컴포넌트로 합하고(Inline Method) 다시 나눠준다.

</br>

#### 2. CSS

 __길어지는 이유는?__

- html이 길어지거나 js 코드가 길어지거나
- 기본적으로 html이 커지는 경우가 많다.
- 그래서 적절하게 css에서 쓰는 selector들 className 이런거를 활용해야 한다.

</br>

#### 3. 디자인의 레이어 활용

</br>

#### 4. Information Architecture

- JSON 스키마의 영향
- 많이 사용하게 된다.
- 중간에서 데이터를 얻어오고 한번 정제하거나 백엔드에 API 형태 수정을 요청한다.
- 자연스러운 SRP를 사용하기 위해 자연스레 요구되는 방법

작은 컴포넌트 = 부품을 만들어서 조립
조합만 잘하면 여러 UI들을 만들 수 있는 방법

</br>

#### 5. Atomic Design

계층형 구조를 몇 가지 카테고리로 묶은 방법이다.

---

### Props

> [Passing Props to a Component](https://beta.reactjs.org/learn/passing-props-to-a-component)
>
> [Components와 Props](https://ko.reactjs.org/docs/components-and-props.html)
>

- 부모 컴포넌트에서 자식 컴포넌트로 props 전달이 가능함

- 부모 컴포넌트에서 props를 너무 잘게 잘라서 보내주면 자식이 개별로 사용/수정이 용이하지만 큰 덩어리로 보내면 줄줄이 props를 나열하지 않아도 된다는 장점이 있다.

- 자식 컴포넌트에서 props를 받을 때

```jsx
// 방법 1
function Avatar(props) {
  let person = props.person;
  let size = props.size;
  ...
}

// 방법 2
function Profile({ person, size }) {
 ...
}
```

두가지 방법이 있는데 우리는 방법 2를 택해서 실습해 볼 예정이다.

---

새로운 프로젝트 하나 생성

className을 쓸때는 디자이너 or 기획자가 정해준걸 그대로 쓰는것도 좋은 방법

```jsx
<div>
 <input type="checkbox" id="only-stock" />
 <label htmlFor="only-stock">Only show products in stock</label>
</div>

<div>
 <label>
 <input type="checkbox" />
  Only show products in stock
 </label>
</div>
```

input and label 사용 방법 두가지
