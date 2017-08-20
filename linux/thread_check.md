## 쓰레드 추적
`man ps` 으로 확인

* -e, -A : Select all processes.
* -L : Show threads, possibly with LWP and NLWP columns.
* -F : Set extra-full format; implies -f.

고로 `ps -Lef | grep node`로 정보를 확인한다.

* LWP(Light Weight Process, is thread)는 thread의 ID
* NLWP(Number Light Weight Process)는 해당 프로세스에서 동작하고 있는 프로세스의 총 갯수

#### 쓰레드가 잘 돌아가는 지는 `strace` 로 확인할 수 있다고 한다.
strace는 애플리케이션들이 사용하는 system call과 signal 등을 추적해서 성능 저하를 일으키는 부분은 없는지, 에러가 나는 부분은 없는지 등등 확인하는데 사용하는 디버깅 툴이다.

아래는 주요 옵션이다.

* -o : 파일로 저장
* -e : 시스템콜 추적
* -e trace=옵션 : 옵션(read, open 등) 시스템 콜을 출력
* -c : 시스템콜 통계 생성
* -p : 프로세스ID 넣어주면 추적