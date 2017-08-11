# Node의 흐름 제어

## 1. 중첩 콜백
### 콜백지옥에 빠지기 쉬운 상황

* task1 실행 이후에 task2,3,4 등이 순차적 실행
* task1 실행 결과를 이용해 task2 가 실행

예제로 본다면 아래와 같은 상황이다.

	task1(a, b, function(err, result1) {
		task2(c, function(err, result2) {
			task3...
				task4...
		});
	});

## 2. Async
참조문서 : https://caolan.github.io/async/docs.html

외부모듈이기 때문에 `npm install async`로 설치한다.

### 행위 순서 제어
* series(tasks[, callback]) : 각 태스크는 함수이고, 중간에 에러가 나면 다음태스크를 실행하지 않고, 완료 콜백으로 에러를 전달한다. results에는 각 태스크의 결과가 배열로 전달된다.

		function task1(callback) {
			console.log('task1 message');
			callback(null, 'result1');
		}
		function task2(callback) {
			console.log('task2 message');
			//callback(null, 'result2'); //정상
			callback('fail', null); //에러
		}
		function task3(callback) {
			console.log('task3 message');
			callback(null, 'result3');
		}
		async.series([task1, task2, task3], function(err, results) {
				if (err) {
					console.log('error : ' + err);
				} else {
					console.log('task finish');
					console.log(results);
				}
			}
		);
* seriesEach
* waterfall(tasks[, callback]) : 태스크로 정보를 전달하려고 할때 사용하며, 다음 태스크로 전달할 값을 콜백의 파라미터로 준다. 태스크 함수의 파라미터로 이전 태스크의 값을 전달한다. results에는 마지막 태스크가 넘겨준 값만 갖고있는다.

		async.waterfall([
			function task1(callback) {
				console.log('task1 message');
				callback(null, 'result1');
			},
			function task2(arg, callback) {
				console.log('task2 message' + arg);
				callback(null, 'result2-1', 'result2-2');
			},
			function task3(arg1, arg2, callback) {
				console.log('task3 message' + arg1 + arg2);
				callback(null, 'result3');
			}
			],
			function (err, results) {
				console.log('task finish');
				console.log(results);
			}
		);
* parallels(tasks[, callback]) : 여러 태스크를 동시에 실행한다. 모든 태스크를 마치면 완료 콜백이 실행된다.

		console.time('PARALLEL_TIMER');
		
		async.parallel([
			function task1(callback) {
				console.log('task1 start');
				setTimeout(function() {
					console.log('task1 end');
					callback(null, 'result1');
				}, 300);
				console.log('task1 message');
			},
			function task2(callback) {
				console.log('task2 start');
				setTimeout(function() {
					console.log('task2 end');
					callback(null, 'result2');
				}, 500);
				console.log('task2 message');
			},
			function task3(callback) {
				console.log('task3 start');
				setTimeout(function() {
					console.log('task3 end');
					callback(null, 'result3');
				}, 200);
				console.log('task3 message');
			}
			],
			function (err, results) {
				console.log('task finish');
				console.log(results);
				console.timeEnd('PARALLEL_TIMER');
			}
		);

결과는 아래와 같다.

	task1 start
	task1 message
	task2 start
	task2 message
	task3 start
	task3 message
	task3 end
	task1 end
	task2 end
	task finish
	[ 'result1', 'result2', 'result3' ]
	PARALLEL_TIMER: 530.118m

### 콜렉션
콜렉션 내 각 항목을 사용하는 비동기 동작이다. 예를 들어 다수 파일을 비동기로 읽었을 때, 전부 다 읽었다는 걸 콜백으로 처리해주는 것이다.

* each(arr, iterator[, callback])
* forEachOf
* map, filter

## 3. Promise
참조문서 : https://www.promisejs.org/api/

비동기 동작의 흐름 제어할 때 사용한다. JavaScript ES6에 추가되었기 때문에, node에도 당연히 사용가능하다.

### promise의 상태
* pending : 동작 완료 전, 여기서 성공과 실패가 된다.
* fullfilled : 비동기 동작 성공
* rejected : 동작 실패

### promise 객체 생성 및 상태반영
	new Promise(function (fullfill, reject) {
		//비동기 동작
		if (err)
			reject(err);
		else
			fullfill(result);
	});

### promise 이후의 동작 : then
성공과 실패일 때 각각 콜백처리 한다.

	new Promise(task).then(fullfilled, rejected);
	function fullfilled(result) {}
	function rejected(err) {}

보통 task()의 return으로 Promise 객체를 리턴한다. 성공 실패 비동기 처리할 수 있다.

	function task(fullfill, reject) {
		console.log('task start');
		setTimeout(function() {
			console.log('task end');
			fullfill('task success');
		}, 300);
	}
	
	function fullfilled(result) {
		console.log('fullfilled : ', result);
	}
	
	function rejected(err) {
		console.log('rejected : ', err);
	}
	
	new Promise(task).then(fullfilled, rejected);