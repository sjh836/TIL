# logstash grok pattern
grok은 비정형 데이터를 파싱하여 정형데이터로 만드는 라이브러리다. key/value형태로 적재된다. 골때리는건 정규표현식쓰는 방법이 독특하다는 거다..

참조문서 : https://www.elastic.co/guide/en/logstash/current/plugins-filters-grok.html

## 1. 기본문법
`%{SYNTAX:SEMANTIC}`

* SYNTAX = 텍스트와 일치하는 패턴의 이름
* SEMANTIC = 식별자

	(ex)
	
		%{NUMBER:duration} %{IP:client}
        
		(필터링) 
        SEMANTIC SYNTAX
        식별자 텍스트

        (결과)
        버전 3.44
        클라이언트 1.1.1.1

## 2. 기존 패턴 라이브러리
https://github.com/elastic/logstash/blob/v1.4.0/patterns/grok-patterns

## 3. 사용자 정의 패턴
### 3-1. 정의
logstash-5.4.0/patterns에 파일을 하나 만들고 여기서 기존 grok-pattern들 처럼 사용자가 정의해주고 임포트해주면 된다. (디렉터리명과 파일명은 아무 상관이 없다)

ex.

`POSTFIX_QUEUEID [0-9A-F]{10,11}`

### 3-2. 사용자패턴 만든거 사용할 때
    grok {
        patterns_dir => ["./patterns"]
    }

#### 테스트
* log : 127.0.0.1 - - [29/May/2017:05:08:09 +0900] "GET /index HTTP/1.1" 200 1272
* 패턴 : ^%{IPORHOST:clientip} (?:-|%{USER:ident}) (?:-|%{USER:auth}) \[%{HTTPDATE:timestamp}\] \"(?:%{WORD:verb} %{NOTSPACE:request}(?: HTTP/%{NUMBER:httpversion})?|-)\" %{NUMBER:response} (?:-|%{NUMBER:bytes})
* 코드

		input {
			stdin { }
		}
		
		filter {
			grok {
				patterns_dir => ["./patterns"]
		    	match => { "message" => "%{TEST}" }
		  	}
		}
		
		output {
		        stdout {
		                codec => rubydebug { }
		        }
		}

* 결과

![test](http://img1.daumcdn.net/thumb/R1920x0/?fname=http%3A%2F%2Fcfile26.uf.tistory.com%2Fimage%2F222BA548592B597F0C019D)

## 4. 웹 디버거
1. https://grokdebug.herokuapp.com/
2. http://grokconstructor.appspot.com/do/match