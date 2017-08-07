# body-parser 모듈

참조문서 : https://www.npmjs.com/package/body-parser

post로 요청된 body를 쉽게 추출할 수 있는 모듈이다. 추출된 결과는 request객체(IncomingMessage 타입)에 body 속성으로 저장된다. API문서보면 다양한 메소드들이 많지만, 여기서는 URL-encoded form body parser만 사용한다.

`npm install body-parser` 로 설치한다.

## 1. http 내장모듈만 쓰는 것과 뭐가 틀린가?
http모듈로만 post body를 파싱하려면, `req.on('data', function(chunk) { body += chunk; });` 와 같이 이벤트를 등록해야한다. 그다음에 인코딩처리를 해줘야한다.

그치만 body-parser를 쓰면 `bodyParser.urlencoded()`를 등록하면, 자동으로 req에 body속성이 추가되고 저장된다. 만약 urls에 접근하고싶다면, req.body.urls이다. 인코딩도 default로 UTF-8로 해준다. 이벤트등록할 필요 자체가 사라진다.

## 2. urlencoded()의 옵션
만약 아무 옵션을 주지 않는다면, body-parser deprecated undefined extended: provide extended option 같은 문구가 뜬다.

문서를 보면, `.use(bodyParser.urlencoded({ extended: true or false }));` 로 쓰라고 한다. extended 는 중첩된 객체표현을 허용할지 말지를 정하는 것이다. 객체 안에 객체를 파싱할 수 있게하려면 true

