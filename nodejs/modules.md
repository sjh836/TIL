# node의 기본모듈
참조문서 : https://nodejs.org/dist/latest-v6.x/docs/api/

node에서 모듈은 다른 언어의 라이브러리에 해당한다. 이 포스팅 당시 버전은 v6.11.0 이다. 

### API 문서가 잘되어 있다~
API의 안정도(stability)

* 0 : deprecated
* 1 : Experimental
* 2 : Stable
* 3 : Locked , 더이상 변경없음

## 1. node의 모듈
### 1-1. 모듈의 로딩
절대경로 혹은 상대경로 이용

`require(모듈명);`

### 1-2. 모듈의 종류
* 기본 모듈 : 미리 컴파일된 상태로 제공되는 라이브러리 디렉터리
* 확장모듈 : npm으로 별도설치, node_modules

### 1-3. 모듈 함수
객체 생성없이 모듈에 직접 사용하는 함수다.

### 1-4. 주요 기본 모듈
#### 1-4-1. 프로세스환경
os, process, cluster

#### 1-4-2. 파일, 경로, URL
fs, path, URL, querystring, stream

#### 1-4-3. 네트워크 모듈
http, https, net, dgram, dns

## 2. 전역 객체(global)
전역 네임스페이스 객체다. 전역 속성 및 메소드에 대한 접근을 제공한다.

별도의 모듈 로딩없이 사용가능하다. global 모듈은 생략이 가능하다. 예를들면 console은 global모듈안에 있어서, 원래는 global.console.log() 이지만, console.log()로 생략가능하다.

전역변수를 선언 `var test = 'hello';` 하고 global을 찍어보면 맨 밑에 `test : 'hello'` 가 들어가 있다.

직접 콘솔에서 global 등등을 찍어보며, 무엇이 있는지 확인해보면 정말 굿이다.

### 2-1. process
각 node 어플리케이션은 process 개체 인스턴스이다. process 개체의 메소드 및 속성 대부분은 어플리케이션 및 환경에 대한 식별과 정보를 제공한다. 주요한 것만 정리해보면,

#### 실행 정보
* env : 어플리케이션 실행 환경
* version : node.js 버전
* arch, platform : CPU와 플랫폼 정보
* argv : 실행 명령 파라미터
	* node 소스.js 인자1 인자2
	* process.argv[2] = 인자1
	* process.argv[3] = 인자2

#### 이벤트
* exit : 어플리케이션 종료 이벤트
* beforeExit : 종료되기 전에 발생하는 이벤트
* uncaughtException : 예외처리되지 않은 예외 이벤트

#### 함수
* exit : 코드를 이용해 어플리케이션을 종료
* nextTick : 이벤트 루프 내 동작을 모두 실행 후 콜백 실행

### 2-2. 타이머
정확하게 n초 후 타이머가 돌아간다는 보장은 없다. 브라우저에서 하는 거랑 똑같다.

#### 지연동작
* setTimeout(callback=함수, delay=밀리초, arg...=콜백함수의 파라미터)
* clearTimeout : 타이머 취소

#### 반복동작
* setInterval(callback=함수, delay=밀리초, arg...=콜백함수의 파라미터)
* clearInterval : 타이머 취소

### 2-3. 콘솔
* 로그 남기기
	* console.info() : stdout
	* console.log() : stdout
	* console.warn() : stderr
	* console.error() : stderr
	* 객체를 찍어보려면 'obj :',obj

* 커스텀 콘솔 예제(파일로 로그남기기)

		var output = fs.createWriteStream('./stdout.log');
		var errorOutput = fs.createWriteStream('./stderr.log');
		var logger = new Console(output, errorOutput);

* 실행시간 측정
	* 시작시점 : console.time(TIMER_NAME);
	* 종료시점 : console.timeEnd(TIMER_NAME);
	* TIMER_NAME 만 똑같으면 된다. ms단위로 찍힘

### 2-4. 버퍼
JS는 바이너리 데이터를 다루는 기능이 없다. node에서는 Buffer를 통해 이진데이터를 다룬다.

파일에서 이진데이터를 얻을 수도 있고, 네트워크에서 소켓을 통해 얻을 수도 있다.

아예 새로운 버퍼를 만드려면, new Buffer()를 통해 만든다. size가 정해지면 바꿀수 없다.

#### 모듈 함수
* 바이트 길이 : Buffer.byteLength(string[, encoding])
* 비교 : Buffer.compare(buf1, buf2)
* 붙이기 : Buffer.concat(list[, totalLength])
* 버퍼 확인 : Buffer.isBuffer(obj)
* 인코딩 : Buffer.isEncoding(encoding)

