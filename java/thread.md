# Thread

참조문서 : https://docs.oracle.com/javase/8/docs/api/java/lang/Thread.html

## 1. 쓰레드란?
자바의 메인메소드 역시 하나의 실행흐름으로서, 메인 쓰레드에 해당한다. 이것은 main() 메소드에서 `Thread.currentThread().getName();` 를 통해 확인이 가능하다. 쓰레드를 이용하면 하나의 프로세스에서도 병렬적(동시적)으로 처리, 즉 여러 개의 처리 루틴을 가질 수 있다. 단순 반복의 코드를 실행할 때도 여러 개의 쓰레드를 만들어서 분리 시킨 뒤 결과 데이터를 받아 합치면 그만큼 시간을 절약할 수 있다. 특히 짧은 시간 내에 많은 사용자를 처리해야하는 대용량 시스템이나 CPU 활용을 극대로 해서 처리해야 하는 복잡한 연산 작업 등에 많이 쓴다.

## 2. 쓰레드 생성 방법
1. `extends Thread` 를 상속받아 run()을 오버라이딩
    * 객체 하나에 스레드 하나를 연결시킬 때 이렇게 쓴다.
2. `implements Runnable` 를 이용해서 run()을 오버라이딩
    * 보통 이걸 더 많이 쓴다. 다중상속 문제

## 3. java 쓰레드의 생명 주기
![Thread_life_cycle](http://img1.daumcdn.net/thumb/R1920x0/?fname=http%3A%2F%2Fcfile27.uf.tistory.com%2Fimage%2F99D9E94B5A3CAD770ADF38)

## 4. 쓰레드의 정보
쓰레드들이 내부에 갖고있는 정보들은 아래와 같다. 이 중 일부는 getter를 통해 보거나, setter를 통해 설정할 수 있다.

|                   | 타입   | 관련메소드 | 내용                                                                  |
|-------------------|--------|------------|-----------------------------------------------------------------------|
| name              | String | get, set   | 스레드 이름                                                           |
| id                | int    | get        | 스레드 고유 식별자 번호                                               |
| PC(Program Count) | int    |            | 현재 실행중인 스레드 코드의 주소                                      |
| State             | int    | get        | 6개 중 하나                                                           |
| Priority          | int    | get, set   | 스레드 스케줄링 때 사용되는 값으로 1~10 (10이 최상위, 기본 5)                 |
| ThreadGroup       | int    | get        | 여러 개의 쓰레드가 하나의 그룹을 형성할 수 있는데, 스레드가 속한 그룹 |
| Register Stack    |        |            | 스레드가 실행되는 동안 레지스터들의 값                                |

## 5. 쓰레드의 상태
생명주기에도 있듯이, 쓰레드는 어떤 상태 중 하나이다. 크게 6가지가 있다.

* NEW : 스레드가 생성된 상태, 아직 start() 되지 않음.
* RUNNABLE : start()가 호출되어 실행 대기, run() 하면 RUNNING(CPU 점유)이 된다. Runnable pool 에 모여있다.
* WAITING : 일시 정지, 다른 스레드의 통지를 기다림
* TIMED_WAITING : 일시 정지, 주어진 시간동안 기다림
* BLOCK : 일시 정지, 사용하려는 객체의 lock이 풀릴때 까지 대기 등등..
* TERMINATED : 실행마치고 종료, run()이 끝나면 이거되면서 소멸

## 6. 쓰레드 스케줄링
자바 쓰레드는 우선순위랑 라운드로빈으로 스케줄링한다.

* 우선순위 : Priority 값이 높은 스레드가 실행 상태를 더 많이 가짐, 기본 5이고 개발자가 `setPriority()` 를 통해 컨트롤 가능
* 라운드로빈 : 시간 할당량(Time Slice)를 정해서 하나의 스레드를 정해진 시간만큼 실행시키고 다시 다른 스레드를 실행함, JVM이 컨트롤

## 7. lang.Thread 주요 메소드
* start() : NEW 에서 RUNNABLE 로 보낸다.
* run() : RUNNING 이 된다면 여기에 있는 이 메소드에 오버라이딩된 로직을 수행한다.
* yield() : (우선권이 동일한) 스레드에게 실행할 기회를 양보함. 이걸 실행하면 RUNNING 상태에서 RUNNABLE 상태로 바뀐다.
* sleep() : 현재 실행중인 스레드를 주어진 시간동안 TIMED_WAITING 상태로 빠트린다. yield와 차이점은 주어진 시간이 있고 없는 것.
* join() : 다른 스레드와 협동 작업할 때 주로 쓴다. 호출되면 BLOCKED 상태가 되었다가, 기다리는 스레드의 작업이 끝나면 다시 RUNNABLE 로 간다. 이해를 돕기 위해 사진과 간단한 코드를..

    ![thread join](http://img1.daumcdn.net/thumb/R1920x0/?fname=http%3A%2F%2Fcfile6.uf.tistory.com%2Fimage%2F99096B4E5A3CAD8F3CA412)

        @Override
        public void run() {
            int sum = 0;
		    for (int i = 0; i <= 100; i++) {
			    sum += i;
            }
        }

        public static void main(String[] args) {
            Thread test = new Thread(new 클래스명());
            test.start()
            test.join()
            System.out.println("메인 쓰레드는 test쓰레드가 다 끝나고서야 이 문장이 보일겁니다.");
        }

### Object의 메소드이지만 관련한 거
join()과 비슷하게 다른 스레드와 협동을 위해 쓴다. 쉽게 그림으로 표현하자면..

![thread wait, notify](http://img1.daumcdn.net/thumb/R1920x0/?fname=http%3A%2F%2Fcfile9.uf.tistory.com%2Fimage%2F9913374E5A3CAD9C0A4F59)

join()과는 이런 차이가 있다 : https://stackoverflow.com/questions/43867510/multithreading-difference-between-join-and-wait-notify

* wait() : 스레드를 WAITING 상태가 되게한다. notify() 될때까지 waiting pool에서 기다린다.
* notify() : WAITING 상태인 스레드 1개를 RUNNABLE 하게 한다. 어떤 스레드가 깨어날 지는 알 수 없다.
* notifyAll() : 모든 WAITING 상태인 스레드를 RUNNABLE 하게 한다.

## 8. synchronized 키워드
뮤텍스와 세마포어 같은 개념이다. 해당 키워드를 메소드에 달아주거나, 코드의 특정 부분을 synchronized 블록으로 처리해주면 된다. synchronized 처리를 하게 되면 어떤 스레드가 어떤 코드를 차지하는 동안 다른 스레드의 접근을 막아낸다. 다른 스레드가 객체의 synchronized 메소드를 호출한 경우, 메소드를 실행하지 못하고 모니터링 락이 반환되어 객체의 잠금 상태가 풀릴 때까지 대기해야 한다. 이런 상태를 synchronized blocking이라고 한다. 1개의 공유데이터를 여러개의 스레드가 쓰려고할때 데이터오염을 막아준다.