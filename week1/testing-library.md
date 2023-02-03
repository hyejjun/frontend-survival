## Jest - testing library

---

[테스트 작성 시 참고할 부분](https://www.betterspecs.org/) <br/>
-> 어떤 식으로 테스트를 작성해야 하는지 참고 할 수 있음. 문법은 다름.
<br />

---
Given when then

---



```bash
npm run watch:test
```
-> 파일의 변화를 감지할 때마다 테스트 진행.

테스트를 먼저 짜고 구현부를 만든다.

<br/>

간단한 예제.
```ts
function add(x: number, y: number): number {
  return x + y;
}

test("숫자 더하기", () => {
  expect(add(1, 2)).toBe(3);
});

describe('add 함수',()=>{
	it('returns sum of two numbers',()=>{
		expect(add(1,2)).toBe(3);
	})
})

describe("add 함수", () => {
  context("두개의 양수가 주어졌을때", () => {
    it("항상 두 개의 숫자보다 큰 값을 돌려준다.", () => {
      expect(add(1, 2)).toBe(3);
    });
  });
});

```

테스트는 여러 생각, 다른 케이스에 대해 생각하게 한다...

---

<br/>

## React Test Library

`Greeting.tsx`
```tsx
export default function Greeting({name}: {name: string}) {
	return <div>hello {name}</div>;
}
```

`Greeting.test.tsx`
```tsx
import { render, screen } from "@testing-library/react";

import Greeting from "./Greeting";

test('Greeting', () => {
  render(<Greeting name="길동" />);

  screen.getByText('hello 길동'); //정확하게 일치
  screen.getByText(/hello/); // 포함하는지? 못찾으면 에러

  expect(screen.queryByText(/hi/)).not.toBeInTheDocument(); // 없어야함
  expect(screen.queryByText(/hi/)).toBeInTheDocument(); // 있어야함
  expect(screen.queryByText(/hi/)).not.toBeVisible(); // 안보여야함
});
```

-> 컴포넌트만 테스트 하는것은 피하는게 좋다. 본질에 집중하지 못하게 됨.

---

### jest-dom testing-library 기능을 확인 할 수 있다.

[jest-dom](https://github.com/testing-library/jest-dom)

