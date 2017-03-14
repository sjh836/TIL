# Git

## 1. 설정: ~/.gitconfig 에 저장된다
* git config --global user.name 자신의 닉네임
* git config --global user.email 자신의 이메일

## 2. 기본 명령어
* git init : 저장소 생성
* git add 파일 : git이 파일을 추적하도록 명령, 새로 생성되거나 변경된것 등
* git status : git 저장소의 상태를 확인

* git commit
* git commit -a : 추적중인 변경된 모든 파일을 add한다(한번도 add안된건 X)
* git commit -m "메세지" 

* git log : 커밋된 로그 조회
* git log -p : 로그에서 출력되는 버전 간의 차이점을 출력하고 싶을 때
* git diff 버전1..버전2 : 버전1과 버전2 간의 차이점을 비교할 때
* git diff : git add하기 전과 add한 후의 파일 내용을 비교할 때 

* git reset --hard 버전 : 이전버전으로 돌아가며 그 이후것은 삭제됨
* git revert 버전 : 버전의 커밋을 취소한 내용을 새로운 버전으로 만드는 명령

* git branch : branch 목록보기, *있는게 현재 브랜치
* git branch 이름 : 이름으로 branch 생성
* git checkout 브랜치명 : 현재 브랜치에서 브랜치명으로 이동
* git branch -d 브랜치명 : 브랜치명 삭제
* git branch -b 브랜치명 : 브랜치만들고 바로 checkout

* git log --branches(모든 브랜치를 보여준다) --decorate --* graph(라인으로 표시) --oneline(커밋한줄로 표현)
* git log 브랜치1..브랜치2 : 브랜치1에는 없고 브랜치2에는 있는 것을 비교해서 보여준다
* git diff 브랜치1..브랜치2 : 브랜치간 차이점 비교

* git merge B브랜치 : 현재 브랜치에서 B브랜치를 불러와서 병합한다.

* git stash : 추적등록된(add 한번이상) 작업중인 변경사항들을 다른 곳에 저장해둔다.
* git stash apply : 저장된 stash를 작업디렉터리에 불러온다
* git stash list : 저장된 stash 리스트를 출력한다. {0}이 제일 최신, git reset을해도 stash 리스트는 지워지지 않는다.
* git stash drop : {0} 최신 리스트 1개를 삭제한다.
* git stash pop : apply하고 drop을 한다.

## 3. 원리
### ①. git init

**.git의 구성**

* HEAD
* config
* description
* /branches
* /hooks
* /objects
* /refs

### ②. git add
git add를 했을때 index(=stage area,tree구조)에 object이름과 실제파일이름이  추가되고(추적등록)
objects에 blob타입으로 파일내용이 추가된다.
같은 파일이라도 파일내용이 달라지면 새로운 object가 생긴다.
object의 이름은 SHA1로 HASH된다. 즉 파일내용이 같으면 object명이 같다.

###③. git commit
git commit을 하면 objects에 commit객체와 tree객체가 추가된다. tree객체는 commit할때의 index를 스냅샷찍어서 저장한다. commit객체는 tree객체명과 Author, Date, Msg를 저장한다. 2번째 commit객체부터는 parent라고 이전 commit객체명을 저장한다.

즉 object에는 blob(파일내용)과 tree(디렉터리와 blob의 정보(파일명)들을 담음)와 commit이 있다.

### ④. git status
git status는 (파일을 수정했을때) index에서 blob객체가 가르키는 내용과 실제 파일내용이 다를 경우, changes not staged for commit를 출력한다.
add를 했을경우, index에서 blob객체가 가르키는 내용과 실제 파일내용이 같아지므로, changes to be commited를 출력한다.
index와 최신 commit의 tree객체를 비교했을 때 다른게 있으면 커밋대기상태를 인지한다. 다른게 없으면 커밋할게 없다.

### ⑤. git branch
HEAD는 현재 브랜치를 가르킨다. checkout을 하면 HEAD파일이 바뀐다. 브랜치는 .git/refs/heads/브랜치명 의 경로에 생성된다. 이 파일(텍스트파일)에는 커밋객체를 가르킨다. 

## 4. 브랜치로 일하기
현업에서는 이슈를 해결하기위해 이슈마다 브랜치를 딴다. 이렇게 딴 브랜치로 이슈를 해결한 후 master에서 이슈브랜치를 병합하는 것을 Fast-forward 방식이라고 한다. 실제 master 브랜치는 커밋 object가 생성되지 않지만, 포인터를 앞으로 옮긴다. (fast-forward빨리감기라고 함)

별도의 커밋을 갖고있는 master브랜치와 이미 분기된 다른 브랜치와 merge할 때는 fast-forward 방식이 아니다. 이때 git은 공통 조상을 찾고 3way-merge를 이용하여 갈라진 커밋객체를 합치고 새 커밋객체를 만든다.