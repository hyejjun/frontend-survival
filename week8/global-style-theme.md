# Global Style & Theme

__학습 키워드__

- Reset CSS
- box model
- Theme
- ThemeProvider

---

## Reset CSS

`reset.css`

```css
*{
 margin: 0;
 padding: 0;
 border: 0;
 font-size: 100%;
 font: inherit;
 vertical-align: baseline;
}

ol, ul {
 list-style: none;
}

table {
 border-collapse: collapse;
 border-spacing: 0;
}
```

우리가 프로젝트를 처음 시작할때 브라우저에서 기본적으로 잡혀있는 패딩이나 마진같은거 싹 지우고 시작을 한다.

css로 reset 해주고 작업을 하는데 이거를 styled-components에서 적용해보자.

## styled-reset 사용하기

### 1.styled-reset 패키지 설치

```bash
npm i styled-reset
```

### 2. App 컴포넌트에서 사용

```tsx
import { Reset } from 'styled-reset';

export default function App() {
 return (
  <>
   <Reset />
   <Greeting />
  </>
 );
}
```

---

## Global Style

### 1. 전역 스타일 작성하기

`src/styles/GlobalStyle.ts`

```ts
import { createGlobalStyle } from 'styled-components';

const GlobalStyle = createGlobalStyle`
 html {
  box-sizing: border-box;
 }
 
 *,
 *::before,
 *::after {
  box-sizing: inherit;
 }
 
 html {
  font-size: 62.5%;
 }
 
 body {
  font-size: 1.6rem;
 }
 
 :lang(ko) {
  h1, h2, h3 {
   word-break: keep-all;
  }
 }
`;

export default GlobalStyle;
```

기본적으로 적용이 되야할 스타일을 GlobalStyle에서 적용한다.

하나씩 보자.

1. `box-sizing: border-box;`

박스에서 border부터 width가 측정되게 함.

테두리와 안쪽 여백의 크기도 요소의 크기로 고려하는 것.

정말 많이 쓰는데 이거를 global로 잡아주자.

2. `font-size: 62.5%`

이거는 약간 꼼수 같은거

```css
html {
  /* 62.5% of 16px browser font size is 10px */
  font-size: 62.5%;
}

.some-element {
  /* 1.2 * 10px = 12px */
  font-size: 1.2rem;
}
```

브라우저의 루트 글꼴 크기는 16px이다.

루트 글꼴 크기를 10px로 만들려면 루트 글꼴 크기를 픽셀 단위로 설정하면 된다.

10 단위로 계산하는 것이 편리하기 때문,

루트는 이렇게 설정하고 필요한 곳에서 `rem`을 통해 조절할 수 있다.

3. `word-break: keep-all;`

```css
:lang(ko) {
  h1, h2, h3 {
   word-break: keep-all;
  }
}
```

```.
안녕하
세요
```

이런식으로 줄바꿈 되는 경우를 방지한다. (한글로 한정함)

### 2. 전역 스타일 App 컴포넌트에 적용하기

```tsx
import { Reset } from 'styled-reset';

import GlobalStyle from './styles/GlobalStyle';

export default function App() {
 return (
  <>
   <Reset />
   <GlobalStyle /> { /* 전역스타일 적용 */ }
   <Greeting />
  </>
 );
}
```

---

## Theme

이거는 정말 정말 중요하고 유용하다.

디자인 시스템의 근간을 마련하는데 활용.

다크 모드 등 특정 모드를 만들어서 사용하는 경우에 활용 가능함.

눈에 보이는 단편적인 정보를 넘어서, “의미”에 집중할 수 있게 됨.

`기본 Theme : defaultTheme 을 정의하자.`

`src/styles/defaultTheme.ts`

```ts
const defaultTheme = {
 colors: {
  background: '#FFF',
  text: '#000',
  primary: '#F00',
  secondary: '#00F',
 },
};

export default defaultTheme;
```

의미를 명확히 드러낸다는 것

=> `white : "#FFF"` 라고 쓰지 않고

`background : "#FFF"` 이런식으로 쓰라는 뜻

`App.tsx`

```tsx
import { ThemeProvider } from 'styled-components';

import { Reset } from 'styled-reset';

import defaultTheme from './styles/defaultTheme';

import GlobalStyle from './styles/GlobalStyle';

export default function App() {
 return (
  <ThemeProvider theme={defaultTheme}> {/* ThemeProvider 를 활용하여 theme을 설정한다.*/}
   <Reset />
   <GlobalStyle />
   <Greeting />
  </ThemeProvider>
 );
}
```

