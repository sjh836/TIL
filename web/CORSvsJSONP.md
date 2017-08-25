# CORS vs JSONP
참조문서 : https://stackoverflow.com/questions/12296910/so-jsonp-or-cors

해당 참조문서는 12년도 글이라서 최신 브라우저일 경우 CORS를, 아니라면 JSONP를 사용하라고 권장하고 있지만, 이제는 대부분 CORS를 지원한다.

![브라우저 호환성](http://img1.daumcdn.net/thumb/R1920x0/?fname=http%3A%2F%2Fcfile24.uf.tistory.com%2Fimage%2F23054933597857283324D5)

## CORS는 웹 표준
CORS는 크로스도메인을 해결하는 웹 표준이고, 보안상 권장되고 있다. JSONP는 script injection으로, GET밖에 지원안한다. CORS는 이와 대비되게 다양한 메소드를 허용하고 있다.

또한 JSONP는 http에서 https는 호출가능하지만, https에서 http는 호출이 불가능하다.
`This request has been blocked; the content must be served over HTTPS.`

## CORS의 유일한 한계
CORS명세를 보면 Access-Control-Allow-Origin에는 단 하나의 문자열리터럴만 넣을 수 있다. node라고 예를들면, `res.setHeader('Access-Control-Allow-Origin', value);` 에서 value에는 *, null, 단일 도메인뿐만 되는 것이다.

따라서 서브도메인이나 패밀리도메인들이 API서버를 쓰고 싶을때 CORS로 특정 도메인만을 허용하는 것은 문제가 될 수 있다. *를 하자니 너무 넓고, 단일도메인만 할 수는 없는 노릇이니까... 

## 로직으로 한계극복
아파치의 경우, .htaccess라는 파일에서 정규표현식을 이용해 해결할 수 있다고 한다.

* 참조문서 : https://stackoverflow.com/questions/1653308/access-control-allow-origin-multiple-origin-domains

node.js에서는 아래와 같은 로직으로 해결할 수 있다.

	// 서버 응답헤더 설정
	const responseHeader = (req, res) => {
		const allowedOrigins = ['http://127.0.0.1', 'null']; // CORS 허용할 도메인 목록
		const origin = req.headers.origin;
	
		res.setHeader('Content-Type', 'application/json; charset="utf-8"');
		
		// CORS 허용
		if(allowedOrigins.indexOf(origin) > -1) { // 
			res.setHeader('Access-Control-Allow-Origin', origin);
		}
		res.setHeader('Access-Control-Allow-Methods', 'GET, POST, OPTIONS');
		res.setHeader('Access-Control-Max-Age', '3600');
		res.setHeader('Access-Control-Allow-Headers', 'Origin, Accept, Content-Type, Access-Control-Request-Method, Access-Control-Request-Headers');
	};