# 접근제어자

## 1. 접근제어자의 종류
|           | 현재 클래스 | 동일 패키지 | 상속받은 하위클래스 | 모든 클래스 |
|:---------:|:-----------:|:-----------:|:-------------------:|:-----------:|
|   public  |      O      |      O      |          O          |      O      |
| protected |      O      |      O      |          O          |      X      |
|  private  |      O      |      X      |          X          |      X      |
| (default) |      O      |      O      |          X          |      X      |

* public : 모든 외부에서 직접 접근하거나 호출 가능
* protected : 현재 클래스와 동일 패키지이거나 다른 패키지이더라도 상속 시에는 접근하거나 호출 가능
* private : 현재 클래스 밖에서는 사용X
* (default) : 현재 클래스와 동일한 패키지 내에서만 접근하거나 호출 가능, 뭐 안붙이면 이거다.

## 2. 주로 어디다 쓰는가
* 클래스는 public 과 default만 선택가능하다.
* 필드는 다 쓸 수 있지만 보통 private로 한다. 완벽한 상수는 public static을 주로 사용한다.
* 메소드도 다 할 수 있다. 추상메소드에는 private 쓰면 안된다.

## 3. 접근자와 설정자
일반적으로 private 변수에 접근하거나 값을 설정하려면 getter와 setter 메소드를 이용한다. 이렇게 하면 주요한 장점으로는 아래와 같다. IDE단에서 아예 제네레이터 편하게 해준다.

* getter : 데이터의 복사본을 던져주기 때문에 원본데이터 손상X
* setter : 파라미터를 검증해서 값을 넣을 수 있다.