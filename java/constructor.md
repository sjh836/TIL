# 생성자

## 1. 생성자란?
객체를 생성할 때 init하는 것이다. 생성자는 오로지 클래스에서 객체를 생성(new)할 때만 사용된다. 몇가지 특징으로는 생성자의 이름은 클래스명과 동일하고, 리턴 타입이 없다.

## 2. 기본 생성자
생성자를 따로 정의하지 않고 클래스를 만들더라도, 객체는 만들어진다. 이것은 컴파일러가 default 생성자를 끼워넣었기 때문이다.

    // Test.java
    public class Test {
        public static void main(String[] args) {
            System.out.println("hello world");
        }
    }

    // javap -c Test.class
    C:\Users\devljh\Desktop>javap Test.class
    
    Compiled from "Test.java"
    public class constructor.Test {
        public constructor.Test();
            Code:
            0: aload_0
            1: invokespecial #8 // Method java/lang/Object."<init>":()V
            4: return

        public static void main(java.lang.String[]);
            Code:
            0: getstatic     #16 // Field java/lang/System.out:Ljava/io/PrintStream;
            3: ldc           #22 // String hello world
            5: invokevirtual #24 // Method java/io/PrintStream.println:(Ljava/lang/String;)V
            8: return
    }

까보면 public constructor.Test(); 를 만들지도 않았는데 들어가 있는걸 볼 수 있다.

## 3. 사용자 정의 생성자
개발자가 생성자를 만들면, 컴파일러는 기본 생성자를 끼워넣지 않는다. 객체 생성 시 필수적인 데이터가 있도록 할 때 생성자를 선언한다.

### this 키워드
현재 코드를 실행하는 현재 객체 자체를 가리킨다. 생성자 파라미터명과 필드명이 같을 때 가독성을 위해 `this.필드 = 파라미터` 로 써준다.

또 다른 쓰임새로는 생성자 안에서 `this(파라미터, 40)` 과 같은 다른 생성자를 호출할 수도 있다.

### 생성자 오버로딩
메소드와 마찬가지로 시그니처만 다르면 쓸 수 있다. (주로 파라미터로..)
