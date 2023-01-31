## fnm 으로 node 버전 관리하기
#
### 1) fnm 설치 <br/>
$ brew install fnm <br/>

~/.bashrc 또는 ~/.zshrc에 다음을 추가한다. <br/>
eval "$(fnm env)"

### 2) node js 설치  <br/>
- 설치 가능한 버전 확인  <br/>
fnm ls-remote <br/>

- LTS(Long Term Support) 버전을 설치하고 기본으로 사용하게 한다. <br/>
fnm install --lts <br/>
fnm use lts-latest <br/>
fnm default $(fnm current) <br/>

- 설치된 상태 확인
fnm list <br/>
fnm current

- node js 버전 확인 <br/>
node -v

#
[TypeScript + React + Jest + ESLint + Parcel 개발 환경 세팅](setting.md)