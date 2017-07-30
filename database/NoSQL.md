# NoSQL

참조문서 : [NoSQL을 여행하는 히치하이커를 위한 안내서](http://blog.naver.com/PostView.nhn?blogId=naverdev&logNo=120116323974&parentCategoryNo=8&viewDate=&currentPage=1&listtype=0)

## 1. 개념
NoSQL은 Not Only SQL의 약자이다. 기본 RDBMS의 한계를 극복하기 위해 만들어진 새로운 형태의 데이터베이스다. 릴레이션이 아니므로 고정된 스키마가 없고 조인이 힘들다. 빅데이터를 다룰 때, RDBMS로만 트래픽을 감당하기 어려워졌고, 이를 해결하려고 NoSQL이 탄생했다. NoSQL은 분산 환경에서 대용량의 데이터를 빠르게 처리하기 위해서 개발되었다. 핵심은 Horizontal Scalability(수평확장)과 High Availability(고가용성)이다.

### RDBMS의 한계
많은 데이터량과 데이터 처리량이 계속적으로 증가한다면 RDBMS는 아래와 같은 문제점을 만난다.

* 스키마 문제 : 빅데이터를 RDB의 스키마에 맞춰 변경해서 넣으려면 매우 긴 시간의 down time이 발생
* 스케일업의 한계 : RDBMS는 애초부터 스케일 아웃을 염두에 두고 설계되지 않았다. 관계 모델과 트랜잭션의 연산, 일관성, 속성을 유지하면서 분산환경(스케일아웃)에서 RDBMS를 조작하는 것은 어렵다.

## 2. 특징과 장점
* 거대한 Map으로서 key-value 형식을 지원한다.
* RDBMS가 데이터의 관계를 Foreign Key 등으로 정의하고 Join 등 관계형 연상을 하지만 NoSQL은 관계를 정의하지 않는다.
* 대용량 데이터 저장을 한다.
* 분산형 구조를 통해 여러대의 서버에 분산하여 저장하고 상호복제하여 데이터 유실이나 서비스 중지에 대비한다.
* Schema-less
* 읽기 작업보다 쓰기 작업이 더 빠르며, 일반적으로 RDBMS에 비하여 쓰기와 읽기 성능이 빠르다.

## 3. CAP
분산형 구조는 일관성(Consistency), 가용성(Availability), 분산 허용(Partitioning Tolerance)의 3가지 특징을 가지고 있다. CAP 이론은 이 중 2가지만 만족할 수 있다는 이론이다. NoSQL은 대부분 이 CAP 이론을 따르고 있다.

1. 일관성(Consistency) : 분산된 노드 중 어느 노드로 접근하더라도 데이터 값이 같아야 한다. (데이터 복제 중에 쿼리가 되는 일관성을 제공하지 않는 시스템의 경우 다른 데이터 값이 쿼리될 수 있다.)
2. 가용성(Availability) : 클러스터링된 노드 중 하나 이상의 노드가 실패(Fail)라도 정상적으로 요청을 처리할 수 있는 기능을 제공한다.
3. 분산 허용(Partition Tolerance): 클러스터링 노드 간에 통신하는 네트워크가 장애가 나더라도 정상적으로 서비스를 수행한다. 노드 간 물리적으로 전혀 다른 네트워크공간에 위치도 가능하다.

일반적으로 RDBMS는 일관성과 가용성을 만족한다. NoSQL은 가용성과 분산허용을 만족하는 제품군과 일관성과 분산허용을 만족하는 제품군으로 나눌 수 있다.

## 4. 데이터 모델 분류
NoSQL은 거대한 Map<String, Object>이라고 할 수 있다. 따라서 대부분 NoSQL은 Key/Value 개념을 지원한다. 이 때 저장소는 RAM일 수도, 디스크일 수도 있다. 보통 데이터 모델(key에 저장되는 값)에 따라 NoSQL을 분류한다.

### 4-1. Key/Value Database : Redis(인메모리), Oracle Coherence 등
* 단순한 저장구조를 가지며, 복잡한 조회 연산을 지원하지 않는다.
* 고속 읽기와 쓰기에 최적화된 경우가 많다. key에 대한 단위 연산이 빠른 것이지, 여러 key에 대한 연산은 느릴 수 있다.
* 메모리를 저장소로 쓰는 경우, 아주 빠른 get과 put을 지원한다.
* Value는 문자열이나 정수와 같은 원시 타입이 들어갈 수도 있고, 아래 사진처럼 **또 다른 key/Value이 들어갈 수도 있다.** 이를 Column Family이라고 하며, Key 안에 (Column, Value) 조합으로 된 여러 개의 필드를 갖는 것을 말한다.

![Column Family 구조](http://img1.daumcdn.net/thumb/R1920x0/?fname=http%3A%2F%2Fcfile29.uf.tistory.com%2Fimage%2F23B39533597857E6165619)

### 4-2. Big Table Database (= Ordered Key/Value) : Hbase, Cassandra 등
* Key/Value Store와 데이터 저장 방식은 동일하다. 보통 NoSQL은 RDBMS의 order by같은 정렬기능을 제공해주지 않는다. 그러나 이 모델은 내부적으로 Key를 정렬한다. 
* 키를 정렬함으로써, 값을 날짜나 선착순으로 정렬해서 보여줄 때 매우 유용하다.

### 4-3. Document Database : MongoDB, CouchDB, Riak
* Key/Value Store의 확장된 형태로, value에 Document라는 타입을 저장한다. Document는 구조화된 문서 데이터(XML, JSON, YAML 등)을 말한다.
* 복잡한 데이터 구조를 표현가능하다.
* Document id 또는 특정 속성값 기준으로 인덱스를 생성한다. 이 경우 해당 key 값의 range에 대한 효율적인 연산이 가능해 지므로 이에 대한 쿼리를 제공한다. 따라서 Sorting, Join, Grouping 등이 가능해진다.
* 쿼리 처리에 있어서 데이터를 파싱해서 연산을 해야하므로 overhead가 key-value 모델보다 크다. 큰 크기의 document를 다룰 때는 성능이 저하된다.

### 4-4. Graph Database : Sones, AllegroGraph, neo4j
* node들과 relationship들로 구성된 개념이다. 역시 Key/Value Store이며 모든 노드는 끊기지 않고 연결되어 있다. 
* relationship은 direction, type, start node, end node에 대한 속성 등을 가진다. 보통 양(코스트, 무게 등)적인 속성들을 가진다.