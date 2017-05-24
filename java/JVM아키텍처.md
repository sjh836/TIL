# JVM 아키텍처

## 1. JVM(Java Virtual Machine)이란?

![포함관계 사진](http://img1.daumcdn.net/thumb/R1920x0/?fname=http%3A%2F%2Fcfile23.uf.tistory.com%2Fimage%2F234ED84A592574FE276FC5)

jvm은 java를 운영체제로부터 독립적으로 실행시켜주는 핵심이자 원리이다. 예를들어 C언어의 경우 소켓프로그램을 윈도우에서 짜면, 리눅스에서는 같은 코드로 돌아가지 않는다. 컴파일조차 실패할거다. 헤더파일도 다르고 함수명도 전부 다르니ㅋㅋ 그렇지만 java는 소켓프로그램짜두면 윈도우든 리눅스든 맥이던 플랫폼에 상관없이 돌아간다. 

## 2. JVM 구성요소

![jrejvm사진](http://img1.daumcdn.net/thumb/R1920x0/?fname=http%3A%2F%2Fcfile5.uf.tistory.com%2Fimage%2F236D3B4A592574FF070285)

JVM은 위 그림처럼 4가지 구성 요소를 가지고 있다.

## 3. Class Loader
.java 파일을 javac로 컴파일하면 바이트코드(.class)가 나온다. 이 파일을 컴파일 타임이 아닌 런타임에(동적으로) 메모리로 올려서 실행하는 부분이 Class Loader이다.

### ※ Class Loader에 의해 바이트코드가 메모리에 로딩되는 과정
컴파일된 class 파일이 실행되는 순서는 5단계로 나누어진다.

![class loader 사진](http://img1.daumcdn.net/thumb/R1920x0/?fname=http%3A%2F%2Fcfile23.uf.tistory.com%2Fimage%2F213EB14A592575000A125E)

1. Load : 메모리로 로딩
2. Verify : 안전성 검증, 이때 파일들의 보안검사를 실행하여 악성코드를 차단한다. 메모리 연결이 가능한지도 검토한다.
3. Prepare : 실행 준비로 본격적인 메모리를 할당한다.
4. Resolve : 세밀한 메모리의 처리를 담당한다.
5. Initialize : class 파일에서 지정된 클래스 변수를 할당하는 작업을 진행

## 4. Execution Engine
Class Loader에 의해 메모리에 로딩된 후에는 Execution Engine이 주도한다. class파일을 해석을 주로 한다.

### ※ 실행 방식의 2가지
1. 인터프리터
2. JIT(Just In Time)

## 5. Runtime Data Area
Execution Engine에게 해석된 프로그램은 Runtime Data Area에 배치되어 돌아간다. JVM메모리영역이다. JVM은 필요에 따라 GC나 쓰레드 동기화등 관리를 해준다.

### ※ Runtime Data Area의 구조

![Runtime Data Area 사진](http://img1.daumcdn.net/thumb/R1920x0/?fname=http%3A%2F%2Fcfile26.uf.tistory.com%2Fimage%2F2455614A59257C5B0255F6)

1. **Method Area :** 클래스영역이라고도 한다. 여기에는 인터페이스, 클래스, 메소드, 필드 등의 모든 정보가 들어가 있다. 이름, 반환형, 인자 수, 인자타입 등등 시그니처들도 전부.. 접근제어자도 포함된다. 이 영역은 모든 쓰레드들이 공유한다. new를 통해 동적으로 생성할 때 저장되므로 GC의 대상이다.
2. **Heap :** 생성된 인스턴스가 살고 있는 곳이다. GC의 대표적인 대상이며, 왜 HEAP에 저장하는지는 힙정렬과 관련이 있다. 사용빈도가 높은 인스턴스를 맨 위(루트노드)로 올려둬서 히트율을 올리는 방식이다. 모든 쓰레드들이 공유한다.
3. **JVM Stacks :** 호출스택(call stack)이라고도 불린다. 메소드 실행에 필요한 공간을 제공한다. 메소드의 매개변수, 지역변수 등 임시 데이터를 저장한다. 기본형 타입은 스택에 직접 값을 쓰고, 레퍼런스형 타입은 스택에 힙영역의 주소를 쓴다. 메소드가 끝나면 메모리공간은 반환된다. 쓰레드마다 1개씩 갖는다.
4. **PC Registers :** java는 c언어와 다르게 스택베이스 언어이다. C언어는 `int x=123;`에서 123을 레지스터에 MOV하고, x라는 이름의 메모리공간에 MOV하는 방식으로 동작한다. 그러나 java는 플랫폼에 독립적이기 위해 이러한 방식(CPU인스트럭션이 다 다르니까)을 하지않고 PC Registers이라는 일종의 스택을 이용해 동작한다. 이를 쉽게 이해하기 위해서는 역시 코드를 보면 빠를 것같다. javap를 통해 소스를 까보면

		// Test.java
		int x=123;
		System.out.println(x);

		// javap -c Test.class
		0: bipush        123
        2: istore_1
        3: getstatic     #2   // Field java/lang/System.out:Ljava/io/PrintStream;
        6: iload_1
        7: invokevirtual #3   // Method java/io/PrintStream.println:(I)V
        10: return

	123을 스택에 push하고, 그것을 첫번째 지역변수에 store(값 복사)하는 방식을 취하고 있다. 각 쓰레드마다 PC Registers를 가진다.

5. Native Method Stacks : 자바 외 언어로 작성된 코드를 위한 스택이다. JNI(Java Native Interface)를 통해 호출한다. 

## 6. Garbage Collector
메모리 관리를 GC가 해준다. 즉, 비즈니스 로직에만 집중할 수 있게 해준다. 예를 들어 C언어는 개발자가 malloc하면 꼭 free를 해주어야 했다. 그러나 GC는 이것을 똑똑하게 자동으로 처리해준다. GC에 대해서는 따로 정리할 예정이다...(언젠가ㅋㅋ)