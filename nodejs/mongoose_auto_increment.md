# mongoose-auto-increment

참조문서 : https://www.npmjs.com/package/mongoose-auto-increment

시퀀스 넘버키를 편하게 쓸 수 있다. `npm install mongoose-auto-increment` 를 다운로드받는다.

## 1. 연결
	const connection = mongoose.connect(....)
	autoIncrement.initialize(connection);

으로 연결한다. mongoose.connect의 콜백에 `autoIncrement.initialize()`를 넣으면 `Error: mongoose-auto-increment has not been initialized` 가 떨어진다.

## 2. 시퀀스넘버 설정
	const logger = new Schema({..})
	logger.plugin(autoIncrement.plugin, 'log');
	const Logger = mongoose.model('log', logger);

이때 컬렉션명 일치가 중요하다. 이렇게하면 _id가 0부터 순차증가한다.

내부 모듈(index.js) 까보면 초기화할때 컬렉션을 만들고, save이벤트 발생전에 pre로 아래와 같은 로직을 처리한다.

	IdentityCounter.findOneAndUpdate(
	$inc: { count: settings.incrementBy }

몽고 쉘 에서 찾아보면 

	> db.identitycounters.find()
	{ "_id" : ObjectId("5979cf841bca97176446e0ab"), "model" : "log", "field" : "_id", "count" : 3, "__v" : 0 }

## 3. 이외 옵션
API문서보면 인덱스 시작 숫자 변경, 증가수 변경, 필드 설정등이 가능하다. 

	bookSchema.plugin(autoIncrement.plugin, {
	    model: 'Book',
	    field: 'bookId',
	    startAt: 100,
	    incrementBy: 100
	});