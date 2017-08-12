# Request 모듈

참조문서 : https://www.npmjs.com/package/request

## 1. 기본 사용
`npm install request` 로 설치한다.

	var request = require("request");
	
	request("https://www.naver.com", function(err, res, body) {
		console.log(body);
	});

첫번째 인자로 준 URL의 html소스코드가 모두 찍힌다. 콜백함수의 response는 http모듈의 IncomingMessage 와 비슷하지만(res는 IncomingMessage 타입), 추가적으로 body등을 갖고 있다.

body는 string 타입으로 해당 URL의 html소스를 통째로 들고있다.

## 2. 다양한 Parameter 설정
다양한 설정으로 요청이 가능하다.

	var request = require("request");
	
	request({
		uri: "https://www.naver.com",
		method: "GET",
		timeout: 10000,
		followRedirect: true,
		maxRedirects: 10
		},
		function(error, response, body) {
			console.log(body); 
		}
	);

* timeout : ms초 이후에 타임아웃
* followRedirect : 리다이렉트시키면 따라갈껀지, 말껀지
* maxRedirects : 리다이렉션의 최대 처리 갯수

실제로 `followRedirect: false`, `maxRedirects: 1` 를 주고 www.naver.com에 요청을 보내면 http.get과 동일한 결과를 보인다.

	<html>
	<head><title>302 Found</title></head>
	<body bgcolor="white">
	<center><h1>302 Found</h1></center>
	<hr><center> NWS </center>
	</body>
	</html>

## 3. Form 데이터 전송
application/x-www-form-urlencoded 등을 지원해준다.

	request({
		uri: "http://localhost:3000",
		method: "POST",
		form: {
			urls: "http://devljh.com"
		}
	}, function(err, res, body) {
			console.log(body);
		}
	);

	//또는

	request.post('http://service.com/upload').form({key:'value'})

## 4. 기타 기능
이외에도 정말 많은 기능들을 제공해준다. 이 모든게 대부분 request() 메소드 하나로 해결이 가능하다. 아래는 API문서를 참조하자

* Streaming
* HTTP Authentication
* Custom HTTP Headers
* OAuth Signing
* Proxies
* Unix Domain Sockets
* TLS/SSL Protocol
* Support for HAR 1.2
* All Available Options