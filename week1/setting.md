## setting

#

### TypeScript + React + Jest + ESLint + Parcel 개발 환경 세팅

* Node.js 가 최신 버전인지 확인한다. <br/>
[fnm 으로 node 버전 관리](fnm.md)
#

터미널을 켠다.
#
### 1. 작업 폴더 생성
```bash
mkdir my-app

cd my-app
```
#
### 2. VS code 켜기
```bash
code .
```

#
### 3. npm 패키지 준비
```bash
npm init -y
```
-  전부 yes를 하겠다는 의미이다.

#
### 4. .gitignore 생성
```bash
touch .gitignore
```
-> touch : 빈 파일을 생성하는 명령어 <br/>

- [gitignore 예시](https://github.com/github/gitignore/blob/main/Node.gitignore) <br/>
-> 구글에 "github gitignore node" 라고 검색하면 <br/>
github에서 제공하는 node js 용 예시 .gitignore를 확인 할 수 있다. 이걸 사용하자! <br/>
-> Raw 버튼을 눌러서 텍스트 파일을 전체 복사하고 내 폴더안에 .gitigore에 붙여넣는다. <br/>

#

### 5. TypeScript 설정
```bash
npm i -D typescript
OR
npm install --save-dev typescript
```
- devDependencies 에 넣어서 개발환경에서만 사용하겠다는 의미
- 배포하는 크기를 줄여줄 수 있음.

```bash
npx tsc --init
```
- 타입스크립트 컴파일러, 초기화 하는것
- npx : 뭔가 실행한다는 것.
- tsconfig.json 파일이 생성된다.
- TypeScript 프로젝트를 어떻게 빌드할지 설정하는 파일이다.

#### tsconfig.json 파일의 jsx 속성을 변경한다.
- 왜 변경?
- jsx를 쓸거다..
- 16번째 줄에 "jsx": "react-jsx", 로 변경하고 활성화 시킨다.
#

### 6. ESLint 설정
```bash
npm i -D eslint
```
- eslint를 개발환경에 설치한다. <br/>

```bash
npx eslint --init
```
- npx로 실행시킨다. <br/>
-> 어떤거 선택했는지 체크  <br/>

```bash
npx eslint --fix
```
- 고치는거

#### [.eslintrc.js 파일을 수정한다.](eslintrc.md)

.eslintignore 파일을 생성 후 작성한다.
- touch .eslintignore
```
/node_modules/
/dist/
/.parcel-cache/
```
#

### 7. React 설치
```bash
npm i react react-dom
npm i -D @types/react @types/react-dom
```
#

### 8. 테스팅 도구 설치
```bash
npm i -D jest @types/jest @swc/core @swc/jest \
    jest-environment-jsdom \
    @testing-library/react @testing-library/jest-dom
```
- jest 라는 테스팅 도구 설치
- jest랑 swc를 같이 쓰는게 목표
#

### 9. jest.config.js 파일 작성
- 테스트에서 SWC를 사용한다.
- js로 ts 그냥 실행 못함. 변환해줘야함. 그거를 여기에 작성.
#### [jest.config.js 파일을 작성한다.](jestconfig.md)  <br/>

#

### 10. Parcel 설치
```bash
npm i -D parcel
```
- web / dev 서버를 띄우는 도구
- Parcel 은 파일 변화를 자동으로 다시 빌드(rebuild) 하고 빠른 모듈 교체를 지원하는 내장 개발용 서버가 있어 빠른 개발이 가능해 집니다. 그저 진입 파일을 지정하면 됩니다.
- 새로고침 안해도 변경됨
#

### 11. package.json 수정
프로젝트를 실행하는 명령어를 작성해준다.
#### [package.json 파일을 수정한다.](packagejson.md)  <br/>

```js
 "scripts": {
    "start": "parcel --port 8080",
    "build": "parcel build",
    "check": "tsc --noEmit",
    "lint": "eslint --fix --ext .js,.jsx,.ts,.tsx .",
    "test": "jest",
    "coverage": "jest --coverage --coverage-reporters html",
    "watch:test": "jest --watchAll"
  },
```

```bash
npm (run) start
npm run build
npm run check
npm run lint
npm test
```

node 의 경우 처음 실행하는게 main으로 잡아주고 있음
```js
  "main": "index.js",
```
그런데 우리는 웹서버를 띄워줄거라서 source로 바꿔준다.
```js
  "source": "index.html",
```
#

### 12. 기본 코드 작성
- `index.html`
```html
<!DOCTYPE html>
<html lang="ko">
<head>
	<meta charset="UTF-8">
	<meta http-equiv="X-UA-Compatible" content="IE=edge">
	<meta name="viewport" content="width=device-width, initial-scale=1.0">
	<title>React Demo App</title>
</head>
<body>
	<div id="root"></div>
	<p>Hello, world</p>
	<script type="module" src="./src/main.tsx"></script>
</body>
</html>
```

- `src/main.tsx`
```js
import ReactDOM from 'react-dom/client';

const App = () => <div>hi nice</div>;

const element = document.getElementById('root');

if (element) {
	const root = ReactDOM.createRoot(element);
	root.render(<App />);
}
```
