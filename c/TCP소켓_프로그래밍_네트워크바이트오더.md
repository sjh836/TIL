# TCP소켓 프로그래밍의 기초

# 0. 다루는 소켓에 대하여
일반적으로 사용되는 INET(TCP/IP 를 이용한 인터넷 주소 패밀리)와 TCP(데이터 연결지향형, 신뢰성이 높은 Stream) 에 대해서 다룬다. 리눅스(centos7) 환경을 기준으로 다룬다.

# 1. 소켓이란?
직관적인 예로 전구는 소켓을 이용하여 전기를 공급받는다. 이처럼 컴퓨터도 네트워크에서 데이터를 얻기위해 소켓을 사용할 수 있다. 소켓 프로그램은 서버-클라이언트의 2개의 프로그램으로 쌍을 이룬다. 서버는 서비스를 제공하는 프로그램이고, 클라이언트는 서버에 서비스를 요청하는 프로그램이다.

## TCP 소켓이란?
TCP소켓은 연결지향형이다. 중간에 데이터가 소멸되지 않고 목적지까지 신뢰성있게 전송된다. 전송 순서대로 데이터가 수신된다. 통신하는 node간에 1대1의 파이프가 생성되었다고 생각하면 쉬울 것같다. 따라서 라인상의 문제가 없다면 데이터는 소멸되지 않는다. 또한 데이터의 경계가 존재하지 않는다. 경계가 존재하지 않는다는 것은 read()와 write()의 호출 수가 일치하는 것이 큰 의미를 갖지 못한다는 걸 의미한다. 100번 write()를 했어도 1번에 read()할 수 있고, 1번 write()했어도 100번에 나눠서 read()할수 있다. 결과는 같다!

# 2. TCP 서버-클라이언트의 흐름
서버프로그램은 포트(port)에 대기하며 클라이언트의 연결을 기다리고 있다가(listen), 클라이언트가 접근을 요청하면 이를 받아들여서(Accept) 연결을 설정하고, 클라이언트에게 서비스를 제공해준다.

