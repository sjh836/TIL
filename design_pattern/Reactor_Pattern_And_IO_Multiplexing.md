# Reactor Pattern 과 I/O Multiplexing

* 참조문서
    * https://dzone.com/articles/understanding-reactor-pattern-thread-based-and-eve
    * https://stackoverflow.com/questions/14317992/thread-per-connection-vs-reactor-pattern-with-a-thread-pool
    * https://hila.sh/2019/12/28/reactor.html

![리액터패턴](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FdhZZuh%2FbtqTNzVxPMD%2FfWPkFiHy8gra8rFTlJBOg1%2Fimg.png)

## 1. 반응자(Reactor) 패턴이란?
* 동시성을 다루는 디자인 패턴 중 하나로, 동시에 들어오는 여러 클라이언트의 요청들을 처리하는 기법이다.
    * 순차적으로 처리
    * 멀티프로세스, 멀티스레드 대신 싱글스레드를 채택함으로써, C10K 문제 해결
* 이벤트 핸들링 패턴, event driven architecture 라고도 할 수 있겠다.
    * 개인적으론, 이벤트루프의 고전적이고 초기모델같은 느낌이다..
* 멀티프로세스나 멀티스레드 방식을 사용하지 않음으로써, 얻는 장점들은 여기서 설명하진 않겠다..
    * cf) 컨텍스트 스위칭, 동기화 등..
* (예제 코드는 마지막 참조문서가 잘 설명하고 있다.)

### 1-1. 리액터 패턴의 구성
* Reactor
    * 싱글스레드다. (이벤트루프, 무한반복문 돌며 수신받은 이벤트를 Dispatcher에 전달)
    * 이벤트를 전방에서 수신한다.
    * EventType과 등록된 EventHandler를 맵핑하여 들고있다.
* Dispatcher
    * EventType에 맞는 Handler 에게 요청을 전달하여 처리한다. (Demultiplexing 역할)
* Handler
    * 비즈니스 로직들, 실제 작업을 수행한다.
    * 응답성을 유지하기 위해, CPU작업이 작거나, 잘개 쪼개서 분할되어야한다. (하나의 거대한 요청이라면, 이거 처리하는 동안 이벤트루프가 block 된다)
    * IO wait 작업들이 불가피하다면, 다른 쓰레드에서 수행되어야한다. (IO작업 기다리느라 이벤트루프가 block 된다)

### 1-2. 리액터 패턴의 흐름
1. 어플리케이션 시작 : 리액터 초기화(이벤트핸들러 등록)
2. 리액터가 이벤트 수신 대기..
3. 이벤트 수신
4. 디스패처를 통해, 핸들러에게 이벤트 전달
5. 처리 후, return

## 2. 다중화와의 유사성
* 싱글스레드로 요청(=이벤트) 처리, multiplexing 과 demultiplexing 에 대한 것이 fd set 을 활용하는 select, epoll 과 유사하다.

### 2-1. 다중화(multiplexing = 멀티플렉싱)란 무엇인가?
* 하나의 채널로 2개 이상의 데이터(=시그널)을 전송하는 기술이다.
* 최소한의 물리장비를 사용하면서, 최대한의 데이터를 전송하기 위해 고안되었다.
    * ex) TDM : 시분할 다중화 , FDM : 주파수 분할 다중화

### 2-2. 입출력 다중화(I/O 멀티플렉싱)
* SW개발 세계에서, 입출력 다중화는 하나의 프로세스 또는 쓰레드에서 여러 입력과 출력을 다루는 기법을 말한다.
    * ex) 리눅스의 select, epoll. 윈도우의 IOCP. 맥의 Kqueue 를 기반으로 함
* 클라이언트와 서버의 연결은 소켓을 통해 이루어지는데, 이 소켓은 하나의 파일(=file descriptor)이다.
* 이 기법을 사용하면, 서버에서 클라이언트와의 연결을 별도의 프로세스(or 쓰레드)를 생성해서 맺지않고, 하나의 프로세스(or 스레드)로 관리한다.
    * 이 기법말고, 네트워크 프로그래밍에서 서버에서 클라이언트의 연결요청을 accept 하려면, 멀티프로세스나 멀티스레드를 사용했어야 했다.
    * TCP 소켓프로그래밍 포스팅 : https://sjh836.tistory.com/32
