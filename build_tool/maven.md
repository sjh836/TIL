# Maven

참조문서 : http://maven.apache.org/

이클립스에서만 깨작깨작해본 나는 메이븐은 이름만 들어보았을 뿐.. 라이브러리 추가할 거있으면 dependency 만 복붙해서 저장하면 자동으로 추가되길래 개편하다. 까지가 메이븐 공부하기 전..ㅋㅋㅋ

공식문서가 엄청엄청 잘되어있다.

## 1. 메이븐이란?
주로 java 진영에서 프로젝트 빌드, 관리에 사용되는 도구이다. 개발자들이 전체 개발과정을 한 눈에 알아볼 수 있다. 아파치 프로젝트이다.

### maven의 특징
* 빌드 절차 간소화
* 동일한 빌드 시스템 제공
* 프로젝트 정보 제공

### 구조
![메이븐 구조](http://img1.daumcdn.net/thumb/R1920x0/?fname=http%3A%2F%2Fcfile30.uf.tistory.com%2Fimage%2F999BF0365A534021248CEE)

### ant와의 차이점
1. Ant가 비교적 자유도가 높다. 전처리, 컴파일, 패키징, 테스팅, 배포 가능
2. Maven은 정해진 라이프사이클에 의하여 작업 수행하며, 전반적인 프로젝트 관리 기능까지 포함하고 있음. (Build Tool + Project Management)

### gradle과의 차이점
1. XML 대신 groovy 스크립트를 사용하여 동적인 빌드 가능.
2. maven은 멀티프로젝트에서 상속구조인데, gradle은 주입 방식이다. 멀티프로젝트에서 gradle이 더 적합하다.

## 2. 플러그인
* 메이븐은 플러그인을 구동해주는 프레임워크(plugin execution framework)이다. 모든 작업은 플러그인에서 수행한다.
* 플러그인은 다른 산출물(artifacts)와 같이 저장소에서 관리된다.
* 메이븐은 여러 플러그인으로 구성되어 있으며, 각각의 플러그인은 하나 이상의 goal(명령, 작업)을 포함하고있다. Goal은 Maven의 실행단위이다.

    ![플러그인과 골](http://img1.daumcdn.net/thumb/R1920x0/?fname=http%3A%2F%2Fcfile4.uf.tistory.com%2Fimage%2F99989C3E5A533FC31EBE86)
* 플러그인과 골의 조합으로 실행한다. ex. `mvn <plugin>:<goal>` = `mvn archetype:generate`
* 메이븐은 여러 goal을 묶어서 lifecycle phases 로 만들고 실행한다. ex. `mvn <phase>` = `mvn install`

### 플러그인 목록
|          구분         |                                     Plugin명                                     |                설명                |
|:---------------------:|:-------------------------------------------------------------------------------:|:----------------------------------:|
| core plugins          | clean, compiler, deploy, failsafe, install, resources, site, surefire, verifier | 기본 단계에 해당하는 핵심 플러그인 |
| Packaging types/tools | ear, ejb, jar, rar, war, app-client, shade                                      | 압축 도구                          |
| Reporting plugins     | changelog, changes, checkstyle, javadoc, pmd, surefire-report                   | 리포팅 도구                        |
| Tools                 | ant, antrun, archetype, assembly, dependency, pdf, plugin, repository           | 기타 다양한 도구                   |

## 3. 라이프사이클
메이븐은 프로젝트 생성에 필요한 단계(phases)들을 Build Lifecycle이라 정의하고 default, clean, site 세가지로 표준 정의한다. Lifecycle은 Build Phase 들로 구성되며 일련의 순서를 갖는다. phase 는 실행단위로서 goal과 바인딩된다.

아래 사진은 Build default 라이프사이클의 주요 phase이고 그 밑에는 전체이다.

![maven의 주요 phase](http://img1.daumcdn.net/thumb/R1920x0/?fname=http%3A%2F%2Fcfile26.uf.tistory.com%2Fimage%2F9975E4405A533FDF1A53F9)

* clean : 빌드 시 생성되었던 산출물을 삭제
    1. pre-clean : clean 작업 전에 사전작업
    2. clean : 이전 빌드에서 생성된 모든 파일 삭제
    3. post-clean : 사후작업
* default : 프로젝트 배포절차, 패키지 타입별로 다르게 정의됌
    1. validate : 프로젝트 상태 점검, 빌드에 필요한 정보 존재유무 체크
    2. initialize : 빌드 상태를 초기화, 속성 설정, 작업 디렉터리 생성
    3. generate-sources : 컴파일에 필요한 소스 생성
    4. process-sources : 소스코드를 처리
    5. generate-resources : 패키지에 포함될 자원 생성
    6. compile : 프로젝트의 소스코드를 컴파일
    7. process-classes : 컴파일 후 후처리
    8. generate-test-source : 테스트를 위한 소스 코드를 생성
    9. process-test-source : 테스트 소스코드를 처리
    10. generate-test-resources : 테스팅을 위한 자원 생성
    11. process-test-resources : 테스트 대상 디렉터리에 자원을 복사하고 가공
    12. test-compile : 테스트 코드를 컴파일
    13. process-test-classes : 컴파일 후 후처리
    14. test : 단위 테스트 프레임워크를 이용해 테스트 수행
    15. prepare-package : 패키지 생성 전 사전작업
    16. package : 개발자가 선택한 war, jar 등의 패키징 수행
    17. pre-integration-test : 통합테스팅 전 사전작업
    18. integration-test : 통합테스트
    19. post-integration : 통합테스팅 후 사후작업
    20. verify : 패키지가 품질 기준에 적합한지 검사
    21. install : 패키지를 로컬 저장소에 설치
    22. deploy : 패키지를 원격 저장소에 배포
* site : 프로젝트 문서화 절차
    1. pre-site : 사전작업
    2. site : 사이트문서 생성
    3. post-site : 사후작업 및 배포 전 사전작업
    4. site-deploy : 생성된 문서를 웹 서버에 배포

## 4. 의존성
개발자는 프로젝트에 사용할 라이브러리를 pom.xml에 dependency로 정의만 해두면 메이븐이 repository에서 검색해서 자동으로 추가해준다. 심지어 참조하고있는 library까지 모두 찾아서 추가해준다. 이것을 '의존성 전이' 라고 한다.

예제

    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.8.2</version>
      <scope>test</scope>
    </dependency>

### 의존관게 제한 기능
불필요한 라이브러리 다운로드를 방지하기 위해 추가기능을 제공한다.

* Dependency mediation : 버전이 다른 두 개의 라이브러리가 동시에 의존 관계에 있을 경우 Maven은 좀더 가까운 의존관계에 있는 하나의 버전만을 선택
* Dependency management : 직접 참조하지는 않으면서 하위 모듈이 특정 모듈을 참조할 경우, 특정 모듈의 버전을 지정
* Dependency scope : 현재 Build 단계에 꼭 필요한 모듈만 참조할 수 있도록 참조 범위를 지정
    * compile : 기본값, 모든 classpath에 추가, 컴파일 및 배포 때 같이 제공
    * provided : 실행 시 외부에서 제공, 예를들면 WAS에서 제공되어 지므로 컴파일 시에는 필요하지만, 배포시에는 빠지는 라이브러리들
    * runtime : 컴파일 시 참조되지 않고 실행때 참조
    * test : 테스트 때만
    * system : 저장소에서 관리하지 않고 직접 관리하는 jar 파일을 지정
    * import : 다른 pom파일 설정을 가져옴, `<dependencyManagemet>`에서만 사용
* Excluded dependencies : 임의의 모듈에서 참조하는 특정 하위 모듈을 명시적으로 제외처리
* Optional dependencies : 임의의 모듈에서 Optional로 참조된 모듈은 상위 모듈이 참조될 때 Optional 모듈은 참조제외

### 의존 라이브러리의 경로
* 로컬 : USER_HOME/.m2/repository 에 저장된다.

## 5. profile
Maven은 서로 다른 환경에 따라 달라지는 설정을 각각 관리할 수 있는 Profile 기능을 제공한다.

## 6. POM.xml
pom.xml 은 메이븐을 이용하는 프로젝트의 root에 존재하는 xml 파일이다. pom은 프로젝트 객체 모델(Project Object Model)을 뜻한다. 프로젝트 당 1개가 있다. 이것만 보면 프로젝트의 모든 설정, 의존성 등을 알 수 있다!!

### 엘리먼트
* `<groupId>` : 프로젝트의 패키지 명칭
* `<artifactId>` : artifact 이름, groupId 내에서 유일해야 한다.

        <groupId>org.springframework</groupId>
    	<artifactId>spring-webmvc</artifactId>
* `<version>` : artifact 의 현재버전 ex. 1.0-SNAPSHOT
* `<name>` : 어플리케이션 명칭
* `<packaging>` : 패키징 유형(jar, war 등)
* `<distributionManagement>` : artifact가 배포될 저장소 정보와 설정
* `<parent>` : 프로젝트의 계층 정보
* `<dependencyManagement>` : 의존성 처리에 대한 기본 설정 영역
* `<dependencies>` : 의존성 정의 영역
* `<repositories>` : 이거 안쓰면 공식 maven 저장소를 활용하지만, 사용하면 거기 저장소를 사용
* `<build>` : 빌드에 사용할 플러그인 목록을 나열
* `<reporting>` : 리포팅에 사용할 플러그인 목록을 나열
* `<properties>` : 보기좋게 관리가능, 보통 버전에 많이 쓴다.

        <!-- properties 에 이렇게 추가하면 -->
        <spring-version>4.3.3.RELEASE</spring-version>

        <!-- dependencies 에 이렇게 쓸수 있다. -->
        <version>${spring-version}</version>

### 큰 뼈대

    <modelVersion>4.0.0</modelVersion>

    <!-- The Basics -->
    <groupId>...</groupId>
    <artifactId>...</artifactId>
    <version>...</version>
    <packaging>...</packaging>

    <dependencies>...</dependencies>
    <parent>...</parent>
    <dependencyManagement>...</dependencyManagement>
    <modules>...</modules>

    <properties>...</properties>

    <!-- Build Settings -->
    <build>...</build>
    <reporting>...</reporting>

    <!-- More Project Information -->
    <name>...</name>
    <description>...</description>
    <url>...</url>
    <inceptionYear>...</inceptionYear>
    <licenses>...</licenses>
    <organization>...</organization>
    <developers>...</developers>
    <contributors>...</contributors>

    <!-- Environment Settings -->
    <issueManagement>...</issueManagement>
    <ciManagement>...</ciManagement>
    <mailingLists>...</mailingLists>
    <scm>...</scm>
    <prerequisites>...</prerequisites>
    <repositories>...</repositories>
    <pluginRepositories>...</pluginRepositories>
    <distributionManagement>...</distributionManagement>
    <profiles>...</profiles>