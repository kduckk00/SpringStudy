# Day2

## 스프링 AOP

**1. 배경**

- 애플리케이션의 메소드 = 핵심 비즈니스 로직 + 부가적 코드(로깅, 트랜잭션) -> 유지보수 어려움 -> AOP로 해결하려 함
- AOP의 핵심은 **관심 분리**
- 관심 분리 = 메소드를 횡단 관심(로깅, 트랜잭션 등) + 핵심 관심(비즈니스 로직)으로 분리하는 것

**2. AOP 사용을 위한 설정**

1. pom.xml 파일에 dependecy 추가

```java
<!-- Aspectj -->
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjrt</artifactId>
    <version>${org.aspectj-version}</version>
</dependency>
		
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <version>${org.aspectj-version}</version>
</dependency>
```

2. applicationContext.xml 파일에 네임스페이스 추가

```java
<beans ...
    xmlns:aop="http://www.springframework.org/schema/aop"
    xsi:schemaLocation="http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.2.xsd">
```



## AOP용어 및 기본 설정

 **1. 조인포인트(Joinpoint)**

- 클라이언트가 호출하는 모든 비즈니스 메소드

**2. 포인트컷(Pointcut)**

- 필터링된 조인포인트(조인포인트의 부분집합)

**3. 어드바이스(Advice)**

- 횡단 관심에 해당하는 공통 기능. 독립된 클래스의 메소드로 작성

**4. 위빙(Weaving)**

- 포인트컷으로 지정한 핵심 관심 메소드가 호출될 때, 어드바이스에 해당하는 횡단 관심 메소드가 삽입되는 과정  
- 스프링에서는 런타임 위빙 방식만 지원

**5. 애스팩트(Aspect) or 어드바이저(Advior)**

- 애스팩트(or 어드바이저. 비슷한 의미) = 포인트컷 + 어드바이스



## AOP 설정 - XML 방식

**1. `<aop:config>`**

- AOP 설정에서 루트 엘리먼트
- 여러 번 사용 가능
- 하위에 `aop:pointcut`, `aop:aspect`가 위치 가능

```xml
<beans ...>
    <aop:config>
        <aop:pointcut ...> </aop:pointcut>
        <aop:aspect ...> </aop:aspect>
    </aop:config>
</beans>
```

**2. `<aop:pointcut>`**

- 포인트컷을 지정
- `<aop:config>`의 자식 or `<aop:aspect>`의 자식 엘리먼트(이 경우 해당 aspect 내에서만 사용 가능)로 사용 가능
- 여러 개 정의 가능
- 애스팩트 설정 시 포인트컷ID 참조

```xml
<aop:config>
    <aop:pointcut id="포인트컷ID" expression="execution(...)"></aop:pointcut>
</aop:config>
```

**3. `<aop:aspect>`**

- 포인트컷 메소드와 어드바이스 메소드 결합
- 설정에 따라 위빙 결과가 달라짐

```xml
<!-- getPointcut으로 설정한 포인트컷 메소드가 호출될 때,
     log라는 어드바이스 객체의 printLog()메소드 실행,
     이때 printLog() 메소드 동작 시점이 <aop:before> -->

<bean id="log" class="..."></bean>

<aop:config>
    <aop:pointcut id="getPointcut" expression="execution(...)"></aop:pointcut>
    
    <aop:aspect ref="log">
        <aop:before pointcut-ref="getPointcut" method="printLog"></aop:before>
    </aop:aspect>
</aop:config>
```

**4. `<aop:advisor>`**

- 애스팩트와 비슷하지만, 트랜잭션 설정과 같은 특수한 경우에 어드바이저 사용

```xml
<!-- <tx:advice>로 생성된 어드바이스의 id는 확인되지만 메소드 이름은 모름
     이럴 때 <aop:aspect> 대신 <aop:advisor> 사용 -->

<bean id="txManager" class="..."> </bean>

<tx:advice id="txAdvice" transaction-manager="txManager">
    <tx:attributes>
        <tx:method name="get*" read-only="true"></tx:method>
        <tx:method name="*"></tx:method>
    </tx:attributes>
</tx:advice>

<aop:config>
    <aop:pointcut id="allPointcut" expression="execution(...)"></aop:pointcut>
    
    <aop:advisor pointcut="allPointcut" advice-ref="txAdvice"></aop:advisor>
</aop:config>
```



## 포인트컷 표현식

포인트컷 표현식은 ***execution*** 명시자를 이용하여 기술

`execution(반환타입 패키지경로 클래스명 메소드명(매개변수))`

**1. 반환타입**

| 표현식 | 설명                          |
| ------ | ----------------------------- |
| *      | 모든 반환타입 허용            |
| void   | 반환타입이 void인 메소드      |
| !void  | 반환타입이 void가 아닌 메소드 |

