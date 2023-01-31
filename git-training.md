## pull request 훈련

### 1. fork 한 레퍼지토리 ssh로 git clone

### 2. git clone
```bash
git clone <복사한 Repository 주소>
```
### 3. PR 보낼 저장소 주소를 upstream이라는 이름으로 추가
```bash
git remote add upstream <PR 보낼 원격 Repository 주소>
```
### 4. 원격 저장소 확인 
```bash
git remote -v
```
### 5. upstream 원격 저장소의 최신 상태를 반영
```bash
git fetch upstream
git rebase upstream/main
```

### 6. 브랜치 생성
```bash
git switch -c <브랜치 이름> upstream/main
```
### 7. 작업 진행

### 8. 작업 완료 후 커밋
```bash
git add .
git commit -m "memo"
```

### 9. origin 원격 저장소에 작업 브랜치 올리기
```bash
git push origin <브랜치 이름>
```

### 10. Compare & pull request
- 이상이 없으면 처리