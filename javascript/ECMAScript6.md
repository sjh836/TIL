# ECMAScript 6

참조문서

* https://developer.mozilla.org/ko/docs/Web/JavaScript
* http://www.ecma-international.org/ecma-262/6.0/
* https://www.slideshare.net/WooyoungCho/ecmascript-6-64456124

흔히 생각하는 javascript는 2009년에 제정되고 11년에 개정된 ECMAScript 5.1 표준이다.

ES6에서는 arrow functions, class, 향상된 객체리터럴, let과 const, iterator, for-of 등이 추가되었다. 좀더 나은 fp와 oop를 가능하게 해준다. 또한 기존 ES5코드와 모두 하위호환이 된다.

여기서는 ES6에 추가된 사항을 중점적으로 정리한다. 또한 몰랐던 사실을 알게된 것도 정리해보자.

## 1. 원시 데이터타입
원시타입은 단 하나의 값만 나타낼 수 있고, 불변(immutable)이다. pass-by-value이다. 원시타입에는 6가지가 있다. 추가된 것은 도입이라고 기록하고 추가 절에서 알아보자.

* 숫자
* 문자열
	* 백틱(`) **도입**
	* 달러($) **도입**
* 불리언
* null
* undefined
* symbol **도입**

### 1-1. 템플릿 문자열
ES5에서는 문자열 병합(+)으로만 문자열 사이에서 변수를 표현했다. ES6에서는 따옴표로 문자열을 감싸지 않고 백틱(`)으로 문자열을 감싸면, ${표현식}을 이용하여 삽입할 수 있다. 물론 병합과 섞어 쓸 수도 있다. 표현식안에는 계산식, 함수 등 값이 들어갈 수 있는거면 뭐든 들어간다.

ex. \`hello ${name}, good!`

### 1-2. 심볼
ES6에서 도입된 새로운 데이터 타입이다. 심볼은 항상 유일하며, 다른 어떤 심볼과도 일치하지 않는다. 항상 유일하다는 특징을 제외하면 원시 값의 특징을 모두 갖고 있다. new를 사용하지 않고 Symbol() 생성자로 만든다. 고유한 식별자가 필요할 때 사용한다.

	const foo = Symbol(123);
	const bar = Symbol(123);
	foo === bar // false

## 2. 참조 데이터타입
객체는 복잡한 값을 나타낼 수 있고, 가변적이다. pass-by-reference이다. 객체의 본질은 컨테이너다. 속성명은 문자열 또는 심볼이어야 한다. 값은 어떤 타입이든 상관없다.

몇가지 내장된 객체 타입이 있다.

* Array
* Date
* RegExp
* Number
* String
* Boolean
* Map과 WeakMap **도입**
* Set과 WeakSet **도입**

Number, String, Boolean 객체타입은 특별한 값을 저장하기 위해 사용(Number.INFINITY 등)하거나 함수 형태로 기능을 제공(toUpperCase() 등)하기 위해 쓰인다.

자바스크립트 엔진은 임시객체를 만들어 속성이나 함수를 사용가능하게 해주고, 사용즉시 임시객체를 회수해간다. 객체가 임시로 만들어진다는 사실은 속성을 만들어보면 알 수 있다.

	const str = "hello";
	str.test = 123;
	str.test // undefined

### 2-1. Map과 Set
다른 언어의 Map, Set과 비슷한 것 같다. Map은 객체처럼 키값으로 연결하지만 객체보다 유리하다. 셋은 배열과 비슷하지만 중복이 허용되지 않는다.

## 3. 형 변환
### 3-1. 숫자로 바꾸기
* Number 생성자 이용 : `Number(str);`
* parseInt(str, radix) : str문자열을 기수로 바꾼다. ex. `parseInt('155.956bps', 10); // 155`
* parseFloat(str)
* Date는 valueOf()를 이용해서 19700101부터 몇 밀리초가 지났는 지 숫자로 바꾼다.

### 3-2. 문자열로 바꾸기
* 모든 객체에 있는 toString()을 이용.

## 4. 제어문
### 4-1. for-of 반복문 도입
ES6에 새로 생긴 반복문으로 컬렉션의 요소에 루프를 실행한다.

	// 사용법
	for(variable of object)
		statement

	// 예제
	const loop = ["hi", "hello", "test"];
	for(let e of loop)
		console.log(e);

## 5. 문장과 표현식
* 문장(statement) : 지시문
* 표현식(expression) : 값이다. 다른 표현식과 결합해서 다른 값을 얻을 수도 있다. 값이기 때문에 할당에 쓸 수 있다. 즉, 표현식의 결과(값)을 변수, 상수, 객체속성에 할당할 수 있다.

자바스크립트는 표현식을 작은 단위로 쪼개서 한 부분씩 실행한다. 표현식을 평가하는 순서를 연산자 우선순위라고 부른다.