![흐름도](http://img1.daumcdn.net/thumb/R1920x0/?fname=http%3A%2F%2Fcfile3.uf.tistory.com%2Fimage%2F2543654E58C7B7572403D9)

# 3. socket.h 의 API
`#include <sys/socket.h>`

참조: [API문서](http://www.di.uniba.it/~reti/LabProRete/socketAPIreference.pdf)

## 3-1. 소켓의 생성
`int socket(int domain, int type, int protocol);`

### 3-1-1. 반환값
성공 = 파일 디스크립터, 실패 = -1

### 3-1-2. 인자
**domain :** 소켓이 사용할 프로토콜 체계 정보 전달

- PE_INET : IPv4 인터넷 프로토콜 체계
- PE_INET6 : IPv6 인터넷 프로토콜 체계

**type :** 소켓이 데이터 전송방식에 대한 정보 전달

- SOCK_STREAM : TCP
- SOCK_DGRAM : UDP

**protocol :** 두 컴퓨터간 통신에 사용되는 프로토콜 최종적 정보 전달(동일한 프로토콜이 둘 이상 존재할 때 의미있다)

- IPPROTO_TCP : TCP
- IPPROTO_UDP : UDP

## 3-2. 소켓 주소의 할당
`int bind(int sockfd, struct sockaddr *myaddr, socklent_t addrlen);`

### 3-2-1. 반환값:
성공 = 0, 실패 = -1
### 3-2-2. 인자
**sockfd :** 주소정보를 할당할 소켓의 파일 디스크립터

**myaddr :** 주소정보를 가진 구조체 변수의 주소 값

**addrlen :** myaddr의 길이정보

### 3-2-3. struct sockaddr 구조체: IPv4만을 위한 구조체가 아니라 불편해서 보통 형변환을 통해 쓴다.
	struct sockaddr
	{
	  sa_family_t sin_family; //주소체계
	  char sa_data[14]; //주소정보
	}    
### 3-2-4. struct sockaddr_in 구조체: IPv4만의 주소정보를 담기 위해 정의
	struct sockaddr_in
	{
	  sa_family_t sin_family; //주소체계
	  uint16_t sin_port; //포트
	  struct in_addr sin_addr; //IP주소
	  char sin_zero[8]; //사용X, 0으로 패딩값을 채운다
	}
#### 3-2-4-1. 주소체계
**AF_INET :** IPv4 인터넷 프로토콜 체계

**AF_INET6 :** IPv6 인터넷 프로토콜 체계

#### 3-2-4-2. 포트:
네트워크 바이트 순서로 16비트 TCP/UDP 포트를 저장한다
#### 3-2-4-3. IP주소:
네트워크 바이트 순서로 32비트 IP주소를 저장한다.

### 3-2-5. struct in_addr 구조체
	struct in_addr
	{
	  in_addr_t s_addr; // IPv4 주소
	}

## 3-3. 소켓에 연결요청 대기(연결요청이 가능한 상태)
`int listen(int sockfd, int backlog);`

### 3-3-1. 반환값:
성공 = 0, 실패 = -1
### 3-3-2. 인자
**sockfd :** 서버 소켓의 파일 디스크립터

**backlog :** 연결요청 대기 큐의 크기정보 전달

## 3-4. 소켓에 접속 허용(연결요청을 승인함, 데이터 입출력용 소켓을 생성)
`int accept(int sockfd, struct sockaddr *addr, socklent_t addrlen);`

### 3-4-1. 반환값:
성공 = 파일 디스크립터, 실패 = -1
### 3-4-2. 인자
**sockfd :** 서버 소켓의 파일 디스크립터

**addr :** 연결 요청한 클라이언트의 주소정보를 담을 변수의 주소 값을 전달

**addrlen :** addr의 바이트 크기를 담은 변수의 주소 값을 전달

## 3-5. 소켓에 연결 요청
`int connect(int sockfd, struct sockaddr *serv_addr, socklen_t addrlen);`

### 3-5-1. 반환값:
성공 = 0, 실패 = -1
### 3-5-2. 인자
**sockfd :** 클라이언트 소켓의 파일 디스크립터

**serv_addr :** 연결요청할 서버의 주소정보를 담은 변수의 주소 값을 전달

**addlen :** serv_addr의 크기를 바이트 단위로 전달

# 4. 소켓과 파일
## 4-1. 파일 디스크립터:
시스템으로부터 할당받은 파일 또는 소켓에 부여된 정수를 의미한다.
## 4-2. file 관련 함수
![입출력버퍼](http://img1.daumcdn.net/thumb/R1920x0/?fname=http%3A%2F%2Fcfile26.uf.tistory.com%2Fimage%2F262E633A58D9EE1C1BE33B)

엄밀히 말하면 read()는 입력버퍼에, write()는 출력버퍼에 쓰는 것이다. TCP가 통신하는 상대 호스트로 알아서 흘려보내준다.

### 4-2-1. 파일 열기
	#include <sys/types.h>
	#include <sys/stat.h>
	#include <fcntl.h>
	int open(const char *path, int flag);

#### 4-2-1-1. 반환값:
성공 = 파일 디스크립터, 실패 = -1
#### 4-2-1-2. 인자
**path :** 파일이름을 나타내는 문자열의 주소값

**flag :** 오픈 모드

- O_CREAT : 파일 생성
- O_TRUNC : 기존 데이터 삭제
- O_APPEND : 뒤이어 저장
- O_RDONLY : 읽기전용
- O_WRONLY : 쓰기전용
- O_RDWR : 읽기쓰기 겸용

### 4-2-2. 파일 닫기
	#include <unistd.h>
	int close(int fd);

#### 4-2-2-1. 반환값:
성공 = 0, 실패 = -1
#### 4-2-2-2. 인자
**fd :** 파일 디스크립터

### 4-2-3. 파일에 쓰기
	#include <unistd.h>
	ssize_t write(int fd, const void * buf, size_t nbytes);

#### 4-2-3-1. 반환값:
성공 = 전달한 바이트 수, 실패 = -1
#### 4-2-3-2. 인자
**fd :** 파일 디스크립터

**buf :** 버퍼 주소

**nbytes :** 전송할 데이터의 바이트 수

### 4-2-4. 파일 읽기
	#include <unistd.h>
	ssize_t read(int fd, void *buf, size_t nbytes);

#### 4-2-4-1. 반환값:
성공 = 수신한 바이트 수(끝나면 EOF), 실패 = -1
#### 4-2-4-2. 인자
**fd :** 파일 디스크립터

**buf :** 버퍼 주소

**nbytes :** 수신할 최대 바이트 수

# 5. 네트워크 바이트 순서와 호스트 바이트 순서
CPU에 따라서 4바이트 정수 1을 메모리 공간에 저장하는 방식이 달라질 수 있다. 빅엔디안의 경우 00000000 00000000 00000000 00000001 로 저장하지만, 리틀엔디안은 00000001 00000000 00000000 00000000으로 저장한다.

네트워크 바이트 순서에서 표준은 빅엔디안이다. 호스트에서는 CPU마다 다르지만 인텔의 경우 리틀엔디안을 채택하고 있다. (따라서 인텔인사이드 스티커가 붙어있다면 리틀엔디안이라고 생각..) 그러므로 네트워크 상에서 데이터를 전송할 때에는 데이터의 배열을 빅엔디안 기준으로 변경할 필요가 있다. 관련 API를 소개한다

	unsigned short htons(unsigned short);
	unsigned short ntohs(unsigned short);
	unsigned long htonl(unsigned long);
	unsigned long ntohl(unsigned long);

위 함수에서 h는 호스트 바이트 순서를 의미하고, n은 네트워크 바이트 순서를 의미한다. 맨 마지막 s는 short(2바이트, 주로 포트)를, l은 long(4바이트, 주로 IP주소)를 의미한다.

그러나 소켓프로그래밍할때 주소할당을 제외한 나머지 데이터전송은 전부 소켓에서 알아서 처리해준다. 따라서 주소할당을 할때만 바이트 순서에 대한 고려가 있으면 되겠다.

## s_addr에 주소 할당하는 3가지 방법
1. **= htonl(INADDR_ANY) :** INADDR_ANY는 서버의 IP주소를 자동으로 찾아서 대입해주는 함수이다(복잡한 #define으로 정의됨)
2. **in_addr_t inet_addr(const char * string); :** ip주소 문자열을 32비트로 바꾸고 네트워크 바이트순서로 바꿔준다
3. **int inet_aton(const char * string, struct in_addr * addr); :** 2번 기능에 추가로 s_addr에 할당까지 처리해준다.