## 1. linux timezone 설정
**참조문서:** http://docs.aws.amazon.com/ko_kr/AWSEC2/latest/UserGuide/set-time.html

기본적으로 EC2 인스턴스는 UTC 표준시간대로 설정된다. 이는 date를 쳐보면 알 수 있다. 이것을 한국시간대에 변경하려면, localtime 심볼릭링크를 지워주고 다시 잡아주면 된다.

`rm /etc/localtime`

`ln -sf /usr/share/zoneinfo/Asia/Seoul /etc/localtime`

이 후, 

`# date`

`Thu May 11 03:53:23 KST 2017`

를 확인할 수 있다.

## 2. tomcat timezone 설정
그러나 tomcat의 시간은 리눅스서버의 시간과는 별도였다.... 톰캣에서는 /usr/share/tomcat8/bin/catalina.sh에서 setenv.sh을 읽어다가 환경변수를 잡아준다. tomcat8에서는 기본적으로 setenv.sh가 없기때문에 만들어준다.

`vi /usr/share/tomcat8/bin/setenv.sh`

	#!/bin/bash
	export CATALINA_OPTS="$CATALINA_OPTS -Dfile.encoding=UTF8 -Duser.timezone=GMT+9"