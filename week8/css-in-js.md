# CSS in JS

__학습 키워드__

- CSS in JS 란
- CSS

---

CSS 를 잘 사용하기 위해선 CSS 자체에 대해서 잘 알고 있어야 합니다.

레퍼런스를 참고하여 CSS 를 정리해봅시다.

스타일 자체는 굉장히 많기 때문에 자주 사용하는 것들 위주로 찾아서 정리하시면 좋습니다.

특히나 선택자, 박스모델, media query, Flexbox, Grid 정도는 필수적으로 알고 계셔야 합니다.

참고 레퍼런스:

- [https://developer.mozilla.org/ko/docs/Web/CSS/Reference](https://developer.mozilla.org/ko/docs/Web/CSS/Reference)
- [flexbox](https://css-tricks.com/snippets/css/a-guide-to-flexbox/)
- [Grid](https://css-tricks.com/snippets/css/complete-guide-grid/)

---

1. 용어의 정의(정확한 정의)
2. 역사 또는 왜 필요한가/왜 생겼는가
3. 특징 (또는 장/단점)
4. 실제 사용 사례나 경험

---

## CSS in JS 란?

### 1. 정의

: CSS-in-JS는 JavaScript를 사용하여 컴포넌트의 스타일을 지정하는 스타일링 기법이다.

### 2. 왜 필요한가?

CSS를 작성하는 어려움은 다음과 같다.

```.
- Global namespace: 글로벌 공간에 선언된 이름의 명명 규칙 필요
- Dependencies: CSS간의 의존 관계를 관리
- Dead Code Elimination: 미사용 코드 검출
- Minification: 클래스 이름의 최소화
- Sharing Constants: JS와 CSS의 상태 공유
- Non-deterministic Resolution: CSS 로드 우선 순위 이슈
- Isolation: CSS와 JS의 상속에 따른 격리 필요 이슈
```

CSS-in-JS로 위의 이슈를 모두 해결할 수 있다고 한다.

①②③④⑤⑥⑦⑧⑨⑩

### 3. 특징 (장/단점)

### CSS-in-JS의 장점 : 통합 스타일링 언어

> [A Unified Styling Language (2017)](https://blog.rhostem.com/posts/2017-06-24-unified-styling-language)

1. Scoped styles (범위가 지정된(scoped) 스타일)

2. Critical CSS (필수적인(critical) CSS)

    -> CSS-in-JS는 기본적으로 필수적인 CSS가 우선해서 작동하도록 요구한다.

3. Smarter optimisations (더 똑똑한 최적화)

4. Package management (패키지 관리)

5. Non-browser styling (브라우저가 아닌 환경의 스타일링)

__[장점]__

__① 지역 스코프 스타일 적용 가능__

-> 일반적인 css를 사용하게되면 class를 사용할때 이름이 겹치지 않는지 확인하는 귀찮은 과정이 필요하다. 확인하는 건 개발자의 몫...

그런데 css in js 를 사용하게 되면 지역 스코프로 지정하게 되므로 이러한 문제를 해결 할 수 있다.

__② Colocation 가능__

단일 컴포넌트에 관련된 모든 것을 같은 위치에 두는 것

일반적인 css 를 사용하면 css와 js가 별도의 파일에 위치하게 됨
css 파일의 위치와 관계없이 스타일이 전역으로 적용되므로 colocation을 구현하기 어렵다.

반면에 CSS in JS 를 사용하면 스타일을 사용하는 리액트 컴포넌트 내부에 직접 스타일을 작성할 수 있으므로 앱의 유지보수가 편해진다.

__③ js 변수를 style에 사용할 수 있다.__

__[단점]__

① 런타임 오버헤드를 더한다.

② 번들 크기를 늘린다.

③ React devTools를 어지럽힌다.

---

> [All You Need To Know About CSS-in-JS (2017)](https://d0gf00t.tistory.com/22)
>

CSS-in-JS를 사용하면 무슨 장점이 있는가?

- 컴포넌트로 생각하기
  - 더이상 스타일시트의 묶음을 유지보수 할 필요가 없습니다.
  - CSS-in-JS는 CSS 모델을 문서 레벨이 아니라 컴포넌트 레벨로 추상화합니다(모듈성).

- CSS-in-JS는 JavaScript 환경을 최대한 활용하여 CSS를 향상시킵니다.

- "진정한 분리 법칙"
  - CSS에는 명시적으로 정의 하지 않은 경우, 부모 요소에서 자동으로 상속되는 속성이 있습니다.
  - jss-isolate 플러그인 덕분에 JSS 규칙은 부모 요소의 속성을 상속하지 않습니다.

- 스코프가 있는 선택자—CSS는 하나의 전역 네임스페이스만 있습니다. 복잡한 애플리케이션 내에서 선택자 충돌을 피할 수 없습니다.
  - BEM과 같은 네이밍 컨벤션은 한 프로젝트 내에서는 도움이 되지만, 서드파티 코드를 통합할 때는 도움이 되지 않습니다. JSS는 JSON으로 표현된 것을 CSS로 컴파일 할 때, 기본적으로 고유한 이름을 생성합니다.

- 벤더 프리픽스—생성된 CSS 규칙은 자동적으로 벤더 프리픽스가 붙어있으므로 생각할 필요가 없습니다.

- 코드 공유—JavaScript와 CSS사이에 상수와 함수를 쉽게 공유할 수 있습니다.

- 현재 화면에 사용중인 스타일만 DOM에 있습니다(react-jss).

- 죽은 코드 제거

- CSS 유닛 테스트

---

내용이 생각보다 방대한데..

차근차근 살펴보자