# spring 의 설정

각 컨테이너들은 관리할 객체들을 위한 별도의 설정파일이 있다. 서블릿 컨테이너는 web.xml이고, 스프링 컨테이너는 applicationContext.xml 이다.

## 스프링 컨테이너
스프링 컨테이너는 크게 두 종류가 있다.

* BeanFactory : 컨테이너가 구동될 때 등록된 bean 객체들이 생성되는 게 아니라, 클라이언트의 요청(Lookup)에 의해서만 객체가 생성되는 Lazy Loading 방식을 사용한다. 
* ApplicationContext : BeanFactory 를 상속받았다. BeanFactory 기능 외에 트랜잭션 관리나 메세지 기반의 다국어 처리 등 다양한 기능을 지원한다. 또한 BeanFactory 와는 다르게 등록된 bean 객체들을 pre-loading한다. 웹 어플리케이션 개발도 지원한다. 대부분 이 컨테이너를 사용한다. 아래는 ApplicationContext 의 구현 클래스다.
    * GenericXmlApplicationContext : 파일 시스템이나 클래스 경로에 있는 XML 설정 파일을 로딩하여 구동하는 컨테이너.
    * XmlWebApplicationContext : 웹 기반의 스프링 어플리케이션을 개발할 때 사용하는 컨테이너.

## 스프링 XML 설정
스프링 컨테이너는 bean 저장소에 해당하는 xml 설정 파일을 참조하여 bean 의 생명 주기를 관리하고 여러가지 서비스를 제공한다.

### 엘리먼트
* `<beans>` : 루트 엘리먼트
* `<bean>` : 클래스를 등록할 때 사용
    * id : 생략가능, 클라이언트가 lookup할때 사용할 이름, 유일해야 함, 자바 식별자 규칙(숫자시작X,공백X,특수기호X,낙타표기)과 동일
    * name : id와 비슷하나 식별자규칙 안지켜도 됌, id를 주로 씀
    * class : 필수속성, 정확한 패키지와 클래스명까지 기입
    * init-method : bean 생성할 때 초기화 실행할 메소드 기입
    * destroy-method : bean 객체가 소멸되기 전에 호출될 메소드 기입
    * lazy-init : true로 하면 lazy loading으로 처리할 bean 지정, 자주 안사용되면서 메모리 많이 차지하는 bean에게 걸어줌
    * scope : 디폴트로 singleton, prototype 을 사용하면 bean이 lookup될 때 마다 새로운 객체를 생성
* `<description>`
* `<alias>`
* `<import>` : 스프링 설정파일을 분리할 수 있는데, 이걸 하나로 통합할 때 쓴다. 한파일에 작성하는 것과 같은 효과.

        <import resource="applicationContext-ehcache.xml"/>
        <import resource="lucyConfiguration.xml" />
        <import resource="applicationContext-datasource.xml"/>
        <import resource="applicationContext-dao-config.xml"/>
        <import resource="applicationContext-dao.xml"/>
        <import resource="applicationContext-service.xml"/>
        <import resource="applicationContext-common.xml"/>
        <import resource="applicationContext-environment.xml"/>

## 스프링 어노테이션 설정
### 컴포넌트 스캔
스프링 컨테이너는 클래스 패스에 있는 클래스들을 스캔하여 @Component가 설정된 클래스들을 자동으로 객체 생성한다.

	<!-- Scans the classpath of this application for @Components to deploy as beans	-->
	<context:component-scan base-package="com.naver">
		<context:include-filter type="annotation" expression="org.springframework.stereotype.Component"/>
		<context:include-filter type="annotation" expression="org.springframework.stereotype.Repository"/>
		<context:include-filter type="annotation" expression="org.springframework.stereotype.Service"/>
		<!-- <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/> -->
	</context:component-scan>

* @Component("id") : id를 넣어줘야 lookup, 미지정 시 클래스의 이름을 그대로 쓰고 첫문자만 소문자로

### 컴포넌트를 상속받는 어노테이션
@Component 하나만 쓰면 무슨 클래스인지 구분하기 힘들어서, 이런식으로 나눈다.

|  어노테이션 |      위치      |           의미           |
|:-----------:|:--------------:|:------------------------:|
|   @Service  | XXX+Service    | 비즈니스 로직을 처리     |
| @Repository | XXX+DAO        | 데이터베이스 연동을 처리 |
| @Controller | XXX+Controller | 사용자 요청을 제어       |

### 의존성 주입
| 어노테이션 |                               설명                              |
|:----------:|:---------------------------------------------------------------:|
| @Autowired | 주로 변수 위에 설정하여 해당 타입의 객체를 찾아서 자동으로 할당 |
| @Qualifier | 특정 객체의 이름을 이용하여 의존성 주입                         |
|   @Inject  | @Autowired와 동일                                               |
|  @Resource | @Autowired와 @Qualifier 결합                                    |

* @Autowired : 메소드도 가능하다. 스프링컨테이너는 타입을 체크하고 그 객체가 메모리에 존재하는지 확인해서 변수에 주입해준다. 만약 메모리에 없다면 NoSuchBeanDefinitionException 을 발생시킨다.
* @Qualifier : 같은 타입이 2개 이상 있을 때, NoUniqueBeanDefinitionException이 발생하는데, @Autowired와 함께 @Qualifier("id")를 붙이면 딱 지정해서 주입가능하다.
* @Resource : @Autowired가 변수의 타입을 기준으로 DI를 처리한다면, @Resource(name=?)는 name을 사용하며 @Autowired 기능까지 있다.