* 멀티프로세스, 멀티스레드 방식은 FD(=소켓)을 하나씩 전담해서 맡는 방식이라면,
* 멀티플렉싱 서버는 다음과 같이 동작한다.
    1. epoll 인스턴스 생성
    2. 무한반복문 수행 (=싱글쓰레드, 이벤트루프 컨셉)
    3. 새 클라이언트의 연결이 있다면, FD를 epoll 인스턴스에 등록
    4. epoll 이 관찰중인 FD 중에 변경이 있다면(=이벤트 발생), 변경된 FD 목록만 전달해준다. (무한반복문이 read 해갈수 있도록)
    5. read 한 데이터를 비즈니스 로직(ex. echo서버)을 통해 처리
    6. 처리된 데이터를 소켓을 통해 클라이언트에게 전달
* epoll 은 커널레벨에서 멀티플렉싱을 지원해준다. select와 다르게 커널이 파일 상태 테이블(fd 비트 배열, fd set)을 직접 관리한다.
    * 내부적으로는 select 와 동작방식이 비슷함 (select를 사용해서 개발자가 어플리케이션에서 직접 구현하던게, 커널로 들어간 것이기에..)
    * epoll 은 동기형 통지모델이다. https://sjh836.tistory.com/109

## 3. 혼동하기 쉬운 포인트
* 1번에서도 언급했듯이, 요청은 순차적으로 처리된다. (순수한 리액터 패턴 기준)
    * 요청A가 응답되지 않았는데, 요청B가 들어온다면, 요청A가 끝날 때까지 기다려야한다.
* 1-1번에서 언급했지만, 리액터 패턴에서 제일 중요한 것은, 싱글스레드가 block 되지 않도록 비즈니스 로직을 작성하는 것이다.
    * 커널레벨의 비동기 시스템콜 활용, 별도의 쓰레드풀 활용 등
    * 잘 이해하고, 작성해야한다는 어려움때문에, 보통은 리액터 패턴을 잘 구현한 프레임워크나 라이브러리를 사용한다. (vert.x , netty 등)
* 리액터 패턴의 코드 예제를 찾아보면, 위 문제들 때문에 핸들러에 쓰레드를 새로 물리거나, 디스패처를 물리거나 하는 예제들이 있다.
    * (이런 예제때문에.. 오히려 햇갈렸음.. 싱글스레드 기반이라면서 갑자기 왠 멀티쓰레드가....)
* 개인적으로는, 쓰레드풀을 핸들러나 디스패처에 일괄적으로 물리는 것보단, IO 작업에만 적용되거나, 개발자가 주의해서 block 안되게 하는 방법밖엔 없을 것 같다..
    * ex) 이벤트루프가 IO작업을 만나면 큐에 등록하고, 별도 워커쓰레드풀에서 처리해서 비동기로 넘겨준다던지..
    * 멀티코어 활용의 문제라면, 코드레벨의 쓰레드풀보단, 리액터 패턴의 어플리케이션 자체를 여러개 뛰우는 방식이 낫다고 생각한다.
    * 실제로, nodejs 를 여러개 뛰우는 것이 예제가 될 수 있겠다. nginx도 worker process 는 여러개 뛰운다.

## 4. project reactor 와의 관계
* 리액티브 스트림의 리액터 프레임워크(project reactor)와는 이름만 같을 뿐이긴 하다.
* project reactor 개발자들이, 리액터 패턴의 영향과 모범사례를 참고하긴 했다고 한다.
* reactor 의 초기버전인 1.x 를 보면, 리액터 패턴과 정말 유사하다.
    * https://github.com/reactor/reactor/tree/v1.1.7.RELEASE
    * https://spring.io/blog/2013/07/18/reactor-1-0-0-m1-a-foundation-for-asynchronous-fast-data-applications-on-the-jvm/
* 예제 코드
    ```
    // 리액터 생성 : 쓰레드풀을 사용하는 dispatcher
    Reactor reactor = Reactors.reactor().env(new Environment()).dispatcher(Environment.THREAD_POOL).get();

    // 이벤트 핸들러 등록
    reactor.on(Selectors.$("channel"), event -> System.out.println(event.getData()));

    // 테스트를 위해 임의 이벤트 발생
    reactor.notify("channel", Event.wrap("test"));
    ```