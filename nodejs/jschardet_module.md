# jschardet

참조문서 : https://www.npmjs.com/package/jschardet

텍스트가 어떤 인코딩인지 모르는 상황일 때, jschardet 모듈을 통해 탐지할 수 있다. iconv와 쓰면 좋다. `npm install jschardet` 로 설치한다.

## 1. 사용 방법
text라는 임의의 문자열을 detect()로 문자코드를 판정한다. 객체를 반환하는데, { encoding: "EUC-KR", confidence: 0.99 } 이런식이다. 따라서 format.encoding 를 분기처리해주면 되겠다.

	var format = jschardet.detect(text);

	if (format.encoding != 'UTF-8') { // EUC-KR 등이 true를 반환할 것이다
		var iconv = new Iconv(format.encoding, 'utf-8//translit//ignore'); //UTF-8로 변환
		text = iconv.convert(text).toString();
	}

## 2. 문자셋 지원 현황
EUC-KR, ISO-2022-KR, UTF-16, UTF-8, ASCII 등