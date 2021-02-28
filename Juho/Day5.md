# Day5

## MyBatis

**1. MyBatis 프레임워크 특징**

1. 짧은 자바 코드로 DB 연동 처리
2. SQL 명령어를 자바코드에서 분리 -> XML 파일에 따로 관리
3. XML 파일에 저장된 SQL 명령어를 대신 실행 & 자바 객체에 자동으로 매핑



## JPA

**1. JPA의 특징**

- 모든 ORM 프레임워크들의 공통 인터페이스 제공
  (JPA를 이용해 Hibernate, TopLink 등의 프레임워크 이용 가능)

**2. JPA 관련 라이브러리 추가**

```xml
<!-- JPA, Hibernate -->
<dependency>
    <groupId>org.hibernate</groupId>
    <artifactId>hibernate-entitymanager</artifactId>
    <version>5.1.0.Final</version>
</dependency>
  	
<!-- H2 -->
<dependency>
    <groupId>com.h2database</groupId>
    <artifactId>h2</artifactId>
    <version>1.4.200</version>
</dependency>
```

**3. JPA 시작**

1. 엔티티 클래스 매핑(DB 테이블과 매핑될 영속 클래스)

| 어노테이션        | 의미                                                         |
| ----------------- | ------------------------------------------------------------ |
| `@Entity`         | 해당 클래스를 테이블과 매핑되는 엔티티 클래스로 지정.        |
| `@Table`          | 엔티티와 관련된 테이블을 매핑. name 속성 생략 시 클래스 이름의 테이블과 매핑 |
| `@Id`             | 엔티티 클래스의 필수 어노테이션. 해당 필드를 테이블의 기본 키와 매핑 |
| `@GeneratedValue` | `@Id`가 선언된 필드에 기본 키를 자동으로 생성하여 할당       |
| `@Temporal`       | 날짜 타입을 매핑할 때 사용                                   |
| `@Transient`      | 해당 필드를 테이블과의 매핑에서 제외                         |

1. persistence.xml 파일 작성

   - JPA는 persistence.xml 파일로 설정 정보를 관리

   - META-INF 폴더 아래에 있으면 JPA가 인식

   - 연동할 DB당 하나의 영속성 유닛(`persistence-unit`) 사용

     ```xml
     <persistence-unit name="JPAProject">
         <class>com.springbook.biz.board.Board</class>
         <properties>
             <!-- 필수 속성 -->
             <property name="javax.persistence.jdbc.driver" value="org.h2.Driver"></property>
     		<property name="javax.persistence.jdbc.user" value="sa"></property>
     		<property name="javax.persistence.jdbc.password" value=""></property>
     		<property name="javax.persistence.jdbc.url" value="jdbc:h2:tcp://localhost/~/test"</property>
     		<property name="hibernate.dialect" value="org.hibernate.dialect.H2Dialect"></property>
     			
     		<!-- 옵션 -->
     		<property name="hibernate.show_sql" value="true"></property>
     		<property name="hibernate.format_sql" value="true"></property>
     		<property name="hibernate.use_sql_comments" value="false"></property>
     		<property name="hibernate.id.new_generator_mappings" value="true"></property>
     		<property name="hibernate.hbm2ddl.auto" value="create"></property>
         </properties>
     </persistence-unit>
     ```

2. 클라이언트 프로그램

   - JPA를 이용해 CRUD를 구현하기 위해 `EntityManager` 객체 사용
   - `EntityManager` 객체는 `EntityManagerFactory` 객체로부터 얻을 수 있음



## JPA 환경설정

**1. 영속성 유닛 설정**

1. 영속성 유닛 이름 설정

   - 연동할 DB당 하나씩 등록

   - 설정된 이름은 DAO 클래스 구현 시 `EntityManagerFactory` 객체 생성에 사용됨

     ```xml
     <persistence-unit name="JPAProject">
         ...
     </persistence-unit>
     ```

     

2. 엔티티 클래스 등록

   - 스프링 프레임워크에서 JPA를 사용하면 자동으로 엔티티 클래스를 검색 & 처리

     ```xml
     <persistence-unit name="JPAProject">
         <class>com.springbook.biz.board.Board</class>
         ...
     </persistence-unit>
     ```

3. 영속성 유닛 프로퍼티 설정

   - DB 커넥션

   - 스프링 프레임워크와 연동 시 데이터소스가 스프리 설정파일에 등록되어 있어 영속성 유닛 설정에서 제거될 수 있음

     ```xml
     <persistence-unit name="JPAProject">
     		<class>com.springbook.biz.board.Board</class>
     		<properties>
     			<!-- 필수 속성 -->
     			<property name="javax.persistence.jdbc.driver" value="org.h2.Driver"></property>
     			<property name="javax.persistence.jdbc.user" value="sa"></property>
     			<property name="javax.persistence.jdbc.password" value=""></property>
     			<property name="javax.persistence.jdbc.url" value="jdbc:h2:tcp://localhost/~/test"></property>
                 ...
         </properties>
     </persistence-unit>
     ```

4. Dialect 클래스 설정

   ```xml
   <property name="hibernate.dialect" value="org.hibernate.dialect.H2Dialect"></property>
   ```

5. JPA 구현체 관련 설정

   ```xml
   <persistence-unit name="JPAProject">
       <class>com.springbook.biz.board.Board</class>
       <properties>
           ...
           <!-- 옵션 -->
           <property name="hibernate.show_sql" value="true"></property> 생성된 SQL을 출력
           <property name="hibernate.format_sql" value="true"></property> SQL 출력 시 보기 좋게 출력
           <property name="hibernate.use_sql_comments" value="false"></property> SQL에 포함된 주석도 출력
           <property name="hibernate.id.new_generator_mappings" value="true"></property> 새로운 키 생성 전략 사용
           <property name="hibernate.hbm2ddl.auto" value="create"></property> DDL 구문 자동으로 처리 여부
       </properties>
   </persistence-unit>
   ```

   - `hibernate.hbm2ddl.auto` 속성값

     | 속성값        | 의미                                                        |
     | ------------- | ----------------------------------------------------------- |
     | `create`      | 애플리케이션 실행 시 기존 테이블 삭제 (DROP -> CREATE)      |
     | `create-drop` | 애플리케이션 종료 직전 테이블 삭제 (DROP -> CREATE -> DROP) |
     | `update`      | 기존 테이블이 있으면 재사용 (ALTER)                         |

     
