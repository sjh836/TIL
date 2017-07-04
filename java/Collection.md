# Collections 프레임워크
참조문서 : https://docs.oracle.com/javase/8/docs/api/java/util/Collection.html

## 1. 컬렉션이란?
java.util 패키지에서 가장 핵심, 여러 개의 객체를 보관할 수 있게 만들어진 클래스들(자료구조), 자바의 자료구조는 내부적으로 모두 배열로 구현되어 있다.

### 주요 용어
* 리스트(List) : 순서를 가지고 있으며, 중복을 허용하는 보관 구조(인덱스 번호가 핵심)
* 세트(Set) : 순서를 가지지 않고, 데이터의 중복을 허용하지 않는 구조
* 맵(Map) : 키-값을 가지며, 키를 가지고 원하는 데이터를 검색하는 구조
* Element : 자료구조 안에 들어가는 데이터를 의미

### 시간복잡도
사진출처: http://bigocheatsheet.com/

![시간복잡도](http://img1.daumcdn.net/thumb/R1920x0/?fname=http%3A%2F%2Fcfile6.uf.tistory.com%2Fimage%2F2376363A595B25033330B7)

## 2. *List 계열
리스트는 내부적으로 무한대의 배열을 가진다. ArrayList를 까보면 `private transient Object[] elementData;` 가 있다.

배열은 크기가 고정되지만, 리스트는 크기가 고정되지 않는데, 이는 변화가 생겨도 필요한 크기만큼 새로운 배열을 만들어서 기존 자료를 옮기기 때문이다.

이를 자세히 보기 위해 add()의 코드를 까보면

	ensureCapacity(size+1); //원본 배열보다 크기가 큰 배열을 생성
	System.arraycopy(...); //기존 배열을 새 배열에 복사
	elementData[index]=element;
	size++;

### 특징
* 인덱스가 존재 : 배열과 유사
* 데이터가 중복 가능 : 인덱스가 다르면 같은 데이터라도 별도 관리

### List 인터페이스의 대표적인 구현 클래스
1. ArrayList : 단방향 포인터 구조 → 순차적 접근에 최적
	* 생성 : add(요소), add(인덱스, 요소)=밀리게함
	* 읽기 : get(인덱스)
	* 수정 : set(인덱스, 요소)=대체
	* 삭제 : remove(인덱스)=읽고 삭제
2. LinkedList : 양방향 포인터 구조 → 삽입, 삭제에 최적
3. Vector : ArrayList와 동작 방식 유사, 멀티스레드 환경에 안전

## 3. *Map 계열
key로 value를 바로 찾아가는 개념이다.

### 핵심
* 키의 유일성을 보장해줘야 한다. 반드시 키는 하나만 있어야 함. 같은 키로 put되면 마지막 것만 저장된다.
	* 키 생성 - hashCode() : 객체를 구별하기 위해 객체 내용을 고유한 정수값으로 출력시켜주는 메소드(객체 메모리 주소를 바탕으로). 사용자 정의 클래스의 객체를 생성하여 hashcode를 쓰려면 오버라이딩해야 한다. ex: `toString().hashCode();`
* 순서가 없다

### Map 인터페이스의 대표적인 구현 클래스
1. HashMap : 빠름, 키값에 null 허용
2. Hasptable : 비교적 느림, 멀티스레드 환경에 안전(메소드 단위 synchronized 선언), 키값에 null 불가
3. ConcurrentHashMap : 키값에 null 불가, 멀티스레드 환경에 안전(lock striping 기법=여러 개의 세그먼트를 두고 각 세그먼트마다 별도의 락)
	* putIfAbsent() : 키가 존재하면 기존 값 반환, 없으면 저장 후 반환

### 주요 메소드
* 생성 : put(key, value)
* 읽기 : get(key)
* 삭제 : remove(key)
* 갖고있는지 체크 : containsKey(key), containsValue(value)

## 4. *Set 계열
집합을 의미한다.

### 특징
* 순서없음, 순서대로 삽입되지도 않는다.
* 데이터 중복 불가 : 인덱스 번호가 없는 경우 중복을 허용하지 않는다.

### Set 인터페이스의 대표적인 구현 클래스
1. HashSet :
add(object)를 하기 전에 중복방지를 위하여 ==(메모리주소), hashCode(), equals()를 수행하여 검사한다.
remove(object)를 할 때도 메모리주소가 같거나 hashCode(), equals()가 같아야 한다.

## 5. Tree* 계열
데이터가 들어가는 순간에 정렬되므로 느리다(하지만 이후에 정렬필요X) 반드시 순서를 결정할 수 있어야만 하고, 이 순서를 결정하기 위해서 Comparable 인터페이스를 사용한다. Tree구조는 균형트리나 B-tree처럼 되면 검색속도가 매우 빠르다.

사용자 정의 클래스는 Comparable 인터페이스를 구현해줘야 한다. ex: `정렬기준멤버변수.compareTo();`

### 대표적인 클래스
1. TreeSet
2. TreeMap

## 6. Stack
LIFO(Last Input First Output)구조다. 내부적으로 Vector의 상속을 받는다.

### 주요 메소드
* 생성 : push(element)
* 읽기 : peek()=맨마지막 데이터
* 삭제 : pop()=맨마지막에 들어간 데이터를 읽고 삭제

## 7. Queue
FIFO(First Input First Output)구조다.

### 대표적인 클래스
1. LinkedList
2. PriorityQueue : 들어온 순서와 상관없이 우선순위가 높은 element가 output된다. 이를 위해 Comparable의 compareTo()를 구현해 주어야 한다.

### 주요 메소드
* 생성 : offer(element)
* 읽기 : peek()=맨 아래 있는 데이터
* 삭제 : poll()=데이터를 읽고 삭제한다.

## 8. 데이터 나열을 위한 iterator()
대부분 자료구조 클래스가 Iterator 인터페이스를 얻을 수 있는 iterator()를 지원한다. Map에서 iterator()를 쓰려면, 키(Set구조)를 이용하면 된다. ex: `map.keySet().iterator()`

### Iterator 인터페이스의 메소드
* hasNext() : 뒤에 남은 데이터가 있으면 true, 없으면 false, 주로 while()에 사용
* next() : 다음 데이터를 반환(시작 시 처음-1 을 가르키고 있다)
* remove() : 현재 조회하는 객체 레퍼런스 삭제

## 9. 자료구조의 정렬
Collections 클래스에서 static으로 정의된 메소드를 이용하여 손쉽게 정렬할 수 있다.

* Collections.sort(param) : 순차정렬
* Collections.reverse(param) : 역순차정렬

이 때 정렬기준은 Comparable 인터페이스의 compareTo()를 이용한다. sort()는 element 갯수-1의 팩토리얼만큼 호출된다.