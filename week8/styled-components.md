# styled-components

__학습 키워드__

- styled-componets

---

유명한 라이브러리들은 대부분 ‘왜’ 만들어졌는지 품고 있는 철학 등이 잘 정리되어 있는 경우가 많습니다.

라이브러리를 사용할 때는 공식문서를 꼭 살펴보는 습관을 하시는 게 좋습니다.

참고 레퍼런스: [https://styled-components.com/docs/basics](https://styled-components.com/docs/basics)

---

1. 용어의 정의(정확한 정의)
2. 역사 또는 왜 필요한가/왜 생겼는가
3. 특징 (또는 장/단점)
4. 실제 사용 사례나 경험

---

## styled-components 란?

1. 용어의 정의(정확한 정의)

CSS in JS 라이브러리이다. 그 중 가장 많이 쓰이기도 한다.

2. 역사 또는 왜 필요한가/왜 생겼는가

styled-components 는 React 컴포넌트 시스템의 스타일을 지정하기 위해 CSS를 어떻게 개선할 수 있을지 고민을 통해 탄생했다.

3. 특징 (또는 장/단점)

styled-components 는 이러한 것들을 제공한다.

- Automatic critical CSS
  - 페이지에서 어떤 컴포넌트가 렌더링되는지 추적하고 자동으로 스타일을 삽입한다.
  - 사용자가 필요한 최소한의 코드만을 로드 할 수 있다.

- No class name bugs
  - class 이름을 자동으로 생성하기 때문에 중첩될 가능성이 없다.

- Easier deletion of CSS
  - css in css는 해당 스타일이 어디서 쓰이고 있는지 알 수 없어 삭제가 어렵다.
  - 반면에 styled-components는 컴포넌트 자체와 css가 연결되어 있기 때문에 컴포넌트가 사용되지 않으면 스타일을 삭제하기가 수월하다.

- Simple dynamic styling

- Painless maintenance

- Automatic vendor prefixing

---

## Styled-componenets 설치하기

- VS Code Extension을 설치한다.

- 패키지를 설치한다.

우리는 swc를 사용하므로 여기서 swc에서 쓸 수 있도록 포팅한 것도 설치해야 한다.

```bash
npm i styled-components
npm i -D @types/styled-components @swc/plugin-styled-components
```

`.swcrc` 작성

```js
{
 "jsc": {
  "experimental": {
   "plugins": [
    [
     "@swc/plugin-styled-components",
     {
      "displayName": true,
      "ssr": true
     }
    ]
   ]
  }
 }
}
```

---

## 사용하기

1. 기본적인 사용 방법

```jsx
import styled from 'styled-components';

const Paragraph = styled.p`
 color: #00F;
`;

export default function Greeting() {
 return (
  <Paragraph>
   Hello, world!
  </Paragraph>
 );
}
```

2. Styled Componenet에 추가로 스타일 입히기

```jsx
import styled from 'styled-components';

const Paragraph = styled.p`
 color: #00F;
`;

// Paragraph 에다가 추가로 스타일을 입혔다.
const BigParagraph = styled(Paragraph)`
 font-size: 5rem;
`;

export default function Greeting() {
 return (
  <BigParagraph>
   Hello, world!
  </BigParagraph>
 );
}
```

3. 기존 컴포넌트에 스타일 입히기

- 기존 컴포넌트가 Class를 잡아줘야 함!
  - `className={className}` 이런식으로..

```jsx
import styled from 'styled-components';

function HelloWorld({ className }: React.HTMLAttributes<HTMLElement>) {
 return (
  <p className={className}>
   Hello, world!
  </p>
 );
}

const Greeting = styled(HelloWorld)`
 color: #00F;
`;

export default Greeting;
```
