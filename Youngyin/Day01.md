# class 02 프레임 개요
## 2.1 프레임 워크의 개념
### 2.1.1 프레임워크의 등장 배경
#### `뼈대 혹은 틀, 아키텍처에 해당하는 골격코드`

아키텍처에 해당하는 골격코드를 프레임워크가 제공한다. 애플리케이션의 기본 아키텍처는 프레임워크가 제공하고, 그뼈대에 살을 붙이는 작업만 개발자가 하는 것이다.

### 2.1.2 프레임 워크의 장점
프레임 워크를 사용하면 애플리케이션에 대한 분석, 설계, 구현 모두에서 재사용성이 증가하는데, 이를 통해 **(1) 빠른 구현 시간, (2) 쉬운 관리, (3) 개발자들의 역량 획일화, (4) 검증된 아키텍처의 재사용과 일관성 유지**와 같은 장점을 얻을 수 있다.

### 2.1.3 자바 기반의 프레임 워크
자바 기반의 프레임 워크는 대부분 오픈소스 형태로 제공된다.
|처리영역|프레임워크|설명|
| ----- | ----- | ---------- |
|presentation|struts|UI Layer에 중점을 두고 개발된 MVC(Model View Controller) 프레임 워크|
|presentation|Spring(MVC)|MVC(Model View Controller)아키텍처를 제공하는 UI Layer 프레임 워크로, Spring프레임 워크에 포함됨|
|Business|Spring(loC, AOP)|Spring은 컨테이너 성격을 가지는 프레임 워크이다. Spring의 IoC와 AOP 모듈을 이용해서 Spring 컨테이너에서 동잗하는 엔터프라이즈 비즈니스 컴포넌트를 개발할 수 있다.|
|Persistence|Hibernate or JPA|ORM(Object Relation Mapping) 프레임 워크는 SQL 명령어를 프레임 워크가 자체적으로 생성하여 DB연동을 처리한다. JPA는 Hibernate를 비롯한 모든 ORM의 공통 인터페이스를 제공하는 자바 표준 API이다.|
|Persistence|ibats or Mybatis|ibats 프레임워크는 개발자가 작성한 SQL 명령어와 자바 객체를 매핑해주는 기능을 제공하며, Mybatis는 ibats에서 파생된 프레임워크이다.|

## 2.2 스프링 프레임워크
### 2.2.1 스프링 탄생 배경
* 스프링 프레임 워크가 등장하기 이전에 잡 기반의 엔터프라이즈 어플리케이션은 대부분 EJB(Enterprise Java Beans)로 개발되었다.
  + 복잡한 스펙 때문에 학습, 개발 및 유지보수가 어렵다.
  + 컴포넌트를 배치하고 실행하기 위해서 고가의 WAS(Web Application Server)가 필요하다.
  + EJB를 제대로 사용하려면 디자인 패턴을 이해하고 적용 할 수 있어야 한다.

* 스프링 프레임워크는
  + POJO(Plan Old Java Object)를 사용하기 때문에 간단하다.
  + 스프링 프레임워크는 많은 디자인 패턴이 적용되어 배포되므로 디자인 패턴을 신경쓰지 않아도 된다.

### 2.2.2 스프링 프레임워크의 특징
#### `IoC와 AOP를 지원하는 경량의 컨테이너 프레임워크`

#### **경량**
* 스프링은 여러개의 모듈로 구성되어 있으며 각 모듈은 여러개의 JAR파일로 구성되어 있다. JAR파일만 있으면 개발과 실행이 모두 가능하므로 애플리캐이션의 배포가 빠르고 쉽다.
* 스프링 프레임워크 단순하고 가벼운 POJO 형태의 객체를 관리하기 때문에 기존의 EJB 객체를 관리하는 것보다 훨씬 가볍고 빠르다.

#### **제어와 역량**
IoC가 적용되면 객체 생성을 자바 코드로 직접 처리하는 것이 아니라 컨테이너가 대신 처리한다. 또한 객체와 객체 사이의 의존관계 역시 컨테이너가 처리한다.

#### **관점 지향 프로그래밍(AOP)**
핵심 비즈니스 로직과 각 비즈니스 메소드마다 반복되해서 등장하는 로직을 분리한다.

#### **컨테이너**
특정 객체의 생성과 관리를 담당하며 객체 운용에 필요한 다양한 기능을 제공하며 일반적으로 서버 안에 포함되어 배포 및 구동된다. 스프링도 컨테이너의 일종이라고 할 수 있다.

