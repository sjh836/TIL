# iconv

참조문서 : https://github.com/bnoordhuis/node-iconv

iconv는 파이썬 등등 다른언어에서도 인코딩 관련 라이브러리로 유명한 것 같다. 여기서는 node 모듈로서 iconv를 다룬다.

`npm install iconv` 로 설치한다.

window의 경우 파이썬이 설치되지 않아, 의존성문제로 설치가 되지 않을 수 있다. 이 경우 파이썬 등등 복잡하게 설치하는 것보단, 윈10 파워쉘을 이용하면 간단하게 해결할 수 있다.

https://stackoverflow.com/questions/21365714/nodejs-error-installing-with-npm 에서는 `npm install --global --production windows-build-tools` 를 하라고 한다.

## 1. 사용법과 옵션
	var Iconv = require('iconv').Iconv;  
	var iconv = new Iconv('euc-kr', 'utf-8//translit//ignore');

1번째 인자에서 2번째 인자로 인코딩을 변환하는 것이다. 2번째 인자에서 //를 통해 추가적인 옵션을 주고있는데,

* translit : 대체 가능한 문자가 있는 경우, 해당 문자로 대체할 수 있도록 함
* ignore : 지원하지 않는 문자의 경우 해당부분을 무시

## 2. 관련 메소드
	iconv.convert(text).toString()

convert를 통해 인코딩포맷을 변환한다. 단지 변환뿐이기 때문에, toString()을 쓴다.

## 3. 사용 가능 포맷
EUC-KR, CP949, ISO-2022-KR, JOHAB, UTF-8, UTF-16, ASCII 등