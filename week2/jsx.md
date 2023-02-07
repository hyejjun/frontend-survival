# JSX

XML-like syntax extension to JavaScript

=> XML 같은 __문법 확장__ => JavaScript로..

- XML 처럼 작성된 부분을 React.createElement을 쓰는 JavaScript 코드로 변환한다.
- 중괄호 { } 를 써서 JavaScript 코드를 그대로 쓸 수 있음
- JavaScript 코드와 1:1로 매칭됨

---

## Babel 에서 실습하기

- "Presets"에서 react 체크 OR 플러그인에서 “@babel/plugin-transform-react-jsx”를 추가해서 JSX 실습을 진행하자.

### 예제 1)

__JSX 코드__

```jsx
<p>Hello, world</p>
```

내가 이렇게 jsx에서 작성을 하면...

</br>

__변환된 JS 코드__

```js
React.createElement("p", null, "Hello, world!");
```

JavaScript로 이렇게 변환된다.

</br>

### 예제 2)

jsx

```jsx
<Greeting name="world"/>
```

js

```js
React.createElement("Greeting", {
 name: "world"
});
```

</br>

### 예제 3)

jsx

```jsx
<button type="submit">send</button>
```

js

```js
React.createElement("button", {
 type: "submit"
}, "send");
```

</br>

### 예제 4)

jsx

```jsx
<div className="test">
 <p>Hello, world!</p>
 <Button type="submit">Send</Button>
</div>
```

js

```js
React.createElement("div",
{ className: "test" },
React.createElement("p", null, "Hello, world!"),
React.createElement("Button", { type: "submit" }, "Send")
);
```

</br>

### 예제 5)

jsx

```jsx
<div>
 <p>Count: {count}!</p>
 <button type="button" onClick={() => setCount(count + 1)}>Increase</button>
</div>
```

js

```js
React.createElement(
"div",
null,
React.createElement("p", null, "Count: ", count, "!"),
React.createElement("button", { type: "submit" }, onClick={() => setCount(count + 1)}, "Increase")
);
```

---

### React에서 jsx는 필수가 아니다

__jsx__

```jsx
function Hello {
  render() {
    return <div>Hello</div>;
  }
}

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<Hello />);
```

__js__

```js
function Hello {
  render() {
    return React.createElement('div', null, `Hello`);
  }
}

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(React.createElement(Hello, null));
```

JSX 대신 __React.createElement__ 를 써서 React Element 트리를 갱신하는데 쓸 수 있다.

---

</br>

### VDOM (Virtual DOM)

- UI의 가상적인 표현을 메모리에 저장하고 ReactDOM 과 같은 라이브러리에 의해 ActualDom 과 비교해서 동기화하는 프로그래밍 개념

- 이 과정을 __재조정__ 이라고 함.

- 이 접근 방식이 React의 __선언적 API__ 를 가능하게 한다.
-> 선언적 API ... 이게 뭐지??

</br>

#### VDOM을 쓰는 이유는?

_빨라서 VDOM을 쓴다? => X 아님!!!_

- fast enough : 사용하기에 충분히 빠름
  - 컴포넌트 분할하는 방법만 바꿔도 퍼포먼스가 올라감

- maintainable : 유지보수가 가능한 APP 만드는걸 도와줌.