## 2.3 IoC(Inversion of Control) 컨테이너
### 2.3.1 결합도(Coupling)가 높은 프로그램
결합도란 하나의 클래스가 다른 클래스와 얼마나 많이 연결되어 있는지를 나타내는 표현이며 결합도가 높은 프로그램은 유지보수가 어렵다.
* 메서드 시그니처: 메서드 이름과 메개변수 리스트의 조합

### 2.3.2 다형성 이용하기
다형성을 이용하여 결합도를 낮출 수 있다. 다형성을 이용하려면 상속과 메소드 재정의 그리고 형변환이 필요하다.

### 2.3.3 디자인 패턴 이용하기    
* Factory 패턴을 이용하여 하위 클래스가 어떤 객체를 생성할지 결정할 수 있다.
---
# class 03 스프링 컨테이너 및 설정 파일
## 3.1 스프링 IOC 시작하기
### 스프링 컨테이너의 종류
스프링에서는 BeanFactory와 이를 상속한 ApplicationContext 두 가지 유형의 컨테이너를 제공한다.
|BeanFactory|ApplicationContext|
| ----- | ----- |
|스프링 설정 파일에 등록된 <bean>객체를 생성하고 관리|스프링 설정 파일에 등록된 <bean>객체를 생성하고 관리, 트랜잭션 관리, 메서지 기반의 다국어 처리 등|
|클라이언트의 요청에 의해서만 <bean>객체가 생성되는 지연로딩|컨테이너가 구동될 때 <bean>객체를 생성하는 즉시로딩|

대부분의 스프링 프로젝트는 ApplicationContext를 이용하며 GenericXmlApplicationContext와 XmlWebApplicationContext 두 클래스가 가장 많이 이용된다.
|구현 클래스|기능|
| ----- | ----- |
|GenericXmlApplicationContext|파일 시스템이나 클래스 경로에 있는 XML 설정 파일을 로딩하여 구동하는 컨테이너이다.|
|XmlWebApplicationContext|웹 기반의 스프링 애플리케이션을 개발할 때 사용하는 컨테이너이다.|

## 3.2 스프링 XML 설정
### 3.2.1 <beans> 루트 엘리먼트
* 스프링 컨테이너는 <bean>저장소에 해당하는 XML 설정 파일을 참조하여 <bean>의 생명 주기를 관리하고 여러가지 서비스를 제공한다.
* 스프링 설정 파일 이름은 무엇을 사용하든 상관없지만 <beans>를 루트 엘리먼트로 사용해야 한다. 그리고 <beans> 앨리먼트 시작 태그에 네임 스페이스를 비롯한 XML 스키마 관련 정보가 설정된다.

### 3.2.2 <import> 엘리먼트
설정을 효율적으로 관리하기 위하여 기능별 여러 XML파일로 나누어 설정하는 것이 더 효율적인데, 이렇게 분리하여 작성한 설정파일을 하나로 통합할 때 <import> 엘리먼트를 사용한다.

### 3.2.3 <bean> 엘리먼트
* 스프링 설정 파일에 클래스를 등록하려면 <bean>엘리먼트를 사용한다. 이때 id와 class 속성을 사용하는데, id속성은 생략 할 수 있지만 class속성은 필수이다. class속성에 클래스를 등록할 떄는 정확한 패키지 경로와 이름을 지정해야 한다.
* **id 속성**은 유일한 값을 가져야 하며 자바의 식별자 작성규칙을 따라야 한다. **name 속성**은 id와 다르게 자바 식별자 작성 규칙을 따르지 않는 문자열도 허용한다.(특수문자 사용 가능) 또한 name속성값 역시 전체 스프링 파일 내에서 유일해야 한다.

