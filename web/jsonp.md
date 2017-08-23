# JSONP

참조문서 : https://blog.seotory.com/post/2016/04/understand-jsonp

JSONP는 json with padding의 약자이다. <script /\> 태그는 SOP 정책에 속하지 않는다는 점을 이용해, 서로 다른 도메인간의 javascript 호출을 위하여 jsonp가 사용되었다.

## 1. 원리
1. API서버에 데이터를 요청할 페이지에서 서버의 데이터를 받아 처리할 콜백 함수를 준비한다. 그 후에 script 태그를 생성하고 API서버에 request한다.
2. 데이터 요청을 받은 서버에서는 콜백 함수를 실행하는 스크립트를 출력한다. 이 때 callback 함수의 인자에는 요청받은 데이터(기존 JSON)가 들어간다.

즉, 기존에 서버에서 `{"key1" : "value1",  "key2" : "value2"}` 이런 형식으로 json을 반환해주었다면, JSONP에서는 `callback({"key1" : "value1",  "key2" : "value2"});` 이런식으로 반환해준다.

![ajax와 jsonp 비교](http://img1.daumcdn.net/thumb/R1920x0/?fname=http%3A%2F%2Fcfile8.uf.tistory.com%2Fimage%2F2166B5335976AA0F1A5EEC)

AJAX랑 비교사진이다. JSONP는 서버측에서 콜백함수를 실행하는 코드를 반환하는 점만 다르다.

## 2. 구현
jQuery v.1.2 이상부터 jsonp 형태가 지원된다. $.ajax 옵션에서 `dataType: "jsonp"` 를 설정해야한다. type은 GET으로 해야한다.

### AJAX api의 JSONP 관련 속성
* dataType : 'jsonp' 지정
* jsonp : 일반적으로 client에서 지정한 'callback' argument (http:host:port/jsonp_uri?callback=jquery function)를 서버에서는 호출할 function으로 지정한다. 하지만 arguemnt명이 callback이 아니라면 jsonp 속성으로 지정하면 변경됨. 
* jsonpCallback : jsonp 결과값이 전달될 function을 지정할 수 있음. 기본적으로는 jquery에서 알아서 지정한다.