#### 버퍼 객체에서 제공해주는 기능
* 길이 : buf.length
* 채우기 : buf.fill(value[, offset][, end])
* 자르기 : buf.slice([start[, end]])
* 비교하기 : buf.compare(otherBuffer)
* 복사하기 : buf.copy(targerBuffer[, targetStart][, sourceStart][, sourceEnd])

#### 문자열 버퍼
* 버퍼 생성 : new Buffer(str[, encoding])
* 버퍼에 문자열 쓰기 : buf.write(string[, offset][, length][, encoding])
* 변환 : buf.toString([encoding][, start][, end])

#### 데이터 읽기,쓰기
##### 8비트 읽고 쓰기
* buf.readInt8(offset[, noAssert])
* buf.writeInt8(value, offset[, noAssert])

##### 16비트 읽고 쓰기
LE=리틀엔디안, BE=빅엔디안(require('os').endianness() 로 판별가능)

* buf.readUInt16LE(offset[, noAssert])
* buf.writeUInt16LE(value, offset[, noAssert])

##### float형 읽고 쓰기
* buf.readFloatLE(offset[, noAssert])
* buf.readFloatBE(offset[, noAssert])
* buf.writeFloatLE(value, offset[, noAssert])
* buf.writeFloatBE(value, offset[, noAssert])

## 3. 유틸리티
전역객체가 아니므로, `require('util')`을 해야한다. isXxx()들은 전부 deprecated 되었다.

* 문자열 포맷 : util.format(placeholder, ...)
	* placeholder : %s = string, %d = number, %j : json
	* ex. util.format('%s = %d', 'one', 1);
* 상속 : util.inherits(constructor=자식, superConstructor=부모)
	* prototype 상속을 연결해준다.

## 4. 이벤트
`require('events')`가 필요하다

EventEmiiter : 이벤트를 다루는 기능 제공.

### 4-1. 이벤트 등록
* emitter.addListener(이벤트명, 리스너함수)
* emitter.on(이벤트명, 리스너함수)
* emitter.once(이벤트명, 리스너함수) : 단 한번만 동작

### 4-2. 이벤트 삭제
* emitter.removeListener(이벤트명, 리스너함수)
* emitter.removeAllListener(이벤트명)

### 4-3. 이벤트 핸들러 갯수
* emitter.setMaxListeners(n)
* emiiter.getMaxListeners()

### 4-4. 이벤트 강제로 발생시키기
* emitter.emit(이벤트명, 리스너함수의 파라미터들...) : 처리성공(true), 실패(false)

### 4-5. EventEmitter 객체에서 커스텀 이벤트
EventEmitter 객체를 new한 객체나 상속받은 객체에서는 커스텀 이벤트를 만들 수 있다.

	var event = require('events');
	var customEvent = new event.EventEmitter();
	
	customEvent.on('tick', function() {
	    console.log('new event');
	});
	
	customEvent.emit('tick');

### 4-6. 이벤트 에러처리
대부분 이벤트리스너 함수는 function(error, param) 이다. 따라서 첫번째 파라미터를 이용하여 if(error) 로 분기처리하면 좋다.

## 5. 경로
`require('path')`을 해야한다. 현재 경로정보는 전역객체의 __filename과 __dirname을 통해 알 수 있다.

### 5-1. 경로 추출
* path.basename(경로) : 파일 이름, 경로 중 마지막 요소
* path.dirname(경로) : 파일이 포함된 폴더 경로
* path.extname(경로) : 확장자
* path.parse(경로) : 파싱해서 객체로 만들어줌(root, dir, base, ext, name이 속성으로 저장)

### 5-2. 경로 생성
* pathUtil.sep : 윈도우와 리눅스의 디렉터리구분자가 틀리니, 이걸쓰면 된다
* pathUtil.join(디렉터리, 디렉터리..) : 경로 붙이기
* pathUtil.format(parse의 반대) : root, dir, base, ext, name을 조합하여 경로를 만든다.

## 6. 파일시스템
`require('fs')`으로 쓰며, 모든 플랫폼에 100% 호환되지 않는다. 동기방식과 비동기방식 함수를 모두 제공해준다.

동기방식 함수는 이름끝에 Sync이 붙으며, 반환값을 이용한다.

* 파일 생성, 읽기, 쓰기, 삭제
* 파일 접근성, 속성
* 디렉터리 생성, 읽기, 삭제
* 파일 스트림

