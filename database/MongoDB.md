# MongoDB

참조문서 : https://docs.mongodb.com/

mongoDB는 C++로 짜여진 오픈소스 데이터베이스이다. 문서지향(Document-Oriented)적이며 뛰어난 확장성과 성능을 자랑한다. NoSQL이다.

## 1. RDB와 비교

|    RDBMS    |       MongoDB      |
|:-----------:|:------------------:|
|   Database  |      Database      |
|    Table    |     Collection     |
| Tuple / Row |      Document      |
|    Column   |     Key / Field    |
|  Table Join | Embedded Documents |
| Primary Key |  Primary Key (_id) |

## 2. 특징과 장단점
### 2-1. 특징
* **Document-oriented storage :** MongoDB는 database > collections > documents 구조로 document는 key-value형태의 BSON(Binary JSON)으로 되어있다.
* **Full Index Support :** 다양한 인덱싱을 제공한다.
	* Single Field Indexes : 기본적인 인덱스 타입
	* Compound Indexes : RDBMS의 복합인덱스 같은 거
	* Multikey Indexes : Array에 미챙되는 값이 하나라도 있으면 인덱스에 추가하는 멀티키 인덱스
	* Geospatial Indexes and Queries : 위치기반 인덱스와 쿼리
	* Text Indexes : String에도 인덱싱이 가능
	* Hashed Index : Btree 인덱스가 아닌 Hash 타입의 인덱스도 사용 가능
* **Replication& High Availability :** 간단한 설정만으로도 데이터 복제를 지원. 가용성 향상.
* **Auto-Sharding :** MongoDB는 처음부터 자동으로 데이터를 분산하여 저장하며, 하나의 컬렉션처럼 사용할 수 있게 해준다. 수평적 확장 가능
* **Querying(documented-based query) :** 다양한 종류의 쿼리문 지원. (필터링, 수집, 정렬, 정규표현식 등)
* **Fast In-Pace Updates :** 고성능의 atomic operation을 지원
* **Map/Reduce :** 맵리듀스를 지원.(map과 reduce 함수의 조합을 통해 분산/병렬 시스템 운용 지원, 하둡처럼 MR전용시스템에 비해서는 성능이 떨어진다)
* **GridFS :** 분산파일 저장을 MongoDB가 자동으로 해준다. 실제 파일이 어디에 저장되어 있는지 신경 쓸 필요가 없고 복구도 자동이다.
* **Commercial Support :** 10gen에서 관리하는 오픈소스

### 2-2. 장점
쌓아놓고 삭제가 없는 경우가 제일 적합하다. (ex. 로그데이터, 세션 등)

* Flexibility : Schema-less라서 어떤 형태의 데이터라도 저장할 수 있다.
* Performance : Read & Write 성능이 뛰어나다. 캐싱이나 많은 트래픽을 감당할 때 써도 좋다.
* Scalability : 애초부터 스케일아웃 구조를 채택해서 쉽게 운용가능하다. Auto sharding 지원
* Deep Query ability : 문서지향적 Query Language 를 사용하여 SQL 만큼 강력한 Query 성능을 제공한다.
* Conversion / Mapping : JSON형태로 저장이 가능해서 직관적이고 개발이 편리하다.

### 2-3. 단점
정합성이 떨어지므로 트랜잭션이 필요한 경우에는 부적합하다. (ex. 금융, 결제, 회원정보 등)

* JOIN이 없다. join이 필요없도록 데이터 구조화 필요
* memory mapped file으로 파일 엔진 DB이다. 메모리 관리를 OS에게 위임한다. 메모리에 의존적, 메모리 크기가 성능을 좌우한다. 2-4를 참고하자.
* SQL을 완전히 이전할 수는 없다.
* B트리 인덱스를 사용하여 인덱스를 생성하는데, B트리는 크기가 커질수록 새로운 데이터를 입력하거나 삭제할 때 성능이 저하된다. 이런 B트리의 특성 때문에 데이터를 넣어두면 변하지않고 정보를 조회하는 데에 적합하다.

### 2-4. 메모리 크기와 MongoDB 성능 이슈
참조문서 : http://bcho.tistory.com/746

MongoDB는 데이터를 write할때, 논리적으로 memory 공간에 write하고 일정 주기에 따라, 이 메모리 block들을 주기적으로 disk로 write한다. 즉 이 디스크 writing 작업은 OS에 의해서 이루어 진다.

실제 메모리(RAM) 사이즈가 작더라도 OS의 가상메모리를 쓸 수 있다. 가상메모리는 block단위로 나뉘어지는데, 이 Block들은 Disk의 block에 mapping이 되고, 이 block들의 집합이 하나의 데이터 파일이 된다.

![주기억장치에서 보조기억장치로 맵핑구조](http://img1.daumcdn.net/thumb/R1920x0/?fname=http%3A%2F%2Fcfile6.uf.tistory.com%2Fimage%2F25373F33597858B523CF3D)

만약 RAM에 해당 데이터 블록이 없다면, page fault가 발생하게 되고, disk에서 그 데이터 블록을 로딩하게 된다. 물론 그 데이타 블록을 로딩하기 위해서는 다른 데이터 블록을 disk에 써야 한다. 즉, page fault가 발생하면, page를 memory와 disk 사이에 switching하는 현상이 일어나기 때문에, disk IO가 발생하고, 성능 저하를 유발하게 된다.

고로, 메모리가 성능을 크게 좌우한다는 것은 page fault와 관련이 있다. page fault시 disk로 write되는 데이터는 LRU 로직에 의해서 결정이 된다. 그래서 자주 안쓰는 데이터가 disk로 out되는데, 일반적인 application에서 자주 쓰는 데이터의 비율은 그리 크지 않다. (예를들어 게시판이나 블로그를 생각해보면, 앞의 1~10 페이지 정도를 사람들이 많이 본다.) 

이렇게 자주 조회되는 데이터를 Hot Data라고 하는데,이 Hot Data들이 집중되서 메모리에 올라가도록 key 설계를 하는 것이 핵심이다. 쓸 데 없이 전체 데이터를 scan하는 등의 작업을 하게 되면, 무조건 page fault가 발생하기 때문에, table scan등이 필요한 시나리오는 별도의 index table(summary table)을 만들어서 사용하는 등의 전략이 필요하다.

## 3. 주요 용어
### 3-1. document
먼저, document는 key-value쌍으로 이루어져 있다. value에는 또 다시 document가 들어갈 수 있다. 또한 동적 스키마를 갖고있어서, 같은 Collection(테이블) 안에 있는 document끼리 다른 스키마를 가질 수 있다.(스키마 프리)

	{
	    "_id": ObjectId("5099803df3f4948bd2f98391"),
	    "username": "ljh",
	    "language": { java: 10, go: 3 }
	}

### 3-2. Primary Key (ObjectId)
ObjectId는 12bytes의 16진수 값으로서, 각 document의 유일성을 보장한다. 첫 4bytes 는 현재 timestamp, 다음 3bytes는 machine id, 다음 2bytes는 MongoDB 서버의 프로세스id, 마지막 3bytes는 순차번호이다. RDB의 auto_increment와 비슷한 듯.

MongoDB에서 컬렉션에 저장된 각 문서에는 기본 키 역할을 하는 고유한 _id 라는 필드가 필요하다.

### 3-3. Collection
Collection은 Document의 그룹이며, Document들이 Collection 내부에 위치하고 있다. 

### 3-4. Database
Database는 Collection들의 물리적인 컨테이너이다. 각 Database는 파일시스템에 여러파일들로 저장된다.