`src/styles/GlobalStyle.ts`

```ts
import { createGlobalStyle } from 'styled-components';

const GlobalStyle = createGlobalStyle`
 body {
  background: ${(props) => props.theme.colors.background};
  color: ${(props) => props.theme.colors.text};
 }
 
 a {
  color: ${(props) => props.theme.colors.text};
 }
 
 button,
 input,
 select,
 textarea {
  background: ${(props) => props.theme.colors.background};
  color: ${(props) => props.theme.colors.text};
 }
`;

export default GlobalStyle;
```

`props.theme` 을 사용할 수 있다.

현재 theme=defaultTheme 이므로 defaultTheme에서 설정한 값으로 적용이 될 것이다.

그런데 이렇게만 설정하면 theme.`colors` -> 여기서 colors를 찾을 수 없다는 에러가 뜬다.

타입을 정해줘야한다.

`src/styles/Theme.ts`

```ts
import defaultTheme from './defaultTheme';

type Theme = typeof defaultTheme;

export default Theme;
```

defaultTheme을 가져와서 그거의 type만 뽑아낸다.

다른 theme을 추가할 때 Theme 타입을 사용.

항상 defaultTheme에 먼저 항목을 추가/삭제하고, 나머지를 여기에 맞추면 된다.

`src/styles/styled.d.ts`

```ts
import 'styled-components';

import Theme from './Theme'

declare module 'styled-components' {
 export interface DefaultTheme extends Theme {}
}
```

그런다음 defaultTheme의 type을 가져와서 DefaultTheme 의 타입을 잡아준다.

앞으로 생성될 다크모드나 다른 모드의 Theme도 `type Theme` 을 기반으로 한다.

그럼 생성될 모드들에서 실수로 속성이 빠질일이 없다. (타입으로 잡아주기 때문)

---

## Theme 활용하기 - 다크모드

`App.tsx`

```tsx
import { useDarkMode } from 'usehooks-ts';

import { ThemeProvider } from 'styled-components';

import { Reset } from 'styled-reset';

import defaultTheme from './styles/defaultTheme';
import darkTheme from './styles/darkTheme';

import GlobalStyle from './styles/GlobalStyle';

import Greeting from './components/Greeting';
import Button from './components/Button';

export default function App() {
const { isDarkMode, toggle } = useDarkMode();

const theme = isDarkMode ? darkTheme : defaultTheme;

return (
 <ThemeProvider theme={theme}>
  <Reset />
  <GlobalStyle />
  <Greeting />
  <Button onClick={toggle}>
   Dark Theme Toggle
  </Button>
 </ThemeProvider>
 );
}
```

- useDarkMode
  - usehooks-ts 에서 가져온다. localStorage를 써서 새로고침시에도 다크모드를 유지할 수 있다.

- darkMode를 하나 만들어준다.

```ts
import Button from './components/Button';

export default function App() {

  const { isDarkMode, toggle } = useDarkMode();

  const theme = isDarkMode ? darkTheme : defaultTheme;

  return(
      <Button onClick={toggle}>
        Dark Theme Toggle
      </Button>
  )
}
```

핵심만 보자면, Button 컴포넌트에서 toggle을 통해 theme을 변경할 수 있게 한다.

클릭될 때 마다 isDarkMode 가 변경되고 theme이 darkMode 가 되었다가 defaultMode가 되었다가 한다.

그러면 useDarkMode를 통해 손쉽게 테마를 변경할 수 있다.

---

### test file - `TypeError: window.matchMedia is not a function`

Jest 테스트에서 “window.matchMedia” 문제 발생한다.

`TypeError: window.matchMedia is not a function`

이런 에러가 발생하면 공식문서에 제공하는 해결법대로 해보자.

> [Mocking methods which are not implemented in JSDOM](https://jestjs.io/docs/manual-mocks#mocking-methods-which-are-not-implemented-in-jsdom)
>

`src/setupTests.ts` 파일에 공식 문서에 나온 코드를 넣으면 해결된다.

```tsx
Object.defineProperty(window, 'matchMedia', {
 writable: true,
 value: jest.fn().mockImplementation((query) => ({
  matches: false,
  media: query,
  onchange: null,
  addListener: jest.fn(), // deprecated
  removeListener: jest.fn(), // deprecated
  addEventListener: jest.fn(),
  removeEventListener: jest.fn(),
  dispatchEvent: jest.fn(),
 })),
});
```

이대로 넣으면 문제 해결 완료