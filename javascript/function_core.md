# 자바스크립트 함수 Core

함수는 1급 객체(대입, 파라미터, 반환, 연산 등 제약 없음)이고 람다 언어(익명 함수나 클로저 등을 정의하기 위한 표현식)이다.

* 모듈화의 근간이다.
* 함수 객체는 Function.prototype에 연결되고, Function은 Object.prototype에 연결된다.

## 1. 함수 생성
함수는 함수 리터럴로 생성할 수 있다.

* 함수리터럴은 function 이라는 필수 예약어, 선택사항인 함수명, 매개변수의 집합(일반 변수는 undefined으로 초기화하지만, 매개변수는 넘겨진 인수로 초기화된다), 코드집합으로 구성된다.

### 함수문장 VS 함수 표현식
함수문장은 hoisting의 대상이 된다. 함수가 위치한 곳에 관계없이 유효범위의 가장 상위로 이동된다. 그렇기때문에 함수 호출이 함수를 정의하는 문장보다 앞에 있건, 뒤에 있건 유효범위만 같으면 문제가 없다.

	// 함수문장
	function add(a, b) {
	    return a + b;
	}
	
	// 함수 표현식
	var add = function (a, b) {
	    return a + b;
	};

아래 함수표현식을 쓰면, 이런 문제를 해결할 수 있다. 범위문제가 해결되므로, 함수정의보다 함수호출이 위에있으면 오류를 발생시킨다.

## 2. 함수 호출
호출 시 넘어오는 인수에 대한 타입체크는 당연히 없다. 따라서 뭘 넘기든지 그대로 매개변수에 할당하고, 수가 부족하면 undefined를, 넘치면 무시한다.

### 2-1. 함수 VS 메소드
* 객체의 속성이 함수이면 그 함수를 보통 메소드라고 부른다.
* 그 외 경우는 함수라고 부른다

### 2-2. 추가적인 매개변수
모든 함수는 명시된 매개변수에 더해서 this와 arguments라는 추가적인 매개변수를 더 받는다.

#### 2-2-1. this
함수 호출 패턴은 4가지가 있다. 각각의 패턴에 따라 this라는 추가적인 매개변수를 다르게 초기화한다.

1. 메소드 호출 패턴 : this는 메소드를 포함하고 있는 객체에 바인딩, this = 객체 자체, this를 사용해서 객체의 값을 읽거나 변경 가능
2. 함수 호출 패턴 : this는 전역객체에 바인딩, 문제점은 메소드가 내부 함수를 이용해 자신의 작업을 돕지 못한다. 해결방법은 메소드 내 변수에 this를 할당하고 사용하는 것. ex. var that = this;
3. 생성자 호출 패턴
4. apply 호출 패턴 : 함수.apply(this에 묶이게 될 값, 매개변수들의 배열)

#### 2-2-2. arguments
매개변수 개수보다 더 많이 전달된 인수들은 인수배열로 묶여서 arguments에 자동으로 할당된다. 엄밀히 따지면 arguments는 배열은 아니고 객체다. 배열의 속성 중 length만 가지고 있기 때문이다.

따라서, 이런 코드도 잘 동작한다.

	var sum = function () {
	    var i, result = 0;
	    for (i=0; i < arguments.length; i++) {
	        result += arguments[i];
	    }
	    return result;
	};

	console.log(sum(1, 2, 3, 4));

## 3. 함수의 반환
함수는 항상 값을 반환한다. 반환값이 지정되지 않으면 undefined를 반환한다. 함수를 new라는 전치연산자와 함께 실행하고 반환값이 객체가 아닌 경우 반환값은 this(새로운 객체)가 된다.

## 4. 클로저(closure)
내부 함수에서 자신을 포함하고 있는 외부 함수의 매개변수와 변수들을 접근하는 것을 클로저라고 부른다(this와 arguments는 예외)

함수는 이미 반환됐지만 **함수 안의 변수**는 **이를 필요로 하는 내부 함수가 하나 이상 존재하는 경우 계속유지**된다. 내부 함수가 외부 함수에 있는 변수의 복사본이 아니라 실제 변수에 접근하고 있는 거다.

따라서, 클로저를 이용하면 아래처럼 status 변수가 private이 되는 효과가 있다.

	var closureTest = function () {
	    var status = 10;
	
	    return {
	        getStatus : function () {
	            return status;
	        },
	        setStatus : function (num) {
	            status = num;
	        }
	    };
	};

	var myClosure = closureTest();

크롬 콘솔에서 직접 해봤다. 굿!

	> myClosure
	< Object {getStatus: function, setStatus: function}
	> myClosure.status;
	< undefined
	> myClosure.getStatus();
	< 10
	> myClosure.setStatus("hello");
	< undefined
	> myClosure.getStatus();
	< "hello"
	> myClosure.getStatus;
	< function () {
	      return status;
	  }

이처럼 클로저를 이용하여 모듈(인터페이스만 제공하는 함수나 객체)을 구현할 수 있다.

## 5. 콜백
자바스크립트 함수는 비연속적인 이벤트를 다루는 것을 좀 더 쉽게 할 수 있는 방법을 제공한다. 서버로의 요청을 비동기식으로 서버의 응답이 왔을 때, 호출되는 콜백 함수를 제공한다. 큰 틀에서 이런식으로 구현 가능하다.

	var request = prepareRequest();
	sendAsyncRequest(request, function (response) {
	    display(response);
	});

## 6. 메모이제이션(memoization)
동일한 계산을 반복해야할 때, 이전에 연산한 결과를 저장하고 있는 객체를 사용할 수 있다. 불필요한 반복 작업이 제거되고 최적화되는 걸 메모이제이션이라고 한다.

예를들어, 피보나치 수열을 재귀 함수로 0~10 까지 반복하면 453번이나 재귀호출된다. 하지만 메모이제이션을 하면 29번만 호출한다.

메모이제이션 작업은 메모이제이션 함수를 만드는 함수를 만들면 편하다. 매개변수로는 memo라는 배열과 fundamental 이라는 메모이제이션을 할 함수를 준다.

	var memoizer = function (memo, fundamental) {
	    var shell = function (n) {
	        var result = memo[n];
	        if (typeof result !== 'number') {
	            result = fundamental(shell, n);
	            memo[n] = result;
	        }
	        return result;
	    };
	    return shell;
	};

