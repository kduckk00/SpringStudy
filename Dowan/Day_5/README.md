# **Day 5**

# CLASS 01 Mybatis 프레임워크 시작하기

## 1.1 Mybatis 프레임워크 특징

  Mybatis 프레임워크의 가장 중요한 특징을 두 가지로 정리하자면 첫째는 한두 줄의 자바 코드로 DB 연동을 처리한다는 것이며 둘째는 SQL명령어를 자바코드에서 분리하여 XML 파일에 따로 관리한다는 것이다. 

 개발자는 실행되는 SQL만 관리하면 되며, Mybatis는 개발자가 이 SQL 관리에만 집중할 수 있도록 도와준다.





# CLASS 02 Mapper XML 파일 설정

## 2.1 SQL Mapper XML 기본 설정

### 2.1.1 Mybatis 구조

~~그림~~

  Mybatis는 SqlMap.xml 파일에 등록된 각 SQL 명령어들을 Map 구조로 저장하여 관리한다.



### 2.1.2 Mapper XML 파일 구조

  Mybatis 프레임워크에서 가장 중요한 파일은 SQL명령어들이 저장되는 SQL Mapp XML 파일이다.  Mapper는 < mapper >를 루트 엘리먼트로 가지는 XML 파일이다.

# 

## 2.2 SQL Mapper XML 추가 설정

### 2.2.2 CDATA Section 사용

  CDATA Section 은 Mybatis와는 상관없는 XML 고유의 문법으로서, CDATA 영역에 작성된 데이터는 단순한 문자 데이터이므로 XML 파서가 해석하지 않아도 된다. 결국 CDATA Section 안에 작성된 데이터는 XML 파서가 처리하지 않고 데이터베이스에 그대로 전달하므로 문제가 발생하지 않는다.





# CLASS 04 JPA 개념

## 4.1 JPA의 특징

  JPA는 모든 ORM 구현체들의 공통 인터페이스를 제공한다.

JDBC는 특정 DBMS에 종속되지 않는 DB 연동 구현을 지원한다. DB 연동 로직을 구현할 때 JDBC API의 인터페이스들을 이용하면 실질적인 DB 연동 처리는 해당 DBMS의 드라이버 클래스들이 담당하는 구조이다. 따라서 DBMS가 변경되는 상황에서도 드라이버만 변경하면 JDBC API를 이용하는 애플리케이션을 수정하지 않는다.





# CLASS 05 JPA 환경설정

## 5.3 JPA API 구조

  엔티티 클래스에 기본적으로 매핑을 설정했으면 이제 JPA에서 지원하는 API를 이용하여 데이터베이스 연동을 처리할 수 있다. 애플리케이션에서 JPA를 이요하여 CRUD 기능을 처리하려면 엔티티 관리자 객체를 사용해야한다.

  JPA를 이용한 애플리케이션의 시작은 EntityManager 객체 생성이라고 할 수 있다.

~~그림~~

EntityManager 객체가 제공하는 CRUD 기능의 메소드

| 메소드                                               | 기능 설명                                             |
| ---------------------------------------------------- | ----------------------------------------------------- |
| persist(Object entity)                               | 엔티티를 영속화한다.(INSERT)                          |
| merge(Object entity)                                 | 준영속 상태의 엔티티를 영속화한다.(UPDATE)            |
| remove(Object entity)                                | 영속 상태의 엔티티를 제거한다. (DELETE)               |
| find(Class< T > entityClass, Object primaryKey)      | 하나의 엔티티를 검색한다. (SELECT ONE)                |
| createQuery(String qlString, Class< T > resultClass) | JPQL에 해당하는 엔티티 목록을 검색한다. (SELECT LIST) |



