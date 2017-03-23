# 하둡(Hadoop)

High-Availability Distributed Object-Oriented Platform

## 1. 하둡이란?
- 대용량 자료를 처리할 수 있는 컴퓨터 클러스터에서 동작하는 분산 응용 프로그램을 지원하는 오픈소스 자바 프레임워크
- 분산 데이터 처리 기술: 큰 용량의 단일 서버보다 여러 서버의 작은 용량을 묶은 컴퓨터 클러스터가 가성비가 더 좋다.
- **하둡의 핵심 철학:** 코드(가벼움)를 데이터(무거움)가 있는 곳으로 보낸다.

### 레이드와 하둡
* 레이드시스템은 디스크는 여러개, OS도 1개, CPU1개. 10펙타바이트를 처리할때 OS와 CPU가 죽어난다(매우 느림)
* 하둡은 디스크도 여러개, OS도 여러개, CPU도 여러개. 10펙타바이트를 처리할때 분산처리해버림(짱빨름)

## 2. 스케일의 확장(scale-out)
- 스케일의 증가: 상업용 RDBS는 확장을 하려면 큰 용량을 갖고 있는 서버를 구매해야한다(scale-up)
- 하둡은 그냥 컴퓨터(nodes)를 추가함으로써 선형적으로 확장해버린다.

## 3. 하둡의 목적
- 매우 큰 데이터를 저장할 수 있어야 한다 -> **HDFS**
- 그 데이터를 이용해서 연산을 수행할 수 있어야함 -> **맵리듀스**

## 4. HDFS(Hadoop Distributed File System)
![HDFS](http://img1.daumcdn.net/thumb/R1920x0/?fname=http%3A%2F%2Fcfile3.uf.tistory.com%2Fimage%2F261EE95058D3A77A15F2C2)

- HDFS마스터는 슬레이브 노드 사이의 저장 공간을 분할하고 데이터 저장 위치를 관리하는 책임을 담당
- 네임노드는 어떤 데이터노드가 각 파일 블록을 관리하는지 등과 같은 파일시스템에 대한 메타데이터를 메모리에 보관
- 데이터노드는 파일 읽기 및 쓰기 파이프라인을 위해 서로 통신
- 파일은 블록으로 구성되며, 각 파일은 여러 차례 복제된다. 이는 파일의 블록별로 동일한 복사본이 여러 개 있다는 뜻이다.
- Hdfs는 장애가 발생해도 데이터를 잃지 않는 견고성, 하드웨어 추가로 성능을 향상하는 확장성, 클러스터 내의 여러 노드에 데이터분할 등의 기능이 있다.

### 하트비트(heartbeat)
- 데이터노드는 네임노드에게 하트비트를 3초마다 보낸다. 하드비트에는 디스크 가용 공간정보, 데이터이동, 적재량 등의 정보가 들어있다
- 핸드셰이킹에 사용된다. 10초이상 못받으면 사용하지 못한다고 인식한다.

## 5. 맵리듀스(MapReduce)
- 맵리듀스 모델을 사용해 애플리케이션을 작성할 경우, 하둡이 확장성과 관련된 모든 일을 알아서 처리
![맵리듀스](http://img1.daumcdn.net/thumb/R1920x0/?fname=http%3A%2F%2Fcfile1.uf.tistory.com%2Fimage%2F212E1D5058D3A77C07181E)
- Map은 데이터 원천을 key와 value의 형태로 연관성있는 데이터 분류로 묶는 작업
- Reduce는 Map화한 작업 중 중복데이터를 제거하고 원하는 데이터를 추출하는 작업
![맵리듀스과정](http://img1.daumcdn.net/thumb/R1920x0/?fname=http%3A%2F%2Fcfile22.uf.tistory.com%2Fimage%2F24076A4958D3A80B2731E7)
- 셔플링: 처리된 결과를 여러 머신에 이리저리 재배치하는 모습
- mapping단계: 데이터를 받아 각 데이터 항목을 mapper로 전달, mapper는 입력데이터를 필터링하고 reducer가 처리할 수 있는 형태로 변형
- reducing단계: mapper로부터 결과값을 받아 처리, reducer는 값을 받아 통합

### 맵리듀스 잡
![맵리듀스잡](http://img1.daumcdn.net/thumb/R1920x0/?fname=http%3A%2F%2Fcfile1.uf.tistory.com%2Fimage%2F2529FB4E58D3A7A53D948E)

- 클라이언트가 수행하려는 작업 단위
- 하둡은 job을 map task와 reduce task로 작업을 나누어서 실행
- 잡 트래커: 태스크 트래커가 수행할 task 스케줄링, 모니터링
- 태스크 트래커: Task를 수행하고, 잡트래커에게 상황 보고

#### ex. 맵리듀스 잡
![돌아라맵리듀스](http://img1.daumcdn.net/thumb/R1920x0/?fname=http%3A%2F%2Fcfile26.uf.tistory.com%2Fimage%2F262B214358D3A8B22D4DC9)