# Oracle SQL

* 시스템관리자로 로그인하기
	1. 암호없이: `/as sysdba`
	2. 암호있이: `sys as sysdba` (암호는없으니 엔터)

* 사용자 조회 : `select * from all_users;`

* 지금 내가 누구인지 조회 : `show user`

* Lock 풀기 : `alter user 사용자 account unlock;` (ex: alter user hr account unlock;)

* 계정 비번 변경 : `alter user 사용자 identified by 비번;` (ex: alter user hr identified by hr;)

* 유저생성 : `create user 사용자 identified by 비번;` (ex: create user lee identified by lee;)

* 권한부여
`grant connect, resource, create view to 사용자;`

* 계정 로그인(connect=conn 축약 가능)
	1. 관리자: `connect /as sysdba;`
	2. 일반: `connect 사용자/비번;`

* 계정 안에 구축되있는 테이블 조회 : `select * from tab;`

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

* Lock 걸린 사용자 조회 : `select username, account_status, to_char(lock_date, 'YYYY.MM.DD HH24:MI') rock_date from dba_users;`

* 계정 삭제 : `drop user 유저명 cascade;`
※ cascade는 사용자 동의없이 사용자 계정과 관련된 모든 데이터베이스 스키마를 삭제한다