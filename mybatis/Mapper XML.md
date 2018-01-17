# Mapper XML
## 1. 엘리먼트
* `<mapper>` : 루트 엘리먼트
    * namespace : 유일한 id 만드는데 도움을 준다. ex. 네임스페이스.id
* `<select>` : select 구문에 사용
    * id : 필수속성, 유일한 id를 써야함
    * parameterType : 인자 타입, 보통 패키지까지 안쓰고 `<typeAlias>`로 간결하게 씀.
    * resultType : 필수속성, select의 결과로 반환된 resultSet이 매핑될 객체의 타입
* `<insert>` : insert 구문에 사용
    * id
    * parameterType
    * `<selectKey>`
        * keyProperty
        * resultType
* `<update>` : update 구문에 사용
    * id
    * parameterType
* `<delete>` : delete 구문에 사용
    * id
    * parameter
* `<resultMap>` : select문에서 resultType 대신 resultMap 을 사용 가능하다. 검색 쿼리가 단순 테이블 조회가 아닌 join 구문을 포함할 때는 하나의 자바객체로 매핑할 수 없다. 이럴 때 resultMap 을 이용한다.

## 2. CDATA
xml 특성상 sql 구문 내 `<` 같은 걸 이용할 수 없다. `<![CDATA[ 여기에 문자열을 쓰면 됌 ]]>` 이렇게 하면 XML 파서가 PCDATA로 인식하지 않는다.

## 3. 보통 대문자를 쓴다
SQL 구문은 대소문자를 구분하지 않지만, SQL문은 대문자로 칼럼명과 파라미터들은 소문자로 표현하여 가독성을 높인다.

## 4. 예제
mybatis-config.xml

    <?xml version="1.0" encoding="UTF-8" ?>
    <!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd">
    <configuration>
        <properties resource="db.properties" />
        
        <!-- Alias 설정 -->
        <typeAliases>
            <typeAlias alias="board" type="qstart.devljh.domain.board.BoardVO" />
        </typeAliases>
        
        <!-- DataSource 설정 -->
        <environments default="development">
            <environment id="development">
                <transactionManager type="JDBC" />
                <dataSource type="POOLED">
                    <property name="driver" value="${jdbc.driverClassName}" />
                    <property name="url" value="${jdbc.url}" />
                    <property name="username" value="${jdbc.username}" />
                    <property name="password" value="${jdbc.password}" />
                </dataSource>
            </environment>
        </environments>
        
        <!-- SQL Mapper 설정 -->
        <mappers>
            <mapper resource="mapper/boardMapper.xml" />
        </mappers>
    </configuration>

boardMapper.xml

    <?xml version="1.0" encoding="UTF-8" ?>
    <!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
    "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

    <mapper namespace="BoardDAO">
        <resultMap id="boardResult" type="board">
            <id property="seq" column="SEQ" />
            <result property="title" column="TITLE" />
            <result property="writer" column="WRITER" />
            <result property="content" column="CONTENT" />
            <result property="regDate" column="REGDATE" />
            <result property="cnt" column="CNT" />
        </resultMap>
        
        <insert id="insertBoard">
            INSERT INTO board(title, writer, content, regDate)
            VALUES(#{title},#{writer},#{content},now())
        </insert>

        <update id="updateBoard">
            UPDATE board SET title=#{title}, content=#{content}
            WHERE seq=#{seq}
        </update>

        <delete id="deleteBoard">
            DELETE FROM board WHERE seq=#{seq}
        </delete>

        <select id="getBoard" resultType="board">
            SELECT * FROM board WHERE
            seq=#{seq}
        </select>

        <select id="getBoardList" resultType="board">
            SELECT * FROM board WHERE
            title LIKE CONCAT('%',#{searchKeyword},'%') ORDER BY seq DESC
        </select>
    </mapper>