# 2주차 과제

## JSX 없이 React 사용하기

---

### 로컬에서 E2E 테스트

- E2E 테스트는 앱이 요구사항대로 동작하는지 브라우저를 띄우고 자동으로 테스트합니다.

```bash
npm run codeceptjs
```

-> 이게 뭘까??

### CI에서 동일한 테스트 방법

```bash
npm run lint

npx tsc --noEmit

npm run test

npm run build

npm run ci
```
