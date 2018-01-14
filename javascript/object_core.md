# 자바스크립트 Object 깊게 파자
* 참고문서 : http://insanehong.kr/post/javascript-object/
* 참고서적 : 자바스크립트 핵심가이드(더글라스 크락포드)

ECMAScript 에서 객체는 크게 3가지로 구분한다.

## 1. Built-in Object (JS 내장객체)
최상위레벨의 객체이다. 자바의 Object같은 느낌. 여기서 파생되는 객체들은 Object 객체가 가지고 있는 기본적인 구성요소를 상속받게 된다.

Object 객체가 가진 특별한 구성요소들은 바로 constructor, prototype 이라는 프로퍼티와 hasOwnProperty(), toString(), isPrototypeOf() 라는 메소드들이다.

Built-in Object에는 Global, Object, String, Number, Boolean, Date, Array, Math, RegExp, Error 등 많은 내장객체가 있다. 이들은 자바스크립트 엔진이 구동되는 시점에서 바로 제공되며 자바스크립트코드 어디에서든 사용하다.

## 2. Native Object (브라우저 내장객체)
Native Object 역시 자바스크립트가 구동되는 시점에서 바로 사용이 가능한 객체들이다. 하지만 이들은 자바스크립트 엔진이 구성하는 기본객체라고 하기 보단 자바스크립트 엔진을 구동하는 브라우저에서 빌드되는 객체들이다. 자바스크립트 프로그램을 작성하면서 사용하는 브라우져객체모델(BOM)과 문서객체모델(DOM)들이 이에 속한다. 이 객체들은 Built-in Object 가 구성된후에 구성된다. 이 Native Object역시 내장객체이다. Built-in Object와 동일하게 자바스크립트 구동시점부터 바로 사용가능하다. 하지만 이들을 Native Object 라고 분류한 이유는 브라우저마다 이 Native Object 를 사용함에 있어서 그 구성을 달리하는 경우가 있기 때문이다.

## 3. Host Object (사용자 정의 객체)
사용자가 생성한 객체 들이다. constructor 혹은 객체리터럴을 통해 사용자가 객체를 정의하고 확장시킨다. 때문에 Built-in Object 와 Native Object가 구성된 이후에 구성된다.

## 4. object 생성
객체를 생성하는 방법은 크게 2가지로 나누어 진다. constructor 를 이용하여 생성하는 방식과 객체 리터럴을 이용하는 것이다. 객체리터럴을 이용한 방식은 JSON을 이용하여 표현한다.

객체 리터럴을 사용해 객체를 생성하는 방법은 **내부적으로** new Object()를 수행한 후 멤버를 구성하는 방법과 동일한 과정을 따른다.

	/* constructor 를 사용하여 객체를 생성하는 예 */
	var foo = new Object(); // 
	foo.name='foo';
	console.log(foo.name);
	> foo
	
	/* JSON 방식을 사용하여 객체 리터럴 */
	var foo = {
	    name : 'foo'
	};
	console.log(foo.name);
	> foo

## Object VS Object()
둘의 차이점은 ()호출 유무이다. 각각 Built-in 객체(이미 만들어진 객체)와 새로운 객체를 생성하여 할당한다.

Built-in 오브젝트를 할당한 곳에는 prototype: Object가 있으며 ()호출한 obj2의  출력결과에는 \_\_proto__:Object가 있을 것이다. 


	// Built-in Object 객체를 obj1에 할당
	var obj1 = Object;
	console.dir(obj1);
	
	// Object()로 새로운 Object를 생성하여 obj2에 할당
	var obj2 = Object();
	console.dir(obj2);

Object()로 인스턴스를 생성하면 Object.prototype에 연결된 프로퍼티로 인스턴스를 생성하기 때문이고 이것이 인스턴스를 생성하는 기준이다.

## 5. 상속
prototype을 이용한 상속에서는 `var 자식객체 = Object.create(부모객체);`가 핵심이다. 이후 자식객체에서 내용을 수정하면 된다. 대신 이 방법은 모든게 public인 단점이 있다.

## 6. 핵심 정리
자바스크립트에서 생성하는 모든 객체는 

### 6-1. Built-in Object 객체에서 파생되어 나온 객체들
### 6-2. 암묵적으로 Array 객체를 상속받음. 객체는 곧 배열 그 자체이다.
* 객체의 메소드는 객체 안에 존재하지 않는다. 메소드를 소유한 parent 객체는 객체에 포함된 메소드들의 참조값만을 가지고 있다. 즉 메소드들 자체도 각각 개별 객체로서 존재한다.
* JSON의 key, value 방식의 객체 리터럴 구조는 배열 구조와 동일하다. 즉 JSON의 표현방식은 [] 라는 Array 표현의 다른 방식일 뿐