## parcel   /   eslint

1. parcel
-> 빌드 도구

package.json 에 source 속성 추가

```json
  "source": "./index.html",
```
이렇게 설정 해줬는데 만약 이걸 아예 작성을 안한다면 npm start 했을때 실행이 안됨.
해당 속성 없이 실행하고 싶다면.
```bash

npx parcel index.html

npx parcel index.html --port 8080

```

둘 중 하나를 선택해서 parcel 로 실행시킬 수 있다. -> 이게 원래 버전임

---

### eslint

- 정적분석
- 코딩 컨밴션 - 일관성있는 코드
- 최신 트렌드 학습에 도움이 됨

vscode eslint 설치하고

`.eslintrc.js`

```js
module.exports = {
	env: {
		browser: true,
		es2021: true,
		jest: true,
	},
	settings: {
		react: {
			version: 'detect',
		},
	},
	extends: ['plugin:react/recommended', 'plugin:react/jsx-runtime', 'xo'],
	overrides: [
		{
			extends: ['xo-typescript'],
			files: ['*.ts', '*.tsx'],
		},
	],
	parserOptions: {
		ecmaVersion: 'latest',
		sourceType: 'module',
	},
	plugins: ['react'],
	rules: {},
};

```

.vscode 폴더 생성 <br/>

`.vscode/settings.json`

```json
{
    "editor.codeActionsOnSave": {
        "source.fixAll.eslint": true
    }
}
```

-> 저장 할 때마다 eslint 적용된다.