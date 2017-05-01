# Redis
## 1. 개념
Redis는 REmote DIctionary System의 약자로 메모리 기반의 Key/Value Store이다.
Cassandra나 HBase와 같이 NoSQL DBMS로 분류되기도 하고, memcached와 같은 In memory 솔루션으로 분리.
성능은 memcached에 버금가면서 다양한 데이타 구조체를 지원함으로써 Message Queue, Shared memory, Remote Dictionary 용도.
BSD 라이센스 기반의 오픈 소스, 최근 VMWare에 인수.

redis는 데이타를 disk에 저장할 수 있다. memcached의 경우 메모리에만 데이타를 저장하기 때문에 서버가 shutdown 된후에 데이타가 유실 되지만, redis는 서버가 shutdown된 후 restart되더라도, disk에 저장해놓은 데이타를 다시 읽어서 메모리에 Loading하기 때문에 데이타 유실되지 않는다.

1. snapshotting (RDB) 방식: 순간적으로 메모리에 있는 내용을 DISK에 전체를 옮겨 담는 방식
2. AOF 방식: AOF(Append On File) 방식은 redis의 모든 write/update 연산 자체를 모두 log 파일에 기록하는 형태
3. 권장: 주기적으로 snapshot으로 백업하고, 다음 snapshot까지의 저장을 AOF 방식으로 수행

## 2. 설치
1. wget http://download.redis.io/releases/
2. redis-3.0.1.tar.gz
3. tar xzf redis-3.0.1.tar.gz
4. cd redis-3.0.1
5. make
6. make install
7. cd utils
8. ./install_server.sh

### 관리
* 확인: ps aux | grep redis
* 서버켜기: /etc/init.d/redis_6379 start
* 서버끄기: /etc/init.d/redis_6379 stop
* 접속: redis-cli