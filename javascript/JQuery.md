# JQuery

참조문서 : http://api.jquery.com/

제이쿼리는 DOM을 조작하거나 Ajax 요청을 할 때 널리 쓰이는 라이브러리다. 제이쿼리로 할 수 있는 건 모두 DOM API로도 할 수 있다. 하지만 제이쿼리를 쓰면 다음과 같은 장점이 있다.

* 브라우저 호환성을 걱정안해도 된다. 
* Ajax 관련 API가 무척 단순하다.
* 내장된 DOM API를 더 유용하고 단순하게 바꾼 메소드를 제공한다.

주로 달러기호($)로 표현하며, 제이쿼리를 불러올 때는 `<script src="http://code.jquery.com/jquery-2.1.4.min.js"></script>` 이런식으로 CDN을 이용하면 된다.

## 1. DOM 기다리기
제이쿼리에서는 브라우저가 페이지를 완전히 읽고 DOM을 구축한 다음에만 호출되는 콜백함수가 있다.

	$(document).ready(function () {}
	// 단축표기
	$(function() {});

## 2. JQuery 객체
제이쿼리로 DOM을 조작할 때, 보통 DOM요소를 wrapping한다. 제이쿼리 함수로 DOM 요소 set을 감싼 것을 제이쿼리 객체라고 부른다. 제이쿼리 함수를 호출할 때는 주로 CSS선택자나 HTML을 사용한다.

CSS선택자로 제이쿼리를 호출하면 해당 선택자에 일치하는 제이쿼리 객체가 반환된다. `const $p = $('p');`

## 3. 요소 조작
DOM API라면 querySelectorAll 때려서 for문으로 순회하면서 작업해야할 것을 제이쿼리는 단순하게 해준다. 자세한건 API 문서를 보자

* eq(N) : N은 인덱스로 해당 요소만 변경할 때 쓴다.(인덱스는 0부터) 
* text(추가) : DOM API의 textContent속성에 대응
* html(추가) : innerHTML속성에 대응
* remove() : 요소 제거, 제이쿼리 객체를 다시반환한다(체인)
* append(추가) : 모든 요소에 새 콘텐츠를 추가
* before(), after() : 형제를 삽입할때
* addClass
* removeClass
* toggleClass

## 4. Ajax
제이쿼리를 이용하면 ajax 역시 간편해진다. get, post 등 메소드들이 있고, 서버 응답처리할때 권장되는 방법인 프라미스도 반환된다.

	function refreshServerInfo() {
		const $serverInfo = $('.serverInfo');
		$.get('http://localhost:3000').then(
			function(data) {
				Object.keys(data).forEach(p => {
					$(`[data-replace="${p}"]`).text(data[p]);
				});
			},
			function(jqXHR, textStatus, err) {
				console.error(err);
				$serverInfo.addClass('error').html('서버연결 실패');
			}
		);
	}
	setInterval(refreshServerInfo, 200);