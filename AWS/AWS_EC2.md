# 아마존 웹 서비스(Amazon Web Services)

참조문서: http://www.pyrasis.com/aws.html

## 1. AWS란?
* 2006년 미국 아마존 서버의 비수기 때, 자원들이 노는 걸 가상화해서 AWS 출시
* AWS는 IT 인프라를 제공하는 IaaS(Infrastructure as a Service)라 할 수 있다.

## 2. AWS의 장점
### 2-1. 기존 서비스들의 문제점
- 호스팅 서비스: 월 단위 사용료, 모든 금액을 선불로 지급, 트래픽 초과하면 차단
- IDC 코로케이션: 매우 비싼 임대료, 서버설치하는 데도 1주쯤 걸린다.
	* 코로케이션방식: 사업자가 직접 서버를 관리하지 않고 인터넷서비스를 제공하는 사업자가 초고속인터넷 백본망에 서버를 연결해주고 관리해주는 사업형태.
- 스케일업의 한계

### 2-2. AWS의 장점
- 사용한 만큼 돈내는 종량제 방식
- 서버 증설 빠르고, 서버 폐기도 빠름
- 트래픽 그래프에 따른 스케일아웃 가능(오토스케일링)
- 마이크로서비스 아키텍처의 완벽한 지원

### 2-3. 요금정책
선순환으로 계속되는 가격인하 정책(규모의 경제가 발생되면 가격을 내린다)

![요금](http://img1.daumcdn.net/thumb/R1920x0/?fname=http%3A%2F%2Fcfile28.uf.tistory.com%2Fimage%2F2324474A594FCEC428E93C)

## 3. 기본개념
1. 리전: AWS의 모든 서비스가 위치하고 있는 물리적인 장소
	* AWS regions 핑테스트: www.cloudping.info
2. 가용 영역(AZ, Availability Zone): 데이터센터(IDC), 같은 지역 혹은 도시라 하더라도 멀리 떨어져있고, 이는 재해에 대비하기 위함이다. 리전 내 컴퓨터들은 전용선으로 연결되어 있어 매우 빠름
3. 에지 로케이션(Edge Location): CDN 서비스인 CloudFront를 위한 캐시 서버(엔드유저에게 가까운 엣지로케이션에서 정적파일들을 쏨으로써 결과적으로 반응속도 향상)

## 4. EC2(Elastic Compute Cloud)
* 가장 먼저 생긴 서비스, 가장 범용적인 서비스, 독립적인 컴퓨터 1대 제공
* 인스턴스 유형: http://www.pyrasis.com/book/TheArtOfAmazonWebServices/Chapter04/01
* 마이크로 인스턴스: 가격이 가장 싼 인스턴스입니다. 낮은 vCPU 성능과 적은 메모리를 제공합니다. 프리 티어(무료 사용 계정)에서는 이 인스턴스 유형을 무료로 사용할 수 있습니다.
* 생성하면 key파일을 잘 보관했다가 putty에 로드하기
* 비밀번호 분실시: http://www.pyrasis.com/book/TheArtOfAmazonWebServices/Chapter07/01
* PuTTY 접속: http://www.pyrasis.com/book/TheArtOfAmazonWebServices/Chapter04/04/01

AWS 리소스의 동작 현황: http://status.aws.amazon.com

보안그룹: EC2 인스턴스에 적용할 수 있는 방화벽 설정

## 5. EBS(Elastic Block Store)
- EC2 인스턴스에 장착하여 사용할 수 있는 가상 저장 장치
- 프리티어: EBS 스토리지 30GB, EBS 200만 I/O를 무료
- 기본개념: http://www.pyrasis.com/book/TheArtOfAmazonWebServices/Chapter04/05
- IOPS는 16KB 단위로 처리
- 스냅샷: EBS볼륨 목록→오른쪽 버튼→create snapshot

## 6. Elastic IP
IP주소는 기본적으로 유동(STOP시 반납, START시 할당)이지만, Elastic IP는 고정된 공인 IP를 제공, 프리티어 무료 제공, 주의할 점은 Elastic IP를 할당 받고, EC2 인스턴스에 연결하여 사용해야만 요금이 부과되지 않는다.

- NETWORK & SECURITY 메뉴 → Elastic IPs 들어가기
- 할당은 오른쪽 버튼→Associate Address