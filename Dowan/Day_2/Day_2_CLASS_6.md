# CLASS 06 스프링 JDBC

## 6.1 스프링 JDBC 개념

  JDBC는 가장 오랫동안 자바 개발자들이 사용한 DB 연동 기술이다. JDBC를 이용하여 DB 연동 프로그램을 개발하면 데이터베이스에 비종속적인 DB 연동 로직을 구현할수 있다.

  JDBCUtil 클래스를 이용하여 커넥션 연결과 해제 로직을 대체할 수도 있다. 그렇더라도 많은 양의 코드가 필요하다. JDBC 관련 코드들은 다른 메소드들을 구현 할 때마다 **반복해서 작성**해야하는 큰 문제가 있다.

  DB연동에 필요한 자바 코드를 누군가가 대신 처리해주고 개발자는 실행되는 SQL 구문만 관리한다면 개발과 유지보수는 훨씬 편해질 것이다. 스프링은 JDBC 기반의 DB 연동 프로그램을 쉽게 개발할 수 있도록 JdbcTemplate 클래스를 지원한다.





## 6.2 JdbcTemplate 클래스

  템플릿 메소드 패턴은 복잡하고 반복되는 알고리즘을 캡슐화해서 재사용하는 패턴으로 정의할 수 있다. 반복되는 DB 연동 로직은 JdbcTemplate 클래스의 템플릿 메소드가 제공하고 개발자는 달라지는 SQL 구문과 설정값만 신경 쓰면 된다.

~~그림~~

JdbcTemplate 은 JDBC의 반복적인 코드를 제거하기 위해 제공하는 클래스다. 따라서 DAO 클래스에서는 JdbcTemplate 클래스가 제공하는 템플릿 메소드를 호출하여 DB 연동을 간단하게 처리할 수 있다. 





## 6.3 스프링 JDBC 설정

### 6.3.1 라이브러리 추가

  스프링 JDBC를 이용하려면 BoardWeb 프로젝트에 있는 pom.xml 파일에 DBCP 관련 < depencency > 설정을 추가해야한다.

### 6.3.2 DataSource 설정

  JdbcTemplate 클래스가 JDBC API를 이용하여 DB연동을 처리하려면 반드시 데이터베이스로부터 커넥션을 얻어야 한다. DataSource 설정은 스프링 JDBC만을 위한 설정이 아니고 테스트할 트랜잭션 처리나 Mybatis 연동, JPA 연동에서도 DataSource가 사용되므로 매우 중요한 설정이라 할 수 있다.

### 6.3.3 프로퍼티 파일을 활용한 DataSource 설정

  "${}" 구문을 이용하여 프로퍼티 이름을 지정하면 프로퍼티 값으로 치환되어 실행된다.





## 6.4 JdbcTempate 메소드

### 6.4.1 update() 메소드

~~사진~~



### 6.4.2 queryForInt() 메소드

~~사진~~



### 6.4.3 queryForObject() 메소드

~~사진~~



### 6.4.4 query() 메소드

~~사진~~





## 6.5 DAO 클래스 구현



### 6.5.1 첫 번재 방법 : JdbcDaoSupport 클래스 상속

  DAO 클래스를 구현할 때, JdbcDaoSupport 클래스를 부모 클래스로 지정하면 getJdbcTemplate() 메소드를 상속받을 수 있다. 그리고 getJdbcTemplate() 메소드를 호출하면 JdbcTemplate 객체가 리턴되어 모든 메소드를 JdbcTemplate 객체로 구현할 수 있다.

  getJdbcTemplate() 메소드가 JdbcTemplate 객체를 리턴하려면 DataSource 객체를 가지고 있어야하므로 반드시 부모 클래스인 JdbcDaoSupport 클래스의 setDataSource() 메소드를 호출하여 데이터소스 객체를 의존성 주입해야한다.



### 6.5.2 두 번재 방법 : JdbcTemplate 클래스 < bean > 등록, 의존성 주입

  DAO 클래스에서 JdbcTemplate 객체를 얻는 두번째 방법은 JdbcTemplate 클래스를 < bean > 등록하고, 의존성 주입으로 처리하는 것이다. 

**JdbcDaoSupport 클래스를 상속하여 구현하는 것보다 좀더 깔끔함**❗