**2. 패키지**

| 표현식               | 설명                                                         |
| -------------------- | ------------------------------------------------------------ |
| com.springbook.biz   | com.springbook.biz 패키지                                    |
| com.springbook.biz.. | com.springbook.biz 패키지로 시작하는 모든 패키지             |
| com.springbook..impl | com.springbook 패키지로 시작 + 마지막 패키지 이름이 impl로 끝내는 패키지 |

**3. 클래스**

| 표현식           | 설명                                             |
| ---------------- | ------------------------------------------------ |
| BoardServiceImpl | BoardServiceImpl 클래스                          |
| *Impl            | 클래스 이름이 Impl로 끝나는 클래스               |
| BoardService+    | 해당 클래스/인터페이스를 상속/구현한 모든 클래스 |

**4. 메소드**

| 표현식   | 설명                                |
| -------- | ----------------------------------- |
| *(..)    | 기본 설정. 모든 메소드              |
| get*(..) | 이름이 get으로 시작하는 모든 메소드 |

**5. 매개변수**

| 표현식                        | 설명                                                         |
| ----------------------------- | ------------------------------------------------------------ |
| (..)                          | 기본 설정. 매개변수의 개수, 타입에 제약이 없음               |
| (*)                           | 반드시 1개의 매개변수를 가지는 메소드                        |
| (com.springbook.user.UserVO)  | 매개변수로 UserVO를 가지는 메소드(패키지 경로 반드시 포함)   |
| (!com.springbook.user.userVO) | 매개변수로 UserVO를 가지지 않는 메소드                       |
| (Integer, ..)                 | 한 개 이상의 매개변수를 가지고, 첫 번째 매개변수 타입이 Integer인 메소드 |
| (Integer, *)                  | 반드시 두 개의 매개변수를 가지고, 첫 번째 매개변수 타입이 Integer인 메소드 |



## 어드바이스 동작 시점

어드바이스 메소드의 동작 시점은 `<aop:aspect>` 엘리먼트 하위에 아래 엘리먼트를 지정

| 동작 시점               | 설명                                                         |
| ----------------------- | :----------------------------------------------------------- |
| `<aop:before>`          | 메소드 실행 전 동작                                          |
| `<aop:after-returning>` | 메소드가 성공적으로 반환되면 동작                            |
| `<aop:after-throwing>`  | 메소드 실행 중 예외 발생 시 동작(catch 블록, 어드바이스 실행 후 예외처리) |
| `<aop:after>`           | 메소드 실행된 후 무조건 실행(finally 블록)                   |
| `<aop:around>`          | 메소드 호출을 가로채 메소드 실행 전후에 동작<br /> `ProceedingJoinPoint` 객체를 매개변수로 받고, 이 객체의 `proceed()` 메소드를 통해 비즈니스 메소드 호출<br /> `proceed()` 의 반환 값이 비즈니스 메소드의 반환 값 |



## JoinPoint와 바인드 변수

**`JoinPoint`** : 어드바이스 메소드를 의미 있게 구현하기 위해 비즈니스 메소드의 정보를 이용하는 인터페이스

- `ProceedingJoinPoint` 인터페이스는 `JoinPoint`를 상속한 것(`proceed()` 메소드를 추가)
- Around 어드바이스에서만(`proceed()` 필요) `ProceedingJoinPoint`를, 나머지 어드바이스에서는 `JoinPoint`를 매개변수로 사용해야 함
- `JoinPoint`를 어드바이스 메소드의 매개변수로 선언하면 `JoinPoint` 객체 사용 가능
- 비즈니스 메소드 호출 시 , 컨테이너가 `JoinPoint` 객체를 생성 & 메소드 호출과 관련된 정보를 `JoinPoint` 객체에 저장
- 어드바이스 종류에 따라 `JoinPoint` 사용방법이 다름

**JoinPoint 메소드**

| 메소드                     | 설명                                                         |
| -------------------------- | ------------------------------------------------------------ |
| `Signature getSignature()` | 비즈니스 메소드의 시그니처(반환타입, 이름, 매개변수) 정보가 저장된 Signature 객체 반환 |
| `Object getTarget()`       | 비즈니스 객체 반환                                           |
| `Object[] getArgs()`       | 비즈니스 메소드의 인자 목록을 Object 배열로 반환             |

**Signature 메소드**

| 메소드                   | 설명                                                         |
| ------------------------ | ------------------------------------------------------------ |
| `String getName()`       | 비즈니스 메소드 이름 반환                                    |
| `String toLongString()`  | 비즈니스 메소드의 반환타입, 이름, 매개변수를 패키지 경로까지 포함하여 반환 |
| `String toShortString()` | 비즈니스 메소드 시그니처를 축약한 문자열로 반환              |



