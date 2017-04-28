# Python 개발환경

## 1. 개발환경 구축하기
- Python: 3.5.2
- Anaconda: 4.2.0(Anaconda3-4.2.0-Windows-x86_64.exe)
- IDE: pycharm professional 2017.1

### Anaconda
Anaconda는 Python 기반의 개방형 데이터 과학 플랫폼입니다. Anaconda의 오픈 소스 버전은 Python 및 R의 고성능 배포이며 데이터 과학을 위해 가장 많이 사용되는 Python, R 및 Scala 패키지 중 100 개 이상을 포함합니다.
또한 아나콘다에 포함 된 유명한 패키지 인 의존성 및 환경 관리자 인 conda를 사용하여 쉽게 설치할 수있는 720 개가 넘는 패키지에 액세스 할 수 있습니다. 

참조: http://dwfox.tistory.com/67

Anaconda 설치: https://repo.continuum.io/archive/index.html

### pycharm 학생 라이센스
www.jetbrains.com/student 에서 대학교 이메일로 인증하면 된다

## 2. 프로젝트 생성
New Project→위치잡고 인터프리터는 가상환경을 쓰는 것이 좋다. (Create Conda Env)

### 가상환경
여러 프로젝트에서 수많은 패키지와 의존성의 문제가 있는데, window기준 Program Files\Anaconda3에 전부 설치하면 모든 파이썬 패키지에 영향을 준다. 따라서 virtualenv(가상환경)을 구축해서(Program Files\Anaconda3\envs에 복사된다) 사용하면 깔끔하게 사용할 수 있다.