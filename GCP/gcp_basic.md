# GCP(Google Cloud Platform)

## 1. GCP 란?
구글 클라우드는 구글의 데이터센터 인프라를 기반으로 컴퓨트, 스토리지, 네트워
킹, 빅데이터, 머신러닝 등의 서비스를 제공하는 글로벌 클라우드다. 여기서는 프리티어, 가격정책과 함께 내가 사용해볼 VM, RDB, 네트워크, 파일 Storage 정도만 알아보려고한다. 아래는 내가 느낀 장단점을 대략 정리해봤다.

### GCP 장점
* 인터페이스가 직관적 깔끔한 편인듯
* 웹 브라우저에서 SSH 접속가능함 (복붙은 안됌ㅠ)
* 유료계정 업그레이드안하면 과금절대안되는 안전장치는 정말 최고!

### GCP 단점
* 한국 리전이 없다
* 크레딧과 무관한 무료제품(micro)군이 약하다. (US리전에만 혜택이 있고, 기본적인 컴퓨팅파워가 약하다. NCP같은 경우 크레딧 30만원과 무관하게 1가상코어와 1GB RAM, HDD 50GB 머신을 줌. AWS에 비해선 좋다)

## 2. 프리티어 시작하기
참조문서 : https://cloud.google.com/free/docs/frequently-asked-questions

1. Gmail 로그인 후 GCP 접속 ( https://cloud.google.com )
2. 회원가입
    * 해외결제가능한 신용카드 필요
    * 1달러 결제되었다가 취소된다
3. 가입한 날짜부터 1년동안 $300 크레딧을 준다.
4. 유료계정으로 업그레이드하지 않는 한 돈이 안나가며, 만약 크레딧을 다 쓰면 쓰고있던 리소스들이 다 중단된다. 이것은 30일까지만 보존되고 삭제처리된다.

### 2-1. 프리티어의 제약
기본적으로 크레딧 내에서 모든 지 가능하지만 컴퓨팅 리소스 할당에 제약이 있다.

* GCE 8코어 이상 인스턴스를 만들 수 없다.
* GPU 를 추가 할 수 없다.

이 제한을 해제하려면 유료 계정으로 업그레이드 해야한다. 다만 업그레이드한다고 돈 나가는게 아니라, $300 크레딧 이후에 과금에 동의한다는 것뿐이다. 잘 계산해서 $300 안넘어간다면 상관없는 거다.

### 2-2. Always Free (프리티어 상관없이 무료 서비스)
참조문서 : https://cloud.google.com/free/docs/always-free-usage-limits

1. App Engine
    * 28 인스턴스 시간/일
    * Cloud Storage 5GB
    * 송신 1GB/일
    * 검색 작업 1,000회/일, 10MB 검색 색인 생성
2. Cloud Datastore (NoSQL)
    * 저장소 1GB
    * 읽기 50,000개, 쓰기 20,000개, 삭제 20,000개
3. Compute Engine
    * US 리전에서만 가능
    * f1-micro 인스턴스
    * HDD 30GB/월, 스냅샷 5GB/월
    * US 외 네트워크 송신 1GB/월
4. Cloud Storage
    * US 리전에서만 가능
    * Regional Storage 5GB/월
    * US 외 네트워크 송신 1GB/월
5. Google Cloud Functions
    * 호출 200만/월
    * 네트워크 송신 5GB/월

## 3. Compute Engine(GCE)
### 3-1. VM 인스턴스 생성
* 이름
* 영역 : 도쿄
* 머신 유형
* 부팅디스크 : SSD, 10기가 이상
* 방화벽 HTTP, HTTPS 허용

### 3-2. 터미널 접속 (putty, windows 10 기준)
참조문서
* https://cloud.google.com/compute/docs/instances/adding-removing-ssh-keys
* https://cloud.google.com/compute/docs/instances/connecting-advanced

#### 키페어 만들기
1. PuTTY Key Generator 실행
2. Generate
3. key comment 에 user name 적기
4. GCE - 메타데이터 - SSH 키 - 수정 버튼 클릭
5. private key 저장
6. public key 등록
7. PuTTY 로 Connection - SSH - Auth - Private key file 업로드
8. user name 적고 login 끝

### 3-3. root 권한 접속
스터디용(SFTP로 vscode 등으로 수정하고 업로드할때 권한막히면..)으로 쓸때 sudo 치기 귀찮으면 root를 뚫자 (centos7 기준)

* sudo passwd 로 root 비밀번호 설정
* sudo vi /etc/ssh/sshd_config 에서 PermitRootLogin 을 yes로 바꿔준다.
* systemctl restart sshd
* 일반계정에서 su root 와 같은 방식으로 root 로 로그인한다.

### 3-4. 가격 정책
* 중지를 해도 스토리지 비용은 계속 나간다
* 삭제는 완전히 사라지는 것임

### 3-5. 선점형 VM
참조문서 : https://cloud.google.com/compute/docs/instances/preemptible

선점형 VM 여부를 설정할 수 있다. 이것은 AWS의 Spot Instance 와 비슷한 개념으로 짧은 시간에 컴퓨팅 리소스가 많이 필요할 때 매우 싸게 사용할 수 있는 방법이다. (다른점 : https://www.quora.com/What-are-the-key-differences-between-AWS-Spot-Instances-and-Googles-Preemptive-Instances )

선점형 VM은 인스턴스를 24시간 후에 종료될 수 있다는 것과 그전에도 구글이 인스턴스를 종료할 수 있다는 것에 동의해야 사용할 수 있다. AWS와는 다르게 다시 시작하지않고, 개발자가 다시 켜줘야한다. 구글이 종료하기 30초전에 알림을 준다는 것 같다. 대신 엄청 싸게 이용가능하다

## 4. Google Cloud Storage(GCS)
클라우드를 위해서 제공되는 Object Storage 이며 AWS의 S3 와 유사하게 보시면 되며 문서든 이미지든 다양한 형태의 파일을 저장한다.

### 4-1. Storage 종류
비싼 순서이다.

1. Multi-Regional Storage : 다중 지역 저장소는 웹 사이트 콘텐츠, 대화 형 작업 부하 또는 모바일 및 게임 응용 프로그램을 지원하는 데이터와 같이 자주 액세스되는 데이터에 적합
2. Regional Storage : 지역 저장소는 데이터를 사용하는 GCE와 동일한 지역 위치에 데이터를 저장하는 데 적합
3. Nearline Storage : 자주 액세스하지 않는 데이터를 저장에 적합
4. Coldline Storage : 데이터 아카이빙, 온라인 백업 및 재해 복구를위한 매우 저렴한 비용의 내구성 높은 서비스

### 4-2. 가격
https://cloud.google.com/storage/pricing

## 5. Google Cloud SQL
MySQL 및 PostgreSQL 데이터베이스 서비스이다.

### 5-1. MySQL 인스턴스 유형
* 1세대 : 500GB까지의 용량까지 지원, 5.6 버전
* 2세대 : 10TB까지 지원, 5.7 버전도 지원

### 5-2. MySQL 인스턴스 유형별 스펙 및 가격
https://cloud.google.com/sql/docs/mysql/pricing

## 6. 공인 IP 설정
공인 IP 주소가 있으면 연결된 프로젝트 리소스를 다른 GCP 네트워크 또는 인터넷에서 사용할 수 있다.
VPC 네트워크 - 외부 IP 주소에 들어간다.

VM 인스턴스가 있으면 고정 셀렉트박스를 눌러서 할당한다.

### 6-1. 가격
https://cloud.google.com/network-tiers/pricing