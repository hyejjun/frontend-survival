# styled-components 의 props 와 attrs

Styled Components는 React 컴포넌트에 넘어온 props에 따라 다른 스타일을 적용하는 기능을 제공합니다.

---

## props 활용하기

```tsx
import styled, { css } from 'styled-components';
import { useBoolean } from 'usehooks-ts';

type ParagraphProps = {
  active? : boolean
}

const Paragraph = styled.p<ParagraphProps>`
  color : ${(props) => (props.active ? '#e91717' : '#043bf0')};
  font-size: 1em;

  ${(props) => props.active && css`
    font-weight: bold;
  `}
`;

export default function DefaultMain() {
  const { value: active, toggle } = useBoolean();

  return (
    <div>
      <Paragraph active={active}>
        TEST
      </Paragraph>
      <button type="button" onClick={toggle}>
        toggle
      </button>
    </div>
  );
}
```

### props를 사용할때는 props의 type을 선언한다.

```tsx
type AAAProps = {
  a? : boolean;
  b? : string;
}
```

props가 있을 수도 없을 수도 있기 때문에 변수 뒤에 `?` 붙여준다.

#### 만약 props에 `?`를 안붙이고 선언시에 styled components 만 쓰면 어떻게 될까?

그러면 무조건 이 props를 요구하게 된다.

```tsx
<StyledButton>hello world</StyledButton>
```

Error

```.
Property 'active' is missing in type '{ children: string; }' but required in type
```

이런식으로 에러가 난다.

</br>

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

- attrs를 props로 잡아주기

```tsx
import styled, { css } from 'styled-components';
import { useBoolean } from 'usehooks-ts';

type ButtonProps = {
  active? : boolean;
  type? : 'button' | 'submit' | 'reset';
}

const Button = styled.button.attrs<ButtonProps>((props) => ({
  type: props.type ?? 'button',
}))<ButtonProps>`
  color: ${(props)=>(props.active ? 'red' : 'blue')};
`;

export default function DefaultMain() {
  const { value: active, toggle } = useBoolean();

  return (
    <div>
      <Button onClick={toggle} active={active}>
        toggle
      </Button>
    </div>
  );
}
```

---

- Button and BigButton 예제

```tsx
import styled, { css } from 'styled-components';
import { useBoolean } from 'usehooks-ts';

type ButtonProps = {
  active? : boolean
  type? : 'button' | 'submit' | 'reset'
}

const Button = styled.button.attrs<ButtonProps>((props) => ({
  type: props.type ?? 'button',
}))<ButtonProps>`
  color: blue;
`;

const BigButton = styled(Button)`
  ${(props) => props.active && css`
    color: pink;
  `}
`;

export default function Switch() {
  const { value: active, toggle } = useBoolean();

  return (
    <div>
      <Button onClick={toggle} active={active}>
        toggle
      </Button>
      <BigButton onClick={toggle} active={active} type="submit">
        Big button
      </BigButton>
    </div>
  );
}
```

주의할 점!!

BigButton을 사용할때 Button의 props를 사용하는건 아니다.

컴포넌트마다 각기 다른 props값을 내려줘야함. 타입만 같이 쓰는것.

