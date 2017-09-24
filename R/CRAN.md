# CRAN

CRAN은 the Comprehensive R Archive Network의 약자이다. R은 사람들이 제작한 패키지를 추가하여 기능을 확장할 수 있다. R에는 방대한 R Add-On Packages 들이 있다. 

RGui에서 Packages 메뉴를 통해 확인할 수 있다. 웹에서는 [CRAN Task Views](https://cran.r-project.org/web/views/) 페이지에서 확인할 수 있다. 각 패키지들은 대분류로 Cluster, Finance, NLP, TimeSeries 등으로 나뉘어져 있다. 들어가보면 CRAN packages 리스트를 통해 해당 패키지에 속한 라이브러리들을 볼 수 있다.

## 1. 패키지 확인
* 설치 위치 : `.libPaths()`
* 설치된 패키지 확인 : `installed.packages()`
* 패키지 도움말 : `library(help="패키지명")`

## 2. 패키지 관리
만약 복수의 패키지를 관리하고 싶다면 문자열 백터를 이용하면 된다. `c("패키지1", "패키지2")` 이런식으로..

### 2-1. 패키지 설치
`install.packages("패키지명")`

### 2-2. 패키지 업데이트
`update.packages("패키지명")` 을 통해 해당 패키지를 업데이트할 수 있다. 만약 인자를 주지않는다면 전체 패키지는 업데이트한다.

### 2-3. 패키지 로드
`library(패키지명)` 을 통해 해당 패키지를 load한다. 이 후 패키지 내 함수를 사용할 수 있다. `library()` 만 쓰면 사용가능한 패키지 리스트들을 볼 수 있다.

### 2-4. 패키지 삭제
`remove.packages("패키지명")`

## 3. 패키지 분류
|                      	| Install                 	| Load           	|
|----------------------	|-------------------------	|----------------	|
| Base Packages        	| 내장                    	| 항상 Load됨    	|
| Recommended Packages 	| 내장                    	| library() 필요 	|
| Other Packages       	| install.packages() 필요 	| library() 필요 	|

여기서 내장이란 R을 설치할 때 같이 배포된 것을 뜻하며, Load는 패키지를 사용하기 위해 library()를 해야하냐마냐의 문제다.

## 4. Base Packages 리스트
* base : Base R 함수들
* compiler : R 바이트 코드 컴파일러
* datasets : Base R 데이터셋들
* grDevices : base와 grid 그래픽스를 위한 그래픽 장치들
* graphics : base 그래픽스를 위한 R 함수들
* grid : 그래픽스 레이아웃 성능을 재작성하고 인터랙션을 위한 일부 지원이 추가됨
* methods : 그린북에 설명되어 있는 것과 같이 R 객체들에 대하여 형식적으로 정의된 메소드들과 클래스들, 그리고 다른 프로그래밍 툴들
* parallel : forking, 소켓, 그리고 난수생성 기능을 포함한 병렬 연산을 지원
* splines : regression spline 함수들과 클래스들
* stats : R 통계관련 함수들
* stats4 : S4 클래스들을 이용한 통계 함수들
* tcltk : Tcl/Tk GUI 구성요소들에 인터페이스와 language 바인딩
* tools : 패키지 개발과 관리를 위한 도구들
* utils : R 유틸리티 기능

## 5. Recommended Packages 리스트
* KernSmooth : Kernel Smoothing 이란 책을 바탕으로 한 함수들(그리고 밀도추정)을 포함
* MASS : Venables 와 Ripley의 핵심 패키지 Modern Applied Statistics with S 으로부터의 함수와 데이터셋들을 포함
* Matrix : 행렬 패키지
* boot : bootstrapping 에 이용된 함수들과 데이터셋들을 포함
* class : classification 에 사용되는 함수들을 포함
* cluster : 클러스터 분석를 위한 함수들이 포함
* codetools : 코드 분석 도구들이 포함
* foreign : Minitab, S, SAS, SPSS, Stata, Systat 등과 같은 통계 소프트웨어에 의해 저장된 데이터를 읽고 쓰는데 필요한 함수들이 포함
* lattice : Trellis 그래픽 함수들의 구현체인 lattice 그래픽스가 포함
* mgcv : GCV 또는 UBRE을 이용한 multiple soomthing parameter selection을 포함한 GAM 그리고 다른 generalized ridge regression 문제들을 위한 루틴들이 포함
* nlme : Gaussian 선형과 비선형의 혼합 모델들(linear and nonlinear mixed-effects models)을 적합하고 비교하는 함수들이 포함
* nnet : Single hideen perceptrons(feed-forward neural networks)와 다항로그선형모델(multinomial log-linear models)을 위한 소프트웨어
* rpart : 재귀적 파티셔닝(recursive partioning)과 regression trees를 수행하는 함수들이 포함
* spatial : Modern Applied Statistics with S 이란 책으로부터 kriging and point pattern analysis 를 위한 함수들이 포함
* survival : 벌점가능성 함수(penalized likelihood)함수를 포함한 생존분석(survival analysis)에 필요한 함수들을 포함