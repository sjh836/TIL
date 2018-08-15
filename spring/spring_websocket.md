# spring websocket

참조문서
* https://docs.spring.io/spring/docs/4.3.18.RELEASE/spring-framework-reference/html/websocket.html
* https://d2.naver.com/helloworld/1336

먼저 웹소켓 이전부터 천천히 알아보자

## 1. 클라이언트는 어떻게 이벤트를 받아왔나?
전통적으로 웹 클라이언트(브라우저)와 웹 서버는 http request - response 방식으로 동작한다. 그런데 서버에서 일방적으로 클라이언트에게 (요청을 안했는데도) 응답을 내려줘야하는 경우도 있을 수 있다. 바로 채팅이나 알림같은 경우이다. 메세지가 왔거나, 친구신청이 왔다거나 하는 경우들이다. 이런 경우들에 기존의 req - res 방식은 자연스럽지 못했는데, 애초에 단방향 통신을 위해 만들어졌기 때문이다. 웹소켓이 나오기전에는 이것을 polling 이나 long polling 을 이용해서 해결했다.

### 1-1. 구조도
![before_websocket](http://img1.daumcdn.net/thumb/R1920x0/?fname=http%3A%2F%2Fcfile4.uf.tistory.com%2Fimage%2F9915D6395B745A8604286F)

### 1-2. http polling
클라이언트는 서버로 주기적으로 request를 날려서 이벤트내용을 확인한다. 예를들면 ajax로 계속 쪽지가 왔나 확인하는 방법이라고 생각하면 된다. 이것은 클라이언트가 많아지면 서버에 부담이 되며, 실시간성도 아니다. 구현이 쉬울뿐..

### 1-3. http long polling
클라이언트가 서버로 request를 보내되, 서버에서는 바로 응답을 내려주지 않고, 이벤트내용이 있을때까지 기다렸다가(long) 응답을 내려준다. 클라이언트는 응답을 받으면 서버로 다시 요청을 보내둔다. 즉, 이벤트내용을 통지받을때까지 커넥션을 물고있는 것이다. 이렇게 하면 요청량은 줄어들 수는 있으나, 이벤트가 계속 발생한다면 결국 위의 polling 과 같아지는 꼴이 된다.

### 1-4. http streaming
streaming도 클라이언트가 서버로 request를 보낸다. 커넥션을 맺은 후, 서버는 이것을 끊지않고 스트림으로 계속 보내기(flush)만 한다. 잦은 이벤트가 발생하는 환경에서 polling, long polling 보다 매우 효율적이다.

## 2. 웹소켓이란?
1번에서 설명한 경우의 대안으로 WebSocket 이 등장했다. HTTP 환경에서 양방향 통신을 지원하기 위한 프로토콜로 RFC 6455로 2011년 12월에 등장했다. 이것은 HTML5 표준에도 들어갔다. **웹소켓은 HTTP 프로토콜을 기반으로 하므로 기존 요청-응답처럼 80(or 443)에서 Handshake 후 ws(or wss)로 프로토콜을 변환해서 커넥션을 맺는다.** 아래 요청과 응답은 security 기반 로컬서버에서 까본 내용이다. (JSESSIONID 등이..)

### 2-1. 구조도
![웹소켓 요청-응답 구조도](http://img1.daumcdn.net/thumb/R1920x0/?fname=http%3A%2F%2Fcfile7.uf.tistory.com%2Fimage%2F99E54E395B745A860A476A)

* 이렇게 맺어진 커넥션은 종료(유저가 페이지를 나가는 경우?)될 때까지 여기서만 통신한다.
* 글쓰는 동안 페이지에서 아무것도 안했는데도 유지되고 있는 커넥션..!
    * ![커넥션 유지중](http://img1.daumcdn.net/thumb/R1920x0/?fname=http%3A%2F%2Fcfile2.uf.tistory.com%2Fimage%2F9969DC395B745A861595BE)
* 프레임 캡쳐
    * ![프레임 캡쳐](http://img1.daumcdn.net/thumb/R1920x0/?fname=http%3A%2F%2Fcfile4.uf.tistory.com%2Fimage%2F99EF40395B745A87097072)

### 2-2. websocket request
    GET ws://localhost:8080/socket/485/rwstxini/websocket HTTP/1.1 // http 가 아닌 ws 프로토콜을 사용한다. (하지만 http를 기반으로 함)
    Host: localhost:8080
    Connection: Upgrade // keep-alive 같은게 아니라 Upgrade되었다.
    Pragma: no-cache
    Cache-Control: no-cache
    User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/68.0.3440.106 Safari/537.36
    Upgrade: websocket // websocket 을 사용
    Origin: http://localhost:8080
    Sec-WebSocket-Version: 13
    Accept-Encoding: gzip, deflate, br
    Accept-Language: ko-KR,ko;q=0.9,en-US;q=0.8,en;q=0.7
    Cookie: JSESSIONID=CF60EE2C564AEE00BA577EAFD5ADC11F
    Sec-WebSocket-Key: yYpUC+0WDhGBW7MffJRBCQ== // 보안을 위한 요청 키, 관련논의 : https://stackoverflow.com/questions/18265128/what-is-sec-websocket-key-for
    Sec-WebSocket-Extensions: permessage-deflate; client_max_window_bits

### 2-3. websocket response
    HTTP/1.1 101 Switching Protocols // 응답코드가 101 이다. 101은 '프로토콜 전환' 으로 요청자가 서버에 프로토콜 전환을 요청했으며 서버는 이를 승인하는 상태이다.
    Server: Apache-Coyote/1.1
    Upgrade: websocket
    Connection: upgrade
    Sec-WebSocket-Accept: y0C2sLRjQhdq3geJIKYeRVUgtFg= // 보안을 위한 응답 키로 Sec-WebSocket-Key를 base64로 인코딩한다.
    Sec-WebSocket-Extensions: permessage-deflate;client_max_window_bits=15
    Cache-Control: no-cache, no-store, max-age=0, must-revalidate
    Pragma: no-cache
    Expires: 0
    X-XSS-Protection: 1; mode=block
    X-Frame-Options: DENY
    X-Content-Type-Options: nosniff
    Date: Wed, 15 Aug 2018 12:47:49 GMT

### 2-4. 웹소켓이 다른 소켓들과 다른점?
* 웹소켓은 UTF-8 포맷의 메세지 스트림만 허용한다.
* 웹소켓은 HTTP를 기반으로 하면서, 양방향 통신에서 HTTP의 문제점을 해결하는 것만 목표로 하고 있다.

## 3. Spring 4.x WebSocket
스프링 4.x부터는 WebSocket 을 아래와 같이 지원한다.

* 메시지 전송과 수신을 위한 하위 레벨 API
* 스프링 MVC 컨트롤러에서의 메시지 처리를 이한 상위 레벨 API
* 메시지 전송을 위한 메시징 템플릿
* 브라우저, 서버, 프록시에서 웹 소켓 지원 부족을 지원하기 위한 SockJS

### 3-1. SockJS
* https://github.com/sockjs/sockjs-client

웹소켓은 표준이지만 예전 브라우저들은 지원을 안하는 것도 있다. (IE 10 이전 등) 이럴때 SockJS을 쓰면 Socket.io 마냥 크로스브라우져를 해결해준다. SockJS를 사용함으로써 브라우저 상관없이 사용할 수 있는 것이다. Spring 진영에서도 SockJS 을 공식 지원한다. SockJS을 사용여부는 매우 간단한데 java config면 withSockJS(), xml이라면 `<websocket:sockjs />` 으로 해결된다. (이거 안쓰면 Native WebSocket 사용)

### 3-2. WebSocketHandler
org.springframework.web.socket 패키지에 있으며 WebSocketHandler 인터페이스를 구현한 bean을 등록해서 웹소켓요청을 처리해야한다. 주요 메소드는 다음과 같다.

* afterConnectionEstablished(WebSocketSession session) : 클라이언트가 서버로 연결된 이후에 실행
* handleMessage(WebSocketSession session, WebSocketMessage<?> message) : 클라이언트가 서버로 메세지를 전송했을 때 실행
* afterConnectionClosed(WebSocketSession session, CloseStatus status) : 클라이언트가 연결을 끊었을 때 실행
* handleTransportError(WebSocketSession session,Throwable exception) : 연결된 클라이언트에서 예외 발생 시 실행

웹소켓을 사용할 때는 보통 채팅용으로 많이 사용해서 그런지, WebSocketHandler 나 AbstractWebSocketHandler 를 사용하기 보다 이를 확장한 TextWebSocketHandler 를 많이 사용한다.

* handleTextMessage(WebSocketSession session, TextMessage message) : handleMessage 와 비슷한데 텍스트

### 3-3. HandshakeInterceptor
org.springframework.web.socket.server 패키지에 있으며 HandshakeInterceptor 인터페이스를 구현한 bean은 ws 커넥션 과정의 Handshake 때 beforeHandshake()와 afterHandshake()를 수행한다. HandshakeInterceptor 는 HTTP 정보 (특히 쿠키-세션ID)를 옮길 때 많이 사용하는 듯하다. 구현체인 HttpSessionHandshakeInterceptor 에서는 HTTP.SESSION.ID라는 이름으로 JSESSIONID를 옮겨담는다. `attributes.put("HTTP.SESSION.ID", session.getId());`

만약, spring security와 spring websocket을 같이 쓴다면 HandshakeInterceptor를 구현해서 채팅유저의 인증을 손쉽게 처리할 수 있다. (물론 security 안써도 가능은 하다)

어떤 블로그포스팅에서는 클라이언트단에서 id 등을 보내서 처리하고 있었는데, 이것은 변조 등의 위험이 있을 수 있어 추천하지 않는다.

#### 클라이언트에서 유저정보를 포함해 처리하는 예
    // 클라이언트
    msg = {};
    msg.id = "${user.id}";
    msg.부가정보 = ...
    msg.text = $("#msg").val();
    sock.send(JSON.stringify(msg));

    // 서버
    json 메세지 파싱 후 처리 

## 4. 예제
### 4-1. maven 설정
    <!-- Properties  -->
    <spring-version>4.3.18.RELEASE</spring-version>
    <jackson.version>2.9.6</jackson.version>

    <!-- Dependency -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-websocket</artifactId>
        <version>${spring-version}</version>
    </dependency>
        <dependency>
        <groupId>com.fasterxml.jackson.core</groupId>
        <artifactId>jackson-databind</artifactId>
        <version>${jackson.version}</version>
    </dependency>

### 4-2. 웹소켓 설정
    <websocket:handlers>
        <websocket:mapping handler="chatHandler" path="/socket" />
        <websocket:handshake-interceptors>
            <bean class="com.devljh.infra.handler.intercept.HandshakeInterceptor" />
        </websocket:handshake-interceptors>
        <websocket:sockjs />
    </websocket:handlers>

    <bean id="chatHandler" class="com.devljh.domain.chat.ChatHandler" />

* websocket:handlers 에 allowed-origins 속성으로 CORS 관련 문제를 해결할 수 있다.

### 4-3. HandshakeInterceptor
    public class HandshakeInterceptor extends HttpSessionHandshakeInterceptor {
        @Autowired
        private UserService userService;

        @Override
        public boolean beforeHandshake(ServerHttpRequest request, ServerHttpResponse response, WebSocketHandler wsHandler, Map<String, Object> attributes) throws Exception {
            User user = (User) SecurityContextHolder.getContext().getAuthentication().getPrincipal();
            attributes.put(UserConstant.HEADER_USER_KEY, userService.findBy(user.getId()));
            return super.beforeHandshake(request, response, wsHandler, attributes);
        }
    }

### 4-4. ChatHandler
    public class ChatHandler extends TextWebSocketHandler {
        private List<WebSocketSession> sessionList;

        private ChatHandler() {
            sessionList = new ArrayList<>();
        }

        @Override
        public void afterConnectionEstablished(WebSocketSession session) throws Exception {
            User user = getUser(session);
            sessionList.add(session);
            log.info("채팅 접속 : {}", user.getId());
        }

        @Override
        protected void handleTextMessage(WebSocketSession session, TextMessage message) throws Exception {
            User user = getUser(session);
            log.info("메세지전송 = {} : {}", user.getId(), message.getPayload());
            for (WebSocketSession currentSession : sessionList) {
                currentSession.sendMessage(new TextMessage(user.getId() + " : " + message.getPayload()));
            }
        }

        @Override
        public void afterConnectionClosed(WebSocketSession session, CloseStatus status) throws Exception {
            User user = getUser(session);
            sessionList.remove(session);
            log.info("연결 끊김 : {}", user.getId());
        }

        private User getUser(WebSocketSession session) {
            return (User) session.getAttributes().get(UserConstant.HEADER_USER_KEY);
        }
    }

### 4-3. 클라이언트
* sockjs cdn : https://cdnjs.com/libraries/sockjs-client 에서 확인 가능
    * ex. `<script type="text/javascript" src="https://cdnjs.cloudflare.com/ajax/libs/sockjs-client/1.1.5/sockjs.min.js"></script>`
* sockjs 관련 스크립트 추가

        devljh.webSocket = {
            init: function (oParam) {
                this._url = oParam.url || "";
                this._initSocket();
                this._initEvent();
            },
            _initSocket: function () {
                this._socket = new SockJS(this._url); // 소켓 연결
                this._socket.onmessage = function (evt) { // evt 파라미터는 웹소켓을 보내준 데이터
                    $("#data").append(evt.data + "<br/>");
                };
                this._socket.onclose = function (evt) {
                    $("#data").append("연결 끊김");
                }
            },
            _initEvent: function () {
                $("#sendBtn").on("click", (function() {
                    var msg = $("#message").val();
                    this._socket.send(msg);
                }).bind(this));
            }
        };
* dom 하단에 모듈 init 해주기

        $(document).ready(function() {
            devljh.webSocket.init({
                url: "<c:url value="/socket" />"
            });
        });