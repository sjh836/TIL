# Reactive와 Spring 4

Spring 프레임워크도 5.x 부터는 리액티브를 지원하기 시작했지만, 이 글에서는 그 전까지의 상황을 살펴보았다. 리액티브가 왜 필요하고, 스프링5가 나오기전까진 반응성을 어떻게 구현하였는지를 살펴본다.

## 1. 리액티브(반응형)이 왜 필요한가?
### 1-1. C10K 문제
* http://www.kegel.com/c10k.html
* 다수의 클라이언트를 동시에 처리할 수 있는가?
* 서버에서 10K(1만)개의 클라이언트에 대한 연결, 응답, 부하에 관련된 이야기
* 스레드는 서버의 cpu core 수에 종속적, 멀티스레드로 10k 요청에 대해 처리할 수 있는 한계가 있다.
    * 요청 스레드들이 io wait 에 전부 빠져있다면?
* 대안) event-driven 방식
    * 이벤트(=요청)와 io처리(I/O 멀티플렉싱)를 분리
    * 이벤트루프는 싱글스레드 모델
    * io처리는 커널 비동기 라이브러리(epoll, kqueue 등)를 사용하여 callback 받음

### 1-2. 리액티브 선언문
* https://www.reactivemanifesto.org/ko
* ![리액티브선언문](https://blog.kakaocdn.net/dn/bC7wMT/btqR6wyZpCT/kzFF6ILelo0y3h9TWTopn1/img.png)
* 탄력성(Elastic) : 더 많은 사용자가 사용할 때, 시스템 처리량이 자동으로 증가해야하고, 사용자가 적어지면 자동으로 감소해야 한다.
* 복원력(Resilient) : 특정 시스템 실패에도 반응성을 유지할 수 있는 능력
    * ex. 결제모듈이 실패하더라도, 주문을 일단 접수하고 자동으로 재시도한다던지
* 메세지 기반 : 분산 시스템에서 컴포넌트를 연결하는 방법, 낮은 결합도, 시스템 격리 및 확장성 유지를 동시에 달성
    * 메세지 브로커(ex. kafka)를 사용하면 메세지 대기열을 모니터링하고 부하관리나, 탄력성을 제어할 수도 있음
* 단순히, 비동기 논블로킹이 리액티브의 본질인 줄 알았지만, 이걸 보면서, 리액티브가 꽤 포괄적이라는 것을 느꼈다. (아래에서도 스프링 클라우드 역시 리액티브의 범주에 넣고 있으니..)

## 2. 리액티브 스프링이 왜 필요했는가?
### 2-1. jvm 에서 인기있는 리액티브 프레임워크
* akka : scala에서 탄생, java도 지원
* vert.x : jvm에서 nodejs를 대체하기 위해 설계됨
* (spring 은 가장 인기가 많지만 지원이 미흡했다. 4까진..)

### 2-2. 스프링 클라우드의 등장
* 분산 시스템 구축을 단순화하는 기반 프레임워크, 리액티브 시스템을 구축하는 데 더 적합하다
* MSA
* 디스커버리 패턴, 레지스트리 패턴, 게이트웨이
* 8장에서 자세히 다룸

### 2-3. 서비스 레벨에서의 반응성
* 실제 구현레벨에서 코드를 보며, 리액티브 스프링이 왜 필요했는지 알아보자

#### 2-3-1. 명령형 프로그래밍(imperative programming)
* 보편적인 코딩 기법
* OrdersService.process 를 호출하면, ShoppingCardService.calculate 를 처리하는 동안 스레드가 차단된다
    ```
    public class OrdersService {
        private final ShoppingCardService scService;

        void process() {
            scService.calculate(input);
            ...
        }
    }

    public class ShoppingCardServiceImpl implements ShoppingCardService {
        @Override
        public Output calculate(Input value) {
            ...
        }
    }
    ```

#### 2-3-2. 콜백 기업
* 장점 : 응답을 기다리지 않고, 즉시 다른 작업을 진행할 수 있어짐
* 단점 : 콜백지옥

```
public class OrdersService {
    private final ShoppingCardService scService;

    void process() {
        shoppingCardService.calculate(input, output -> {
            ...
        });
    }
}

public interface ShoppingCardService {
    void calculate(Input value, Consumer<Output> c);
}

public class SyncShoppingCardService implements ShoppingCardService {
    @Override
    public void calculate(Input value, Consumer<Output> c) {
        // 동기식 구현
        c.accept(new Output());
    }
}

public class AsyncShoppingCardService implements ShoppingCardService {
    @Override
    public void calculate(Input value, Consumer<Output> c) {
        // 비동기식 구현. blocking operation 이 있다면 유리
        new Thread(() -> {
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

            c.accept(new Output());
        }).start();
    }
}
```

#### 2-3-3. java.util.concurrent.Future
* 콜백지옥 회피
* 여러 구현, 복잡성을 뒤로 숨겨줌

```
public class OrdersService {
    private final ShoppingCardService shoppingCardService;

    void process() {
        Future<Output> result = shoppingCardService.calculate(input);

        ...

        try {
            result.get();
        } catch (InterruptedException | ExecutionException e) {
            e.printStackTrace();
        }
    }
}

public interface ShoppingCardService {
    Future<Output> calculate(Input value);
}

public class FutureShoppingCardService implements ShoppingCardService {
    @Override
    public Future<Output> calculate(Input value) {
        FutureTask<Output> future = new FutureTask<>(() -> {
            Thread.sleep(1000);
            return new Output();
        });

        new Thread(future).start();

        return future;
    }
}
```

#### 2-3-4. CompletableFuture
* java 8 에서 등장
* CompletionStage 를 통해 js의 promise 와 유사한 API 제공
    * 함수형 스타일
    * 가독성이 좀더 좋음 `.thenApply(() -> {}).thenCombine(() -> {}).thenAccept(() -> {});`
* CompletionStage 는 이전 stage 와 동일한 thread 를 사용함
* CompletableFuture.supplyAsync 는 기본적으로 ForkJoinPool을 활용한다. Executor 지정도 가능
* Spring4 에서는 하위 버전 java 지원을 목표로 했기 때문에, CompletableFuture 대신 ListenableFuture 를 자체적으로 만들어서 제공

```
public class OrdersService {
    private final ShoppingCardService shoppingCardService;

    void process() {
        Input input = new Input();

        shoppingCardService.calculate(input)
                           .thenAccept(v -> System.out.println(shoppingCardService.getClass().getSimpleName() + " execution completed"));

        ...
    }
}

public interface ShoppingCardService {
    CompletionStage<Output> calculate(Input value);
}

public class CompletionStageShoppingCardService implements ShoppingCardService {
    @Override
    public CompletionStage<Output> calculate(Input value) {

        return CompletableFuture.supplyAsync(() -> {
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

            return new Output();
        });
    }
}
```

#### 2-3-5. 한계
* 결국 blocking operation 부분을 별도의 스레드로 래핑하고 있다
    * 이 때, spring4 에서는 서블릿API 를 활용하여 스레드 할당
    * cf) 보통은 java.util.concurrent.ExecutorService 를 활용한 스레드풀을 사용하긴 함
* 멀티스레딩 비용(공유메모리에 엑세스, 동기화, 오류처리 등), 컨텍스트 스위칭 비용 등을 보았을 때, 비효율적이다.
* 비동기 처리란 일반적인 요청-응답 패턴뿐 아니라, 데이터의 연속적인 스트림 처리에도 해당된다. (배압으로 인한 변환된 흐름 등)
* 이런 리액티브 프로그래밍 기술은 스프링 프레임워크에 잘 통합되어 있지 않았다. 따라서, 새로운 모듈과 함께 스프링5 가 나오게 된 것!