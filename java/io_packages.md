# java.io 패키지

참조문서 : https://docs.oracle.com/javase/8/docs/api/java/io/package-frame.html

io 부분에서 JDK 1.4 이전 방식을 클래식 I/O 라고 하고, 이후 방식을 NIO(Non-blocking I/O)라고 한다. 이제는 클래식 IO를 사용한다 해도 내부적으론 NIO를 활용하기 때문에 성능차이는 크게 없다.

## 1. java에서 I/O란?
I/O를 스트림(Stream)이라고 부르기도 한다. 접두어로 in, read 가 붙으면 입력관련이고, out, write 가 붙으면 출력관련이다. 데이터를 읽고 쓰는 대상으로는 디스크, 사용자 입출력, 네트워크 등이 있다.

|                  	| 읽기 전용   	| 쓰기 전용    	|
|------------------	|-------------	|--------------	|
| 1 Byte 단위 처리 	| InputStream 	| OutputStream 	|
| 2 Byte 단위 처리 	| Reader      	| Writer       	|

## 2. try-with-resources 를 이용한 close 자동 처리
참조문서 : https://docs.oracle.com/javase/tutorial/essential/exceptions/tryResourceClose.html

스트림 이용에서 마지막으로 close() 처리는 필수이다.(물론 스트림뿐만 아니라 DB커넥션 등에서도..) finally 구문에서 close()를 반복적으로 호출했어야 했는데, JDK 1.7부터는 try-with-resources 로 편리하게 자원을 이용끝과 동시에 해제할 수 있다. java.lang.AutoCloseable의 구현체는 try-with-resources 구조의 resource 에 해당된다.

	public static void main(String[] args) {
		String str = "숨겨도 트윙클 어쩌나 눈에 확 띄잖아";
		byte[] arr = str.getBytes();
		
		try (OutputStream out = new FileOutputStream("C:\\devljh\\test2.txt")) {
			out.write(arr);
		} catch (Exception e) {
			e.printStackTrace();
		}
	}

1.7 이전이였다면 try 옆에 (...) 대신에 finally로 out.close() 했어야 한다.

