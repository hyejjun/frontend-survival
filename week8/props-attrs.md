# styled-components 의 props 와 attrs

Styled Components는 React 컴포넌트에 넘어온 props에 따라 다른 스타일을 적용하는 기능을 제공합니다.

---

## props 활용하기

```tsx
import React from "react";
import styled, { css } from 'styled-components';

type ButtonProps = {
  active?: boolean;
  background?: string;
}

const StyledButton = styled.button`
  color: ${(props) => (props.active ? '#F00' : '#888')};
  background: ${(props) => props.background || "white"};
  ${(props) => props.active && css`
    font-weight: bold;
  `}
`;

function Button({ active, background }) {
  return (
    <StyledButton active={active} background={background} Î>
      hello world
    </StyledButton>
  );
}
```

이렇게 props의 값에 따라서 특정 스타일을 잡아줄 수 있다.

근데 여기서 `import { css } from 'styled-components';` 얘는 뭐지??

여러 줄의 CSS 코드를 조건부로 보여주고 싶다면 css 를 사용해야 한다.

css 를 불러와서 사용을 해야 그 스타일 내부에서도 다른 props 를 조회 할 수 있다.

그냥 string 형태로 써줘도 되긴 하는데 보기 불편하다.

---

## 속성 추가 (attrs)

html element 의 기본속성을 attrs로 설정할 수 있다.

불필요하게 반복되는 속성을 처리할 때 유용하다.

```html
<!-- button -->
<button type="button">
<button type="submit">

<!-- input -->
<input type="text">
<input type="password">
```

같은 경우가 있다.

`attrs`로 어떻게 적용하는지 확인해보자.

- button 속성 적용

```tsx
import styled from 'styled-components';

const Button = styled.button.attrs({
 type: 'button',
})`
 border: 1px solid #888;
 background: transparent;
 cursor: pointer;
`;

export default Button;
```

- input 속성 적용

```tsx
const Input = styled.input.attrs(props => ({
  type: "text",
  size: props.size || "1em",
}))`
  border: 2px solid palevioletred;
  margin: ${props => props.size};
  padding: ${props => props.size};
`;

const PasswordInput = styled(Input).attrs({
  type: "password",
})`
  border: 2px solid aqua;
`;

render(
  <div>
    <Input placeholder="A bigger text input" size="2em" />
    <PasswordInput placeholder="A bigger password input" size="2em" />
  </div>
);
```