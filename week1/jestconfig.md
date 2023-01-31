```javascript
module.exports = {
	testEnvironment: 'jsdom',
	setupFilesAfterEnv: ['@testing-library/jest-dom/extend-expect'],
	transform: {
		'^.+\\.(t|j)sx?$': [
			'@swc/jest',
			{
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
			},
		],
	},
	testPathIgnorePatterns: ['<rootDir>/node_modules/', '<rootDir>/dist/'],
};

```

[TypeScript + React + Jest + ESLint + Parcel 개발 환경 세팅](setting.md)