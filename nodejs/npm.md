# NPM

참조문서 : https://docs.npmjs.com/

NPM(Node Package Manager)은 패키지 매니저로 확장모듈 설치, 삭제, 검색, 정보, 패키지 정보 작성 등을 할 수 있다. 노드의 모듈시스템은 상호운용이 가능하도록 모듈을 생성하는 방법인 CommonJS 모듈 시스템을 본 따 만들었다.

## 1. 주요 옵션
* npm init : 패키지 준비, package.json 생성
* npm install : 패키지 정보에 있는 모듈 설치
* npm install --production : devDependencies 에 있는 거 빼고 설치
* npm install [Module] : 개별 패키지 설치
* npm install 패키지명 --save : 개별 패키지를 설치하고 의존성 정보를 package.json에 기록
* npm install 패키지명 --save-dev : 개발할때만 쓰는 의존성 정보를 package.json에 기록
* npm list : 설치된 모듈 목록 보기
* npm info : 모듈 정보
* npm search : 모듈 검색
* npm update : 모듈 업데이트
* npm uninstall : 모듈 삭제

## 2. 모듈 설치
의존성있으면 같이 설치한다.

`npm install [Module][@Version]`

### 전역 설치
* 한번 설치로 모든 프로젝트에서 사용 가능
* 라이브러리 폴더 lib/node_modules 에 저장
* 권리자 권한 필요 -g

### 프로젝트에 설치
* 프로젝트마다 설치
* 현재 폴더 내 node_modules 에 저장

## 3. 패키지 정보
package.json 는 패키지에 대한 정보를 갖고있다. 모듈을 설치할때 --save 옵션을 넣으면 자동으로 패키지 정보에 기록한다.

* devDependencies : 개발중인 내 프로젝트가 어떤 모듈에 의존성이 있을 때 기록해준다.

## 4. 모듈 만들기
소스 코드 분리에 모듈이 효과적이다. `./`를 쓰지 않으면 lib/node_modules에서 찾는다.

* 모듈 작성 : module.exports
* 모듈 로딩 : require('./sample.js')

예를들면

	// exprots.js
	module.exports.sayHello = function(args) {
		console.log('hello ' + args);
	}
	
	// main.js
	var say = require('./exports.js');
	
	say.sayHello('test');

객체의 예제

	// exprots.js
	var student = {
		hour : 0,
		study : function() {
			this.hour++;
			console.log(this.hour + '시간 공부함');
		}
	};
	
	module.exports = student;
	
	// main.js
	var student = require('./exports.js');
	
	student.study();
	console.log(student.hour);

모듈은 캐쉬된다. require를 2번해도 하나의 객체에 캐쉬된다. 예를 들어, 위의 예제에서

	// main.js
	var student1 = require('./exports.js');
	student1.study();

	var student2 = require('./exports.js');
	student2.study();

하면 각각 1이 찍힐 것 같지만, 1과 2가 찍힌다.