**1. Before 어드바이스와 JoinPoint**

```java
public class BeforeAdvice {
	public void beforeLog(JoinPoint jp) {
		String method = jp.getSignature().getName();
		Object[] args = jp.getArgs();
		
		System.out.println("[사전 처리] " + method + "() 메소드 ARGS 정보 : " + args[0].toString());
	}
}
```

- 사전 처리 로직을 구현하기 위해 `JoinPoint` 객체를 매개변수로 사용



**2. After Returning 어드바이스와 JoinPoint**

```java
public class AfterReturningAdvice {
	public void afterLog(JoinPoint jp, Object returnObj) {
		String method = jp.getSignature().getName();
		
		if (returnObj instanceof UserVO) {
			UserVO user = (UserVO) returnObj;
			if (user.getRole().equals("Admin")) {
				System.out.println(user.getName() + " 로그인(Admin)");
			}
		}
		
		System.out.println("[사후 처리] " + method + "() 메소드 리턴값 : " + returnObj.toString());
	}
}
```

```xml
<bean id="afterReturning" class="com.springbook.biz.common.AfterReturningAdvice"></bean>
	
<aop:config>
	<aop:pointcut id="getPointcut" expression="execution(* com.springbook.biz..*Impl.get*(..))"/>
	
	<aop:aspect ref="afterReturning">
		<aop:after-returning pointcut-ref="getPointcut" method="afterLog" returning="returnObj"></aop:after-returning>
	</aop:aspect> 
</aop:config>
```

- 비즈니스 메소드 정보를 알아내기 위해 `JoinPoint` 객체를 매개변수로 사용
- 비즈니스 메소드의 반환값을 바인딩 하는 **바인드 변수** `Object returnObj`를 사용할 수 있음
- 바인드 변수 추가 시 설정 파일에 `<aop:after-returning>` 엘리먼트의 `returning` 속성으로 매핑 정보를 추가해야 함



**3. After Throwing 어드바이스와 JoinPoint**

```java
public class AfterThrowingAdvice {
	public void exceptionLog(JoinPoint jp, Exception exceptObj) {
		String method = jp.getSignature().getName();
		
		System.out.println("[예외 처리] " + method + "() 메소드 수행 중 발생된 예외 메시지 : " + exceptObj.getMessage());
	}
}
```

```xml
<bean id="afterThrowing" class="com.springbook.biz.common.AfterThrowingAdvice"></bean>

<aop:config>
	<aop:pointcut id="allPointcut" expression="execution(* com.springbook.biz..*Impl.*(..))"/>
		
	<aop:aspect ref="afterThrowing">
		<aop:after-throwing pointcut-ref="allPointcut" method="exceptionLog" throwing="exceptObj"></aop:after-throwing>
	</aop:aspect>
</aop:config>
```

- 예외가 발생한 메소드 정보를 알아내기 위해 `JoinPoint` 객체를 매개변수로 사용
- 비즈니스 메소드에서 발생한 예외 객체를 `exceptObj`라는 바인드 변수를 통해 받음. 모든 예외 객체를 받기 위해 `Exception` 타입
- 바인드 변수 추가 시 설정 파일에 `<aop:after-throwing>` 엘리먼트의 `throwing` 속성으로 매핑 정보를 추가해야 함



**4. Around 어드바이스와 ProceedingJoinPoint**

```java
public class AroundAdvice {
	public Object aroundLog(ProceedingJoinPoint pjp) throws Throwable {
		String method = pjp.getSignature().getName();
		
		StopWatch stopWatch = new StopWatch();
		stopWatch.start();
		
		Object obj = pjp.proceed();
		
		stopWatch.stop();
		System.out.println(method + "() 메소드 수행에 걸린 시간 : "
				+ stopWatch.getTotalTimeMillis() + "(ms)초");
		
		return obj;
	}
}
```

- 다른 어드바이스와 다르게 반드시 `ProceedingJoinPoint` 객체를 매개변수 사용
- 비즈니스 메소드를 호출하는 `proceed()` 메소드를 가지고 있음



## 어노테이션 기반 AOP

### 어노테이션 기반 AOP 설정

**1. 어노테이션 사용을 위한 스프링 설정**

- 스프링 설정 파일에 `<aop:aspectj-autoproxy>` 엘리먼트를 선언
- AOP 관련 어노테이션은 어드바이스 클래스에 설정
- 어노테이션을 컨테이너가 처리하기 위해 반드시 어드바이스 빈이 있어야 함 -> `<bean>` 등록 or `@Serivce` 어노테이션 사용