### 3.2.4 <bean> 엘리먼트 속성
```
<bean
  id = "tv"
  class = "polymorphism.SamsungTV"
  init-method = "initMethod"
  destory-method = "destoryMethod"
  lazy-init = "true"
  scope = "singleton"/>
```
#### **init-method 속성**
객체를 생성할 때 멤버변수를 초기화하는 메소드를 지정한다.
#### **destory-method 속성**
스프링 컨테이너가 객체를 삭제하기 전에 호출할 임의의 메소드를 지정할 수 있다.
#### **lazy-method 속성**
시스템의 부담을 줄이기 위해 컨테이너가 구동되는 시점이 아닌 해당<bean>이 사용되는 시점에 객체를 생성하도록 지정할 수 있다.
#### **scope 속성**
* 하나의 객체만 생성하여 유지하기 위해서는 객체를 생성하고 주소를 복사하여 재사용하여야 한다.
* scope의 속성값은 기본이 싱글톤이다. 이는 해당 bean이 스프링 컨테이너에 의해 단 하나만 생성되어 운용되도록 한다.
* <bean>의 scope 속성을 "prototype"으로 지정할 수 있는데, 이렇게 지정하면 스프링 컨테이너는 해당 bean이 요청될 떄마다 매번 새로운 객체를 생성하여 반환한다.
---
# class 04 의존성 주입
## 4.1 의존성 관리
* 객체의 생성과 의존관계를 컨테이너가 자동으로 관리한다는 것이 스프링 스프링은 IoC(제어의 역행)의 핵심 원리이다.
* 스프링은 IoC(제어의 역행)을 Dependency Lookup과 Dependency Injection의 두 가지 형태로 지원한다.

|Inversion of Control|||
| ----- | ----- | ----- |
|Dependency Lookup|Dependency Injection||
||Setter Injection|Constructor Injection|

* 컨테이너가 애플리케이션 운용에 필요한 객체를 생성하고 클라이언트는 컨테이너가 생성한 객체를 검색하여 사용하는 방식을 Dependency Lookup이라고 한다. Dependency Lookup은 실제 어플리케이션 개발 과정에서는 사용하지 않으며  대부분 Dependency Injection을 사용한다.
* Dependency Injection은 객체 사이의 의존관계를 스프링 설정 파일에 등록된 정보를 바탕으로 컨테이너가 자동으로 처리해준다. 이것은 다시 Setter 메소드를 기반으로 하는 세터 인젝션과 생성자를 기반으로 하는 생성자 인젝션으로 나뉜다.

## 4.2 생성자 인젝션 이용하기
* 스프링 인젝션을 이용하면 생성자의 매개변수로 의존관계에 있 객체의 주소 정보를 전달할 수 있다.
* 생성자 Injection을 위해서는 <bean>등록 설정에서 시작 태그와 종료 태그 사이에 <Constructor-arg> 엘리먼트를 추가하면 된다. 그리고 생성자 인자로 전달할 객체의 아이디를 <Constructor-arg> 엘리먼트에 ref 속성으로 참조한다.

### 4.2.1 다중 변수 매핑
* 생성자 인젝션에서 초기화해야 할 멤버 변수가 여러 개이면 여러 값을 한번에 전달해야 한다. 그리고 스프링 설정 파일에 <Constructor-arg> 엘리먼트를 매개변수의 개수만큼 추가해야 한다.
* 인자로 전달될 데이터가 <bean>으로 등록돤 다른 객체일 때는 ref 속성을 이용하여 해당 객체의 아이디나 이름을 참조하지만, 고정된 문자열이나 정수 같은 기본형 데이터 일 때는 value속성을 사용한다.
* 생성자가 여러 개 오버로딩 되어 있다면 어떤 생성자를 호출해야 하는지 분명하지 않을 수 있다. 이를 위해 index 속성을 지원하며 어떤 값이 몇번째 매개변수로 매핑되는지 지정할 수 있다.
```
<bean id = "tv" class = "polymorphism.SamsungTV">
  <constructor-arg index = "0" ref="sony"></constructor-arg>
  <constructor-arg index = "1" value=2700000></constructor-arg>
</bean>
```

### 4.2.2 의존 관계 변경
```
<bean id = "tv" class = "polymorphism.SamsungTV">
  <constructor-arg index = "0" ref="sony"></constructor-arg>
  <constructor-arg index = "1" value=2700000></constructor-arg>
</bean>

<bean id = "sony" class = "polymorphism.SonySpeaker"></bean>
<bean id = "apple" class = "polymorphism.AppleSpeaker"></bean>
```
ref 속성의 객체아이디를 apple로 변경하면 자바 코드의 변경 없이도 SamsungTV가 사용하는 스피커를 변경할 수 있다.

## 4.3 Setter 인젝션 이용하기
### 4.3.1 Setter 인젝션 기본
Setter 메소드는 스프링 컨테이너가 자동으로 호출하며 호출하는 시점은 <bean>객체 생성 직후이다. 따라서 Setter 인젝션이 동작하려면 Setter 메소드 뿐아니라 기본 생성자도 반드시 필요하다.

