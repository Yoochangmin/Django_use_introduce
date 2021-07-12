# Gig사용법

----

```
git config --global user.name "Yoochangmin"
git config --global user.email "cdals9141@gmail.com"

echo "# firestproject" >>README.md    //README.md 에 firestprojecet // 설명서추가
git init  //현재 디렉터리를 새로운 깃저장소로 초기화
git status //현재 staging area에 올라가지 않은 파일 확인
git add // staging area에 파일 저장  git add . 모든 파일 올림 
git ignore 파일 만든뒤 //  gitignore.io 에서 쳐서 복사 붙여넣기
git commit -m "first commit"// staging area 올린 파일 저장 
git log //commit 한 내용 확인 q
git branch -M main // master 브랜치 이름을 main으로
git branch "이름" // "이름" 브랜치 생성
git checkout 이름 // "이름" 브랜치로 이동
git push origin --delete 브랜치명 // 브랜치 삭제
```

```
git remote add origin "레포터리지 주소" // 레포터리지 주소를 origin라는 remote이름으로 연결
git push origin main// origin이름에 연결되있는 파일을  main브랜치에 올림 
```

