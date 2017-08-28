# HTTP 구조

## 1. 특징
* 인터넷상에서 데이터를 주고 받기 위한 서버/클라이언트 모델을 따르는 전송 프로토콜
* 애플리케이션 레벨의 프로토콜로 TCP/IP위에서 작동
* 클라이언트에서 요청(request)를 보내면 서버는 요청을 처리해서 응답(response)
* 80번 port 이용

### 비상태연결(Stateless, Connectless)
서버에 연결하고 요청해서 응답을 받으면 연결을 끊어버린다.

* 장점: 접속유지 최소화, 불특정 다수를 대상으로 하는 서비스에 유리
* 단점: 연결을 끊어버리기 때문에, 클라이언트의 이전 상태를 알 수 없음, 따라서 로그인을 해도 정보유지 불가, 이를 해결하기 위해 쿠키 등등을 이용

### Keep Alive
HTTP 1.1 부터는 keep-alive 기능을 지원

HTTP는 하나의 연결에 하나의 요청을 하는 것을 기준으로 설계가 되어, 문서에 20여개의 파일이 있다면 계속 연결하고 다운하고 연결끊어야한다. TCP통신 과정에서 비용이 많이 소모함. 따라서 Keep Alive 기능 등장.

지정된 시간동안 연결을 끊지 않고 요청을 계속해서 보낼 수 있다.

### HTTPS
SSL은 전자상거래에서의 데이터 보안을 위해서 개발한 통신 레이어다. SSL은 표현계층의 프로토콜로 응용 계층 아래에 있기 때문에, 어떤 응용 계층의 데이터라도 암호화해서 보낼 수 있다

![https구조](http://img1.daumcdn.net/thumb/R1920x0/?fname=http%3A%2F%2Fcfile23.uf.tistory.com%2Fimage%2F216CF8395963739014DFE0)

* HTTPS의 기본 포트번호는 443
* HTTP에 비해 많이 느림

## 2. Request
### 메세지 구조
* 요청 라인 : GET / HTTP/1.1
	* 요청 메소드 : GET, POST, PUT, DELETE
	* 요청 URL
	* HTTP 버전
* 요청 헤더 : 키-값 방식으로 들어감
	* Accept : 클라이언트가 받을 수 있는 컨텐츠
	* Cookie : 쿠키
	* Content-Type : 메세지 바디 종류
	* Content-Length : 메세지 바디 길이
	* If-Modified-Since : 특정 날짜 이후에 변경됐을 때만
* 요청 바디(엔티티)

### Content-Type과 Body
참조문서 : https://www.w3.org/Protocols/rfc1341/4_Content-Type.html

1. form 형태 : URLEncoded 방식
	* application/x-www-form-urlencoded
	* 메세지 바디 : 쿼리 문자열
2. json 형태
	* application/json
3. 멀티파트 형태 : 이진파일 넘길 때 사용, 하나의 메세지 바디에 파트를 나눠서 작성
	* boundary는 파트 구분자
	* multipart/form-data: boundary=frontier

## 3. Response
### 메세지 구조
* 응답 라인
	* 버전
	* 상태 코드
	* 상태 메세지 : HTTP/1.1 200 OK
* 응답 헤더
	* Content-Type : 바디 데이터의 타입
	* Content-Length : 바디 데이터 크기
	* Set-Cookie : 쿠키 설정
	* ETag : 엔티티 태그
* 응답 바디 : HTML, JSON, Octet Stream 등이 있다.

### 상태 코드
* 1xx : 정보
* 2xx : 성공
	* 200 : OK. 요청 성공
	* 201 : Created. 생성 요청 성공
	* 202 : Accepted. 요청 수락(처리 보장X)
	* 204 : 성공했으나 돌려줄게 없음
* 3xx : 리다이렉션
	* 300 : Multiple choices. 여러 리소스에 대한 요청 결과 목록
	* 301,302,303 : Redirect. 리소스 위치가 변경된 상태
	* 304 : Not Modified. 리소스가 수정되지 않았음
* 4xx : 클라이언트 오류
	* 400 : Bad Request. 요청 오류
	* 401 : Unauthorized. 권한없음
	* 403 : Forbidden. 요청 거부
	* 404 : Not Found. 리소스가 없는 상태
* 5xx : 서버 오류
	* 500 : Internal Server Error. 서버가 요청을 처리 못함
	* 501 : Not Implemented. 서버가 지원하지 않는 요청
	* 503 : Service Unavailable. 과부하 등으로 당장 서비스가 불가능한 상태

### Content-Type과 Body
주요 컨텐츠 타입은 아래와 같다.

* text/plain, text/html
* application/xml, application/json
* image/png, image/jpg
* audio/mp3, video/mp4

