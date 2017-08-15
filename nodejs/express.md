# Express 모듈
참조문서 : http://expressjs.com/en/4x/api.html

express는 경량 HTTP 웹 프레임워크다. 요청과 응답, 미들웨어 사용, 템플릿 엔진을 제공한다.

`npm install express` 로 설치한다.

## 1. 미들웨어
* 작은 단위 모듈로 요청과 응답을 처리하는 함수 형태
* express는 여러 개의 미들웨어로 구성
* 미들웨어 사용 설정 : app.use([미들웨어])

예제

	app.use(function (req, res) {
		res.send('hello Express!');
	});

### 1-1. 미들웨어 설정(mount)
`app.use([path,] function [,function])`

* path를 생략하면 루트(/)이다.
* function을 2개이상 둘 수도있다. 즉 같은 요청을 여러개의 미들웨어로 처리가 가능하다는 것
* 메소드별 미들웨어 설정 가능 : get, post, put, delete 등
* function(req, res, next) {} : next()로 다음 미들웨어가 동작하도록 호출할 수 있다.

### 1-2. 미들웨어 종류
* 애플리케이션 수준의 미들웨어
* 라우터 수준의 미들웨어
* 에러 처리
* 빌트인
* 써드파티 : cookie-parser 등과 같이 설치해야 사용 가능

		var cookieParser = require('cookie-parser');
		app.use(cookieParser());

### 1-3. 미들웨어 동작 순서
1. 파비콘 처리 미들웨어
2. 로깅
3. 정적 파일
4. 서비스 미들웨어

## 2. 정적 파일 처리 미들웨어
express에 내장되어 있는 express.static을 이용한다.

* 생성 : express.static(path, [options])
* 옵션
	* etag : etag 사용 여부, 기본 true
	* lastModified : Last-Modified 헤더 사용, 기본 true
	* maxAge : max-age 메세지 헤더, 기본값 0
	* index : 경로요청 시 기본 제공 파일, 기본값 index.html

정적파일 위치 설정은 다수 가능하다. 첫번째 정적파일 미들웨어에서 못찾으면 다음 미들웨어에서 찾는 식으로 돌아간다.

보통 가상 경로를 설정하고 사용한다.

`app.use('/static', express.static('files');`

## 3. 라우팅
* 클라이언트의 요청을 특정한 미들웨어로 분배
* http 메소드별, URL경로별로 라우팅 가능

### 3-1. 라우팅 메소드
*  app.all(path, callback[, callback])
*  app.route(path) : 같은 url에 여러 메소드쓸때 사용
*  app.get(path, callback[, callback])
*  app.post(path, callback[, callback])
*  app.put(path, callback[, callback])
*  app.delete(path, callback[, callback])

### 3-2. 동적 파라미터
app.get('/user/:id', callback) 하게되면 req.params.id 로 동적으로 파라미터를 얻을 수 있다. 다수 설정도 가능하다.

### 3-3. 경로에 정규식 사용
앞에 글자에 적용된다. 여러글자에 먹이려면 괄호() 이용

* ? : 문자 존재하거나 생략
* \+ : 1번 이상 반복
* \* : 임의의 문자

### 3-4. 라우팅 로직 분리
보통은 router.js 에서

	var express = require('express');
	var router = express.Router();
	
	router.get(path, callback);
	
	module.exports = router;

그러면 메인로직인 app.js 에서 이런식으로 받는다. 

	app.use('/user', require('./userRouter.js'));
	app.use('/board', require('./boardRouter.js'));

## 4. 에러 처리
모든 미들웨어에서 각각 내부에서 에러를 처리하는 로직이 있다면 비효율적이다. 에러처리 미들웨어를 따로 만들어 일관된 에러 처리를 하는게 좋다.

에러 파라미터를 첫번째로 설정한다.

	app.use(function(err, req, res, next) {
		res.status(500).send(err.stack);
	});

일반 미들웨어에서 이런식으로 넘겨준다.
	
	var error = new Error();
	error.code = ...
	return next(error);

## 5. 요청 분석
* req.query : 쿼리 문자열
* req.path : 요청 URL 중 경로
* req.params : URL의 파라미터
* req.cookie : 요청 메세지 내 쿠키 분석
* req.body : 요청 메세지 바디 분석

## 6. 응답 메소드
* res.json() : JSON 응답메세지 전송
* res.redirect() : 리다이렉션 응답 전송
* res.render() : 템플릿으로 렌더링
* res.send() : JSON, HTML, Buffer 전송, 메세지 헤더에 Content-Type 자동 설정
* res.sendStatus() : 상태코드와 상태메세지 진송
* res.status() : 상태 코드 설정, 응답메소드 종료X
* res.download() : 파일 다운로드

