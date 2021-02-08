# Day1 
## 프레임워크 개요

**1. 프레임워크란**  
- 애플리케이션의 구조인 아키텍처에 해당하는 골격 코드

 **2. 프레임워크의 장점**  
 - **빠른 구현 시간** : 골격 코드는 프레임워크가 제공, 개발자는 비즈니스 로직만 구현하면 됨  
 - **쉬운 관리** : 같은 프레임워크를 적용한 애플리케이션은 아키텍처가 같으므로 유지보수 용이  
 - **개발자 역량 획일화** : 초보 개발자와 숙련된 개발자 간의 품질 격차 해소  
 - **검증된 아키텍처의 재사용 및 일관성 유지** : 프레임워크에서 제공하는 아키텍처 이용 -> 아키텍처 왜곡, 변형 X    
 
## 스프링 프레임워크
**1. 탄생 배경**  
- 스프링 이전의 자바 엔터프라이즈 애플리케이션은 대부분 EJB로 개발  
- But, EJB는 스펙이 복잡해 학습에 필요한 시간↑, 개발과 유지보수도 힘듦  
- EJB로 만든 컴포넌트 배치 및 실행 시 WAS가 필요(고가의 장비)

**2. 스프링 프레임워크의 특징**  
- **경량(Lightweight)**  
스프링은 JAR 파일로 구성된 여러 개의 모듈로 구성 -> 개발, 실행, 배포가 쉽고 빠름  
POJO라는 가벼운 객체를 관리  

- **제어의 역행(Inversion of Control, IoC)**  
객체 생성과 의존관계 처리를 자바 코드로 직접 하는 것이 아닌 **컨테이너**가 대신 처리하는 것  
**낮은 결합도**를 유지하게 도와 유지보수가 편리해짐

- **관점지향 프로그래밍(Aspect Oriented Programming, AOP)**  
핵심 비즈니스 로직마다 존재하는 공통 로직을 외부 독립된 클래스로 분리(공통 로직을 핵심 비즈니스 로직에 직접 명시하지 않고 선언적으로 처리)  
**높은 응집도**를 도와 유지보수가 편리해짐

- **컨테이너(Container)**  
특정 객체의 생성과 관리를 담당하고 객체 운용에 필요한 다양한 기능을 제공  
스프링에서는 `BeanFactory`를 상속한 `ApplicationContext`가 컨테이너

## 스프링 컨테이너 및 XML 기반 설정 파일
IoC 컨테이너는 컨테이너에서 관리할 객체들을 위한 XML 설정 파일이 필요
|종류|기능|
|--|--|
|**BeanFactory**|스프링 설정 파일에 등록된 Bean 객체를 지연 로딩으로 생성 및 관리|
|**ApplicationContext**|BeanFactory와 달리 즉시 로딩으로 Bean 객체 관리. 트랜잭션 관리나 다국어 처리, 웹 애플리케이션 개발 지원

**1. 스프링 XML 설정**
- `<beans>` XML 설정 파일의 루트 엘리먼트
- `<import>` 기능별로 여러 XML 파일로 나누어진 설정 파일을 통합
- `<bean>` 스프링 설정 파일에 클래스를 등록. class 속성(패키지 경로 + 클래스 이름)은 필수

**2.** `<bean>` **엘리먼트 속성**
- `init-method="methodName"` : 객체 생성 후 멤버변수 초기화를 위해 실행할 메소드
-  `destroy-method="methodName"` :  컨테이너가 객체를 삭제하기 직전 실행할 메소드
- `lazy-init` : true로 설정 시 bean이 사용되는 시점에 객체를 생성
- `scope` : 컨테이너가 생성한 bean의 사용 범위 지정  
	`scope="singleton"` :  싱글톤으로 관리  
	 `scope="prototype"` : 해당 bean 요청마다 새로운 객체 생성  

## 의존성 주입
|종류|동작|
|--|--|
|Dependency Lookup|컨테이너가 생성한 객체를 클라이언트가 검색하여 사용|
|Dependency Injection|객체 의존관계를 설정 파일을 바탕으로 컨테이너가 처리. 생성자 인젝션과 Setter 인젝션이 있음|

**1. 생성자 인젝션**  
 - 스프링 컨테이너는 설정 파일에 등록된 클래스의 객체를 생성할 때 **기본 생성자**를 호출  
 - 생성자 인젝션을 이용해 매개변수를 가지는 생성자 호출 가능  
 - `<bean>` 엘리먼트 사이에 `<constructor-arg ref="인자 객체의 id">` 또는 `<constructor-arg value="기본 타입 값">`추가  
 - 매개변수가 여러 개이면 index 속성 사용  `<constructor-arg index="매개변수 위치"`
 
