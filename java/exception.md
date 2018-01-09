# 예외처리

참조문서 : https://docs.oracle.com/javase/8/docs/api/java/lang/Throwable.html

## 1. 예외처리란?
Exception Handling 이라고 하며, 잘못된 하나로 인해 전체 시스템이 무너지는 결과를 방지하기 위한 기술적인 처리이다. java에서는 예외와 에러도 객체로 처리한다.

### 예외가 주로 발생하는 원인
* 사용자의 잘못된 데이터 입력
* 잘못된 연산
* 개발자가 로직을 잘못 작성
* 하드웨어, 네트워크 오작동
* 시스템 과부하

## 2. Throwable 클래스
![Throwable 계층구조](http://img1.daumcdn.net/thumb/R1920x0/?fname=http%3A%2F%2Fcfile23.uf.tistory.com%2Fimage%2F99E8073E5A41F0C80C92B5)

Throwable 클래스는 예외처리를 할 수 있는 최상위 클래스이다. Exception과 Error는 Throwable의 상속을 받는다. 유명한 것들만 정리해보았으며, 실제로 더 많다. io.IOException도 lang.Exception의 상속을 받는다.

### 에러(Error)와 예외(Exception)
* 에러는 시스템 레벨에서 발생하여, 개발자가 어떻게 조치할 수 없는 수준을 의미. ex. JVM OOM
* 예외는 개발자가 구현한 로직에서 발생하며, 개발자가 다른 방식으로 처리가능한 것들, JVM은 정상 동작. ex. 인덱스범위 검사

## 3. 예외 (Exception)
![예외](http://img1.daumcdn.net/thumb/R1920x0/?fname=http%3A%2F%2Fcfile21.uf.tistory.com%2Fimage%2F99C85A375A41F0FF31FD76)

### 예외의 2가지 종류
* **Checked Exception** : 예외처리가 필수이며, 처리하지 않으면 컴파일X. JVM 외부와 통신(네트워크, 파일시스템 등)할 때 주로 쓴다.
    * RuntimeException 이외에 있는 모든 예외
    * IOException, SQLException 등
* **Unchecked Exception** : 컴파일 때 체크되지 않고, runtime에 발생하는 Exception을 말한다.
    * RuntimeException 하위의 모든 예외
    * NullPointerException, IndexOutOfBoundException 등

### 대표적인 클래스들
* NullPointerException : Null 레퍼런스를 참조할때 발생, 뭔가 동작시킬 때 발생한다.
* (XXX)IndexOutOfBoundsException : 배열과 유사한 자료구조(문자열, 배열, 자료구조)에서 범위를 벗어난 인덱스 번호 사용으로 발생
* (XXX)FormatException : 문자열, 숫자, 날짜 변환 시 잘못된 데이터(ex. "123A" -> 123 으로 변환 시)로 발생, 보통 사용자의 입력, 외부 데이터 로딩, 결과 데이터의 변환 처리에서 자주 발생한다.
* ArthmeticException : 정수를 0으로 나눌때 발생
* ClassCastException : 변환할 수 없는 타입으로 객체를 변환할 때 발생
* IllegalArgumentException : 잘못된 인자 전달 시 발생
* IOException : 입출력 동작 실패 또는 인터럽트 시 발생
* IllegalStateException : 객체의 상태가 매소드 호출에는 부적절한 경우 
* ConcurrentModificationException : 금지된 곳에서 객체를 동시에 수정하는것이 감지될 경우 발생
* UnsupportedOperationException : 객체가 메소드를 지원하지 않는 경우 발생

### 주요 메소드들
* printStackTrace() : 발생한 Exception의 출처를 메모리상에서 추적하면서 결과를 알려준다. 발생한 위치를 정확히 출력해줘서 제일 많이 쓴다. void를 리턴한다.
* getMessage() : 한줄로 요약된 메세지를 String으로 반환해준다.
* getStackTrace() : jdk1.4 부터 지원, printStackTrace()를 보완, StackTraceElement[]이라는 문자열 배열로 변경해서 출력하고 저장한다.

## 4. 에러 (Error)
![에러](http://img1.daumcdn.net/thumb/R1920x0/?fname=http%3A%2F%2Fcfile1.uf.tistory.com%2Fimage%2F99A0BC395A41F10B2ACD92)

개발자는 error 가 발생했다는 사실을 알 수는 있다. 유명한 에러로는 다음과 같다.

* OutOfMemoryError : JVM에 설정된 메모리의 한계를 벗어난 상황일 때 발생한다. 힙사이즈가 부족하거나, 너무 많은 class를 로드할때, 가용가능한 swap이 없을때, 큰 메모리의 native메소드가 호출될 때 등이 있다. 이를 해결하기위해 dump 파일분석, jvm 옵션 수정 등이 있다.

## 5. 예외처리를 해보자
java에서 모든 예외가 발생하면 (XXX)Exception 객체를 생성한다. 예외를 처리하는 방법에는 크게 2가지가 있다.

* 직접 try ~ catch 를 이용해서 예외에 대한 최종적인 책임을 지고 처리
* throws Exception 을 이용해서 발생한 예외의 책임을 호출하는 쪽이 책임지도록 함, 주로 호출하는 쪽에 예외를 보고할 때 사용(다른 메소드의 일부분으로 동작하는 경우엔 던지는 거 추천)

### 5-1. 예외 잡기 (try ~ catch 구문)
로직 중에 예외가 발생할지도 모르는 부분에 try ~ catch 구문으로 보험처리한다.

* try 에는 위험한 로직이 들어가고, catch 에는 예외 발생 시 수행할 로직이 들어간다.
* try 중이라도 예외가 발생한 다음의 코드들은 실행되지 않으며 catch 구문으로 넘어간다.
* catch 구문은 여러개 쓸 수 있다. else if 처럼..
* finally 는 마지막에 실행하고 싶은 로직이 들어간다. `.closd()`가 대표적

### 5-2. 예외 던지기 (throws 구문)
예외 처리를 현재 메소드가 직접 처리하지 않고 호출한 곳에다가 예외의 발생 여부를 통보한다. 호출한 메소드는 이걸 또 던질건지 직접 처리할 건지 정해야한다.(return보다 강력)

    public class ThrowsEx {
        public void call_A() throws Exception {
            call_B();
        }

        private void call_B() throws Exception {
            call_C();
        }

        private void call_C() throws Exception {
            System.out.println(1 / 0);
        }

        public static void main(String[] args) throws Exception {
            ThrowsEx test = new ThrowsEx();
            test.call_A();
        }
    }

실행 결과는 아래와 같다.

    Exception in thread "main" java.lang.ArithmeticException: / by zero
        at exception.ThrowsEx.call_C(ThrowsEx.java:13)
        at exception.ThrowsEx.call_B(ThrowsEx.java:9)
        at exception.ThrowsEx.call_A(ThrowsEx.java:5)
        at exception.ThrowsEx.main(ThrowsEx.java:18)

## 6. 사용자 정의 예외
개발자가 새로운 예외 클래스를 만들 수 있다. 보통은 Exception 클래스를 상속받아 만든다.