# http 모듈
참조문서 : https://nodejs.org/dist/latest-v6.x/docs/api/http.html

`require('http');` 를 써야한다. nodejs는 자체적으로 http 서버를 갖고 있기 때문에, 아파치나 nginx 같은거 안써도 된다.

## 1. 개요
![통신 구조](http://img1.daumcdn.net/thumb/R1920x0/?fname=http%3A%2F%2Fcfile26.uf.tistory.com%2Fimage%2F2233FF3C5963757C28FFC2)

### HTTP 서버
* http.Server : HTTP 서버
* http.IncomingMessage : HTTP 서버의 요청 메세지, Readable Stream 이다.
* http.ServerResponse : HTTP 서버의 응답 클래스

### HTTP 클라이언트
* http.Client : HTTP 클라이언트
* http.ClientRequest : HTTP 클라이언트 요청 메세지
* http.IncomingMessage : HTTP 서버의 응답 메세지, Readable Stream 이다.

## 2. 서버 클래스
http.createServer([requestListener]) 를 통해 서버 객체를 생성한다. 그 후 클라이언트의 접속을 대기한다(listening)

### 2-1. 이벤트
* request : 클라이언트의 리소스 요청 시
* connection : 소켓 연결
* close : 서버 종료

### 2-2. 메소드
* server.listen(PORT)
* server.close()
* server.setTimeout()

간단한 연산 서버 예제이다. (반복문돌며 start부터 end까지 합산)

	var http = require('http');
	var url = require('url');
	var port = 3000;
	
	var server = http.createServer(function(req, res) {
		var urlParsed = url.parse(req.url, true);
		var start = parseInt(urlParsed.query.start);
		var end = parseInt(urlParsed.query.end);
	
		if (!start||!end) {
			res.statusCode = 404;
			res.end('Wrong Parameter');
		} else {
			var result = 0;
	
			for (var i = start; i < end; i++) {
				result += i;
			}
			res.statusCode = 200;
			res.end('sum : ' + result);
		}
	}).listen(port);
	console.log('서버 가동중 : ' + port);

### 2-3. 요청 처리
request 이벤트의 이벤트 리스너함수 첫번째 인자인 req는 IncomingMessage 타입이다.

#### IncomingMessage 요청 분석
* req.url : 요청 URL, url.parse()
* req.method : 요청 메소드
* req.headers : 요청 메세지 헤더
* req(streamable)

post의 body는 IncomingMessage의 data 이벤트 사용

	var server = http.createServer(function(req, res) {
		var body ='';
		req.on('data', function(chunk) {
			console.log('%d bytes of data', chunk.length);
			body += chunk;
		});
	
		req.on('end', function() {
			console.log('post body 다 읽었다');
			console.log(body);

			res.end();
		});
	}).listen(port);

### 2-4. 응답 처리
http.ServerResponse 클래스로 처리한다.

#### 메세지 상태
* response.statusCode = 200;
* response.statusMessage = 'OK';

#### 메세지 헤더 : 바디 전에 작성
* response.writeHead(statusCode[, statusMessage][, headers])
* response.removeHeader(name)
* response.getHeader(name)
* response.setHeader(name, value)

자주쓰이는 예제

	res.writeHead(200, {
		'Content-Length': body.length,
		'Content-Type': 'text/plain'
	});

#### 메세지 바디
* response.end([data][, encoding][, callback]) : 바디작성 종료, 이걸안쓰면 클라이언트가 계속 대기하다가 timeout
* response.write(chunk[, encoding][, callback])

### 2-5. 웹에서 중복 POST 요청 방지
POST 요청 처리 후, redirect 응답

* 상태코드 : 302
* 헤더필드 : Location

ex. `res.writeHead(302, {'Location':'http://www.naver.com'});`

### 2-6. 정적 파일 요청
파일은 path 모듈로 찾는다.

미리 작성된 파일로 응답, req.url로 분석하고, fs.access로 확인하고 fs.readFile(path, callback)을 이용한다.

이후 res.write(data)로 쓰면 된다.

아니면 스트림 파이프로, fs.createReadStream(path).pipe(res) 같은 경우도 되겠다. 메모리 측면에서 더 유리함.

### 2-7. JSON 처리
내장클래스로 모듈 로딩이 불필요하다.

json형태로 요청이 들어오면, parse()한다. json형태로 응답하려면 Content-Type을 application/json으로 하고, stringfy()하고 내보낸다.

* JSON.stringfy() : 문자열로 만듬
* JSON.parse() : 객체만듬

## 3. 클라이언트 클래스
createClient()는 deprecated 되었고, http.request(options[, callback]) 를 써야한다. 이걸 통해 클라이언트 요청을 보낼 수 있다.

만약, 바디없이 GET방식으로 요청을 보내려면 http.get(options[, callback]) 를 쓴다.