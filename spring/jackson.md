# jackson

참조문서 : http://www.baeldung.com/jackson-annotations

## 1. jackson 이란?
jackson은 자바진영 json 라이브러리로 잘 알려져 있지만 json 뿐만 아니라 XML, YAML, CSV 등 다양한 형식의 데이타를 지원하는 data-processing 툴이다.

스트림 방식이므로 속도가 빠르며 유연하며 다양한 third party 데이터 타입을 지원하며 annotation 방식으로 메타 데이타를 기술할 수 있으므로 JSON 의 약점중 하나인 문서화와 데이타 validation 문제를 해결할 수 있다.

## 2. 의존성 추가
databind 는 jackson-core, jackson-annotation 에 의존성이 있으므로 pom 파일에는 databind 모듈만 기술한다.

    <!-- Properties  -->
    <jackson.version>2.9.6</jackson.version>

    <!-- jackson -->
    <dependency>
        <groupId>com.fasterxml.jackson.core</groupId>
        <artifactId>jackson-databind</artifactId>
        <version>2.9.6</version>
    </dependency>

## 3. 어노테이션
* @JsonIgnoreProperties : json으로 변환(Serialize, Deserialize) 시 제외시킬 속성을 지정. VO class 위에서 선언
* @JsonIgnore : VO의 멤버변수 위에 선언해서 제외처리 (비밀번호 같은거)
* @JsonProperty : json으로 변환 시에 사용할 이름이다. (DB 칼럼과 이름이 다르거나 api 응답과 이름이 다르지만 매핑시켜야 할 때)
* @JsonInclude : 값 존재 유무에 따라 Serialize 시 동작을 지정 (default는 ALWAYS)
    * ALWAYS : 속성의 값에 의존하지 말고 항상 포함
    * NOT_EMPTY : null 또는 값이 빈 경우가 아니면 포함
    * NOT_NULL : null 이 아니면 포함
    * NOT_DEFAULT : bean의 기본생성자로 정의된 필드값과 다르게 변경된 필드만 포함
* @JsonAnyGetter : Map 타입의 멤버변수의 getter 위에 선언한다. json 변환 시 "key":"value" 형식으로 나온다.
* @JsonAnySetter : Deserialize 때
* @JsonGetter : 어떤 필드값을 가져올 떄 이 메소드로 접근해서만 가져오라고 jackson에게 명시적으로 알린다.
* @JsonSetter : 위와 동일, Deserialize 때
* @JsonAutoDetect :
* @JsonUnwrapped : 어떤 필드가 대해 중첩 구조로 넣는다면, 그러지 말라고 명시적으로 알린다
* @JsonRootName : VO class 위에 선언해서 wrapping 해서 json 변환
* @JsonFormat : 날짜, 시간값을 직렬화할 때 형식을 지정
    * @JsonFormat(shape = JsonFormat.Shape.STRING, pattern = "dd-MM-yyyy hh:mm:ss")