**2. 포인트컷 설정**

```java
@Service
public class LogAdvice {
    
    @Pointcut("execution(* com.springbook.biz..*Impl.*(..))")
    public void allPointcut() {}
    
    @Pointcut("execution(* com.springbook.biz..*Impl.get*(..))")
    public void getPointcut() {}
}
```

- 어노테이션으로 포인트컷 선언 시 `@Pointcut` 사용
- 여러 개 포인트컷 선언 시 여러 포인트컷을 식별하기 위해 **참조 메소드** 이용 (몸체가 비어있는 메소드, 포인트컷 식별 용도)



**3. 어드바이스 설정**

```java
@Service
public class LogAdvice {
    @Pointcut("execution(* com.springbook.biz..*Impl.*(..))")
    public void allPointcut() {}
    
    @Before("allPointcut()")
    public void getPointcut() {
        System.out.println("[공통 로그] 비즈니스 로직 수행 전 동작");
    }
}
```

- 어드바이스의 동작 시점은 어드바이스 메소드 위에 설정
- 반드시 어드바이스 메소드가 결합될 포인트컷을 참조해야 함
- `@Before` `@AfterReturning` `@AfterThrowing` `@After` `@Around` 다섯 가지 동작 시점 어노테이션 존재



**4. 애스팩트 설정**

```java
@Service
@Aspect // Aspect = Pointcut + Advice
public class LogAdvice {
    @Pointcut("execution(* com.springbook.biz..*Impl.*(..))")
    public void allPointcut() {}
    
    @Before("allPointcut()")
    public void getPointcut() {
        System.out.println("[공통 로그] 비즈니스 로직 수행 전 동작");
    }
}
```

- `@Aspect` 를 이용하여 애스팩트 설정
- `@Aspect` 가 설정된 애스팩트 객체에는 반드시 포인트컷과 어드바이스를 결합하는 설정이 있어야 함



### 어드바이스 동작 시점에 따른 XML 설정 -> 어노테이션 설정

**1. Before 어드바이스**

```java
@Service
@Aspect
public class BeforeAdvice {
	
	@Pointcut("execution(* com.springbook.biz..*Impl.*(..))")
	public void allPointcut() {}
	
	@Before("allPointcut()")
	public void beforeLog(JoinPoint jp) {
        ...
	}
}
```

**2. After Returning 어드바이스**

```java
@Service
@Aspect
public class AfterReturningAdvice {
	
	@Pointcut("execution(* com.springbook.biz..*Impl.get*(..))")
	public void getPointcut() {}
	
	@AfterReturning(pointcut="getPointcut()", returning="returnObj")
	public void afterLog(JoinPoint jp, Object returnObj) {
        ...
    }
}
```

**3. After Throwing 어드바이스**

```java
@Service
@Aspect
public class AfterThrowingAdvice {
	
	@Pointcut("execution(* com.springbook.biz..*Impl.*(..))")
	public void allPointcut() {}
	
	@AfterThrowing(pointcut="allPointcut()", throwing="exceptObj")
	public void exceptionLog(JoinPoint jp, Exception exceptObj) {
        ...
    }
}
```

**4. After 어드바이스**

```java
@Service
@Aspect
public class AfterAdvice {
	
	@Pointcut("execution(* com.springbook.biz..*Impl.*(..))")
	public void allPointcut() {}
	
	@After("allPointcut()")
	public void finallyLog() {
        ...
	}
}
```

**5. Around 어드바이스**

```java
@Service
@Aspect
public class AroundAdvice {
	
	@Pointcut("execution(* com.springbook.biz..*Impl.*(..))")
	public void allPointcut() {}
	
	@Around("allPointcut()")
	public Object aroundLog(ProceedingJoinPoint pjp) throws Throwable {
        ...
	}
}
```

**6. 외부 Pointcut 참조하기**

```java
@Aspect
public class PointcutCommon {
	@Pointcut("execution(* com.springbook.biz..*Impl.*(..))")
	public void allPointcut() {}
	
	@Pointcut("execution(* com.springbook.biz..*Impl.get*(..))")
	public void getPointcut() {}
}
```

```java
@Service
@Aspect
public class BeforeAdvice {
	
	@Before("PointcutCommon.allPointcut()")
	public void beforeLog(JoinPoint jp) {
		String method = jp.getSignature().getName();
		Object[] args = jp.getArgs();
		
		System.out.println("[사전 처리] " + method + "() 메소드 ARGS 정보 : " + args[0].toString());
	}
}
```

- 어드바이스 클래스마다 비슷하거나 같은 포인트컷이 반복 선언되는 문제 발생 ex) allPointcut()이 4번 선언됨
- 외부 독립된 클래스에 포인트컷을 따로 설정하여 해결
- **클래스 이름 + 참조 메소드 이름** 으로 외부 포인트컷 지정



