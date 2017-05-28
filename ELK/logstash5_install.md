# logstash 5.4 설치메뉴얼
centos 7을 기준으로 한다. jdk8이 필요하다.

참조문서: https://www.elastic.co/guide/en/logstash/5.4/index.html

## 1. 설치

	curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.4.0.tar.gz
	tar -xvf logstash-5.4.0.tar.gz
	cd logstash-5.4.0/bin

* 테스트

	`./logstash -e 'input { stdin { } } output { stdout {} }'`

	input을하면 ouput이 출력된다. -e 는 명령창에 직접 코드치는 옵션이다. localhost:9600 에 들어가보면 json이 간단히 떠있다. 종료는 ctrl+d로 한다.

		{"host":"ljh.server","version":"5.4.0","http_address":"127.0.0.1:9600","id":"dd123456-f6ec-12f8-bebf-12d3ae9a1234","name":"ljh.server","build_date":"2017-04-28T18:14:40Z","build_sha":"1234bc8cf2e9ff169bf5a08b1234c62dbeb92f9b","build_snapshot":false}

	
## 2. 설정
logstash는 입출력 도구이며, input > filter > output 의 파이프라인구조를 갖는다. config/logstash.conf를 수정하여 코드를 작성하고 `bin/logstash -f config/logstash.conf`를 통해 돌리면 된다.