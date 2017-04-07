# JS를 이용한 HTML include

HTML에서 보통 머릿말, 꼬리말, 메뉴바 등이 흔하게 중복된다.(서버사이드언어나 동적언어를 사용하지 않는다면 메뉴가 바뀔때마다 전부다 수동으로 바꾸어 주어야한다.) 이러한 문제를 해결하는 방법에는 여러가지가 있겠지만 AJAX를 이용해서 해결해보자

## 1. 사용법
1. `<head>`에 `<script src="js/includeHTML.js"></script>`를 넣어준다.
2. `<body>`에 `<div include-html="navbar.html"></div>`를 넣고 `<script>includeHTML();</script>`를 호출해준다. 여기서는 navbar를 관리하고 있다.

## 2. includeHTML.js 코드

	function includeHTML() {
	  var z, i, elmnt, file, xhttp;
	  z = document.getElementsByTagName("*");
	  for (i = 0; i < z.length; i++) {
		elmnt = z[i];
	    file = elmnt.getAttribute("include-html");
	    if (file) {
	      xhttp = new XMLHttpRequest();
	      xhttp.onreadystatechange = function() {
	        if (this.readyState == 4 && this.status == 200) {
	          elmnt.innerHTML = this.responseText;
	          elmnt.removeAttribute("include-html");
	          includeHTML();
	        }
	      }      
	      xhttp.open("GET", file, true);
	      xhttp.send();
	      return;
	    }
	  }
	}

**참조:** https://www.w3schools.com/w3css/w3data_home.asp