## 스프링 JDBC

**1. JDBC** 

- DB에 비종속적인 자바-DB 연동 기술
- JDCB 관련 메소드를 구현할 때마다 DB 연동에 필요한 비슷한 코드가 반복됨(문제점)

```java
// 글 등록
public void insertBoard(BoardVO vo) {
	System.out.println("JDBC insertBoard()");
	try {
		conn = JDBCUtil.getConnection();
		stmt = conn.prepareStatement(BOARD_INSERT);
		stmt.setString(1, vo.getTitle());
		stmt.setString(2, vo.getWriter());
		stmt.setString(3, vo.getContent());
		stmt.executeUpdate();
	} catch (Exception e) {
		e.printStackTrace();
    } finally {
		JDBCUtil.close(stmt, conn);
	}
}
	
// 글 수정
public void updateBoard(BoardVO vo) {
	System.out.println("JDBC updateBoard()");
	try {
		conn = JDBCUtil.getConnection();
		stmt = conn.prepareStatement(BOARD_UPDATE);
		stmt.setString(1, vo.getTitle());
		stmt.setString(2, vo.getWriter());
		stmt.setInt(3, vo.getSeq());
		stmt.executeUpdate();
	} catch (Exception e) {
		e.printStackTrace();
	} finally {
		JDBCUtil.close(stmt, conn);
	}
}
```





**2. JdbcTemplate 클래스**

- JDBC 사용 시 반복되는 DB 연동 로직을 캡슐화 하여 재사용 하기 위해 템플릿 메소드 패턴을 적용한 클래스
- 개발자는 달라지는 SQL 구문과 설정값만 신경 쓰면 됨
- DAO 클래스에서 `JdbcTemplate` 클래스의 템플릿 메소드를 호출 -> `JdbcTemplate` 내부적으로 JDBC API 이용하여 DB 연동 처리



**3. 스프링 JDBC 설정**

1. 라이브러리 추가

   ```xml
   <!-- Spring -->
   <dependency>
   	<groupId>org.springframework</groupId>
   	<artifactId>spring-jdbc</artifactId>
   	<version>${org.springframework-version}</version>
   </dependency>
   
   <!-- DBCP -->
   <dependency>
   	<groupId>commons-dbcp</groupId>
   	<artifactId>commons-dbcp</artifactId>
   	<version>1.4</version>
   </dependency>
   ```

   

2. DataSource 설정(DB 커넥션을 얻는 것)

   ```xml
   <!-- BasicDataSource 객체 연결에 필요한 프로퍼티들을 setter 인젝션으로 설정 -->
   <!-- BasicDataSource 객체 삭제 전 연결을 해제하기 위해 close() 메소드를 destroy-method로 지정 -->
   
   <!-- DataSource 설정 -->
   <bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource" destroy-method="close">
   	<property name="driverClassName" value="org.h2.Driver"></property>
   	<property name="url" value="jdbc:h2:tcp://localhost/~/test"></property>
   	<property name="username" value="sa"></property>
   	<property name="password" value=""></property>
   </bean>
   ```

   

3. 프로퍼티 파일을 활용한 DataSource 설정 (PropertyPlaceholderConfigurer 이용)

   ```
   src/main/resources/config/database.properties 내용
   
   jdbc.driver=org.h2.Driver
   jdbc.url=jdbc:h2:tcp://localhost/~/test
   jdbc.username=sa
   jdbc.password=
   ```

   ```xml
   <!-- DataSource 설정 -->
   <context:property-placeholder location="classpath:config/database/properties"></context:property-placeholder>
   
   <!-- "${}" 구문이 <context:property-placeholder> 엘리먼트로 지정된 파일 위치의 값으로 치환됨 -->
   <bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource" destroy-method="close">
   	<property name="driverClassName" value="${jdbc.driver}"></property>
   	<property name="url" value="${jdbc.url}"></property>
   	<property name="username" value="${jdbc.username}"></property>
   	<property name="password" value="${jdbc.password}"></property>
   </bean>
   ```



**4. JdbcTemplate 메소드**