## 6. 연산자
* 그냥 일치(===) 비교 연산자 써라, 타입때문에 이상한 결과나오면 타입캐스팅해서 === 써라
* 문자열 병합 : 왼쪽에서 오른쪽으로 평가 후 병합
	* 3 + 5 + "8" = "88"
	* "3" + 5 + 8 = "358"
* 쉼표 연산자 : 두 표현식을 결합하여 평가한 후, 두번쨰 표현식을 반환한다. ex. `a = (b++, c++);` 일 때 a에는 c값이 들어가고 b++, c++ 이 실행된다.
* typeof null 의 결과는 object 이다. JS가 10일만에 설계된 맹점 중 하나다. 왜 이런지는 [이 문서](https://github.com/FEDevelopers/tech.description/wiki/%E2%80%9Ctypeof-null%E2%80%9D%EC%9D%98-%EC%97%AD%EC%82%AC)에서 확인할 수 있다.

### 6-1. 해체 할당(destructuring assignment) 도입
ES6에서 새로 도입되었다. 객체나 배열을 변수로 해체할 수 있다.

#### 6-1-1. 객체 해체
객체를 해체할 때는 변수이름과 속성이름이 일치해야 한다. 일치하지 않으면 a처럼 undefined가 할당된다. 아래처럼 선언과 할당을 동시에 하지 않을거면, 괄호를 써야한다. `({a, b, c} = obj)`

	// 객체 선언
	const obj = { b:'hello', c:36, d:null };
	
	// 해체 할당
	const {a, b, c} = obj;
	console.log(a); // undefined
	console.log(b); // "hello"
	console.log(c); // 36
	console.log(d); // ReferenceError, d is not defined

#### 6-1-2. 배열 해체
배열을 해체할 때는 변수이름을 마음대로 쓸 수 있다. 배열순서대로 대응한다. 남은 배열의 요소들은 버려지는데, 확산 연산자(spread operator)를 사용하면 새 배열에 할당할 수 있다. 배열 해체는 iterable 객체에도 사용 가능하다.

	// 배열 선언
	const arr = ["hello", 36, null, 1, 5, "hi"];

	// 해체 할당
	let [a, b, ...rest] = arr;
	console.log(a); // "hello"
	console.log(b); // 36
	console.log(c); // ReferenceError: c is not defined
	console.log(rest); // (4) [null, 1, 5, "hi]

배열 해체를 이용하여 변수의 값을 서로 바꿀 수도 있다.

	let a = 5, b = 10;
	[a, b] = [b, a]
	console.log(a); // 10
	console.log(b); // 5

## 7. 함수
함수를 참조만 할 수 있다는 건 JS의 큰 특징이다. 이로 인해 변수, 배열, 객체속성 등에 할당이 가능해졌다. 뭐든지 괄호()를 붙이면 함수로 인지하고 호출한다.

### 7-1. 매개변수
JS에선 함수의 시그니처에 매개변수가 포함되지 않는다. 함수이름이 같다면 매개변수 개수가 어떻든 다 같은 함수이다.

#### 7-1-1. 매개변수 해체
객체해체, 배열해체를 통해 매개변수를 받을 수도 있다.

	function getString({first, last}) {
		return `${first} and ${last}`;
	}
	
	const obj = {
		first:"ljh",
		last:"good"
	};
	
	getString(obj);

#### 7-1-2. 확산(spread) 연산자 ... 도입
... 는 확산연산자로 ES6에서 새로 도입되었다. 이를 통해서 남는 매개변수를 배열로 받을 수 있다. ES5에서의 arguments와 비슷하지만, arguments는 실제 배열이 아니라 배열과 비슷한 객체이었다. 이런 약점을 ... 로 해결했으니 이걸 쓰는게 좋다.

	function spreadTest (a, b, ...rest) {
		return (a + b) * rest.length;
	}

	spreadTest(1, 2, "hello", true) // 6

이런 것도 가능하다.

	function spreadTest2 (a, b, c) {
		return a + b + c;
	}
	
	spreadTest2(...[1,2,3]) // 6

#### 7-1-3. 매개변수 기본값(default) 도입
매개변수에 값을 할당하지 않으면 undefined가 할당된다. 하지만 ES6부터는 매개변수에 기본값을 할당해줄 수 있다. `function defaultTest (a, b = 36) {` 이런식으로 사용한다.

### 7-2. 메소드 추가
객체의 속성값으로 함수가 들어가면 메소드라고 부른다. ES5에서는 속성:function() {} 이런식으로 할당했지만, ES6에서는 아래처럼 메소드를 추가할 수 있다.

	const obj = {
		name:"ljh",
		sayHello() {
			return "hello";
		}
	};

### 7-3. this의 이해
this는 함수를 어떻게 선언했느냐가 아니라 어떻게 호출했느냐에 따라 달라진다. 만약 위 예제에서 obj.sayHello()를 호출하면 this는 obj에 바인딩된다. 하지만, `const test = obj.sayHello();` 를 하고 test()를 하면 this는 전역객체에 바인딩, strict모드라면 undefined에 바인딩된다.

* 함수.call(객체, 추가인수들..) : this가 첫번째 인수인 객체에 바인딩된다. 2번째부터는 호출함수의 인자로 들어간다.
* 함수.apply(객체, 추가 인수들의 배열) : call과 비슷하지만 2번째 인수는 배열 타입이어야 한다.
* 함수.bind(객체, 추가인수들..) : 영구히 this가 객체에 바인딩된다. call, apply로 바꿀 수 없다. 매개변수도 마찬가지이다.

### 7-4. 화살표 표기법(arrow notation) 도입
function이라는 단어와 중괄호 숫자를 줄이려고 ES6에서 새로 도입되었다. 화살표 함수는 항상 익명 함수이다. 함수형 프로그래밍을 JS에 도입하는 중요한 거랜다..

* function 키워드를 생략해도 됨
* 함수에 매개변수가 단 하나 뿐이라면 괄호()도 생략할 수 있음
* 함수 본문이 표현식 하나라면 중괄호{}와 return문도 생략할 수 있음

예제는 각각 function키워드 사용한 익명함수와 화살표 표기법을 사용한 익명함수이다.

	const f1 = function() { return "hello"; }
	const f1 = () => "hello";
	
	const f2 = function(name) { return `hello, ${name}`; }
	const f2 = name => `hello, ${name}`;

	const f3 = function(a, b) { return a + b; }
	const f3 = (a, b) => a + b;

가장 중요한 차이는 this가 정적으로 묶인다. 내부 메소드의 경우, 객체속성을 쓰려면 this를 다른 변수에 할당하고 써야하는데, 화살표함수에서는 this가 자신을 둘러싼 것(바로 바깥쪽)에 바인딩된다. 따라서 내부함수에서 그냥 this를 갖다쓰면 된다.

또 다른 차이점은 객체 생성자로 사용할 수 없고, arguments 변수를 사용할 수 없다.(확산연산자 있어서 상관은 없음)

## 8. 스코프
### 8-1. 전역 스코프
맨 아래이다. 호출스택 까보면 맨 밑에 있다. 여기서 선언되는 것을 전역 변수라고 한다.

### 8-2. 블록 스코프 도입
ES6에서는 let과 const 키워드를 이용해 변수 선언을 할 수 있다. 블록스코프에 바인딩된다. 다른언어와 마찬가지로 이름이 겹치면 지역변수가 우선이다.

* let : var를 대체할 수 있고, 전역 객체에 들어가지 않는다. for문의 init에 위치할 경우, 반복할때 마다 새로 생성된다.
* const : 상수 선언으로, 한번 할당되면 변경할 수 없다.

### 8-3. 함수 스코프
var로 변수선언을 하면, 스코프만 같다면 선언 전후에 상관없이 사용할 수 있다. 하지만 let으로 변수를 선언하면 선언전에는 오류가 발생한다. var에서는 호이스팅이 적용되기 때문이다.

#### 8-3-1. var 호이스팅(hoisting)
js는 해당 스코프 안에서 var로 선언한 변수를 맨 위로 끌어올린다. 중요한 건 할당(=)은 끌어올리지 않고, 선언만 끌어 올린다.

	console.log(test); // undefined
	var test = 36;
	console.log(test); // 36

이런 점때문에 ES6에서는 var를 대체할 수 있는 let을 만들었다.

#### 8-3-2. 함수 호이스팅
var 변수와 마찬가지로 `function f() {}` 같은 함수도 호이스팅의 대상이다. 대신 `let f = function() {}` 는 변수의 규칙을 따라 호이스팅되지 않는다.

### 8-4. strict 모드
전역 스코프 상단이나 함수 스코프 상단에 'use strict'를 사용하면, 스크립트가 스트릭트 모드로 실행된다. 뜻하지 않은 전역객체 참조 등을 막기 위한 것이다. 이런 경우 전역객체에 바인딩되지 않고 undefined가 바인딩된다.

## 9. 배열
배열에서 어떤 메소드는 배열 자체를 수정하고, 어떤 메소드는 아예 새 배열(사본)을 반환한다. API문서 참조하자.

### 9-1. 배열 요소 조작
#### 9-1-1. 추가/제거
* push() : 끝에 요소를 추가, 변경된 길이 반환
* pop() : 끝의 요소를 제거, 제거값 반환
* shift() : 앞의 요소를 제거, 제거값 반환
* unshift() : 앞의 요소를 추가, 변경된 길이 반환

#### 9-1-2. 여러 요소 추가
* concat(추가 요소들) : 배열 끝에 여러 요소를 추가한 사본을 반환, 따라서 바꾸려면 다시 할당해줘야 한다. 대괄호는 한번만 분해한다.

		const arr = [1, 2, 3];
		arr.concat(4, 5, 6); // (6) [1, 2, 3, 4, 5, 6]
		arr.concat([4, 5, 6]); // (6) [1, 2, 3, 4, 5, 6]
		arr.concat([4, 5], 6); // (6) [1, 2, 3, 4, 5, 6]
		arr.concat([4, [5, 6]]); // (5) [1, 2, 3, 4, [5, 6]]

#### 9-1-3. 일부 가져오기
* slice(시작인덱스, 끝인덱스) : 시작부터 끝-1 인덱스까지의 배열 요소를 가져와 사본을 반환, 음수 인덱스를 쓰면 끝에서부터 요소를 센다.

#### 9-1-4. 임의 위치에 추가/제거
* splice(시작인덱스, 제거할 요소 수, 추가할 요소들..) : 시작인덱스부터 제거할 요소들의 수만큼 제거하고 추가할 요소들을 순차적으로 넣는다. 삭제된 요소배열이 반환된다.

		const arr = [1, 2, 3, 4, 5];
		arr.splice(2, 2, "a", "b") // (5) [1, 2, "a", "b", 5]

#### 9-1-5. 내부에서 요소 교체
* **copyWithin(target, start, end)** : ES6에서 새로 도입된 메소드다. 같은 배열에서 위치값 복사해서 붙여넣을 때 쓴다.

#### 9-1-6. 배열 채우기
* **fill(value[, start = 0[, end = this.length]])** : ES6에서 새로 도입된 메소드다. 정해진 값으로 배열을 채운다. 범위를 지정하려면 start, end 인덱스를 쓰면 된다.

#### 9-1-7. 정렬과 역정렬
* reverse() : 배열의 값을 역순으로 바꿔서 수정해버린다. [1,2,3]이면 [3,2,1]으로
* sort() : 요소를 순서대로 정렬하며 원본자체를 수정한다. 정렬함수를 받을 수 있는데, 이를 이용해서 객체도 정렬할 수 있다.

		const arr = [{name:jacob}, {name:amanda}];
		arr.sort((a, b) => a.name > b.name); // name속성값의 알파벳 순으로 정렬

### 9-2. 배열 검색
* indexOf(searchElement, fromIndex) : 값과 ===하는 첫번째 요소의 인덱스를 반환한다. 찾지못하면 -1를 반환. 시작할 인덱스는 생략가능하다.
* lastIndexOf(searchElement, fromIndex) : indexOf()와 같으나, 끝에서부터 동작한다.
* findIndex(callback[, thisArg]) : 함수를 써서 검색조건을 지정할 수 있다.
* find(callback[, thisArg]) : indexOf()는 인덱스를 찾아주는데, find()는 요소 자체를 원할때 쓴다. 못찾으면 undefined를 반환
* some(callback[, thisArg]) : 찾으면 true, 못찾으면 false
* every(callback[, thisArg]) : 조건에 모든 요소가 다 맞으면 true, 하나라도 아닌 요소가 있으면 false

### 9-3. map과 filter
map과 filter는 원본을 수정하지 않고 사본을 반환한다. map은 배열 요소를 변형한다. 일정한 형식의 배열을 다른 형식으로 바꿔준다. 콜백함수의 첫번째 인자로 각 요소들이 들어간다. 즉, 요소마다 콜백함수를 호출하는 꼴이다.

	const cart = [{name:"치토스", price:1500},{name:"프링글스",price:3300}];
	const names = cart.map(x => x.name);
	const prices = cart.map(x => x.price);
	
	const names2 = ["홈런볼", "꼬깔콘"];
	const prices2 = [1200, 1500];
	const cart2 = names2.map((x, i) => ({name:x, price:prices2[i]}));

filter는 원본배열에서 필요한 것들만 사본에 담아서 반환한다.

### 9-4. reduce
reduce는 원본배열 자체를 수정한다. 보통 배열을 값하나로 줄이는데 사용한다. 콜백함수의 첫번째 인자는 누적할 변수가 들어간다. 두번째 인자에 요소가 들어간다.

	const arr = [3, 6, 1, 9];
	const sum = arr.reduce((a, x) => a+=x, 0); // sum = 19, 맨뒤의 0은 a의 초기값이다.

### 9-5. 문자열 병합
배열의 문자열 요소들을 구분자로 합치려고 할 때 쓰면 좋다. join()은 매개변수로 구분자 하나를 받고 요소들을 하나로 합친 문자열을 반환한다. 문자열 요소를 합칠 때 undefined, null 등은 빈 문자열 취급한다.