# RESTful
Representational State Transfer의 약자로, 로이 필딩(HTTP 프로토콜 주요 저자)이 제창했다.

## 1. REST 아키텍처의 조건
* 클라이언트/서버 : 클라이언트의 요청과 서버의 응답 기반
* 상태 없음 : 클라이언트의 상태(다른 URL에 있다던지)와 관계없이 요청으로만 응답
* 캐시 : 클라이언트는 서버의 응답을 캐시한다. 같은 요청마다 계속 다운로드받을 수는 없으므로.. 네트워크 비용 절감
* 계층화 시스템 : 서버는 다양한 형태의 중간 계층을 이용해서 확장할 수 있다. 클라이언트는 서버의 계층에 무관하게 통신할 수 있다.
* Code on Demand : 리소스를 다룰 수 있는 코드 전송(JS 등)
* 인터페이스 일관성 : 시스템 구조를 단순화시키고 작은 단위로 분리해서 독립적으로 개선하고 확장할 수 있다.

## 2. REST 인터페이스 원칙
* 자원 식별 : 개별 리소스를 식별할 수 있어야 한다.
* 메세지로 리소스 조작 : 메세지에 작성한 리소스를 다루는 정보를 이용해서 리소스를 얻어오거나 리소스를 조작한다.
* 자기 서술적 메세지 : 요청과 응답 메세지에는 메세지를 처리할 수 있는 정보를 포함한다.
* 하이퍼미디어 : 하이퍼링크를 이용해서 유기적으로 연결된 문서로 쉽고 간단하게 정보를 접할 수 있다.

## 3. REST 서비스 설계
### 인터페이스 설계
* 간단하고 직관적인 API
* 리소스를 다루는 행위는 HTTP 메소드 사용(get, post, put, delete)
* api 버전업 고려
* 명사형 단어 사용 권장
* 목록 형태의 리소스를 다루는 API는 복수형 명사
* 목록에서 특정 조건으로 필터링 : 쿼리 문자열 이용