# EHCache

참조문서

* http://www.ehcache.org/documentation/
* https://blog.outsider.ne.kr/1094
* http://javacan.tistory.com/entry/133

캐시 엔진 중에 유명한 것들은 memcached, redis 등이 있다. ehcache는 간단한게 쓸 수 있는 캐시라이브러리이며, spring에서 어노테이션으로도 쉽게 쓸 수 있다. 캐시를 적용하기 최적인 경우는 20%의 데이터가 전체 조회 건수의 80%를 차지할 경우(80:20 법칙?) 캐시를 사용함으로써 성능을 대폭 향상 가능하다.

## 1. EHCache란?
캐시 엔진 중의 하나이다. 주요 특징으로는 아래와 같다.

* 경량의 빠른 캐시 엔진
* 확장성 : 메모리, 디스크 저장 지원, 멀티 CPU의 동시 접근에 튜닝
* 분산 지원 : 동기, 비동기 복사, 피어 자동 발견
* 높은 품질

### 왜 쓰는가?
* Java 메서드에 캐싱을 적용함으로써 캐시에 보관된 정보로 메서드의 실행 횟수를 줄여준다.
* 대상 메서드가 실행될 때마다 추상화가 해당 메서드가 같은 인자로 이미 실행되었는지 확인하는 캐싱 동작을 적용
* 해당 데이터가 존재한다면 실제 메서드를 실행하지 않고 결과를 반환하고 존재하지 않는다면 메서드를 실행하고 그 결과를 캐싱한 뒤에 사용자에게 반환해서 다음번 호출 시에 사용할 수 있게 한다. 이 방법을 통해 비용이 큰 메서드(CPU든 IO든) 해당 파라미터로는 딱 한 번만 실행
* 물론 이 접근방법은 얼마나 많이 호출하든지 간에 입력(혹은 인자)이 같으면 출력(결과)도 같다는 것을 보장하는 메서드에서만 동작

## 2. 설치
ehcache-spring-annotations 의존성 추가하고 XML을 설정(cacheManager bean 생성 등)한다.

## 3. 엘리먼트와 속성
설정 파일에 `<cache>` 를 통해 캐시를 설정한다.

|              속성명             |                                                                    설명                                                                   | 필수 |
|:-------------------------------:|:-----------------------------------------------------------------------------------------------------------------------------------------:|:----:|
| name                            | 캐시의 이름                                                                                                                               | O    |
| maxElementsInMomory             | 메모리에 저장될 수 있는 객체의 최대 개수                                                                                                  | O    |
| eternal                         | true로 하면 timeout 설정 무효, element가 캐시에서 삭제안됌                                                                                | O    |
| overflowToDisk                  | 메모리에 저장된 객체 개수가 maxElementsInMemory에서 지정한 값을 넘으면 디스크에 넘치는 객체들을 저장할 지 여부                            | X    |
| timeToIdelSeconds               | Element가 지정한 Seconds 동안 사용되지 않으면 캐시에서 제거. 값이 0이면 조회 관련 만료 시간을 지정X. 디폴트는 0                           | X    |
| timeToLiveSeconds               | Element가 존재하는 시간. Seconds가 지나면 캐시에서 자동 제거. 값이 0이면 조회 관련 만료 시간을 지정X. 디폴트는 0                          | X    |
| diskPersistent                  | VM이 재가동할 때 디스크 저장소에 캐싱된 객체를 저장할지의 여부                                                                            | X    |
| diskExpiryThreadIntervalSeconds | Disk Expiry 쓰레드의 수행 시간 간격을 초 단위로 지정. 디폴트 120                                                                          | X    |
| memoryStoreEvictionPolicy       | 객체의 개수가 maxElementsInMemory에 도달했을 때, 메모리에서 객체를 어떻게 제거할 지에 대한 정책을 지정. LRU, FIFO, LFU 가능, 디폴트는 LRU | X    |

### PS. 페이지 교체 알고리즘
운영체제때 배운게 이런 곳에서....

* LRU(Least Recently Used) : 가장 오래된걸 날림(마지막 사용시간을 기록)
* LFU(Least Frequently Used) : 가장 참조 적게된 걸 날림
* FIFO : 선입선출(메모리에 들어온 시간을 기록)

## 4. XML 설정
applicationContext-ehcache.xml

    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:aop="http://www.springframework.org/schema/aop" xmlns:ehcache="http://ehcache-spring-annotations.googlecode.com/svn/schema/ehcache-spring"
        xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
            http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd
            http://ehcache-spring-annotations.googlecode.com/svn/schema/ehcache-spring
            http://ehcache-spring-annotations.googlecode.com/svn/schema/ehcache-spring/ehcache-spring-1.1.xsd">

        <!-- declare ehCache -->
        <ehcache:annotation-driven />

        <ehcache:config cache-manager="cacheManager">
            <ehcache:evict-expired-elements interval="60" />
        </ehcache:config>

        <!-- ehCache bean -->
        <bean id="cacheManager" class="org.springframework.cache.ehcache.EhCacheManagerFactoryBean">
            <property name="configLocation" value="classpath:ehcache.xml" />
        </bean>

    </beans>

ehcache.xml : 여기서 생성된 고유한 cache들은 map같은 개념으로 동작하는 듯.

    <?xml version="1.0" encoding="UTF-8"?>
    <ehcache xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:noNamespaceSchemaLocation="http://ehcache.org/ehcache.xsd" updateCheck="false">

        <defaultCache
            eternal="false"
            maxElementsInMemory="500"
            overflowToDisk="false"
            diskPersistent="false"
            timeToIdleSeconds="0"
            timeToLiveSeconds="600"
            memoryStoreEvictionPolicy="LRU"/>
        
        <cache
            name="serviceCache10sec"
            eternal="false"
            maxElementsInMemory="100"
            overflowToDisk="false"
            diskPersistent="false"
            timeToIdleSeconds="0"
            timeToLiveSeconds="10"
            memoryStoreEvictionPolicy="LRU" />
        
        <cache
            name="serviceCache1min"
            eternal="false"
            maxElementsInMemory="800"
            overflowToDisk="false"
            diskPersistent="false"
            timeToIdleSeconds="0"
            timeToLiveSeconds="60"
            memoryStoreEvictionPolicy="LFU" />
    </ehcache>

## 5. 어노테이션
XML 설정파일에서 `<ehcache:annotation-driven />`를 하면 자바 코드에서 어노테이션을 사용할 수 있다.

* @Cacheable : 캐시할 수 있는 메소드를 지정
* @CachePut : 메서드 실행에 영향을 주지 않고 캐시를 갱신
* @CacheEvict : 캐시에서 오래되거나 사용하지 않는 데이터를 제거하는 메소드를 지정, void 반환형에서만 사용 가능
* @Caching : 어노테이션을 여러개 사용할때

        @Caching(evict = { @CacheEvict("primary"), @CacheEvict(value = "secondary", key = "#p0") })
        public Book importBooks(String deposit, Date date)