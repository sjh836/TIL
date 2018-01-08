# static 키워드

## 1. static 이란?
객체마다 데이터를 가지고 있는데, 이걸 공유해야 할 상황에 static을 쓴다. 여러 객체의 메소드들이 동시에 접근, 수정할 수 있는 전역변수같은 느낌이다.

static 키워드를 사용한 변수를 **클래스 변수**라고 한다. 즉 객체가 아닌 클래스의 변수이기 때문에 new로 객체를 안만들었어도 사용이 가능하다. ex. `System.out.println(Test.count);`

## 2. 메모리 상 위치
클래스 변수는 JVM Runtime Data Area에서 Method area 에 들어간다. 여기서 클래스는 Method area에, 객체는 heap에 올라간다. static이 붙으면 클래스의 시그니처?같은 것들이 메모리 상에 로딩되면서 같이 올라가는 것이다. 따라서 아래에서 설명할 static 변수, static 메소드은 Method area 에 들어가므로(생성되므로) 모든 객체에서 가져다 쓸 수 있다.

## 3. static 메소드
static 메소드는 모든 객체가 완벽하게 똑같이 동작하는 메소드를 위해서 사용한다. 대표적으로 Integer.parseInt() 메소드같은 것들이 있다.

## 4. static 블록
단 한번만 동작한다. 역시나 클래스의 정보가 Method area 에 올라갈 때 "static 블록아 가즈아~!!" 가 찍히는 것이기 때문에, main메소드에 아무것도 없어도 자동으로 찍힌다.

    public class staticEx {
        static {
            System.out.println("static 블록아 가즈아~!!");
        }
        
        public void printGza() {
            System.out.println("가즈아~!!!!!");
        }
        
        public static void main(String[] args) {
            staticEx se = new staticEx();
            se.printGza();
            se.printGza();
        }
    }

    // 실행결과
    static 블록아 가즈아~!!
    가즈아~!!!!!
    가즈아~!!!!!
