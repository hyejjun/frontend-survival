# Routing

__학습 키워드__

- HTML DOM API
  - Location
  - pathname

---

1. 용어의 정의(정확한 정의)
2. 역사 또는 왜 필요한가/왜 생겼는가
3. 특징 (또는 장/단점)
4. 실제 사용 사례나 경험

---

## Routing

일반적인 웹사이트를 만들때 필연적으로 사용하는 기술 중 하나다.

웹사이트를 만든다고 한다 하면 어떤 주소로 들어가냐에 따라서 다른 화면이 나오게 된다.

이런것들을 만드는 방법이 여러가지 있는데 리액트를 사용하니 하나의 웹페이지를 하나의 컴포넌트로 만들고 url에 따라서 적절한 컴포넌트를 보이게 한다.

## HTML DOM API

1. 정의 및 사용성

: Document Object Model(DOM)은 HTML, XML 문서의 프로그래밍 인터페이스이다.

- 여기서 인터페이스는 상호 작용할 수 있는 부분이라고 생각하면 된다.

DOM API는 DOM의 구조에 접근해서 문서의 내용 (구조, 스타일, 내용 등)을 변경할 수 있게 한다.

2. 사용 사례

DOM API는 다양하게 있지만 그 중에 오늘은 `location` 과 `pathname` 에 대해 다뤄볼 것이다.

```js
window.location
```

```js
window.location.pathname
```

예시

전체 URL

```.
https://developer.mozilla.org/ko/docs/Web/API/Location
```

`location.pathname`

'/ko/docs/Web/API/Location'

`location.hostname`

'developer.mozilla.org'

`location.host`

'developer.mozilla.org'
(:port 가 있다면 :port 까지 포함)

---

### React 에서 실습하기

```tsx
export default function App() {
  const { pathname } = window.location;
  return (
    <div>
      {pathname}
    </div>
  );
}
```

```.
http://localhost:8080/about

- location.pathname

'/about'


- location.host

'localhost:8080'


- location.hostname

'localhost'

```

`pathname`

/

/about

URI에 따라 다르게 나온다.

---

### React 로 실습하기

1. window.laction.pathname을 이용해서 라우팅하기

```tsx
import Footer from './components/Footer';
import Header from './components/Header';

import AboutPage from './pages/AboutPage';
import HomePage from './pages/HomePage';

export default function App() {
  const { pathname } = window.location;

  return (
    <div>
      <Header />
      <main>
        {pathname === '/' && <HomePage />}
        {pathname === '/about' && <AboutPage />}
      </main>
      <Footer />
    </div>
  );
}

```

2. 페이지에 대한 컴포넌트들을 모아서 처리하는 방법

```tsx
import Footer from './components/Footer';
import Header from './components/Header';
import AboutPage from './pages/AboutPage';
import HomePage from './pages/HomePage';

/*
const pages: Record<string, React.FC> = {
  '/': HomePage,
  '/about': AboutPage,
};
-> 강제로 타입을 잡아준다.
*/

const pages = {
  '/': HomePage,
  '/about': AboutPage,
};

export default function App() {
  const { pathname } = window.location;

 // const Page = pages[pathname] || HomePage;
  const Page = Reflect.get(pages, pathname) || HomePage;  // 암것도 없으면 HomPage

  return (
    <div>
      <Header />
      <main>
        <Page />
      </main>
      <Footer />
    </div>
  );
}
```

```tsx
const Page = pages[pathname] || HomePage;

const Page = Reflect.get(pages, pathname) || HomePage;
```

원래는 pages['/about'] 이런식으로 객체안에 있는 저 값을 반환받고자 했는데 타입에러가 나서

Refelct.get()으로 변경해주었다.

#### `Reflect.get()`

객체 혹은 배열에서 사용가능하다.

```js
const object1 = {
  x: 1,
  y: 2
};

console.log(Reflect.get(object1, 'x'));
// 예상 결과: 1

const array1 = ['zero', 'one'];

console.log(Reflect.get(array1, 1));
// 예상 결과: "one"
```

---

`App.test.ts`

여기서 주소에 따라서 잘 넘어가는지 테스트를 해보고 싶음.

근데 테스트에는 window가 없음

그래서 우리가 할 수 있는건

```js
const { pathname } = window.location;
```

얘를 추상화 하는 것.

이런 부분들을 직접하지 않고 React Router를 사용해서 해보자.