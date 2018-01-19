# freemarker

참조문서 : https://freemarker.apache.org/docs/index.html

## 1. freemarker란?
템플릿 엔진이다. 자바 객체에서 데이터를 생성해서 템플릿에 넣어주면 freemarker에서 템플릿에 맞게 변환하여 HTML 파일을 생성한다. 다른언어도 쓸 수 있지만, JVM 위에서 돌아가므로 주로 자바 서블릿에서 많이 쓴다. freemarker는 HTML 출력만을 위한 엔진은 아니고 텍스트라면 그 어떠한 것도 가능하다. 어떠한 포맷이라도 텍스트에서 텍스트로 변환해서 출력하기 때문이다.freemarker는 웹기반 프레임워크가 아니고 완전한 POJO기반 템플릿 엔진이다. `.java + .ftl = .html` 이다. 비슷한 템플릿 엔진으로는 velocity 가 있다.

![freemarker 방식](http://img1.daumcdn.net/thumb/R1920x0/?fname=http%3A%2F%2Fcfile1.uf.tistory.com%2Fimage%2F994F7D465A54863C24BB43)

### 장점 : JSP의 가장 큰 단점을 극복
JSP를 기반으로 EL, JSTL을 사용해도 view를 mvc구조로 아름답게 짤 수 있지만, Java EE에 종속적이라는 단점이 있다.

## 2. 문법
자세한 문법은 잘 정리된 문서를 보자
* http://wiki.gurubee.net/display/SWDEV/FreeMarker?

#### 기본
* FTL tag : `<# >`
* 주석 : `<#-- comment -->`
* <#list [Object code에서 key값 ]  as  [별칭할 값]> 
* 조건문 : `<#if>` 
* for 문

        <#list  1..10  as i > 
            ${i}
        <#assign i=i+1?int>
* 변수 재정의 : `<#setting [변수1]=[변수2]>` , `<#assign x=0>`
* 변수 선언, 할당  : `<#assign x=0>`
* int형으로 변수 선언 : `<#assign x=0 ? int>`
* x값을 출력 : ${x}

## 3. freemarker 설정
### freemarkerConfig
* templateLoaderPath : 프리마커 템플릿 경로 지정
* defaultEncoding
* freemarkerSettings : 프리마커 세부 설정을 지정

### FreeMarkerViewResolver
* contentType : 기본 컨텐츠타입을 지정
* cache : 캐시 사용 여부
* prefix : 뷰 페이지 경로를 지정
* suffix : 뷰 페이지 확장자명을 입력. 입력하지 않으면 확장자까지 컨트롤러에서 해줘야함

### 설정 예제
pom.xml

    <!-- freemarker -->
    <dependency>
        <groupId>org.freemarker</groupId>
        <artifactId>freemarker-gae</artifactId>
        <version>2.3.23</version>
    </dependency>

applicationContext.xml

    <!-- FreeMarker configuration -->
    <bean id="freemarkerConfig" class="org.springframework.web.servlet.view.freemarker.FreeMarkerConfigurer">
        <property name="templateLoaderPath" value="/WEB-INF/freemarker"/>
        <property name="defaultEncoding" value="UTF-8"/>
        <property name="freemarkerSettings">
            <map>
                <entry key="template_update_delay" value="60000"/>
                <entry key="auto_flush" value="false"/>
                <entry key="default_encoding" value="UTF-8"/>
                <entry key="whitespace_stripping" value="true"/>
            </map>
        </property>
    </bean>

dispatcher-servlet.xml

    <bean class="org.springframework.web.servlet.view.freemarker.FreeMarkerViewResolver">
        <property name="order" value="2" />
        <property name="cache" value="true" />
        <property name="suffix" value=".ftl" />
        <property name="contentType" value="text/html; charset=UTF-8" />
        <property name="exposeSpringMacroHelpers" value="true" />
    </bean>

## 4. hello world
/WEB-INF/freemarker/hello.ftl

    <#ftl encoding="utf-8"/>

    <html>
    <head>
        <title>빨간색코딩 프리마카</title>
    </head>
    <body>
        <h1>컨트롤러의 메세지: ${message}</h1>
    </body>
    </html>

HelloController.java

    import org.springframework.stereotype.Controller;
    import org.springframework.web.bind.annotation.RequestMapping;
    import org.springframework.web.servlet.ModelAndView;

    @Controller
    public class HelloController {
        @RequestMapping("/hello")
        public ModelAndView hello(){
            String message = "프리마커를 해보자~";
            System.out.println("콘솔추울력");

            ModelAndView mv = new ModelAndView();
            mv.setViewName("hello");
            mv.addObject("message", message);
            return mv;
        }
    }

![helloworld_freemarker 구동화면](http://img1.daumcdn.net/thumb/R1920x0/?fname=http%3A%2F%2Fcfile24.uf.tistory.com%2Fimage%2F99E2DB435A54859821E5C4)