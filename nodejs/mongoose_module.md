# Mongoose

참조문서 : http://mongoosejs.com/docs/guide.html

Mongoose는 MongoDB의 ODM(Object Data Mapping)으로 nodejs 모듈이다. ODM은 데이터베이스와 객체지향 프로그래밍 언어 사이 호환되지 않는 데이터를 변환하는 프로그래밍 기법을 말한다. 여기서는 4.11버전을 사용중이다.

`npm install mongoose` 로 설치한다.

## 1. mongodb VS mongoose
자바로 생각하면 mongodb모듈은 JDBC드라이버고, mongoose는 Hibernate나 Mybatis같은 개념같다. 속도가 예민하다면 mongodb모듈을 쓰라고 말하고 있다.

## 2. 4.11 버전 이슈
몽구스 promise 사용을 선택할 수 있는데, `mongoose.Promise = require('bluebird');` 꺼를 써도되고, ES6의 전역객체의 promise를 써도된다. 그럴라면 `mongoose.Promise = global.Promise;` 해줘야한다.

connect() 함수에 useMongoClient 파라미터가 추가되었다. 이거 안쓰면 deprecated 뭐라 그런다. 이외에 여기에 들어있던 server/replset/mongos 등이 모두 deprecated되었다.

## 3. DB 연결
`mongoose.connect(uri[, options, callback]);` 로 접속한다.

uri는 `mongodb://${host}:${port}/${database명}` 으로 구성된다. 보통 포트는 27017인 것 같다.

options 객체에는 아래와 같이 들어간다.

	const options = {
		useMongoClient: true
	}

## 4. 스키마
MongoDB에는 완전 스키마가 없는 게 아니라 스키마로 부터 자유로운 정도다. 여기서 mongoose는 데이터 유효성 검증을 위해 스키마라는 개념을 쓴다.(validator) 모델 객체에 들어갈 데이타 형뿐 아니라, 데이타의 규약등을 지정할 수 있는 기능인데, 예를 들어 문자열의 길이나, 특수문자 지원 여부등을 검증할 수 있다.

	function NameAlphabeticValidator(val) {
	    return val.match("^[a-zA-Z\(\)]+$");
	}
	
	function StringLengthValidator(val) {
	    if(val.length>10)
			return null;
	    return val;
	}

	var userSchema = mongoose.Schema({
		userid: {
			type : String,
			validate : NameAlphabeticValidator
		},
		age : Number,
		city : {
			type : String,
			validate: [{
				validator : NameAlphabeticValidator,
				msg : 'city should be alphabet only'
			},
			{
				validator : StringLengthValidator,
				msg : 'city length should be less than 10'
			}]
		}
	});

### 4-1. Default
데이터 값을 입력하지 않으면 default로 값을 넣을 수 있다.

	var defaultSchema = new mongoose.Schema({
		name : {
			type : String,
			default : 'Hello Terry'
		},
		published_date: {
			type: Date,
			default: Date.now
		}
	});

### 4-2. 타입
스키마에 사용되는 타입은 8가지가 있다.

1. String : 문자열
2. Number : 정수
3. Date : 날짜
4. Buffer : 2진데이터
5. Boolean : 참거짓
6. Schema.Types.Mixed : 다양한 타입, 타입없다고 봐도 된다
7. Schema.Types.ObjectId : 명시적으로 _id를 나타낼때 쓴다. 안써도 자동생성
8. Array : 배열

## 5. Model
model은 데이터베이스에서 데이터를 읽고 생성하고 수정하는 인터페이스를 정의해준다. `var Book = mongoose.model('book', bookSchema);` 이런식으로 생성한다. 컬렉션이름을 지정하지 않으면 보통 1번째 인자에 s를 붙여 복수형으로 사용한다.

	var student = mongoose.Schema({
	    name : 'string',
	    address : 'string',
	    age : 'number'
	}, {
	    collection : 'good', // 컬렉션명 지정
	    versionKey : false // "__v" 필드 생성X
		strict : false
	});

* 임의로 컬렉션명을 지정하고 싶다면 collection : '컬렉션명'
* 버전키를 안보이게하고싶으면 versionKey : false
* 스키마를 사용하지 않으려면 strict : false

## 6. CRUD
몽고 쉘 명령어와 비슷한듯 하다.

### 6-1. 생성 : .save
### 6-2. 조회 : .find
### 6-3. 제거 : .remove