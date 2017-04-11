# 어노테이션(Annotation)

슬라이드쉐어: http://www.slideshare.net/JungHoonLee41/annotation-73584342

## 1. 어노테이션이란?
* @를 이용한 주석, 자바코드에 주석을 달아 특별한 의미를 부여한 것
* 컴파일러가 특정 오류를 억제하도록 지시하는 것과 같이 프로그램 코드의 일부가 아닌 프로그램에 관한 데이터를 제공, 코드에 정보를 추가하는 정형화된 방법.

## 2. 어노테이션의 용도
1. @Override 어노테이션처럼 컴파일러를 위한 정보를 제공하기 위한 용도

2. 스프링 프레임워크의 @Controller 어노테이션처럼 런타임에 리플렉션을 이용해서 특수 기능을 추가하기 위한 용도

3. 컴파일 과정에 어노테이션 정보로부터 코드를 생성하기 위한 용도

**※ 자바 리플렉션:** 다른언어에는 존재하지 않는 특별한 기능, 컴파일 시간이 아닌 실행시간에 동적으로 특정 클래스의 정보를 객체를 통해 분석 및 추출해내는 프로그래밍 기법

## 3. 기본 어노테이션(JDK 제공)
* @Override: 해당 메소드가 부모 클래스에 있는 메소드를 재정의했다는 것을 명시적으로 선언
* @Deprecated: 더이상 사용되지 않는 클래스나 메소드 앞에 추가
* @SuppressWarnings: 프로그램에는 문제가 없는데 간혹 컴파일러가 경고를 뿜을 때가 있는데, 이를 무시하라고 프로그래머에게 알려줌

## 4. 메타 어노테이션
어노테이션을 선언할때 사용한다. 프로그래머가 어노테이션을 만들때 사용하는 것이다.

1. @Target
2. @Retention
3. @Documented: 해당 어노테이션 정보가 JavaDocs(API) 문서에 포함
4. @Inherited: 모든 자식 클래스가 부모 클래스의 어노테이션을 사용할 수 있다는 것을 선언
5. @interface: 어노테이션 선언할 때 사용

### 4-1. @Target
#### 어노테이션이 적용할 위치를 결정
`import java.lang.annotation.Target;`

#### 적용 가능 대상(디폴트는 전 범위)
`import java.lang.annotation.ElementType;`

* ElmentType.TYPE : 클래스, 인터페이스, 열거 타입
* ElmentType.ANNOTATION_TYPE : 어노테이션 타입
* ElmentType.FIELD : 필드
* ElmentType.CONSTRUCTOR : 생성자
* ElmentType.METHOD : 메서드
* ElmentType.PARAMETER : 파라미터
* ElmentType.LOCAL_VARIABLE : 로컬 변수
* ElmentType.PACKAGE : 패키지

### 4-2. @Retention
#### 어떤 시점까지 어노테이션이 영향을 미치는지 결정
`import java.lang.annotation.Retention;`

#### 적용 가능 시점
`import java.lang.annotation.RetentionPolicy;`

* RetentionPolicy.SOURCE: 어노테이션 정보가 컴파일 시 사라짐
* RetentionPolicy.CLASS: 클래스 파일에 있는 어노테이션정보가 컴파일러에 의해 참조 가능, 가상머신에서는 사라짐(리플렉션 불가, 디폴트)
* RetentionPolicy.RUNTIME: 실행 시 어노테이션 정보가 가상머신에 의해서 참조 가능

## 5. 사용자 어노테이션 선언법
	public @interface 어노테이션이름
	{
	   // 엘리먼트들
	
	   /*
	   String value();
	   String[] author();
	   int revision() default 0; //엘리먼트에 디폴트값 주는법
	   */
	}
어노테이션은 암묵적으로 어노테이션 인터페이스를 상속받으므로 상속받을수 없다.

## 6. 어노테이션 사용
	@어노테이션(엘리먼트=값, 엘리2=＂값＂)
	public class 클래스
	{}
	@어노테이션2(값) //엘리먼트가 1개일 경우
	Public class 클래스2
	{}

메소드, 필드 등 어디에서나 사용할 수 있다.
