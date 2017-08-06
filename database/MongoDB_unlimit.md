# MongoDB 운용시 권장 ulimit

참조문서 : https://docs.mongodb.com/manual/reference/ulimit/

공식문서에서 아래처럼 설정하랜다.

Recommended ulimit Settings

Every deployment may have unique requirements and settings; however, the following thresholds and settings are particularly important for mongod and mongos deployments:

	-f (file size): unlimited
	-t (cpu time): unlimited
	-v (virtual memory): unlimited
	-n (open files): 64000
	-m (memory size): unlimited
	-u (processes/threads): 64000