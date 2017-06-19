# Oracle SQL

## 1. 관리
* 시스템관리자로 로그인하기
	1. 암호없이: `/as sysdba`
	2. 암호있이: `sys as sysdba` (암호는없으니 엔터)

* 사용자 조회 : `select * from all_users;`

* 지금 내가 누구인지 조회 : `show user`

* Lock 풀기 : `alter user 사용자 account unlock;` (ex: alter user hr account unlock;)

* 계정 비번 변경 : `alter user 사용자 identified by 비번;` (ex: alter user hr identified by hr;)

* 권한부여
`grant connect, resource, create view to 사용자;`

* 계정 로그인(connect=conn 축약 가능)
	1. 관리자: `connect /as sysdba;`
	2. 일반: `connect 사용자/비번;`

* 계정 안에 구축되있는 테이블 조회 : `select * from tab;`

* Lock 걸린 사용자 조회 : `select username, account_status, to_char(lock_date, 'YYYY.MM.DD HH24:MI') rock_date from dba_users;`

* 휴지통 보기 : `show recyclebin;`

* 휴지통 비우기 : `purge recyclebin;`

* 화면 조정: 가로 길이(linesize), 세로길이(pagesize) 조정. `set linesize 300; set pagesize 100;`

## 2. DML 관련
* 테이블 생성 : `create table 테이블명(애트리뷰트명 타입 옵션, 나열...);`
▷ ex: create table buseo (deptno number not null, deptname char(10), floor number, primary key(deptno));

* 테이블 구조 확인 : `desc 테이블명;`

* 테이블 복사(CTAS) : `create table 새 테이블명 as select 애트리뷰트 from 복사할 테이블;`
▷ ex: create table imsi as select * from employees;

* 애트리뷰트 추가 : `alter table 테이블명 add 애트리뷰트명 타입;`
▷ ex: alter table imsi add age varchar2(4);

* 애트리뷰트 삭제 : `alter table 테이블명 drop column 애트리뷰트명;`
▷ ex: alter table imsi drop column email;

* 애트리뷰트 수정 : `alter table 테이블명 modify 애트리뷰트명 타입;`
▷ ex: alter table imsi modify age number(4);

* 애트리뷰트 이름수정 : `alter table 테이블명 rename column 원래 애트리뷰트명 to 새 애트리뷰트명;`
▷ ex: alter table imsi rename column age to nai;

* 테이블 이름수정 : `rename 원래 테이블명 to 새 테이블명;`
▷ ex: rename imsi to imsi2;

* 테이블 삭제 : `drop table 테이블명;`
▷ ex: drop table imsi2;

* 테이블 내 데이터를 전부 삭제(구조는 유지하되, 데이터만 전부 삭제) : `truncate table 테이블명;`
▷ ex: truncate table imsi2;

* 유저생성 : `create user 사용자 identified by 비번;` (ex: create user lee identified by lee;)

* 계정 삭제 : `drop user 유저명 cascade;`
※ cascade는 사용자 동의없이 사용자 계정과 관련된 모든 데이터베이스 스키마를 삭제한다

## 3. DDL 관련
* 테이블 복사

CTAS 기법은 동일 계정내에서 복사하는 것
copy명령은 다른 계정에 테이블복사 가능(@는 SID, 오라클은 orcl, 교육용은 XE)
`COPY FROM HR/비번@orcl CREATE 새테이블명 USING SELECT * FROM 기존테이블명`

▷ ex:
(copy명령) copy from hr/hr@orcl create staff using select * from employees 
= (동일하다)
(CTAS기법) create table staff as select * from employees;

SQL>copy from scott/tiger@orcl create aaa using select * from emp

### 3-1. insert구문
`insert into 테이블명 (애트리뷰트 나열하면 일부삽입, 아예 안쓰면 전체삽입) values (애트리뷰트 수에 맞춰 나열);`

값을 쓸때 문자열은 'string' 로 감싸야 한다. ▷ ex: insert into buseo values(1, 'marketing', 8);

### 3-2. update구문
`update 테이블명 set 애트리뷰트명=수정값 (where절)`

### 3-3. delete구문
`delete from 테이블명 (where절)`

※ DDL은 auto commit, DML은 수동으로 commit해야 한다. 즉 delete된거는 rollback으로 복구 가능.

### 3-4. select구문
* 옵션
	1. all: default라 생략 가능
	2. distinct: 중복된 데이터가 있는 경우 1건으로 처리

* alias(별명) 사용방법 3가지
	1. select 애트리뷰트명 as 별명
	2. select 애트리뷰트명 별명: as생략
	3. select 애트리뷰트명 "자유롭게 별명": ""를 이용하여 다양하게 변경가능. 띄워쓰기도 가능.

* 더미테이블: dual은 더미테이블을 나타냄. ▷ ex: select sysdate from dual;

#### ※ 산술연산자 가능, 합성연산자는 ||인데, 문자와 문자를 연결할때 사용
▷ ex: select first_name || ' ' || last_name "이 름" from employees;

▷ ex: select '나의 연락처는 '||email|| ' 이며 전화번호는 ' ||phone_number||'이다.' "연 락 처" from employees;

## 4. TCL 관련

### 4-1. 트랜잭션 개요
데이터베이스의 논리적 연산단위, 하나 이상의 SQL 문장이 포함됨.

* A원자성: all or nothing
* C일관성: 이전이 정상이면 이후도 정상
* I고립성: 다른 트랜잭션의 영향받으면 안됨
* D지속성: 영구적 저장

### 4-2. 트랜잭션 명령어
* commit: 영구적으로 반영해라
이전 상태: 변경된 행은 잠금 설정, 다른사용자 열람 불가
이후 상태: 잠금 해제, 모든 사용자 열람 가능
* rollback: 변경 전으로 복귀하라(`rollback to 저장점이름;`)
* savepoint: 저장점 만들기(`savepoint 저장점이름;`)

	
	