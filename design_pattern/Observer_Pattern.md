# Observer Pattern

## 1. 관찰자(Observer) 패턴이란?
* 리액티브 프로그래밍의 기초이며, 대부분의 MVC, UI라이브러리들이 내부적으로 사용하는 디자인 패턴이다.
    * Model = Subject(=Observable) , View = Observer 라고 보면, 모델 데이터의 변화에 따라 UI가 바뀌어보이는 것들
    * Swing 에서 각종 버튼들과 리스너들
* 관찰자 패턴에는 이벤트를 발생시키는 역할(주체)와 이벤트를 수신하는 역할(관찰자)가 있다.
    * 주체(subject)는 관찰자(observer)에게 상태변경을 알려준다.
    * 이벤트 기반 처리
    * 일대다 의존성 등록 가능
    * 단방향 통신

## 2. UML과 인터페이스
![UML구조도](https://blog.kakaocdn.net/dn/CsmWQ/btqRVpnvzBf/9qwkD5KG87Aja7tsOO21HK/img.png)

```
public interface Subject<T> {
   void registerObserver(Observer<T> observer); // 동시성 문제로 동기화 필요
   void unregisterObserver(Observer<T> observer); // 동시성 문제로 동기화 필요
   void notifyObservers(T event);
}

public interface Observer<T> {
   void observe(T event);
}
```

## 3. JDK의 Observer
* https://docs.oracle.com/javase/8/docs/api/index.html?java/util/Observable.html
* https://docs.oracle.com/javase/8/docs/api/index.html?java/util/Observer.html
* jdk1부터 지원함. jdk9부터 deprecated 되었다
* deprecated 사유와 한계
    * Observable 가 인터페이스가 아니라 클래스여서, 상속에 이슈가 있다.
        * 다중상속 문제, 메소드 재정의에서 synchronized를 하지 않아서 thread-safe를 해치는 문제
    * 옛날에 만들어진거라 제네릭 미사용 등으로 인하여 타입안정성이 낮음. Serializable도 안함
    * notify 할 때, 순서보장할 수 없음
    * 동시성 문제 해결, 순서보장 등을 위하여 util.concurrent 의 컬렉션을 직접 사용하는게 낫다
        * Observable은 Vector와 `synchronized void addObserver` 등으로 이미 직접 구현이 되어있기 때문
* java.beans.PropertyChangeListener 와 PropertyChangeEvent 를 사용하거나, 리액티브 스트림의 Flow API 를 대신 사용할 것을 권장

## 4. 왜 관찰자 패턴은 잘 사용하지 않을까?
* 주체와 관찰자 간 느슨한 결합이 되었다곤 하지만, 주체 입장에선 관찰자가 누구인지 다 알고있다.
* 이는 unregisterObserver를 제때 하지 못하면, 메모리 누수가 유발되는 것이고 (자원 관리)
* 멀티스레드에서 동시성 등 챙겨야할 부분들이 많음 (많은 걸 직접 구현해야함)

## 5. 관찰자(Observer) 패턴 vs 발행구독(Pub/Sub) 패턴
![옵저버와 발행구독](https://blog.kakaocdn.net/dn/dpQCZr/btqR1E5rUrf/kM6tK3dkoJuCrcp6gJ2Tr0/img.png)

* 관찰자와 발행구독은 비슷하면서도 약간 다른 부분이 있다.
* 발행구독 패턴은 Subject - Observer 사이에 간접적인 계층이 하나 더 있다.
* 구독자는 발행자가 누군지는 모르지만, 이벤트채널(=메세지 브로커, 이벤트버스)은 알고 있는 셈
* 토픽 기반 시스템

## 6. 리액티브 관점에서 옵저버 패턴의 한계
* Reactive Streams 포스팅 : https://sjh836.tistory.com/182