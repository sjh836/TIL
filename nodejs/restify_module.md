# Restify

참조문서 : http://restify.com/docs/home/

Restify는 Express에서 파생된 nodejs REST 프레임워크이다. `npm install restify` 으로 설치한다.

## 1. Express와 뭐가 다른가?
물론 둘다 RESTful API 서버를 만들 수 있다. 차이점만 보자면, express는 웹 모듈로서 웹 애플리케이션을 위해 많은 기능(템플릿, 렌더링 등)을 제공한다.

restify는 express에 비해 가볍고, express의 아키텍처를 그대로 가져오면서, REST API서버운영을 위한 특화된 기능들이 있다. 즉 RESTful API서비스만을 위한 경량 프레임워크이다.

성능차이는 크게 없는 듯하다. 여러 포스팅에서 성능테스트한 글을 확인했지만 express와 restify는 비슷한 결과를 보여주었다.

## 2. 무슨 기능들이 더 있는가?
* 프로파일링을 강조하므로 세부적으로 들어가서 서버의 성능을 최적화
* DTrace을 모든 핸들러에게 제공, 모니터링 가능
	* http://restify.com/docs/dtrace/
* API 문서보면 기존 노드의 http API말고도 몇가지 추가적인 기능들을 제공한다.

### 에러 핸들링
잘못되거나 없는 리소스를 요청하면 자동으로 json으로 뱉어준다. API문서보면 내장에러객체들이 많다.

	{  
	   "code":"ResourceNotFound",
	   "message":"/ljh234234 does not exist"
	}

## 3. 헬로서버 예제

	var server = restify.createServer();
	var port = 3000;
	
	server.get('/:name', function(req, res) {
		res.send('hello ' + req.params.name);
	});
	
	server.listen(port, function() {
		console.log('서버 가동중... in ' + port);
	})

## 4. 내장 플러그인 사용
5.0.0 버전부터 플러그인 사용법이 바뀐듯하다. 예를들어 5.0.0 이전버전에서는 bodyParser()를 쓰려면 `server.use(restify.bodyParser());` 를 사용하면 되었지만, 이제는 `server.use(restify.plugins.bodyParser());` 이런식으로 사용해야 한다.

구체적으로 사용할 수 있는 플러그인들은 API문서를 참고하자

	var server = restify.createServer();
	server.use(restify.plugins.acceptParser(server.acceptable));
	server.use(restify.plugins.authorizationParser());
	server.use(restify.plugins.dateParser());
	server.use(restify.plugins.queryParser());
	server.use(restify.plugins.jsonp());
	server.use(restify.plugins.gzipResponse());
	server.use(restify.plugins.bodyParser());
	server.use(restify.plugins.requestExpiry());
	server.use(restify.plugins.throttle({..});
	server.use(restify.plugins.conditionalRequest());
