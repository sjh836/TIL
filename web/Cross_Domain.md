# 크로스도메인
참조문서

* https://developer.mozilla.org/ko/docs/Web/Security/Same-origin_policy
* https://developer.mozilla.org/ko/docs/Web/HTTP/Access\_control_CORS
* https://www.w3.org/Security/wiki/Same\_Origin_Policy

크로스도메인 이슈가 왜 발생하는지와 헤더에 뭘 넣어야 안뜨는지만 알고 있었다. 깊이 있는 공부를 해보자.

#### 클라이언트단에서 해결
* 브라우저에서 옵션 변경
* 크롬 확장프로그램 플러그인
* JSONP

#### 서버단에서 해결
* CORS

이외에도 document.domain 속성을 사용하는 방법, Cross-document messaging 등이 있다.

## 1. 동일 출처 정책(SOP)
만약 나의 API서버를 외부 웹 서비스에서 막 접근해서 가져다 쓴다면 문제가 있다. 따라서 이런 행위를 차단하려고, 동일한 도메인이 아니면 아래처럼 크로스도메인 이슈를 발생시킨다.

	XMLHttpRequest cannot load http://[도메인]:3000/. No 'Access-Control-Allow-Origin' header is present on the requested resource. Origin 'http://[도메인]:' is therefore not allowed access.

자바스크립트 엔진 표준 스펙에 동일출처정책(same-origin policy)라는 것이 있다. AJAX(XMLHttpRequest), iframe 등으로 접근할 때는 동일 출처일 때만 가능하다는 것이다. 여기서 동일 출처는 프로토콜, 호스트명, 포트를 말한다. **테스트 결과,** 요청에 대한 응답은 오는 듯하다.(서버단에서 검증하는게 아니고 클라이언트에서 확인하다보니..) 응답코드나 Content-Length가 정상적으로 왔다. 그러나 Response를 볼 수는 없었다.

## 2. 클라이언트단에서 해결
일반적으로 클라이언트에서 해결하는 방법들은 범용적일 수 없다. 엔드유저들보고 알아서 SOP 회피하라고 할 수는 없으니.. 서버단에서 해결하는 것이 정석이다.

### 2-1. 웹브라우저에서 동일출처정책 확인X
동일출처정책 확인은 클라이언트에서 하는 것이다보니, 브라우저에서 확인을 안하겠다고 하면 Response를 확인할 수 있다.

### 2-2. 크롬 확장플러그인
웹스토어에서 cors검색해서 확장플러그인 설치하면 된다.

### 2-3. JSONP방식으로 요청
웹 브라우저에서 리소스(css, js) 파일들은 SOP영향없이 로딩이 가능하다. 이런 점을 이용해 서버 응답을 json파일로 바꿔 로딩하는 방식이다. 단점은 리소스파일을 get으로만 읽어오기 때문에, GET방식의 API요청만 가능하다.

## 3. 서버단에서 해결
CORS는 Cross-Origin Resource Sharing의 약자이다. CORS로 외부 요청을 허용할 경우 Ajax 등이 외부에서도 사용가능해진다.

클라이언트는 서버에게 preflight라는 사전요청을 보낸다. HTTP메소드는 OPTIONS를 사용하며, HOST에 클라이언트주소를, Origin에 서버주소를 넣어 보낸다. 이를 통해 권한이 있는지 확인할 수 있다. 서버는 preflight 요청을 핸들링하여 CORS를 설정해주면 된다.

![동작구조도](http://img1.daumcdn.net/thumb/R1920x0/?fname=http%3A%2F%2Fcfile9.uf.tistory.com%2Fimage%2F2395C1335976A96D292432)

### 3-1. 모든 요청 허용
preflight 요청을 받기 위해 OPTIONS 메소드를 추가한다. 헤더에 아래 내용들을 포함시켜주면 된다.

	Access-Control-Allow-Origin: *
	Access-Control-Allow-Methods: GET,POST,PUT,DELETE,OPTIONS
	Access-Control-Max-Age: 3600
	Access-Control-Allow-Headers: Origin,Accept,X-Requested-With,Content-Type,Access-Control-Request-Method,Access-Control-Request-Headers,Authorization

### 3-2. 선별적 허용
클라이언트의 헤더값을 보고 서버에서 응답헤더에 해당 출처를 허용하는 걸 담아주면 된다.

#### 3-2-1. 클라이언트의 요청 헤더
* Origin: 요청을 보내는 페이지의 출처(도메인)
* Access-Control-Request-Method: 실제 요청하려는 메소드
* Access-Control-Request-Headers: 실제 요청에 포함되어 있는 헤더명

#### 3-2-2. 서버의 응답 헤더
* Access-Control-Allow-Origin : 허용할 출처
* Access-Control-Allow-Credentials: 클라이언트 요청이 쿠키를 통해서 자격 증명을 해야 하는 경우에 true. true를 응답 받은 클라이언트는 실제요청시 서버에서 정의된 규격의 인증값이 담긴 쿠키를 같이 보내야 한다.
* Access-Control-Expose-Headers: 클라이언트 요청에 포함되어도 되는 사용자 정의 해더.
* Access-Control-Max-Age: 클라이언트에서 preflight의 요청 결과를 캐시하는 시간(초)
* Access-Control-Allow-Methods: 요청을 허용하는 메소드. 기본값은 GET,POST이며 클라이언트에서는 헤더 값에 해당하는 메서드일 경우에만 실제 요청을 시도하게 된다.
* Access-Control-Allow-Headers: 요청을 허용하는 헤더. 