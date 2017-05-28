# elasticsearch 5.4 설치메뉴얼
현재 날짜기준 최신버전인 5.4를 설치해보자, 5.x부터 많이 바뀌어서 설치에 문제가 있었다. centos 7을 기준으로 한다. jdk8이 필요하다.

참조문서: https://www.elastic.co/guide/en/elasticsearch/reference/5.4/index.html

## 1. 설치
5.x부터는 root유저로 돌릴 수 없기때문에, 애초에 useradd를 통해 es_user를 만든 후 진행해야한다.

	curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.4.0.tar.gz
	tar -xvf elasticsearch-5.4.0.tar.gz
	cd elasticsearch-5.4.0/bin
	./elasticsearch

여기까지하면 localhost:9200에 바로 올라온다.(http포트는 9200~9300에서 설정할 수 있다) curl이나 브라우저를 통해 들어가보면 rest api로 헬스가 간단히 떠있다.

	ex.
	{ "name" : "Ciirfns", "cluster_name" : "elasticsearch", "cluster_uuid" : "wKs5aMyaTu6F55Kd1YG19A", "version" : { "number" : "5.4.0", "build_hash" : "780f8c4", "build_date" : "2017-04-28T17:43:27.229Z", "build_snapshot" : false, "lucene_version" : "6.5.0" }, "tagline" : "You Know, for Search" }

## 2. 클러스터링으로 구축
엘라스틱서치는 설정파일에서 cluster.name를 맞추면 자동으로 클러스터링이 구축된다. discovery.zen를 통해 다른 노드를 쉽게 찾을 수 있다. 여기서는 4대의 노드를 클러스터링한다.

* `node settings must not contain any index level settings` : 5.x부터는 elasticsearch.yml에서 index수준 설정이 없어야한다. 예를들어 기존에는 yml에서  index.number_of_shards: 5 를 처리했지만, 이제는 여기서 설정하는게 아니고 rest로 curl put으로 처리한다.
* `ax file descriptors [4096] for elasticsearch process is too low, increase to at least [65536]` :  이전에는 잘못 구성하면 경고 수준의 로그를 기록하고 es를 구동시켜주었지만, 이제는 시작할때 아예 부트스트랩 검사를 해서 설정이 잘못되면 es가 시작되지 않는다.

		sudo vi /etc/security/limits.conf

		# 추가
		ec_user hard nofile 65536
		ec_user hard nofile 65536
		ec_user hard nproc 65536
		ec_user sort nproc 65536

		sudo vi /etc/rc.local

		# 추가
		echo 1048575 > /proc/sys/vm/max_map_count

		sudo sysctl -w fs.file-max=262114

* 마스터노드 elasticsearch.yml

		cluster.name: es-cluster
		node.name: node-1
		node.master: true
		node.data: false
		network.host: 0.0.0.0
		transport.tcp.port: 9300
		transport.tcp.compress: true
		http.port: 9200
		http.enabled: true
		discovery.zen.ping.unicast.hosts: ['다른노드IP주소:9300', '다른노드IP주소:9300', '다른노드IP주소:9300']


		sudo vi /etc/rc.local

		# 추가
		echo 1048575 > /proc/sys/vm/max_map_count

		sudo sysctl -w fs.file-max=262114

* 데이터노드 elasticsearch.yml

		cluster.name: es-cluster
		node.name: node-2
		node.master: false
		node.data: true
		network.host: 0.0.0.0
		transport.tcp.port: 9300
		transport.tcp.compress: true
		http.port: 9200
		http.enabled: true
		discovery.zen.ping.unicast.hosts: ['다른노드IP주소:9300', '다른노드IP주소:9300', '다른노드IP주소:9300']

* 구축 잘 되었는지 보기

		curl localhost:9200/_cat/health?v
		curl localhost:9200/_cat/nodes?v

![구축완료](http://img1.daumcdn.net/thumb/R1920x0/?fname=http%3A%2F%2Fcfile29.uf.tistory.com%2Fimage%2F2660644D592B50A4149FA9)

## 3. 저장경로 변경
elasticsearch.yml에서 path.data를 통해 설정할 수 있다.

`ex. path.data: /mnt/tmp/es_data`

## 4. 데이터 엑세스 api
엘라스틱서치는 모든 것을 rest api로 지원한다.

`REST Verb /Index/Type/ID`