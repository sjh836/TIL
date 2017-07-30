# Client Javascript

참조문서 : https://developer.mozilla.org/en-US/docs/web/api

클라단 js 오랫만에 써봐서.. 복습해보자

## 1. 문서 객체 모델(Document Object Model)
문서객체모델(DOM)은 HTML문서의 구조를 나타내는 표기법이며 브라우저가 HTML문서를 조작하는 핵심이다. DOM은 트리 구조로 표현하며 노드로 구성된다.

* root 노드를 document라고 부른다.
* DOM 트리의 모든 노드는 Node 클래스의 인스턴스이다.
* Node 객체에는 트리구조를 나타내는 parentNode와 childNodes 속성, 자신에 대한 속성인 nodeName과 nodeType이 있다.

## 2. get 메소드
DOM에는 원하는 HTML 요소를 빨리 찾을 수 있는 메소드들이 있다. DOM 메소드들이 반환하는 컬렉션은 JS배열같은 게 아니라 HTMLCollection의 인스턴스로 배열 비슷한 객체이다. for문같은건 돌릴 수 있으나, map, filter, reduce 같은 Array.prototype 메소드는 사용할 수 없다

배열로 정 바꾸고 싶으면 확산연산자(...) 를 사용해서 바꿀 수 있다.

* document.getElementById(ID이름) : HTML요소의 고유한 ID로 찾는다
* document.getElementsByClassName(클래스명) : 클래스명으로 찾는다
* document.getElementsByTagName(태그명) : 태그명으로 찾는다 

## 3. DOM 요소 쿼리
다른 요소와의 관계를 사용해 원하는 요소를 찾는 방법이 더 잘 쓰인다. CSS 선택자를 사용해 찾는 메소드들이다. 꺽쇠없이 이름만 쓴다. 예를 들어 a는 <a\> 요소를 전부 찾는다. 클래스는 앞에 점(.)을 찍는다.

* querySelector
* querySelectorAll

## 4. DOM 요소 조작, 생성
모든 요소에는 textContent와 innerHTML가 있다. 이걸 수정하면 조작할 수 있다. 생성은 createElement를 통해 새 노드를 만들 수 있다. insertBefore와 appendChild를 통해 붙인다.

* document.createElement(태그명) : 새 노드 생성
* parent.insertBefore(p1, firstChild) : firstChild에 p1을 삽입
* parent.appendChild(p2) : 마지막 자식 요소로 p2를 삽입

## 5. data 속성
HTML5에서는 data- 속성이 도입됐다. 이 속성을 사용해 HTML 요소에 임의의 데이터를 추가할 수 있다. 브라우저는 이 데이터를 무시하므로, js에서 쉽게 요소에 관한 정보를 읽거나 수정할 수 있다.

예를들어 `data-action="highlight"`이 있다면, .dataset을 통해 `DOMStringMap { action:"highlight }` 에 접근할 수 있다.

## 6. 이벤트
DOM API에서는 200개 가까운 이벤트가 정의되어 있다. 각 브라우저마다 비표준 이벤트를 따로 만들고 있어서 다 알기는 불가능한듯.

모든 요소에는 addEventListener라는 메소드가 있다. 이 메소드를 통해 이벤트가 일어났을 때 호출할 함수를 지정할 수 있다. 호출할 함수는 Event 타입의 객체 하나만을 매개변수로 받는다. 이벤트 객체에는 해당 이벤트에 관한 정보가 모두 들어있는데, click이벤트에는 X,Y좌표, target속성 등이 들어있다.

.on속성(ex. onclick)은 구식방법으로 이걸 쓰면 이벤트에 핸들러 단 하나만 등록할 수 있는 단점이 있다. 그래서 addEventListener를 쓴다.

이벤트 모델은 이벤트 하나에 여러가지 함수(핸들러)를 연결할 수 있도록 설계되어있다. 기본 핸들러가 지정된 이벤트도 많다. 이런 기본 핸들러의 실행을 막으려면 event.preventDefault()를 호출하면 된다.

### 6-1. 캡처링과 버블링
HTML은 계층적이므로 이벤트를 꼭 한 곳에서만 처리해야하는 건 아니다. 부모요소 등에서 처리할 수도 있다. 그러면 이벤트에 응답할 기회는 순서가 있는데, 캡처링과 버블링이 있다.

* 캡처링 : 가장 먼 조상부터 시작하는 방법
* 버블링 : 이벤트가 일어난 요소에서 시작해 거슬러 올라가는 방법

이벤트모델에서는 이 2가지를 모두 지원하기 위해 캡처링을 먼저했다가 다시 버블링을 하는 구조로 되어있다.

### 6-2. 이벤트 카테고리
자주 쓰이는 것중 일부이다.

* 드래그 이벤트 : 드래그엔 드롭 인터페이스를 만들 수 있다. dragstart, drag, dragend, drop
* 포커스 이벤트 : form필드같은 편집가능한 요소를 조작하려 할때 반응할 수 있다.
	* focus : 필드에 들어갔을때
	* blur : 필드를 나올때
	* change : 필드의 내용을 바꿀때
* 폼 이벤트 : 사용자가 전송 버튼을 클릭, 엔터치면 폼이 전송되는데, submit 이벤트가 발생한다.
* 입력장치 이벤트
	* 마우스 : click, mousedown, move, mouseup, mouseenter, mouseleave, mouseover, mousewheel
	* 키보드 : keydown, keypress, keyup
	* 터치장비는 마우스이벤트보다 우선으로 동작한다.
* 미디어 이벤트 : HTML5 비디오, 오디오 플레이어에 관련된 이벤트로 pause, play 등이 있다.
* 진행 이벤트 : 브라우저가 콘텐츠를 불러오는 과정에서 발생한다.
	* load : 브라우저가 요소와 자원들을 모두 불러왔을 때 발생
	* error : 자원을 사용할 수 없을 때 발생

## 7. Ajax
ajax는 Asynchronous Javascript And XML의 약자이다. ajax를 이용해 서버와 비동기적으로 통신하면 페이지 전체를 새로고칠 필요없이 서버에서 데이터를 받아 올 수 있다.

클라이언트 자바스크립트에서 http요청을 만들어 서버에 보내고 데이터를 받는다. 받는 데이터형식은 보통 JSON이다. ajax역시 http위에서 동작하며, 경량의 통신이다 보니 웹 어플리케이션이 빨라진다.

	function refreshServerInfo() {
		const ajax = new XMLHttpRequest();
		ajax.addEventListener('load', function() {
			const data = JSON.parse(this.responseText);
			const serverInfo = document.querySelector('.serverInfo');

			Object.keys(data).forEach(p => {
				const replacements = serverInfo.querySelectorAll(`[data-replace="${p}"]`);
				for (let r of replacements) {
					r.textContent = data[p];
				}
			})
		});
		ajax.open('GET', 'http://localhost:3000', true);
		ajax.send();
	}
	setInterval(refreshServerInfo, 200);