**2. Setter 인젝션**  
 - setter 메소드를 호출하여 의존성 주입을 처리  
 - `<property name="setter의 이름에서 set을 지우고 첫글자를 소문자로 바꾼 것"`  
 - 생성자 인젝션과 같이 ref, value 속성을 사용  
 - **p 네임스페이스** 사용 시 효율적으로 처리 가능  

**3. p 네임스페이스**  
 - `<beans>` 의 xmlns 속성에 p 네임스페이스를 추가하면 사용 가능  
 - bean 내의 속성으로 아래와 같이 사용  
 - `p:변수명-ref="참조할 객체 id"` : 참조형 변수에 객체 할당  
 - `p:변수명="설정할 값` : 기본형 변수에 값 설정  

**4. 컬렉션 객체 설정**  
 
|컬렉션|엘리먼트|
|--|--|
|List, 배열|<*list*> <*value*>value<*/value*> <*/list*>|
|Set|<*set value-type="타입"*>  <*value*>value<*/value*> <*/set*>|
|Map|<*map*> <*entry*> <*key*><*value*>key<*/value*><*/key*> <*value*>value<*/value*> <*/entry*> <*/map*>|
|Properties|<*props*> <*prop key="key"*>value<*/prop*> <*/props*>|
---
## 어노테이션 기반 설정
Context 네임스페이스를 `<beans>` 속성에 추가해야 어노테이션 설정 사용 가능  
XML 기반 설정과 어노테이션 기반 설정 모두 클래스에 기본 생성자가 있어야 함
```java
<beans ...
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:...
           http://www.springframework.org/schema/context
           http://www.springframework.org/schema/context/spring-context-4.2xsd
</beans>
```

**1. 컴포넌트 스캔**  
 - `<bean>` 등록하지 않고 자동으로 사용할 객체 생성  
 - 컴포넌트 스캔 사용 시 컨테이너는 `@Component`가 설정된 클래스의 객체를 생성  
 ```java
// 패키지 이름으로 시작하는 모든 패키지를 스캔 대상에 포함
<context:component-scan base-package="패키지이름"></context:component-scan>
```

**2. @Component**  
- 클래스에  `@Component`를  설정해야 컴포넌트 스캔의 대상이 됨  
- id나 name 속성을 지정하지 않으면 클래스 이름의 첫글자가 소문자인 이름으로 설정됨  
```java
@Component // @Component("id") id 지정 가능
public class ComponentClass {...}
```
**3. 의존성 주입 설정**  

|의존성 주입 어노테이션|기능|
|--|--|
|@Autowired|해당 타입의 객체를 찾아 자동으로 할당. 주로 멤버변수 위에 설정|
|@Qualifier|특정 객체의 이름을 이용해 의존성 주입할 때 사용|
|@Inject|@Autowired와 동일 기능(스프링에서 제공 X)|
|@Resource|@Autowired와 @Qualifier의 기능을 합한 것(스프링에서 제공 X)|

 - **@Autowired**  
생성자, 메소드, 멤버변수 위에 모두 사용 가능(결과는 같음 -> 주로 멤버변수 위에 사용)  
`@Autowired`가 붙은 객체가 메모리에 없으면 `NoSuchBeanDefinitionException` 발생  
 ``` java
 @Component
 public class AutowiredClass {
	 @Autowired
	 private ExampleAnimal exampleAnimal;
 }
 ```
 
 - **@Qualifier**  
 의존성 주입 대상이 되는 객체가 둘 이상일 경우 사용  
 ```java
 @Component
 public class QualifierClass {
	 @Autowired
	 @Qualifier("cat")
	 private Animal animal; // Animal을 상속한 cat, penguin bean이 존재
}
```

 - **@Resource**  
 변수 타입을 기준으로 의존성 주입을 처리하는 @Autowired와 달리 객체 이름을 이용  
 ```java
 @Component
 public class ResourceClass {
	 @Resource(name="penguin")
	 private Animal animal;
}
```

**4. 어노테이션 설정, XML 설정**

||장점|단점|
|--|--|--|
|어노테이션|XML 설정 부담X, 의존관계 정보가 코드에 들어있음|코드 수정을 해야 객체 교환 가능|
|XML|코드 수정 없이 실행되는 객체교환 가능|의존관계와 관련된 정보X -> 의존성 주입 확인 위해 설정 해석 필요|
> - 변경되지 않는 객체는 어노테이션으로, 변경 가능성이 있는 객체는 XML 설정으로 사용
> - 라이브러리 형태로 제공되는 클래스는 XML 설정으로만 사용 가능

**5. @Component를 포함하는 @Controller, @Service, @Repository**

|  |의미|
|--|--|
|@Controller|사용자 요청을 제어하는 Controller 클래스. 컨트롤러 객체로 인식|
|@Service|비즈니스 로직을 처리하는 Service 클래스|
|@Repository|DB 연동을 처리하는 DAO 클래스. DB 연동 과정에서 발생하는 예외 변환|
