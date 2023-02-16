# React Component

[thinking in react](https://beta.reactjs.org/learn/thinking-in-react)

-> 단계별로 꼭 봐야만 하는 자료 !!!!!

STEP

1. 컴포넌트 계층구조로 UI 쪼개기
2. 리액트로 정적인 버전을 만들자

html css 로 퍼블리싱하던 작업과 크게 다르지 않음.

리액트의 기초가 되는 것...

이거 다음에는 상태관리-> 동적인 관리를 배워보자.

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

이건 개념적인 것.. 이미 우리가 쓰고 있었음

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

---

key에는 unique 값이 들어가야 한다. 효율적인 데이터 재배치를 위해

보편적으로 쓸 수 있는게 있으면 분리하면 된다.

1강이 정적 인것