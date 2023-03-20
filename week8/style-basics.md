# Style Basics

학습 키워드

- className
- 의미있는 마크업

---

의미있는 마크업을 하기 위해서는 `HTML Element 들이 어떤 의미를 가지고 있는지` 정확하게 알고 있어야 한다.

레퍼런스를 참고하여 HTML 마크업에 대해 정확하게 이해해보자.

참고 레퍼런스: [https://developer.mozilla.org/ko/docs/Web/HTML/Reference](https://developer.mozilla.org/ko/docs/Web/HTML/Reference)

---

## HTML

: Hypertext Markup Language

웹페이지와 그 내용을 구조화 하기 위해 사용하는 코드

콘텐츠의 구조를 정의하는 ***마크업 언어***

HTML은 웹 브라우저에 표시되는 글과 이미지 등의 다양한 콘텐츠를 표시하기 위해 "마크업"을 사용한다.

HTML 마크업은 아래와 같은 다양한 "Element"를 사용한다.

```html
<head>, <title>, <body>, <header>, <footer>, <article>, <section>, <p>, <div>, <span>, <img>, <aside>, <audio>, <canvas>, <datalist>, <details>, <embed>, <nav>, <output>, <progress>, <video>, <ul>, <ol>, <li>
```  

[HTML Element 참고서](https://developer.mozilla.org/ko/docs/Web/HTML/Element)

Element 각각에 대한 설명이 있다.

[HTML 특성 참고서](https://developer.mozilla.org/ko/docs/Web/HTML/Attributes)

---

## Basic class style

React 에서는 어떻게 스타일을 줄 수 있을까?

### 인라인 스타일

"style" 속성을 활용한다. js 객체를 활용하여 변수, 함수를 재사용하기 좋다.

- inline style

```jsx
<div style={{ height: 10 }}>
  Hello World!
</div>
```

- style attribute

```jsx
const divStyle = {
  color: 'blue',
};

function App() {
  return <div style={divStyle}>Hello World!</div>;
}
```

### className 을 통해 적용하기

`index.html`

```html
<style>
 .greeting {
  color: #00F;
 }
</style>
```

`Greeting.jsx`

```jsx
export default function Greeting() {
 return (
  <p className="greeting">
   Hello, world!
  </p>
 );
}
```

공통된 부분이 많을 때 재사용하기 쉬움

그러나 css 는 컴포넌트를 전제로 하지 않기 때문에 컴포넌트 중심으로 작업하려고 하면 불편할 수 있다.