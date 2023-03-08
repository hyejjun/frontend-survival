# TSyringe

__학습 키워드__

- TSyringe
- 의존성 주입(Dependency Injection)
- reflect-metadata
- sington (싱글톤)

---

## TSyringe란?

TypeScript용 DI 도구. External Store를 관리하는데 활용 가능하다.

React 컴포넌트 입장에서는 `전역` 변수처럼 느껴짐

`"Props Drilling"` 문제를 해결할 수 있는 방법 중 하나다.

=> props가 부모로부터 계속 내려가는 그것...

중간 컴포넌트는 이 props를 사용하지 않아도 받아서 넘겨줘야 하는 불편함이 있다.

그래서 이런걸 해결해야하는데...

Context는 전체를 바꿔야 하기 때문에 조금 비효율적이다..

우리는 간단하게 TSyringe를 이용하게 처리를 할 것.

이걸 사용하려면 TSyringe와 얘가 내부적으로 사용하고 있는 reflect-metadata를 설치한다.

```bash
npm i tsyringe reflect-metadata
```

싱글톤으로 관리할 CounterStore 클래스를 준비하기 위해 파일을 생성한다.

```bash
touch src/stores/Store.ts
```

```ts
import { singleton } from 'tsyringe';

@singleton()
export default class Store {
	count = 0;
}
```

실습 진행..