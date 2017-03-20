# 브랜치로 일하기
현업에서는 이슈를 해결하기위해 이슈마다 브랜치를 딴다. 이렇게 딴 브랜치로 이슈를 해결한 후 master에서 이슈브랜치를 병합하는 것을 Fast-forward 방식이라고 한다. 실제 master 브랜치는 커밋 object가 생성되지 않지만, 포인터를 앞으로 옮긴다. (fast-forward빨리감기라고 함)

별도의 커밋을 갖고있는 master브랜치와 이미 분기된 다른 브랜치와 merge할 때는 fast-forward 방식이 아니다. 이때 git은 공통 조상을 찾고 3way-merge를 이용하여 갈라진 커밋객체를 합치고 새 커밋객체를 만든다.

# 충돌해결
같은 파일(=A)의 같은 코드를 각자 브랜치(로컬과 원격저장소가 될수도 있다)로 작업(commit)을 하고 merge를 할 때 충돌이 발생할 수 있다. 이 경우 기본적으로 작업 디렉터리에 있는 A에 2개의 브랜치 작업내용이 ===를 기준으로 삽입된다. 병합하려는 사람이 이를 수동으로 해결해야 한다.

## kdiff3
* 설치법: https://github.com/honux77/practice/wiki/kdiff3

명령어

* `git config --global merge.tool kdiff3` : 연동
* `git mergetool` : 충돌일어난 경우에 이 커맨드 입력

병합이 완료되면 A파일.orig가 발생하는데 백업용이므로 지워도 무관하다.

# git ignore

https://www.gitignore.io/ 를 가면 미리 만들어진 파일을 받을 수 있다.

### 작성법
* \# : 주석
* ! : 예외
* / : 디렉터리를 의미
* *.exe : 확장자 exe파일을 모두 무시
* A/*.exe : A 디렉터리 안에 있는 확장자 exe파일을 모두 무시

.gitignore 은 앞으로 Git 버전 관리에서 제외할 파일 목록을 지정하는 파일이다. 따라서 이미 버전 관리에 포함되어 있는 파일들을 .gitigore 파일에 기록한다고 해서 Git이 알아서 버전 관리에서 제외 하지는 않는다.

이 경우에는 수동으로 해당 파일들을 버전 관리에서 제외시켜줘야 하는데 git bash에서 git rm --cached 를 입력하면 적용된다

# github pull request 흐름

계정A : 원격저장소 소유자

계정B : A의 저장소에 Pull Request를 보내고 싶은 자

1. 계정B : github사이트에서 계정A의 원격저장소를 Fork한다.
2. 계정B : 자신의 계정에 있는 Fork한 원격저장소를 로컬저장소로 clone한다.
3. 계정B : 브랜치를 생성한다.
4. 계정B : 파일을 수정하고 커밋한다.
5. 계정B : push한다.
6. 계정B : github사이트에서 원격저장소의 커밋한 브랜치로 이동하여 확인한다
7. 계정B : github사이트에서 자신의 원격저장소에서 New pull request 버튼을 누른다. base fork와 head fork를 설정한후 create pull request를 한다
8. 계정A : github사이트에서 pull request 요청을 확인하고 수락하거나 기각한다.