# apache tiles

참조문서

* https://www.javatpoint.com/spring-mvc-tiles-example
* https://tiles.apache.org

## 1. tiles 란?
타일즈는 웹 페이지의 상단이나 하단 메뉴와 같이 반복적으로 사용되는 부분들에 대한 코드를 분리해서 예쁘게 한 곳에서 관리를 가능하게 해주는 프레임워크이다.

### JSP include와의 차이
비슷한 역할이지만, tiles가 여러모로 편리하고 좋다. jsp는 페이지 내에 동일한 레이아웃 정보가 들어가므로 전체적인 레이아웃을 변경하게 될 경우 모든 페이지를 수정해야하는 문제점이 있다. 예를들면 만약 50개의 페이지에 상단 메뉴가 include 되어있는데, 페이지명이 바뀌면 50개 파일을 전부 수정해주어야 하는 것이다. tiles는 이런 일이 있으면 설정파일만 변경해주면 된다.

### 특징
화면 기본 구성 레이아웃 템플릿 정의하고 상속을 통하여 대부분 구조를 재사용할 수 있는 기능 및 설정파일을 통한 통합 관리를 통하여 확장성 있고 일관되게 페이지 구성을 관리한다.

### Composite View 패턴
모듈 단위의 뷰 들을 조합해서 하나의 뷰를 구성한다. 트리 구조로 볼 수 있으며, 부모 뷰와 자식 뷰 들의 사이를 부모를 하나의 전체 뷰로 볼 수 있고, 자식 뷰를 작은 한 부분으로 볼 수 있다. 아래는 Composite 패턴의 UML이다.

![Composite_View_Uml](http://img1.daumcdn.net/thumb/R1920x0/?fname=http%3A%2F%2Fcfile4.uf.tistory.com%2Fimage%2F999256465A5486E4187C05)

Component는 표현할 요소들의 추상적인 인터페이스고, Leaf는 Component의 구현체, Composite은 Component의 구현체들을 자식으로 삼으며, 구현체(Leaf)를 관리하기 위한 메소드들이 추가적으로 있다.

즉 Composite View 패턴을 사용해서 화면이 바뀌어도 중복되는 부분(상단, 하단 등)의 코드를 줄일 수 있고, 일관성을 높여 유지보수와 제어를 쉽게한다.

## 2. 용어
Template는 페이지의 구조를 기술하고 Attribute는 구조내에서 실제 내용에 해당하며, definition은 Template(구조)에 Attribute(내용)을 연결하여 랜더링가능한 페이지를 기술한다. 자세히 살펴보면..

* Template : 페이지 레이아웃을 의미하며, jsp 파일로 페이지의 기본 골격을 구성하고 각 페이지의 실제 구성 내용은 definition에서 설정되는 Attribute(실제 내용) 태그를 사용하여 런타임 시 뿌려준다.
    * string 타입 Attribute 추가: `<titles:getAsString name="속성명"/>`
    * template 및 definition Attribute 추가: `<tiles:insertAttribute name="속성명" />`
* Attribute : Template의 빈 공간을 채우기 위하여 사용되는 정보로 3가지 타입으로 구성
    * string: 직접 출력 할 문자열. ex. title
    * template : 템플릿 내 또 일부의 레이아웃을 기술할 수도 있다.
    * definition : 전체 혹은 일부 Attribute 들이 실제 내용으로 채워진 페이지, 페이지 구조(Template)와 레이아웃 내부를 채울 정보(Attribute)가 같이 정의된 페이지를 의미
* Definition : 사용자에게 제공되기 위하여 랜더링되는 Template과 Attribute들을 연결

## 3. XML 설정
pom.xml

    <dependency>
        <groupId>org.apache.tiles</groupId>
        <artifactId>tiles-extras</artifactId>
        <version>3.0.8</version>
    </dependency>

    <dependency>
        <groupId>org.apache.tiles</groupId>
        <artifactId>tiles-servlet</artifactId>
        <version>3.0.8</version>
    </dependency>

    <dependency>
        <groupId>org.apache.tiles</groupId>
        <artifactId>tiles-jsp</artifactId>
        <version>3.0.8</version>
    </dependency>

servlet-context.xml

    <!-- Tiles viewResolver -->
    <bean id="tilesViewResolver" class="org.springframework.web.servlet.view.UrlBasedViewResolver">
        <property name="viewClass" value="org.springframework.web.servlet.view.tiles3.TilesView" />
        <property name="order" value="2" />
    </bean>

	<bean id="tilesConfigurer" class="org.springframework.web.servlet.view.tiles3.TilesConfigurer">
        <property name="definitions">
            <list>
                 <value>/WEB-INF/tiles/*.xml</value>
            </list>
        </property>
    </bean>

## 4. hello world
HelloController.java

    import org.springframework.stereotype.Controller;
    import org.springframework.web.bind.annotation.PathVariable;
    import org.springframework.web.bind.annotation.RequestMapping;
    import org.springframework.web.servlet.ModelAndView;

    @Controller
    public class HelloController {
        // 타일즈 헬로월드
        @RequestMapping("/tiles/{path}")
        public ModelAndView tiles(@PathVariable("path") String path) {
            System.out.println("타일즈 가즈아");

            ModelAndView mv = new ModelAndView();
            mv.setViewName("/" + path);
            return mv;
        }
    }

/WEB-INF/tiles/tiles-default.xml

    <?xml version="1.0" encoding="UTF-8" ?>
    <!DOCTYPE tiles-definitions PUBLIC "-//Apache Software Foundation//DTD Tiles Configuration 3.0//EN" "http://tiles.apache.org/dtds/tiles-config_3_0.dtd">

    <tiles-definitions>
        <definition name="defaultLayout" template="/WEB-INF/views/layouts/layout.jsp">
            <put-attribute name="header" value="/WEB-INF/views/layouts/header.jsp" />
            <put-attribute name="menu" value="/WEB-INF/views/layouts/menu.jsp" />
            <put-attribute name="main" />
            <put-attribute name="footer" value="/WEB-INF/views/layouts/footer.jsp" />
        </definition>

        <definition name="/tilesTest" extends="defaultLayout">
            <put-attribute name="main" value="타일즈 메인입니다" />
        </definition>

        <definition name="/*" extends="defaultLayout">
            <put-attribute name="main" value="/WEB-INF/views/{1}.jsp" />
        </definition>
        
        <!--
        <definition name="/*/*" extends="defaultLayout">
            <put-attribute name="main" value="/WEB-INF/views/{1}/{2}.jsp" />
        </definition>
        -->
    </tiles-definitions>

### 덧붙임
* defaultLayout 템플릿에서는 layout.jsp를 가져다 쓰며, 안에는 헤더, 메뉴, 바디, 푸터가 들어간다는 말이다.
* 2,3번째 definition에서는 controller의 리턴 값과 같은 패턴일 경우, 여기에 맞춰 템플릿을 보여주겠다는 말이다. ex. `/경로/파일`
* /를 붙여줘야한다ㅋㅋ 삽질함..
* extends 는 상속받는다는거, 이 템플릿에서 main 영역만 value로 갈아끼우는 것이다.

### 결과화면
![동작 화면](http://img1.daumcdn.net/thumb/R1920x0/?fname=http%3A%2F%2Fcfile8.uf.tistory.com%2Fimage%2F99C209475A5486BB1ADB7B)