1. `update()`

   - INSERT, UPDATE, DELETE 구문 처리하기 위함

   - "?"에 값을 설정하는 방식에 따라 두 가지 형태 존재

     1. SQL 구문에 설정된 "?" 수만큼 값들을 차례로 나열

        ```java
        // int update(String sql, Object... args)
        public void updateBoard(BoardVO vo) {
            String BOARD_UPDATE = "update board set title=?, content=?, where seq=?";
            int updateCnt = jdbcTemplate.update(BOARD_UPDATE, vo.getTitle(), vo.getContent(), vo.getSeq());
        }
        ```

        

     2. SQL 구문에 설정된 "?" 수만큼의 값들을 Object 배열 객체에 세팅하여 배열 객체를 전달

        ```java
        // int update(String sql, Object[] args)
        public void updateBoard(BoardVO vo) {
            String BOARD_UPDATE = "update board set title=?, content=?, where seq=?";
            Object[] args = {vo.getTitle(), vo.getContent(), vo.getSeq()};
            int updateCnt = jdbcTemplate.update(BOARD_UPDATE, args);
        }
        ```

        

2. `queryForInt()`

   - SELECT 구문으로 검색된 정숫값을 반환 받기 위함

     ```java
     // int queryForInt(String sql)
     // int queryForInt(String sql, Object... args)
     // int queryForInt(String sql, Object[] args)
     public int getBoardTotalCount(BoardVO vo) {
         String BOARD_TOT_COUNT = "select count(*) from board";
         int totalCnt = jdbcTemplate.queryForInt(BOARD_TOT_COUNT);
     }
     ```

     

3. `queryForObject()`

   - SELECT 구문의 실행 결과를 자바 객체(VO)로 매핑하여 반환받기 위함

   - 검색 결과가 없거나 결과가 둘 이상이면 `IncorrectResultSizeDataAccessException`

   - 검색 결과를 자바 객체로 매핑할 `RowMapper` 객체를 반드시 지정해야 함 -> `RowMapper` 인터페이스를 구현한 클래스 필요 -> 즉, `RowMapper` 클래스는 테이블당 하나씩은 필요

   - 검색 결과를 어떤 VO에 어떻게 매핑할 것인지 `RowMapper` 인터페이스에 있는 `mapRow()` 메소드에 구현

     ```java
     // Object queryForObject(String sql)
     // Object queryForObject(String sql, RowMapper<T> rowMapper)
     // Object queryForObject(String sql, Object[] args, RowMapper<T> rowMapper)
     public BoardVO getBoard(BoardVO vo) {
         String BOARD_GET = "select * from board where seq=?";
         Object[] args = {vo.getSeq()};
         return jdbcTemplate.queryForObject(BOARD_GET, args, new BoardRowMapper());
     }
     ```

     ```java
     public class BoardRowMapper implements RowMapper<BoardVO> {
     	@Override
     	public BoardVO mapRow(ResultSet rs, int rowNum) throws SQLException {
     		BoardVO board = new BoardVO();
     		board.setSeq(rs.getInt("SEQ"));
     		board.setTitle(rs.getString("TITLE"));
     		board.setWriter(rs.getString("WRITER"));
     		board.setContent(rs.getString("CONTENT"));
     		board.setRegDate(rs.getDate("REGDATE"));
     		board.setCnt(rs.getInt("CNT"));
     		return board;
     	}
     }
     ```

     

4. `query()`

   - SELECT 문의 실행 결과가 목록일 때 사용

   - 기본 사용법은 `queryForObject()` 와 같음

   - 검색 결과를 VO 객체에 매핑하기 위해 `RowMapper` 객체 사용

   - 검색된 데이터 ROW 수만큼 `mapRow()` 메소드가 실행되고 ROW 정보가 매핑된 VO 객체가 List에 저장되어 반환됨

     ```java
     // List query(String sql)
     // List query(String sql, RowMapper<T> rowMapper)
     // List query(String sql, Object[] args, RowMapper<T> rowMapper)
     public List<BoardVO> getBoardList(BoardVO vo) {
         String BOARD_LIST = "select * from board order by seq desc";
         return jdbcTemplate.query(BOARD_LIST, new BoardRowMapper());
     }
     ```



**5. DAO 클래스 구현**

