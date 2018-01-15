# 서블릿

## 1. 서블릿이란?
* HTTP protocol 서비스를 지원하는 javax.servlet.http.HttpServlet 클래스를 상속하여 개발하며, Servlet은 컨테이너에 의해서 실행되고 관리된다.
* HTML 변경 시 Servlet을 재컴파일해야 하는 단점이 있다.
* 서블릿은 외부 요청마다 프로세스보다 가벼운 스레드로써 응답한다.

## 2. JSP와 서블릿의 차이점
1. JSP : HTML 문서 안에 java 코드를 포함하고 있다. MVC에서 View 역할
2. 서블릿 : 자바 코드 안에 HTML을 포함하고 있다. 주로 MVC에서 컨트롤러 역할

## 3. 서블릿 컨테이너(톰캣 등)란?
* HTTP 요청을 받아서 Servlet을 실행하고 생명주기를 관리하는 역할을 한다.
* servlet과 웹서버가 통신할 수 있는 방법을 제공
* 멀티 스레딩을 지원하여 클라이언트의 다중 요청을 알아서 처리

## 4. 서블릿의 동작 과정
![서블릿 컨테이너의 동작과정](http://img1.daumcdn.net/thumb/R1920x0/?fname=http%3A%2F%2Fcfile5.uf.tistory.com%2Fimage%2F99FD413B5A4B769C1CEB41)

1. 사용자가 URL을 클릭하면 HTTP Request를 Servlet Container에 보낸다. = ① 에 해당
2. Servlet Container는 HttpServletRequest, HttpServletResponse 두 객체를 생성한다. = ② 에 해당
3. 사용자가 요청한 URL을 분석하여 어느 서블릿에 대한 요청인지 찾는다. (web.xml를 참조)
4. 만일 그 클래스가 서블릿 컨테이너에서 한 번도 실행된 적이 없거나, 현재 메모리에 생성된 인스턴스(프로세스)가 없다면 새로 인스턴스를 생성하고(메모리에 로드하고) init()메소드를 실행하여 초기화 한 뒤 스레드를 하나 생성한다. 이미 인스턴스가 존재할 경우에는 새로 인스턴스를 생성하지 않고 기존의 인스턴스에 스레드만 하나 새로 생성한다. 각 서블릿 인스턴스는 서블릿 컨테이너 당 하나만 존재하기 때문에 init() 메소드는 각 서블릿 당 한 번씩만 호출된다. = ③ 에 해당
5. 컨테이너는 서블릿 service() 메소드를 호출하며, POST, GET여부에 따라 doGet() 또는 doPost()가 호 출된다. = ⑤ 에 해당
6. doGet() 또는 doPost() 메소드는 동적인 페이지를 생성한 후 HttpServletResponse객체에 응답을 보낸다. = ⑥ 에 해당
7. 응답이 완료되면 HttpServletRequest, HttpServletResponse 두 객체를 소멸시킨다.

## 5. 배포서술자(DD, web.xml)
* Servlet, Error page, Listener, Fillter, 보안 설정 등 웹 애플리케이션의 설정 파일이다.
* URL과 실제 서블릿의 매핑 정보도 갖고 있다.
* 하나의 웹 애플리케이션에 하나만 존재
* 보통 Web Document Root 에 WEB-INF 디렉터리 아래에 있다.