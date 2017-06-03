# git object

git은 4개의 object로 관리한다. 객체명은 SHA1로 40자리로 해쉬된다. 따라서 내용이 같으면 객체명이 같다. 객체들은 `.git/objects`에 위치한다. 내용을 까볼려면 `git cat-file –p 객체명` 으로 보면된다.

1. blob
2. tree
3. commit
4. tag

## 구조도
![구조도](http://img1.daumcdn.net/thumb/R1920x0/?fname=http%3A%2F%2Fcfile22.uf.tistory.com%2Fimage%2F2229165059327C261AD19C)

## 시나리오
test 디렉터리에 git init를 한 뒤에, hello.txt를 만들고 "안녕 나는 공부중이야" 를 저장한다. 그 뒤 git add하고 git commit한다. 마지막으로 git tag를 한다. 이 과정중에서 생성되는 object들을 보자.

![디렉터리 상황](http://img1.daumcdn.net/thumb/R1920x0/?fname=http%3A%2F%2Fcfile25.uf.tistory.com%2Fimage%2F21489A5059327C250550E7)

### 1. blob
git add할 때 생성된다. 파일 내용이 들어 있다

![blob](http://img1.daumcdn.net/thumb/R1920x0/?fname=http%3A%2F%2Fcfile27.uf.tistory.com%2Fimage%2F271C2C5059327C2409C5FD)

### 2. tree
git commit할 때 생성된다. 타입과 객체명, 파일명이 기록된다.

![tree](http://img1.daumcdn.net/thumb/R1920x0/?fname=http%3A%2F%2Fcfile8.uf.tistory.com%2Fimage%2F254E5A5059327C27051C5A)

### 3. commit
git commit할 때 생성된다. tree객체명, 부모commit객체명, author, committer, message를 기록한다. 이는 commit할때 git config에 있는 name과 email이 찍히는 거다.

![commit](http://img1.daumcdn.net/thumb/R1920x0/?fname=http%3A%2F%2Fcfile21.uf.tistory.com%2Fimage%2F2509A85059327C252BE683)

### 4. tag
git tag할 때 생성된다. commit 객체명, tag이름, tagger, message가 기록된다.

![tag](http://img1.daumcdn.net/thumb/R1920x0/?fname=http%3A%2F%2Fcfile3.uf.tistory.com%2Fimage%2F2617935059327C271B5227)

더 자세한 설명은 http://sjh836.tistory.com/37 에 해놨다.