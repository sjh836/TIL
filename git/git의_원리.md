# git의 원리

## 흐름도
git의 object들이 working디렉터리에서 index를 거쳐 repo까지 어떤 명령어로 어떻게 움직이는지 원리를 정리해봤다. 이제 아래그림을 완벽히 이해할 수 있을 거다!!

![git 흐름](http://img1.daumcdn.net/thumb/R1920x0/?fname=http%3A%2F%2Fcfile6.uf.tistory.com%2Fimage%2F237B984B58CE95E90BD98B)

## 1. git init
### .git의 초기구성
	HEAD
	config
	description
	/branches
	/hooks
	/objects
	/refs

## 2. git add
* git add를 했을때 index(=stage area,tree구조)에 object이름과 실제파일이름이  추가되고(추적등록)
objects에 blob타입으로 파일내용이 추가된다.
* 같은 파일이라도 파일내용이 달라지면 새로운 object가 생긴다.
* object의 이름은 SHA1로 HASH된다. 즉 파일내용이 같으면 object명이 같다.

## 3. git commit
* git commit을 하면 objects에 commit객체와 tree객체가 추가된다.
* tree객체는 commit할때의 index를 스냅샷찍어서 저장한다.
* commit객체는 tree객체명과 Author, Date, Msg를 저장한다.
* 2번째 commit객체부터는 parent라고 이전 commit객체명을 저장한다.

### ☆근본 of 근본☆ : git의 객체(object)
* object에는 blob(파일내용)과 tree(디렉터리와 blob의 정보(파일명)들을 담음)와 commit이 있다.
* 예를들어 1개파일을 생성하고 add하고 commit했을 때, git 내부에서는 blob객체와 tree객체, commit객체 3개가 만들어 지는 것이다.
* 이것은 git push를 했을때 Counting objects: 3, done 의 문구에서도 알 수 있다.
* 추가로 뒤에서 설명할 tag도 object이다. 이 tag객체는 커밋객체를 가리키며 태그명과 태그를 만든사람, 주석이 들어가 있다. 

**즉 git은 4가지 객체들로 이루어져 있다. blob , tree , commit , tag**

## 4. git status
* git status는 (파일을 수정했을때) index에서 blob객체가 가리키는 내용과 실제 파일내용이 다를 경우, changes not staged for commit를 출력한다.
* add를 했을경우, index에서 blob객체가 가리키는 내용과 실제 파일내용이 같아지므로, changes to be commited를 출력한다.
* index와 최신 commit의 tree객체를 비교했을 때 다른게 있으면 커밋대기상태를 인지한다. 다른게 없으면 커밋할게 없다.

## 5. git branch
* HEAD는 현재 브랜치를 가리킨다. checkout을 하면 HEAD파일이 바뀐다.
* 브랜치는 .git/refs/heads/브랜치명 의 경로에 생성된다. 이 파일(텍스트파일)에는 커밋객체를 가리킨다. 

## 6. git merge
* git merge는 현재 브랜치가 가리키는 커밋객체(A)와 병합할 브랜치가 가리키는 커밋객체(B), 이 두 객체가 공통으로 물려받은 조상 커밋객체(C)를 3 way-merge방식으로 병합한다.
* 만약 같은 코드 부분을 수정했으면 충돌이 났다고 사용자에게 알려주고 유저가 병합하라고 둔다. (7번 충돌해결 참조)

## 7. git reset
* reset --hard로 이전 커밋객체로 돌아가더라도, 돌아가기 전에 커밋객체는 삭제되지 않는다. 다만 연결리스트에서 링크연결이 끊겼을 뿐이다.
* 이 경우 ORIG\_HEAD는 reset을 실행하기전(HEAD가 원래 가리킨) 커밋객체를 가리킨다. 따라서 reset ORIG_HEAD로 다시 돌아갈 수 있다.
* .git/logs/refs/heads/브랜치명 에는 브랜치가 실행한 커밋과 reset명령 등이 로그로 모두 남아있다. 여기에 커밋객체 링크가 모두 남는다.

### ※ reset의 옵션
![reset 옵션](http://img1.daumcdn.net/thumb/R1920x0/?fname=http%3A%2F%2Fcfile2.uf.tistory.com%2Fimage%2F212CDB4A58CE94C9308D91)

* --hard의 경우 작업디렉터리의 파일까지 전부 reset한다.
* 옵션을 주지않는다면 디폴트값인 --mixed인데, index까지만 reset하므로 작업디렉터리에 있는 파일(add 전)은 무사하다.
* --soft는 커밋객체가 가리키는 트리만 바꾼다.

## 8. git remote & push (설명편의상 원격저장소명은 origin으로 한다)
git remote add origin URL 를 하게되면 .git/config가 수정된다. remote "origin" 아래에 입력된 URL이 기록된다.

	[remote "origin"]
		url = https://github.com/아이디/원격저장소.git
		fetch = +refs/heads/*:refs/remotes/origin/*

git push -u origin master 를 하게되면, 로컬저장소의 master브랜치와 원격저장소의 master브랜치를 연결시키고 업로드한다. 

연결은 한번만 하면되므로 이후에는 git push만 써도 된다. .git/config 에 생성되어 들어있다.

	[branch "master"]
		remote = origin
		merge = refs/heads/master

push를 할때마다 refs/remotes/origin/master가 갱신되며 push를 완료한 최신 커밋객체(push후 커밋을 안했다면 refs/heads/master와 같음)를 가리킨다. 이렇게 별도로 포인터를 유지하기 때문에 push한 커밋객체와 push하지 않은 커밋객체를 알 수 있다.

## 9. git pull & git fetch
* git pull을 하게되면 .git/refs/heads/master 와 .git/refs/heads/origin/master 는 다운로드받은 커밋객체를 가리킨다. 자동 병합된다.
* git fetch를 하게되면 .git/refs/heads/origin/master 은 다운로드받은 최신 커밋객체를 가리키지만, .git/refs/heads/master는 원래 가리키던 커밋객체를 가리킨다.
* 즉, fetch는 가져오기만 할 뿐, 자동으로 병합하지 않는다. 이것이 pull과의 차이점이다. 만약 git fetch 후에 (master브랜치에서) git merge origin/master 로 병합한다면 git pull과 같아진다.

**정밀하게 제어할때 fetch를 쓸만하다. git diff HEAD origin/master 로 코드비교 등을 할 수 있기 때문이다.**

## 10. git tag
* light weight tag(git tag 태그명)을 만들면 .git/refs/tags/태그명 이 생성된다. 이 파일은 커밋객체를 가리키고 있다.
* annotated tag(git tag -a 태그명)을 만들면 .git/refs/tags/태그명 는 tag객체를 가리킨다. 즉, tag객체가 생성되는데, 이 tag객체는 커밋객체를 가리키며 태그명과 태그를 만든사람, 주석이 들어가 있다.

## 11. git rebase
* git rebase는 git merge와 결과가 같으나, merge는 브랜치 분기를 그대로 냅두지만, rebase는 분기된 것이 일렬로 합쳐진다는 차이점이 있다. 즉 git log를 해서보면 한줄이다.(두 줄이상으로 분기되지 않음)
* git rebase를 하게되면 조상 커밋객체(base)를 상대 브랜치의 최신 커밋객체(rebase)로 바꾼다.
* .git/rebase-apply/patch라는 임시 저장소가 생기고 이곳에 순차적으로 base객체와 현재 브랜치의 (1,2,3..번째) 파생 커밋객체의 차이점(diff)가 들어간다.
* 이 patch에 들어간거랑 상대 브랜치의 최신 커밋객체와 순차적으로 merge하는 것(Applying)이다. rebase가 끝나면 분기가 사라진다.
* 충돌이 발생했을 경우, 유저가 해결한 후에 add하고 git rebase --continue를 해주면 순차적으로 마저 진행된다.(commit 안해도된다)