1. JdbcDaoSupport 클래스 상속

   - `JdbcDaoSupport` 클래스 상속 시 `JdbcTemplate` 객체를 반환하는 `getJdbcTemplate()` 메소드 호출 가능

   - `getJdbcTemplate()` 메소드가 `JdbcTemplate` 객체를 반환하려면 `DataSource` 객체를 가지고 있어야 함
     -> 반드시 `JdbcDaoSupport` 클래스의 `setDataSource()` 메소드를 호출하여 `DataSource` 객체를 의존성 주입

     ```java
     @Repository
     public class BoardDAOSpring extends JdbcDaoSupport {
     	// SQL 명령어
     	private final String BOARD_INSERT = "insert into board(seq, title, writer, content)"
     										+ "values((select nvl(max(seq), 0) + 1 from board), ?, ?, ?)";
     	private final String BOARD_UPDATE = "update board set title=?, content=? where seq=?";
     	private final String BOARD_DELETE = "delete board where seq=?";
     	private final String BOARD_GET    = "select * from board where seq=?";
     	private final String BOARD_LIST   = "select * from board order by seq desc";
     	
     	@Autowired
     	public void setSuperDataSource(DataSource dataSource) {
     		super.setDataSource(dataSource);
     	}
     	
     	/**
     	 *  CRUD 메소드 
     	 */
     	// 글 등록
     	public void insertBoard(BoardVO vo) {
     		getJdbcTemplate().update(BOARD_INSERT, vo.getTitle(), vo.getWriter(), vo.getContent());
     	}
     	
     	// 글 수정
     	public void updateBoard(BoardVO vo) {
     		getJdbcTemplate().update(BOARD_UPDATE, vo.getTitle(), vo.getContent(), vo.getSeq());
     	}
     	
     	// 글 삭제
     	public void deleteBoard(BoardVO vo) {
     		getJdbcTemplate().update(BOARD_DELETE, vo.getSeq());
     	}
     	
     	// 글 상세 조회
     	public BoardVO getBoard(BoardVO vo) {
     		Object[] args = {vo.getSeq()};
     		return getJdbcTemplate().queryForObject(BOARD_GET, args, new BoardRowMapper());
     	}
     	
     	// 글 목록 조회
     	public List<BoardVO> getBoardList(BoardVO vo) {
     		return getJdbcTemplate().query(BOARD_LIST, new BoardRowMapper());
     	}
     }
     
     class BoardRowMapper implements RowMapper<BoardVO> {
     	@Override
     	public BoardVO mapRow(ResultSet rs, int rowNum) throws SQLException {
     		BoardVO board = new BoardVO();
     		board.setSeq(rs.getInt("SEQ"));
     		board.setTitle(rs.getString("TITLE"));
     		board.setWriter(rs.getString("WRITER"));
     		board.setContent(rs.getString("CONTENT"));
     		board.setRegDate(rs.getDate("REGDATE"));
     		board.setCnt(rs.getInt("CNT"));
     		return board;
     	}	
     }
     ```

     

2. JdbcDaoSupport 클래스 `<bean>` 등록(보통 이 방법 사용)

   - 스프링 설정 파일에 `JdbcTemplate` 클래스를 `<bean>` 등록

   - 이때 반드시 `JdbcTemplate` 객체에 `DataSource` 객체를 의존성 주입해야 함

   - DAO 클래스에서 `@Autowired` 를 이용해 `JdbcTemplate` 타입의 객체를 의존성 주입 처리

     ```xml
     <!-- DataSource 설정 -->
     <context:property-placeholder location="classpath:config/database/properties"></context:property-placeholder>
     	
     <bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource" destroy-method="close">
     	<property name="driverClassName" value="org.h2.Driver"></property>
     	<property name="url" value="jdbc:h2:tcp://localhost/~/test"></property>
     	<property name="username" value="sa"></property>
     	<property name="password" value=""></property>
     </bean>
     	
     <!-- Spring JDBC 설정 -->
     <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
     	<property name="dataSource" ref="dataSource"></property>
     </bean>
     ```

     

     ```java
     @Repository
     public class BoardDAOSpring {
     	@Autowired
     	private JdbcTemplate jdbcTemplate;
     	
     	// SQL 명령어
     	private final String BOARD_INSERT = "insert into board(seq, title, writer, content)"
     										+ "values((select nvl(max(seq), 0) + 1 from board), ?, ?, ?)";
     	private final String BOARD_UPDATE = "update board set title=?, content=? where seq=?";
     	private final String BOARD_DELETE = "delete board where seq=?";
     	private final String BOARD_GET    = "select * from board where seq=?";
     	private final String BOARD_LIST   = "select * from board order by seq desc";
     	
     	/**
     	 *  CRUD 메소드 
     	 */
     	// 글 등록
     	public void insertBoard(BoardVO vo) {
     		jdbcTemplate.update(BOARD_INSERT, vo.getTitle(), vo.getWriter(), vo.getContent());
     	}
     	
     	// 글 수정
     	public void updateBoard(BoardVO vo) {
     		jdbcTemplate.update(BOARD_UPDATE, vo.getTitle(), vo.getContent(), vo.getSeq());
     	}
     	
     	// 글 삭제
     	public void deleteBoard(BoardVO vo) {
     		jdbcTemplate.update(BOARD_DELETE, vo.getSeq());
     	}
     	
     	// 글 상세 조회
     	public BoardVO getBoard(BoardVO vo) {
     		Object[] args = {vo.getSeq()};
     		return jdbcTemplate.queryForObject(BOARD_GET, args, new BoardRowMapper());
     	}
     	
     	// 글 목록 조회
     	public List<BoardVO> getBoardList(BoardVO vo) {
     		return jdbcTemplate.query(BOARD_LIST, new BoardRowMapper());
     	}
     }
     
     class BoardRowMapper implements RowMapper<BoardVO> {
     	@Override
     	public BoardVO mapRow(ResultSet rs, int rowNum) throws SQLException {
     		BoardVO board = new BoardVO();
     		board.setSeq(rs.getInt("SEQ"));
     		board.setTitle(rs.getString("TITLE"));
     		board.setWriter(rs.getString("WRITER"));
     		board.setContent(rs.getString("CONTENT"));
     		board.setRegDate(rs.getDate("REGDATE"));
     		board.setCnt(rs.getInt("CNT"));
     		return board;
     	}	
     }
     ```



