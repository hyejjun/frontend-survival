# TDD (Test Driven Development)

__학습 키워드__

- TDD란
- Jest
- Describe - Context - It 패턴
- 단위테스트란

---

많이 테스트를 작성하는 훈련

여러번 반복해서 보면서 따라하기

TDD에서 중복을 제거한다는 게 어떤 의미인지 꼭 알아내기

---

## TDD란?

- 테스트 주도 개발
- 개발 구현 전에 `테스트 코드를 먼저 작성`한다.
  - 자동화된 테스트를 하기 위해서 테스트 코드를 작성한다는 것.
- 인터페이스와 스펙을 먼저 정의한다.
  - 화면 GUI가 아니라
  - 시그니처 : add (x,y) -> number
</br> [add 함수를 사용하면 number의 type을 갖는 어떤 결과 값이 나올거야]

  - 이러한 시그니처들을 모아 놓은 것이 인터페이스.

  - 스펙 : 예제가 있으면 거기에 대한 기대 값

> [테스트 주도 개발](https://github.com/ahastudio/til/blob/main/agile/test-driven-development.md)
>

> [TDD FAQ](https://github.com/ahastudio/til/blob/main/blog/2016/12-03-tdd-faq.md)
>

> [Jest를 이용한 간단한 TDD 예제](https://github.com/ahastudio/til/blob/main/jest/20201204-simple-tdd-example.md)
>

2번 3번 링크는 실습이 필요하다.

### 단위테스트와 TDD의 차이점은?

단위테스트: 구현에 대한 테스트
TDD : 자동화된 단위 테스트 코드를 먼저 작성하게 되어 테스트가 개발을 이끌어 나가도록 하는 방식

### [TDD Cycle]

#### 1. Red

- 실패하는 테스트 코드를 작성. 인터페이스와 스펙에 집중함.
- 인터페이스와 스펙을 어떻게 코드로 잘쓸까? 여기에 집중함

#### 2. Green

- 통과하는 테스트를 빠르게 작성한다.(올바른 방법이 아니여도 완전 괜찮음->리팩토링 할거임)
- 가짜로. 막무가내로 해도 상관 없음.

#### 3. Refactor

- 리팩토링으로 코드를 올바르게 함. (TDD에서 가장 중요한 부분)

중요한 건 이 사이클이 빠르게 돌아가야 한다는 것.

처음부터 완벽하게 하려고 하면 안된다.

TDD의 목표는 제대로 작동하는 코드 그 다음에 클린코드

리팩토링 : 동작은 바뀌지 않고 구현만 바뀐다.

TDD 사이클이 너무 느리고 복잡하다면 테스트 코드가 너무 크거나 중복 제거가 안되지는 않았나 확인해보자.

1분, 10분 이하로 테스트가 진행되게 한다.

`작고 쉬운 문제를 정의` 하고 `중복을 제거` 한다.

테스트 코드를 작성할때 중요한 점이자 개발시 클린 코드를 작성할때 중점이 되는 부분이다.

---

## Jest

일단 테스트 할 수 있는 도구 `jest`를 사용해보자.

> [Jest](https://jestjs.io/)
>

> [Given-When-Then](https://github.com/ahastudio/til/blob/main/blog/2018/12-08-given-when-then.md)
>

Given-When-Then 의 순서로 테스트 코드를 작성해주면 된다.

Describe - Context - It 패턴

### 테스트 케이스 정의하기

1. test 함수로 개별 테스트를 나열하는 방식

2. BDD 스타일로 주체 - 행위 중심으로 테스트를 조직화 하는 방식

1번 먼저 하고 그 다음 2번으로...

### 실습하기

- jest 설치가 되었는지 확인한다.

- 개발환경 초기 세팅 때 해두지만 혹시나 안되어 있다면 install 한다.

```bash
npm i -D jest @types/jest @swc/core @swc/jest \
    jest-environment-jsdom \
    @testing-library/react @testing-library/jest-dom
```

swc로 typescript 를 jest에 쓸 수 있도록 한 것.

jest에서 typescript 사용하도록 `jest.config.js` 파일을 작성한다.

-> 이게 없으면 js로 인식해서 테스트가 정상작동하지 않는다.

```js
module.exports = {
 testEnvironment: 'jsdom',
 setupFilesAfterEnv: [
  '@testing-library/jest-dom/extend-expect',
 ],
 transform: {
  '^.+\\.(t|j)sx?$': ['@swc/jest', {
   jsc: {
    parser: {
     syntax: 'typescript',
     jsx: true,
     decorators: true,
    },
    transform: {
     react: {
      runtime: 'automatic',
     },
    },
   },
  }],
 },
};
```

테스트용 파일을 생성

```bash
touch src/sample.test.ts
```

실행할때는

```bash
npx jest

or

npx jest --watchAll
```

#### ex1)

`sample.test.ts`

```ts
function add(x:number, y:number): number {
  return 1 + 2;
}

test('add', () => {
  expect(add(1, 2)).toBe(3);
});
```

이렇게 1 + 2라고 되어있던 부분을

의도를 드러내면서 코드를 변경

어떤거를 테스트 할거고 그 결과가 어떻게 될거다 라는것을 드러냄

중복을 제거함

```ts
function add(x:number, y:number): number {
  return x + y;
}
```

#### ex2)

BDD 스타일로 테스트 대상과 행위를 명확하게 드러낸다.

```ts
describe('add 함수는', () => {
  it('두 숫자의 합을 리턴한다', () => {
    expect(add(1, 2)).toBe(3);
  });
});
```

이렇게 짧은 경우에는 위에 처럼 간단히 test로 작성하지만

예를들어 add 함수 인자값이 여러개인 경우 이렇게 작성할 수 있다.

```ts
function add(...numbers: number[]): number {
  return (numbers[0] ?? 0) + (numbers[1] ?? 0);
}


// 보통 describe 안에서 사용하는 describe는 context라고 많이 쓴다.
// 이렇게 명시해주고 context로 사용하자.
const context = describe;

describe('add', () => {
  context('with no argument', () => {
    it('returns zero', () => {
      expect(add()).toBe(0);
    });
  });

  context('with only one argument', () => {
    it('returns the same number', () => {
      expect(add(1)).toBe(1);
    });
  });

  context('with two arguments', () => {
    it('returns sum of two number', () => {
      expect(add(1, 2)).toBe(3);
    });
  });
});
```

이렇게 하면 테스트 자체는 통과한다.

문제점 1) 그런데 여기서 인자값이 더 늘어난다면?

문제점 2) 중복 제거가 필요함

add 함수 인자값이 2개로 고정된 경우

```ts
(numbers[0] ?? 0) + (numbers[1] ?? 0)
```

add 함수 인자값이 3개인 경우 - 1

```ts
function add(...numbers:number[]): number {
  return (numbers[numbers.length - 3] ?? 0)
  + (numbers[numbers.length - 2] ?? 0)
  + (numbers[numbers.length - 1] ?? 0);
}
```

add 함수 인자값이 3개인 경우 - 2

```ts
function add(...numbers:number[]): number {
  if (numbers.length === 0) {
    return 0;
  }

  if (numbers.length === 1) {
    return numbers[0];
  }

  if (numbers.length === 2) {
    return numbers[0] + numbers[1];
  }

  if (numbers.length === 3) {
    return numbers[0] + numbers[1] + numbers[2];
  }
}
```

이렇게 중복 되는 부분이 많이 생긴다.

```ts
  if (numbers.length >= 1) {
    return add(...numbers.slice(0, numbers.length - 1)) + numbers[numbers.length - 1];
  }
```

이렇게 되어있던 부분을
하나씩 줄여나간다.

add 함수 안에

`if (numbers.length === 0)` 부분이 있으니까 length가 1 이상인 경우의 조건문을 제외해주자.

1차 수정

```ts
  return add(...numbers.slice(0, numbers.length - 1)) + numbers[numbers.length - 1];
```

2차 수정

재귀함수는 보통 `reduce`로 처리가 가능하다.

```ts
  return numbers.reduce((acc, number) => acc + number);
```

이젠 이렇게 작성해줬기 때문에

```ts
  context('with ten arguments', () => {
    it('returns sum of ten number', () => {
      expect(add(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)).toBe(55);
    });
  });
```

인자값이 아무리 늘어나도 add 함수를 변경해줄 필요가 없다.

---

좀 더 어려운 예제는 맨 위에 작성한 링크를 참고한다.
