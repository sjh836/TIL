# PM2 모듈

참조문서: http://pm2.keymetrics.io/docs/usage/pm2-doc-single-page/

PM2는 Process Manager 2로서, Nodejs 프로세스를 관리해주는 역할을 한다. 노드 어플리케이션이 계속 run할수 있도록 해준다.

보통 `npm install pm2 -g` 로 서버에 전역설치하는 경우가 많다. 

## 기본 명령어
* 실행 : pm2 start (소스.js)
* 중단 : pm2 stop (id)
* 실행목록 : pm2 list
* 목록삭제 : pm2 delete (id)
* 재시작 : pm2 restart
* 정보보기 : pm2 show (id)
* 종료없이 리로드 : pm2 reload
* 모니터링 : pm2 monit

## 모니터링 기능
로그는 아래와 같은 경로에서 확인 가능하다.

* error log path : /home/유저ID/.pm2/logs/app-error-0.log
* out log path : /home/유저ID/.pm2/logs/app-out-0.log

#### 관련 명령어
* 화면 진입 : pm2 monit
* 로그 보기 : pm2 logs ['all'|'PM2'|app_name|app_id] [--err|--out] [--lines ] [--raw] [--timestamp [format]]
* 로그 삭제 : pm2 flush

## 클러스터 모드
node에서도 cluster 내장모듈이 있지만, pm2를 쓰는게 더 효율적이다. 오토스케일링, 로드밸런싱을 자동으로 해준다. 명령어를 입력하면 mode가 cluster로 동작한다.

`pm2 start 소스.js -i 0` 0은 자동으로 cpu 코어 갯수만큼 인스턴스를 생성하는 것을 의미한다.

## JSON으로 실행옵션 관리
json에 실행설정을 잡아두고, pm2 start config.json을 통해서도 실행할 수 있다. 다양한 인자는 문서를 참조하자

	{
	  "apps": [{
	    "name": "FirstApp",
	    "script": "index.js",
	    "watch": false,
	    "env": {
	      "NODE_ENV": "production"
	    },
	    "exec_mode": "cluster",
	    "instances": 0
	  }]
	}