## 트랜잭션 처리

선언적 트랜잭션 처리(컨테이너가 트랜잭션을 자동으로 처리)는 `<aop:advisor>` 를 이용, 어노테이션은 사용 불가

**1. 트랜잭션 네임스페이스 등록**

```xml
<beans ...
       xmlns:tx="http://www.springframework.org/schema/tx"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.2.xsd
		http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-4.2.xsd">
</beans>
```

**2. 트랜잭션 관리자 등록**

- DB 연동 기술에 따라 트랜잭션 관리자가 달라짐
- 모든 트랜잭션 관리자는 `commit()`, `rollback()` 메소드를 가진 `PlatformTransactionManager` 인터페이스를 구현한 클래스

```xml
<!-- DataSource 설정 -->
<bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource" destroy-method="close">
	<property name="driverClassName" value="org.h2.Driver"></property>
	<property name="url" value="jdbc:h2:tcp://localhost/~/test"></property>
	<property name="username" value="sa"></property>
	<property name="password" value=""></property>
</bean>
	
<!-- Transaction 설정 -->
<bean id="txManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
	<property name="dataSource" ref="dataSource"></property>
</bean>
```

**3. 트랜잭션 어드바이스 설정** 

- `<tx:adivce>` 엘리먼트를 이용해 트랜잭션 관리 기능 설정
- 트랜잭션 관리 기능의 어드바이스는 직접 구현X, 컨테이너가 `<tx:adivce>` 설정을 참조하여 자동 생성
  즉, 트랜잭션 관리 어드바이스 객체의 클래스 이름이나 메소드 확인 불가

```xml
<!-- Transaction 설정 -->
<bean id="txManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
	<property name="dataSource" ref="dataSource"></property>
</bean>
	
<tx:advice id="txAdvice" transaction-manager="txManager">
	<tx:attributes>
		<tx:method name="get*" read-only="true"/> <!-- get으로 시작하는 모든 메소드는 읽기 전용처리 -->
		<tx:method name="*"/> <!-- get이 아닌 메소드는 트랜잭션 관리에 포함 -->
	</tx:attributes>
</tx:advice>
```

| `<tx:method>` 엘리먼트의 속성 | 의미                                                         |
| ----------------------------- | ------------------------------------------------------------ |
| name                          | 트랜잭션이 적용될 메소드 이름                                |
| read-only                     | 읽기 전용 여부(default: false, true이면 트랜잭션 관리 대상 X) |
| no-rollback-for               | 트랜잭션을 롤백하지 않을 예외 지정                           |
| rollback-for                  | 트랜잭션을 롤백할 예외 지정                                  |

**4. AOP 설정을 통한 트랜잭션 적용**

- `<aop:advisor>` 를 이용하여 트랜잭션 관리 어드바이스 동작하도록 AOP 설정 추가
- 비즈니스 로직 수행 이후 `txAdvice` 를 통해 `commit()` 또는 `rollback()` 수행
- 포인트컷과 결합할 어드바이스 객체의 아이디와 메소드 이름을 알 수 없으므로 `<aop:aspect>` 를 사용하지 않음
- `<aop:advisor>` 와 `<aop:aspect>` 의 기능(포인트컷과 어드바이스를 결합)은 같음

```xml
<!-- Transaction 설정 -->
<bean id="txManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
	<property name="dataSource" ref="dataSource"></property>
</bean>
	
<tx:advice id="txAdvice" transaction-manager="txManager">
	<tx:attributes>
		<tx:method name="get*" read-only="true"/>
		<tx:method name="*"/>
	</tx:attributes>
</tx:advice>
	
<aop:config>
	<aop:pointcut id="txPointcut" expression="execution(* com.springbook.biz..*(..))"></aop:pointcut>
	
	<aop:advisor pointcut-ref="txPointcut" advice-ref="txAdvice"></aop:advisor>
</aop:config>
```