## 3. *InputStream 계열
![InputStream 상속클래스 이미지](http://img1.daumcdn.net/thumb/R1920x0/?fname=http%3A%2F%2Fcfile25.uf.tistory.com%2Fimage%2F993D7D415A3A59F81D5CCE)

InputStream는 추상클래스이다. 위 사진처럼 데이터 읽는 위치가 다양하기 때문에 많은 구현체들이 있다. InputStream을 얻는 방법으로는 구현체이거나 InputStream을 반환받으면 된다.

### 대표적인 구현 클래스
1. FileInputStream : 파일에서 데이터를 읽음
2. ZipInputStream : 압축파일 포맷(zip)에서 데이터를 읽음
3. DataInputStream, BufferedInputStream : 다양한 스타일의 데이터를 읽음

### 주요 메소드
* read() : 1바이트를 읽어들인다. N번 호출은 N바이트를 읽는다. 반환값은 int이다. 음수가 나오는 경우는 더 읽을 데이터가 없을 때이고, 이외에는 +255를 더해서 양수가 나오게 한다.
* read(byte[]) : 한번에 byte[] 크기만큼 데이터를 읽는다.

## 4. *OutputStream 계열
![OutputStream 상속클래스 이미지](http://img1.daumcdn.net/thumb/R1920x0/?fname=http%3A%2F%2Fcfile2.uf.tistory.com%2Fimage%2F9981F8335A3A59E4226ABD)

OutputStream도 추상클래스이다. 

### 대표적인 구현 클래스
1. DataOutputStream : 객체 생성시 OutputStream을 넣어줘야한다.(확장 개념) UTF-8로 문자열 쓴다. 앞의 2Byte가 뒷 데이터가 어떻게 구성되었는지 알려준다.

### 주요 메소드
* write() : 1바이트를 쓴다.
* write(byte[]) : byte[] 크기만큼 쓴다.
* write(byte[], int offset, int length) : 데이터를 offset부터 length까지 byte[] 크기만큼 쓴다.
* flush() : 버퍼가 가득안찼더라도 버퍼의 내용을 바로 보내버리고 비운다.

## 5. *Reader 계열
![Reader 상속클래스 이미지](http://img1.daumcdn.net/thumb/R1920x0/?fname=http%3A%2F%2Fcfile29.uf.tistory.com%2Fimage%2F99AB23405A3A59D335077A)

Reader 는 추상클래스이다.

### 대표적인 구현 클래스
1. BufferedReader : Reader를 생성자로 받는다. readLine()를 통해 문자열을 편하게 읽을 수 있다.

### 주요 메소드
* read() : 문자하나를 읽는다. 유니코드 값을 반환한다. 더 읽을 거 없으면 -1 반환
* read(char[] cbuf) : 문자 입력 스트림에서 문자를 하나씩 읽어 char[]에 저장하고 읽은 수 만큼 반환
* read(char[] cbuf, int offset, int length)
* skip(long n) : n만큼 문자를 건너뜀

### Scanner와 BufferedReader의 차이
* 정리 잘된 문서 : http://mygumi.tistory.com/43

요약하면..

|                         | BufferReader | Scanner |
|-------------------------|--------------|---------|
| JDK 지원                | 1.1          | 1.5     |
| 버퍼크기(char)          | 8192         | 1024    |
| 예외발생                | 터트림       | 숨김    |
| 동기화(멀티스레드 안전) | O            | X       |

이외에는 scanner가 읽어다가 요리하는데는 useDelimiter() 메소드처럼 더 잘 지원해준다. 참고로 scanner 나오기 전에는 InputStreamReader 클래스같은 변환해주는 것들을 많이 썼다고 한다.

## 6. *Writer 계열
![Writer 상속클래스 이미지](http://img1.daumcdn.net/thumb/R1920x0/?fname=http%3A%2F%2Fcfile9.uf.tistory.com%2Fimage%2F99074D405A3A59C4244B36)

Writer 도 추상클래스이다.

### 주요 메소드
* write(int c) : 문자 출력 스트림으로 c값을 char로 변환하여 쓴다.
* write(char[] cbuf) : 문자 배열을 쓴다.
* write(char[] cbuf, int offset, int length) : 문자 배열을 offset부터 length까지 쓴다.
* write(String str) : 문자열을 쓴다
* flush()

## 7. RandomAccessFile
파일관련해서 읽고 쓰는 작업이 RandomAccessFile 클래스 하나로 가능하다.(readxxx와 writexxx 가 공존) seek() 를 통해 마음대로 읽고 쓰는 위치를 조정할 수 있다. 

이외에 io.File에서 os별 경로구분자, 파일 및 디렉터리 여부 등을 지원해준다.

## 8. 객체 직렬화

객체 자체를 읽거나 쓸 수도 있다. 이 때 ObjectInputStream 이나 ObjectOutputStream 을 사용한다. 이때 객체는 String 변수1, int 변수2, boolean 변수3 일 수도 있고, 변수3, 변수1, 변수2 일 수도 있겠다. 이런 잡다한 정보들은 개발자들이 신경쓰지 않도록 JVM 단에서 처리해준다. 이렇게 하면 객체의 정보의 순서없이 저장할 수 있다. 다만 오라클 jvm이나 ibm jvm 간에는 다르게 동작할 수 있다.(JDK 문서에서 이런거 까지 안정해줬으므로..)

대신 이렇게 객체를 주고받을 때는 같은 소스(ex VO.java)가 있어야한다. 이를 극복하기 위해 RMI(Remote Method Invocation) 등이 있다고 한다.

### Serializable 인터페이스
메소드가 없는 인터페이스이다. 이 인터페이스를 구현하면 객체가 직렬화가 가능하다는 걸 나타내준다. 상수 `serialVersionUID` 는 나중에 클래스가 수정될 때 버전 정보를 가지고 확인하기 위한 수단이다.