### 6-1. 파일읽기
#### 파일 디스크립터 얻기
* var fd = fs.openSync(path, flags..)
* fs.open(path, flags.., callback)

flags에는 r(읽기), w(쓰기), a(추가) 등이 있다.

#### 파일 디스크립터를 통한 읽기
buffer에다 저장하는데, offset(주로 0)부터 position(buffer.length)까지 읽는다.

* fs.read(fd, buffer, offset, length, position, callback)
* fs.readSync(fd, buffer, offset, length, position)

#### 파일 경로를 통한 읽기
문자열이면 utf-8로 인코딩도 파라미터로 줘야한다.

* fs.readFile(filename[,option], callback)
* fs.readFileSync(filename[,option])

#### 파일 닫기
* fs.close(fd, callback) : 콜백에 주로 `function(err) {}`
* fs.closeSync(fd)

### 6-2. 파일상태 확인
파일 다루기 전에 상태확인이 가능하다. 상태에 따라서 에러를 발생할 수 있다.

#### access()를 통한 확인
* fs.access(path[,mode], callback)
* fs.accessSync(path[,mode])

mode에는 아래 종류가 있다.

* fs.constants.F_OK : 존재 확인
* fs.constants.R_OK, W_OK, X_OK : 읽기,쓰기,실행 여부 확인

#### stat()을 통한 확인
* fs.stat(path, callback)
* fs.statSync(path)

아래와 같은 것들을 확인할 수 있다.

* 파일 여부 : stats.isFile()
* 디렉터리 여부 : stats.isDirectory()
* 파일크기 : stats.size
* 생성일, 접근, 수정일 : stats.birthtime, stats.atime, stats.mtime

### 6-3. 파일에 쓰기
data에는 문자열 혹은 buffer가 들어가고, 문자열이면 인코딩을 넣어줘야 한다. 만약 같은 파일 이름이면 덮어쓴다. 파일이 없으면 새 파일을 생성한다.

* fs.write(fd, data[, position[, encoding]], callback)
* fs.writeFile(filename, data[,options], callback)
* fs.writeFileSync(filename, data[, options])

#### 기존 파일에 내용 추가
* fs.appendFile(file, data[,options], callback)
* fs.appendFileSync(file, data[,options])

### 6-4. 파일 삭제
파일이 없으면 에러뜬다.

* fs.unlink(path, callback)
* fs.unlinkSync(path)

### 6-5. 파일이름 변경
* fs.rename(oldPath, newPath, callback)
* fs.renameSync(oldPath, newPath)

### 6-6. 디렉터리 생성
같은 이름의 디렉터리명있으면 실패

* fs.mkdir(path[, mode], callback)
* fs.mkdirSync(path[, mode])

### 6-7. 디렉터리 삭제
디렉터리가 비어있지 않으면 실패

* fs.rmdir(path, callback)
* fs.rmdirSync(path)

### 6-8. 디렉터리 내용 보기
디렉터리가 없으면 에러

* fs.readdir(path, callback)
* fs.readdirSync(path)

### 6-9. 파일 스트림
* fs.createReadStream(path[,options])
* fs.createWriteStream(path[,options])

## 7. 스트림
콘솔 입출력, 파일 읽기쓰기, 서버와 클라이언트간 데이터 송수신도 전부 스트림이다.

* 스트림을 다루기 위한 추상 인터페이스
* 다양한 스트림을 같은 인터페이스로 다룰 수 있다

### 7-1. 스트림의 종류
* 읽기 스트림 : Readable Stream
* 쓰기 스트림 : Writable Stream
* 읽기/쓰기 : Duplex
* 변환 : Transform

### 7-2. 표준 입출력 스트림
* process.stdin : 콘솔 입력
* process.stdout : 콘술 출력

### 7-3. Readable Stream
#### readable 메소드
* 읽기 : readable.read([size])
* 인코딩 : readable.setEncoding(encoding)
* 중지 : readable.pause()
* 재개 : readable.resume()
* 파이프 : readable.pipe(destination[, options]), readable.unpipe([destination])

#### readable 이벤트
* readable : 읽기 가능한 상태
* data : 읽을 수 있는 데이터 도착
* end : 더 이상 읽을 데이터가 없는 상태
* close : 스트림이 닫힌 상태
* error : 에러

#### flowing모드
* 데이터를 자동으로 읽는 모드
* 전달되는 데이터를 다루지 않으면 데이터 유실

##### flowing 모드 구현
* data 이벤트를 구현
* pipe 연결
* resume() 호출