Setter 인젝션을 이용하려면 <property> 엘리먼트를 사용해야 하며 name속성값이 호출하고자 하는 메소드 이름이다.
```
<bean id = "tv" class = "polymorphism.SamsungTV">
  <property name="speaker" ref="sony"></property>
  <property name="price" value=2700000></property>
</bean>
```

### 4.3.2 p 네임스페이스 이용하기
p 네임 스페이스는 네임 스페이스에 대한 별도의 schema Location 이 없다.
* p 네임 스페이스를 선언했으면 다음과 같이 참조형 변수에 참조할 객체를 할당할 수 있다.
```
p:변수명-ref="참조할 객체의 이름이나 아이디"
```
* 기본형이나 문자형 변수에 직접 값을 설정할 떄는 다음과 같이 사용한다.
```
p:변수명="설정할 값"
```
* 예제
```
<bean id = "tv" class = "polymorphism.SamsungTV"
  p:speaker-ref="sony"
  p:price="2700000"/>

<bean id = "sony" class = "polymorphism.SonySpeaker"></bean>
<bean id = "apple" class = "polymorphism.AppleSpeaker"></bean>
```

## 4.4 컬렉션 객체 설정
* 배열이나 java.util.List 타입의 컬렉션 객체는 <list> 태그를 이용하여 설정한다.
* 중복을 허용하지 않는 집합 객체를 사용할 떄는 java.util.Set이라는 컬렉션을 사용한다. 컬렉션 객체는 <set> 태그를 사용하여 설정할 수 있다.
* 특정 key로 데이터를 등록하고 사용할 떄는 java.util.Map 컬렉션을 사용하며, <map> 태그를 사용하여 설정할 수 있다.
* key=value 형태의 데이터를 등록하고 사용할 떄는 java.util.properties라는 컬렉션을 사용하며 <props> 엘리먼트를 사용하여 설정한다.
---
# class 05 어노테이션 기반 설정
## 5.1 어노테이션 설정 기초
* context 네임스페이스 추가
* 컴포넌트 스캔 설정
* @Component
클라이언트가 스프링 컨테이너가 생성한 객체를 요청하려면, 요청할 때 사용할 아이디나 이름이 반드시 설정되어 있어야 한다.

**XML 설정**
```
<bean id = "tv" class = "polymorphism.LgTV"/>
```

**Annotation 설정**
```
@Component("tv")
public class LgTV implements TV {
  public LgTV(){
    System.out.println("==> LgTV 객체생성");
  }
}
```
## 5.2 의존성 주입 설정
### **@Autowired**
* 주로 변수 위에 설정하여 해당 타입의 객체를 찾아서 자동으로 할당
* 스프링 컨테이너는 멤버 변수 위에 붙은 @Autowired 를 확인하는 순간 해당 변수의 타입을 체크 > 타입의 객체가 메모리에 존재하는지 확인한 후에 객체를 변수에 주입
* @Autowired 가 붙은 객체가 메모리에 없다면 NoSuchBeanDefinitionException 발생한다.
### **@Qualifier**
* 의존성 주입 대상이 되는 객체가 두개 이상일 때 NoUniqueBeanDefinitionException 이 발생한다.
* @Qualifier 를 이용하여 특정 개체의 아이디나 이름을 지정할 수 있다.
### **@Inject**
@Autowired와 동일한 기능
### **@Resource**
@Autowired 와 @Qualifier 의 기능을 결합한 어노테이션

* @Autowired는 변수의 타입을 기준으로 객체를 검색하여 의존성 주입을 처리
* @Resource 는 객체의 이름을 이용하여 의존성 주입을 처리한다.
* @Inject 는 객체의 이름을 이용하여 의존성 주입을 처리한다.

## 53 추가 어노테이션
* 시스템을 구성하는 모든 클래스에 @Component를 할당하면 어떤 클래스가 어떤 역할을 수행하는 지 알기 어렵다.
* 스프링 프레임 워크에서는 클래스를 분류하기 위해 @Component 를 상속한 어노테이션을 추가로 제공한다.

|어노테이션|의미|
| ----- | ---------- |
|@Controller|비즈니스 로직을 처리하는 Service 클래스|
|@Service|데이터베이스 연동을 처리하는 DAO 클래스|
|@Repository|사용자 요청을 제어하는 Controller 클래스|
