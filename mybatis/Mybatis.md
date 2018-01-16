# Mybatis 프레임워크

## 1. 마이바티스란?
객체지향 언어인 자바의 관계형 데이터 베이스 프로그래밍을 좀더 쉽게 할수 있게 도와주는 개발 프레임워크이다.

자바는 jdbc api 를 제공해주지만, 이런 JDBC를 이용하면 1개 클래스에 반복된 코드가 존재, 한 파일에 java언어와 sql언어가 있어서 재사용성 등이 안좋아지는 단점이 있다.

Mybatis는 jdbc의 이러한 단점들을 개선했으며, 개발자가 작성한 SQL 명령어와 자바 객체를 매핑해주는 기능을 제공하며, 기존에 사용하던 SQL 명령어를 재사용한다.

### 특징
* 한 두줄의 자바 코드로 DB 연동을 처리
* SQL 명령어를 자바 코드에서 분리하여 XML 파일에 따로 관리

## 2. Ibatis 와 Mybatis
Ibatis는 아파치 프로젝트였을때 이야기고, 구글로 넘어가면서 Mybatis로 이름만 바뀌었다. 간단하게 몇가지 차이점을 나열해보면 아래와 같다.

|                         |        Ibatis       |        Mybatis       |
|-------------------------|:-------------------:|:--------------------:|
|      최소 JDK 버전      | 1.4                 | 1.5                  |
|       패키지 구조       | com.ibatis.*        | org.apache.ibatis.*  |
| SqlMap.xml에서 변경사항 | parameterMap        | parameterType        |
|  엘리먼트 명칭 변경사항 | SqlMapConfig SqlMap | Configuration mapper |
|    네임스페이스 속성    | 선택                | 필수                 |

## 3. 마이바티스 구조
![마이바티스 구조](http://img1.daumcdn.net/thumb/R1920x0/?fname=http%3A%2F%2Fcfile2.uf.tistory.com%2Fimage%2F99E306495A4CCD831EE1A6)

* mybatis-config 는 mybatis의 메인 환경설정 파일이다. 어떤 DBMS와 커넥션 맺을지, 어떤 맵퍼파일들이 있는지 등 알 수 있다.
* Mybatis는 맵퍼 파일에 있는 각 SQL 명령어들을 Map 에 담아서 저장하고 관리한다. 고유 아이디로 꺼내주는 듯

## 4. Mybatis API
* SqlSessionFactoryBuilder 클래스 : build() 메소드를 통해 mybatis-config를 로딩하여 SqlSessionFactory 객체를 생성한다.
* SqlSessionFactory 클래스 : SqlSession 객체에 대한 팩토리 객체다. 이 객체의 openSession() 이라는 메소드를 통해 SqlSession 객체를 얻을 수 있다.

        public class SqlSessionFactoryBean {
            private static SqlSessionFactory sessionFactory = null;
            
            static {
                try {
                    if (sessionFactory == null) {
                        Reader reader = Resources.getResourceAsReader("mybatis-config.xml");
                        sessionFactory = new SqlSessionFactoryBuilder().build(reader);
                    }
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
            
            public static SqlSession getSqlSessionInstance() {
                return sessionFactory.openSession();
            }
        }
* SqlSession 클래스 : Mapper XML 에 등록된 SQL을 실행하기 위해 API 를 제공
    * public Object selectOne(String stmt, Object param) : 하나의 데이터를 검색, 두개 이상 리턴되면 예외 발생
    * public List selectList(String stmt, Object param) : 여러 개의 데이터를 검색
    * public int insert(String stmt, Object param) : 몇 건 삽입했는지 리턴
    * public int update(String stmt, Object param) : 몇 건 갱신했는지 리턴
    * public int delete(String stmt, Object param) : 몇 건 삭제했는지 리턴

            public class BoardDAO {
                private SqlSession mybatis;
                
                public BoardDAO() {
                    mybatis = SqlSessionFactoryBean.getSqlSessionInstance();
                }
                
                public void insertBoard(BoardVO vo) {
                    mybatis.insert("BoardDAO.insertBoard", vo);
                    mybatis.commit();
                }
                
                public void updateBoard(BoardVO vo) {
                    mybatis.update("BoardDAO.updateBoard", vo);
                    mybatis.commit();
                }
                
                public void deleteBoard(BoardVO vo) {
                    mybatis.delete("BoardDAO.deleteBoard", vo);
                    mybatis.commit();
                }
                
                public BoardVO getBoard(BoardVO vo) {
                    return (BoardVO) mybatis.selectOne("BoardDAO.getBoard", vo);
                }
                
                public List<BoardVO> getBoardList(BoardVO vo) {
                    return mybatis.selectList("BoardDAO.getBoardList", vo);
                }
            }

* 테스트 코드 결과

        16:27:27.844 [main] DEBUG o.a.i.d.pooled.PooledDataSource - Created connection 1454031203.
        16:27:27.844 [main] DEBUG o.a.i.t.jdbc.JdbcTransaction - Setting autocommit to false on JDBC Connection [com.mysql.jdbc.JDBC4Connection@56aac163]
        16:27:27.846 [main] DEBUG BoardDAO.insertBoard - ==>  Preparing: INSERT INTO board(title, writer, content, regDate) VALUES(?,?,?,now()) 
        16:27:27.870 [main] DEBUG BoardDAO.insertBoard - ==> Parameters: 짱구야 놀자(String), 김짱구(String), 짱아야 흰둥이랑 산책가라(String)
        16:27:27.878 [main] DEBUG BoardDAO.insertBoard - <==    Updates: 1
        16:27:27.878 [main] DEBUG o.a.i.t.jdbc.JdbcTransaction - Committing JDBC Connection [com.mysql.jdbc.JDBC4Connection@56aac163]
        16:27:27.882 [main] DEBUG BoardDAO.getBoardList - ==>  Preparing: SELECT * FROM board WHERE title LIKE CONCAT('%',?,'%') ORDER BY seq DESC 
        16:27:27.882 [main] DEBUG BoardDAO.getBoardList - ==> Parameters: (String)
        16:27:27.896 [main] DEBUG BoardDAO.getBoardList - <==      Total: 3
        콘솔출력: BoardVO [seq=3, title=짱구야 놀자, writter=김짱구, content=짱아야 흰둥이랑 산책가라, regDate=2018-01-03 16:27:27.0, cnt=0, searchCondition=null, searchKeyword=null]
        콘솔출력: BoardVO [seq=2, title=코끼리와 해파리, writter=홍길동, content=내용, regDate=2018-01-03 16:18:56.0, cnt=0, searchCondition=null, searchKeyword=null]
        콘솔출력: BoardVO [seq=1, title=가입인사, writter=관리자, content=잘 부탁드립니다.., regDate=2018-01-03 16:11:38.0, cnt=0, searchCondition=null, searchKeyword=null]

## 5. 에러로그
오타를 조심하즈아~~~~

* Error building SqlSession. The error may exist in mapper/board-mappings.xml
    * 파일명 오타, s를 빼야함
* Error updating database.  Cause: org.apache.ibatis.reflection.ReflectionException: There is no getter for property named 'writer' in 'class qstart.devljh.domain.board.BoardVO'
    * VO에서 writter 라고 해놨었음