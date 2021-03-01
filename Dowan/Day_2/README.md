# Day 2 목차

# [CLASS 01 스프링 AOP][CLASS 01Link]

[CLASS 01Link]: https://github.com/kduckk00/SpringStudy/blob/main/Dowan/Day_2/Day_2_CLASS_1.md

  새로운 메소드를 구현하는 가장 일반적인 방법은 기존에 잘 만들어진 메소드를 복사해서 구현하는 것이다. 이렇게 되면 결국 비즈니스 메소드에 부가적인 코드들이 반복해서 등장한다. 따라서 코드 분석과 유지보수를 어렵게 만드는데 **AOP**는 이러한 부가적인 공통 코드들을 효율적으로 관리한다.

 **AOP**에서는 메소드마다 공통으로 등장하는 로깅이나 예외, 트랜잭션 처리 같은 코드들을 횡단 관심이라고 한다. 이에 반해 사용자의 요청에 따라 실제로 수행되는 핵심 비즈니스 로직을 **핵심 관심**이라고 한다.




# [CLASS 02 AOP용어 및 기본 설정][CLASS 02Link]

[CLASS 02Link]: https://github.com/kduckk00/SpringStudy/blob/main/Dowan/Day_2/Day_2_CLASS_2.md

  새로운 메소드를 구현하는 가장 일반적인 방법은 기존에 잘 만들어진 메소드를 복사해서 구현하는 것이다. 이렇게 되면 결국 비즈니스 메소드에 부가적인 코드들이 반복해서 등장한다. 따라서 코드 분석과 유지보수를 어렵게 만드는데 **AOP**는 이러한 부가적인 공통 코드들을 효율적으로 관리한다.

 **AOP**에서는 메소드마다 공통으로 등장하는 로깅이나 예외, 트랜잭션 처리 같은 코드들을 횡단 관심이라고 한다. 이에 반해 사용자의 요청에 따라 실제로 수행되는 핵심 비즈니스 로직을 **핵심 관심**이라고 한다.




* * *

# [CLASS 03어드바이스 동작 시점][CLASS 03Link]

[CLASS 03Link]: https://github.com/kduckk00/SpringStudy/blob/main/Dowan/Day_2/Day_2_CLASS_3.md

   스프링 프레임워크도 다른 컨테이너와 마찬가지로 자신이 관리할 클래스들이 등록된 XML 설정파일이 필요하다. 스프링 컨테이너가 사용할 XML 파일은 앞에서 설치한 STS를 이용하면 간단하게 만들수 있다.

## 3.1 Before 어드바이스


## 3.2 After Returning 어드바이스


## 3.3 After Thorwing 어드바이스


## 3.4 After 어드바이스

## 3.5 Around 어드바이스



* * *

# [CLASS 04 JoinPoint와 바인드 변수][CLASS 04Link]

[CLASS 04Link]: https://github.com/kduckk00/SpringStudy/blob/main/Dowan/Day_2/Day_2_CLASS_4.md

## 4.1 JoinPoint 메소드

## 4.2 Before 어드바이스 


## 4.3 After Thorwing 어드바이스


## 4.4 After 어드바이스

## 4.5 Around 어드바이스




* * *

# [CLASS 05 어노테이션 기반 AOP][CLASS 05Link]

[CLASS 05Link]: https://github.com/kduckk00/SpringStudy/blob/main/Dowan/Day_2/Day_2_CLASS_5.md

## 5.1 어노테이션 기반 AOP 설정

## 5.2 어드바이스 동작 시점




* * *

# [CLASS 06 스프링 JDBC][CLASS 06Link]

[CLASS 06Link]: https://github.com/kduckk00/SpringStudy/blob/main/Dowan/Day_2/Day_2_CLASS_6.md

## 6.1 스프링 JDBC 개념

## 6.2 JdbcTemplate 클래스

## 6.3 스프링 JDBC 설정

## 6.4 JdbcTemplate 메소드

## 6.5 DAO 클래스 구현


* * *

# [CLASS 07 트랜잭션 처리][CLASS 07Link]

[CLASS 07Link]: https://github.com/kduckk00/SpringStudy/blob/main/Dowan/Day_2/Day_2_CLASS_7.md

## 7.1 트랜잭션 네임스페이스 등록

## 7.2 트랜잭션 관리자 등록

## 7.3 트랜잭션 어드바이스 설정

## 7.4 AOP 설정을 통한 트랜잭션 적용

## 7.5 트랜잭션 설정 테스트