#### paused 모드
* 데이터가 도착하면 일단 대기하고 read()를 통해 읽음. data이벤트가 없으면 paused 모드이다.

### 7-4. Writable Stream
#### 사용하는 상황들
* http 클라이언트의 요청
* http 서버의 응답
* 파일 쓰기 스트림
* tcp 소켓

#### writable 메소드
* 인코딩 : writable.setDafaultEncoding(encoding)
* 데이터 쓰기 : writable.write(chunk[, encoding][, callback]
* 스트림 닫기 : writable.end([chunk][, encoding][, callback])
* 버퍼 : writable.cork(), writable.uncork()

#### writable 이벤트
* drain : 출력 스트림에 남은 데이터를 모두 보냄
* error : 에러
* finish : 모든 데이터를 쓴 이벤트
* pipe : 읽기 스트림과 연결(pipe)된 이벤트
* unpipe : 연결(pipe) 해제 이벤트

## 8. URL
`require('url')`으로 쓴다.

### 8-1. URL의 구성요소
* 프로토콜
* 호스트
* 포트번호
* 경로
* 쿼리
* 프래그먼트

### 8-2. URL 메소드
* 파싱 : url.parse(urlStr[, parseQueryString][, slashesDenoteJHost])
	* urlStr : URL 문자열
	* parseQueryString : 쿼리 문자열 파싱, 기본값은 false
	* slashesDenoteJHost : //로 시작하는 주소의 경우, 호스트 인식여부. 기본값은 false
	* **쿼리 문자열** : 이름=값&이름=값&... 형태로 정보 전달, url.parse('...', true); 로 파싱 가능
* 만들기 : url.format(urlObj)
	* protocol : 프로토콜
	* host : 서버 호스트 주소
	* pathname : 경로
	* search : 쿼리 문자열
	* auth : 인증 정보
* 변환 : url.resolve(from, to)

### 8-3. URL 인코딩
제한된 문자열만 사용가능하며, 한글같은 경우 인코딩 해줘야 한다. 써드파티 모듈인 urlencode 를 통해 가능.

* URL에 허용되는 문자 : 알파벳, 숫자, 하이픈, 언더스코어, 점, 틸드

## 9. 쿼리 문자열
`require('querystring')`으로 쓴다. 쿼리 문자열은 URL 외에도 사용된다. HTTP 메세지 바디로 정보를 전달할 때도 사용한다.

이름=값 에서 이름이 같으면 자동으로 배열로 만들어준다.

### 9-1. querystring 메소드
* 파싱 : querystring.parse(str[, sep][, eq][, options])
sep, eq : 디폴트는 쿼리 구분자(&)와 = 기호
* 만들기(인코딩 자동) : querystring.stringify(obj[, sep][, eq][, options])

## 10. 클러스터
`require('cluster')`으로 쓴다. 멀티프로세스, 멀티코어를 쓸 수 있다. nodejs 어플리케이션은 1개의 싱글쓰레드로 돌아가다보니, 쿼드코어여도 25%밖에 안쓴다. 클러스터 사용 시, 포트를 공유한다. 코어의 갯수만큼 사용할 수 있다.

클러스터링 기능 지원 프로세스 모듈로는 pm2 가 있다.

### 10-1. cluster 메소드 및 속성
* 마스터 : 메인 프로세스
	* 워커 생성 : cluster.fork()
	* 확인 : cluster.isMaster
* 워커 : 보조 프로세스, 마스터가 생성해줌
	* 확인 : cluster.isWorker
	* 접근 : cluster.worker
	* 식별자 : worker.id
	* 종료 : worker.kill([signal='SIGTERM']) 하면 마스터한테 disconnect와 exit 이벤트 발생
* 마스터-워커 간 데이터 송수신
	* worker.send(data) : 마스터가 워커에게 데이터 전달, worker는 message 이벤트 발생
	* process.send(data) : 워커가 마스터에게 데이터 전달

### 10-2. cluster 이벤트
#### 클러스터의 이벤트
* fork : 워커 생성 이벤트
* online : 워커 생성 후 동작하는 이벤트
* listening : 워커에 작성한 서버의 listen 이벤트
* disconnect : 워커 연결 종료
* exit : 워커 프로세스 종료

#### 워커의 이벤트
* fomessage : 메세지 이벤트
* disconnect : 워커 연결 종료

### 10-3. 마스터와 워커 파일 분리
	cluster.setupMaster({
	    exec: 'worker.js'
	});
	cluster.fork();

* cluster.setupMaster([settings])
	* exec : 워커 파일
	* args : 실행 파라미터