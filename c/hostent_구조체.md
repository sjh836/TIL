## 1. hostent 구조체
	struct hostent
	{
		char *h_name; //공식 도메인 이름
		char **h_aliases; //공식 이외 도메인 이름들
		int h_addrtype; //주소정보 체계(IPv4: AF_INET, IPv6: AF_INET6)
		int h_length; //IP주소의 크기를 담는다. (IPv4는 4)
		char **h_addr_list; //도메인 이름에 대한 IP주소가 정수 형태로 반환될 때 이 멤버 변수를 이용
	}
### * 예제

![요약](http://img1.daumcdn.net/thumb/R1920x0/?fname=http%3A%2F%2Fcfile4.uf.tistory.com%2Fimage%2F25207D4B58E34CD41A50D7)

## 2. 도메인 관련 API
`#include <netdb.h>`

아래 함수들을 호출하면 소켓 라이브러리가 DNS서버에 조회하여  IP주소나 도메인명을 찾는 기능을 제공한다. 이와같은 정보를 hostent 구조체에 담아서 반환해주는 것이다. 개발자가 IP주소를 알아내기위해 별도로 구현할 것이없다..

## 2-1. 도메인 네임 → IP주소로 변환
`struct hostent *gethostbyname(const char *hostname);`

### 2-1-1. 반환값
성공 = hostent 구조체 변수의 주소 값, 실패 = null 포인터 반환

### 2-1-2. 인자
* **hostname:** 도메인명을 담은 문자배열 전달

## 2-2. IP주소 → 도메인 네임으로 변환
`struct hostent *gethostbyaddr(const char *addr, socket_t len, int family);`

### 2-2-1. 반환값
성공 = hostent 구조체 변수의 주소 값, 실패 = null 포인터 반환

### 2-2-2. 인자
* **addr:** IP주소를 갖는 in_addr 구조체 변수의 포인터 전달(char *인 이유는 IPv4이외에도 다양한 주소를 받는 상황을 위하여)
* **len:** 첫번째 인자의 길이 전달(IPv4: 4, IPv6: 16)
* **family:** 주소정보 체계 전달(IPv4: AF_INET, IPv6: AF_INET6)