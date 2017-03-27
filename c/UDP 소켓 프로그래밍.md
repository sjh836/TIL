# UDP 소켓 프로그래밍
TCP소켓에 이어서 쓴다

# 1. UDP소켓이란?
UDP소켓은 비연결지향형 소켓이다. **TCP에 있는 흐름제어가 없다.** 특징으로는 전송순서에 상관없이 가장 빠른 전송,경로를 지향한다. 데이터 손실의 우려가 있고 한번에 전송할 수 있는 데이터의 크기가 제한된다. 데이터의 경계가 존재한다. TCP소켓과 반대라고 생각하면 쉽다. 데이터의 경계가 존재한다는 건, 3번 write()했으면 3번 read()를 해서 수신해야한다는 것이다. 이러한 특징때문에 UDP는 주로 고속의 데이터 전송이나 데이터가 조금 손실되도 상관없는 곳에 쓰인다. 대표적인 예가 동영상 스트리밍을 들 수 있겠다.

중학교 때 스타좀 (많이)해봤는데.. 스타크1을 한사람이면 특히 UDP란 글자가 익숙할거다. 게임플레이어간에 고속통신을 위하여 UDP로 주고받는다는거다. 하지만 미네랄수치나 건물체력수치 이런건 에러의 가능성(데이터손실)이 있기때문에 큐에 쌓아놓고 주기적으로 꺼내서 비교한다던지, 아니면 이부분만 TCP를 사용했지않을까??

# 2. UDP 서버-클라이언트의 흐름
TCP서버는 accept()할때마다 소켓이 생성되지만, UDP는 서버건 클라이언트건 하나의 소켓만 있으면 된다. listen(), accept()이런게 없다.

![udp흐름도](http://img1.daumcdn.net/thumb/R1920x0/?fname=http%3A%2F%2Fcfile21.uf.tistory.com%2Fimage%2F2564D74C58D8F398281AE3)

# 3. UDP 소켓 API

`#include <sys/socket.h>`

참고로 UDP프로그램에서는 sendto() 호출 시 IP와 PORT가 자동으로 할당되므로 별도의 주소정보 할당과정이 불필요하다.

## 3-1. 송신
`ssize_t sendto(int sock, void *buff, size_t nbytes, int flags, struct sockaddr *to, socklen_t addrlen);`

### 3-1-1. 반환값
성공 = 전송된 바이트 수, 실패 = -1

### 3-2-2. 인자
**sock :** 데이터전송에 쓸 UDP소켓 파일디스크립터

**buff :** 버퍼의 주소

**nbytes :** 전송할 데이터의 크기 바이트

**flags :** 옵션지정에 사용되는 매개변수, 없으면 0

**to :** 목적지 주소정보를 담고 있는 sockaddr 구조체 변수의 주소

**addrlen :** 매개변수 to의 크기

## 3-2. 수신
`ssize_t recvfrom(int sock, void *buff, size_t nbytes, int flags, struct sockaddr *from, socklen_t  *addrlen);`

### 3-2-1. 반환값
성공 = 수신한 바이트 수, 실패 = -1

### 3-2-2. 인자
**sock :** 데이터수신에 쓸 UDP소켓 파일디스크립터

**buff :** 버퍼의 주소

**nbytes :** 수신할 최대 바이트 수(buff의 크기를 넘을 수 없다)

**flags :** 옵션지정에 사용되는 매개변수, 없으면 0

**from :** 발신지 주소정보를 담고 있는 sockaddr 구조체 변수의 주소

**addrlen :** 매개변수 from의 크기를 담고 있는 변수의 주소

# 4. connected UDP 소켓
UDP는 기본적으로 unconnected 소켓이다. 그러나 하나의 호스트와 오랜 시간동안 데이터를 송수신해야 한다면, UDP소켓을 connected 소켓으로 만드는 것이 효율적이다. 만드는 방법은 UDP소켓을 선언하고, tcp에서 설명한 connect()함수를 호출해주면 된다.

	sock=socket(PF_INET, SOCK_DGRAM, IPPROTO_UDP);
	adr 주소 구조체 초기화
	connect(sock, (struct sockaddr *)&adr, sizeof(adr));

송수신의 대상이 정해졌기 때문에 sendto(), recvfrom()이 아닌 write(), read() 함수로도 송수신이 가능하다.