# HTML PART1

코드카데미 [Learn HTML & CSS: Part I](https://www.codecademy.com/learn/learn-html-css)

ps. HTML, CSS는 웹의 기초이니 심심할때 풀어주면 시간잘간다. 대충 알지 말자~~~ 

## 2. HTML기본
a태그: target="_blank" 를 쓰면 새 탭으로 연다

W3C 표준: 들여쓰기는 스페이스 2칸

<b\>와 <strong\>태그의 차이점은 눈에 보이는 효과는 같으나 시각장애인에게 음성으로 읽어줄 때 strong은 볼륨이나 톤을 강조해서 읽는다

## 3. CSS기본
**W3C 표준:** 선택자 {하고, 2칸스페이스, }끝나고는 한줄 뛰어야함

**선언방식:**

1. in-line: 태그안에서 속성으로 추가.
2. internal: head안에 넣어서 선언
3. external: head안에 링크넣어서 선언

## 4. 색상
hsl: 색조, 채도, 밝기로 색상 선택

rgba, hsla: 불투명도 추가

다중 선언이면 후자가 우선순위를 갖는다. 여러 인터넷브라우저의 호환성을 고려할 때 이용 (ex. rgba()를 지원안하면 rgb()만 먹이도록)

## 5. 폰트
serif: 끝부분에 추가획있음

sans-serif: 추가획없이 둥글둥글

폰트가 설치안될 경우를 대비하여 font-family에 폰트를 다중 선언(앞에것부터 우선순위)

구글폰트를 사용하면 설치여부 노상관 → https://fonts.google.com/

line-height: 행간격

text-transform: uppercase; 전부 대문자로

## 6. 클래스와 ID
ID는 유니크, 클래스는 중복가능

태그랑 ID선택자같이쓰면 태그#ID 이렇게 쓴다

## 7. 박스 모델
overflow속성에서 hidden은 가리고, scroll은 스크롤바 생성

## 8. 테두리
테두리: border: 굵기px 선스타일 색상;

테두리 모서리: border-radius: 픽셀;

## 9. 컨텐츠
padding은 박스 내부와 박스 테두리 사이의 간격

margin은 박스 테두리 간의 간격

margin: auto; 는 상위 태그의 전체 너비와 같게 설정된다. width와 함께주면 가운데 정렬됨. (ex. margin: 0px auto; 위아래 0, 좌우는 가운데정렬)

inline-block: inline과 같이 한줄에 표현되면서도 block처럼 margin, width, height를 정의 가능

visibility: hidden는 해당 공간을 차지하지만 눈에 보이지만 않는다.

## 10. 박스 사이즈
box-sizing: content-box; 컨텐츠 크기200으로 선언해도 컨텐츠+패딩20+테두리1를 각각 다 먹는다. 총 221. 이게 기본값이다.

box-sizing: border-box; 컨텐츠 크기 200선언하면 그 내부에서 다 해결됨(영향받지 않는다). 컨텐츠 크기 auto.

## 11. 레이아웃
position은 위치 지정

z-index: 값이 높을수록 우선순위 결정

float: 최대한 왼쪽or오른쪽으로 보내버린다.

clear: float된거에 따라붙지 않게 할때 사용. (left, right, both) **ex.** float: left에 따라붙지 않게하려면 clear: left를 쓰면 된다.

## 12. 이미지
background-image: url("주소");

background-repeat: repeat-x: 가로축 반복, repeat-y: 세로축 반복, no-repeat: 반복X

background-position는 브라우저를 3X3으로 나눠서 이미지를 배치할때 사용

### ex. 가운데정렬:
1. `background-position: center center;`
2. `background-position: 50% 50%;`

`background: url("#") no-repeat right center;` 처럼 한번에 쓸수 있다

background-size:
- cover은 이미지 크기 비율을 그대로 유지한 상태에서 이미지가 들어 있는 엘리먼트의 가로 또는 세로에 이미지를 맞춘다.
- contatin은 이미지 크기 비율을 그대로 유지한 상태에서 원하는 영역에 전체 이미지가 들어가도록 가장 작은 크기로 이미지 스케일을 조정한다.

background-attachment: scroll은 기본값으로 스크롤바 내리면 같이 내려간다(상대적), fixed는 스크롤바 내려도 이미지가 그대로다.

background-image: -webkit-linear-gradient(색1, 색2): 색변화시키는 거

## 13. 테이블
tr: 행 추가

td: 열(데이터) 추가

th: 제목 추가

scope="col 또는 row" 을 넣어 열이나 행이 어떤 것에 대한 거라는 걸 명확히 해준다.
**ex.** `<th scope="row"\>Temperature</th\>`

colspan: 열 병합

rowspan: 행 병합