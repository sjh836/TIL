# Mybatis와 spring 연동

스프링과 연동하려면 마이바티스에서 제공해주는 API를 사용해야 한다. 다음과 같은 클래스를 쓴다.

* org.mybatis.spring.SqlSessionFactoryBean
* org.mybatis.spring.SqlSessionTemplate

## 1. 연동 기본설정
applicationContext-dao-config.xml 에 bean 을 추가한다.

    <!-- SqlsessionFactory setup for MyBatis Database Layer -->
    <bean id="sessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource"/>

        <!-- mybatis 기본설정 -->
        <property name="configLocation" value="classpath:/mybatis-config.xml"/>
      
        <!-- mapper 의 위치를 설정 -->
        <property name="mapperLocations" value="classpath:/mapper/*Mapper.xml"/>

    </bean>

## 2. 연동방법1 : SqlSessionDaoSupport 상속
DAO클래스에 SqlSessionDaoSupport 를 상속받아서 사용한다.

    // BoardDAO.java
    @Autowired
    public void setSqlSessionFactory(SqlSessionFactory sqlSessionFactory) {
        super.setSqlSessionFactory(sqlSessionFactory)
    }

    public void insertBoard(BoardVO vo) {
        getSqlSession().insert("BoardDAO.insertBoard", vo);
    }

## 3. 연동방법2 : SqlSessionTemplate 클래스 활용
applicationContext-dao-config.xml 에 SqlSessionTemplate 를 bean 으로 등록하여 사용한다. SqlSessionTemplate 클래스에는 setter가 없어서 생성자로 주입할 수 밖에 없다.

    // applicationContext-dao-config.xml
    <bean id="sqlSessionTemplate" class="org.mybatis.spring.SqlSessionTemplate">
        <constructor-arg index="0" ref="sqlSessionFactory"></constructor-arg>
    </bean>

    // BoardDAO.java
    @Autowired
    private SqlSessionTemplate mybatis;

    public void insertBoard(BoardVO vo) {
        mybatis.insert("BoardDAO.insertBoard", vo);
    }