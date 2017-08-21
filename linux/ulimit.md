# ulimit

ulimit는 프로세스의 자원 한도를 설정하는 명령으로, soft한도와 hard한도 두가지가 있다.

* soft : 새로운 프로그램을 생성하면 기본으로 적용되는 한도
* hard : 소프트한도에서 최대로 늘릴 수 있는 한도

apache와 같이 웹 서비스를 운영 시 동접자가 많은 경우 구동되는 apache 프로세스 수와 해당 프로세스가 처리하게되는 파일 수 또한 증가 하게 된다.

`ulimit [옵션] 값`
 
* -a : 모든 제한 사항을 보여줌.
* -c : 최대 코어 파일 사이즈
* -d : 프로세스 데이터 세그먼트의 최대 크기
* -f : shell에 의해 만들어질 수 있는 파일의 최대 크기
* -s : 최대 스택 크기
* -p : 파이프 크기
* -n : 오픈 파일의 최대수
* -u : 오픈파일의 최대수
* -v : 최대 가상메모리의 양
* -S : soft 한도
* -H : hard 한도

각 항목은 아래와 같다.

	[sjh836@main ~]$ ulimit -a
	core file size          (blocks, -c) 0			// 코어파일의 최대크기
	data seg size           (kbytes, -d) unlimited	// 프로세스의 데이터 세그먼트 최대크기
	scheduling priority             (-e) 0			// 쉘에서 생성되는 파일의 최대크기
	file size               (blocks, -f) unlimited
	pending signals                 (-i) 7246
	max locked memory       (kbytes, -l) 64
	max memory size         (kbytes, -m) unlimited	// 메모리 최대크기
	open files                      (-n) 81920		// 한 프로세스에서 열 수있는 open FD최대숫자
	pipe size            (512 bytes, -p) 8			// 512바이트 block의 파이프 크기
	POSIX message queues     (bytes, -q) 819200
	real-time priority              (-r) 0
	stack size              (kbytes, -s) 8192
	cpu time               (seconds, -t) unlimited	// 총 누적된 cpu시간
	max user processes              (-u) 81920		// 단일 유저가 사용가능한 프로세스의 최대개수
	virtual memory          (kbytes, -v) unlimited	// 쉘에서 사용가능한 가상 메모리의 최대용량
	file locks                      (-x) unlimited

수정은 다양한 방법이 있지만, 제일 쉬운건 아래와 같다. (부팅해도 바뀌는거)

/etc/profile 수정
 
	#ulimit setting
	ulimit -u 8192  # max number of process 수정
	ulimit -n 2048  # open files 값 수정
 
적용 : source /etc/profile