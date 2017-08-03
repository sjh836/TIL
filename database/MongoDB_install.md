# N클라우드에 MongoDB 설치

참조문서 : https://docs.mongodb.com/manual/tutorial/manage-mongodb-processes/

현재 날짜로 최신버전은 3.4.6이다. 리눅스는 CentOS 7이다. 몽고디비를 깔아보자

## 1. 설치
홈페이지에서 링크따와서 wget으로 친다. yum으로하면 다 잡아주니 편하겠지만 업데이트하면 버전땜에 골치아플까봐..

	wget https://fastdl.mongodb.org/linux/mongodb-linux-x86_64-rhel70-3.4.6.tgz
	tar zxvf mongodb-linux-x86_64-rhel70-3.4.6.tgz 

## 2. 환경변수 설정
환경변수를 잡아준다. /etc/profile에서 path잡고 source해준다.

## 3. 실행
실행전에 데이터쌓을 디렉터리를 만들어야한다. 그 후에 `mongod --dbpath ./mongo_data/` 로 실행한다. 데몬을 뛰었으니 쉘로 접속해보자. `mongo` 를 실행해서 접속한 다음 

	> show dbs
	admin  0.000GB
	local  0.000GB

잘 설치되었다.

	[sjh836@main ~]$ sudo mongod --fork --logpath /var/log/mongod.log --dbpath ./mongo_data/
	about to fork child process, waiting until server is ready for connections.
	forked process: 21771
	child process started successfully, parent exiting

으로 실행하면 터미널이 꺼져도 계속 몽고데몬이 돌아간다.

## 4. 종료
컨트롤C로 종료하면 불완전 종료라고한다.. 아래처럼 해야한다.

	use admin
	db.shutdownServer()