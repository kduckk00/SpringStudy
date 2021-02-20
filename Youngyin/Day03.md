# 01-02 Model1 아키텍처로 게시판 개발

## 1.1 Model1 아키텍처 구조

Model1 아키텍처는 JSP와 JavaBeans만을 사용하여 웹을 개발하는 것이다.

* JavaBeans
  
  * 데이터베이스 연동에 사용되는 자바 객체들
  
* Model
  
  * 데이터베이스 연동 로직을 제공하면서 DB에 검색한 데이터가 저장되는 자바 객체
  
* JSP
  * Controller와 View 기능을 모두 처리
  * Controller

    * 사용자의 요청 처리와 관련된 자바 코드를 의미

    * 사용자 입력 정보 추출

      ```java
      String id = request.getParameter("userId")
      ```

    * DB 연동 처리

      ```java
      UserVO vo = new UserVO();
      vo.setld(id);
      UserDAO userDAO = new UserDAO();
      UserVO user = userDAO.getUser(vo);
      ```

    * 화면 내비게이션

      ```java
      if (user != null){
          response.sendRedirect("getBoardList.jsp");
      } else {
          response.sendRedirect("login.jsp");
      }
      ```


## 1.2 로그인 기능 구현

### 포워드와 리다이렉트

* 포워드
  * RequestDispatcher를 이용하여 응답으로 사용할 JSP 화면으로 넘겨서 포워드된 화면이 클라이너트에 전송되는 방식
  * 한번의 요청과 응답으로 처리되므로 **실행속도가 빠름**
  * 클라이언트 브라우저에서 URL이 바뀌지 않아 **응답이 어디에서 들어왔는지 확인하기 어렵다.**

* 리다이렉트
  * JSP에서 브라우저로 응답 메세지를 보냈다가 다시 **서버로 재요청**하는 방식
  * 두번의 요청과 응답으로 처리되므로 **실행속도가 느리다.**
  * 응답이 들어온 파일로 브라우저의 URL이 변경된다.

### 게시글 제목에 하이퍼링크를 설정하는 코드

```xml
<a href="getBoard.jsp?seq=<%=board.getSeq()%>"><%=board.getTitle()%></a>
```



# 03 Model2 아키텍처로 게시판 개발

## 3.1 Model2 아키텍처 구조

* 작고 단순한 시스템을 개발하거나 개발 인력이 충분하지 않을 때에는 구조가 단순한 Model1 아키텍처를 사용하기도 한다.
* Model1 아키텍처는 엔터프라이즈 시스템에 적합하지 않다.
  * 자바 로직과 화면 디자인이 통합되어 유지보수가 어렵다.
* Model2 아키텍처
  * MVC 아키텍처
  * Controller의 등장
    * Controller는 서블릿 클래스를 중심으로 구현한다.

- MVC 아키텍처

| 기능       | 구성 요소      | 개발 주체                       |
| ---------- | -------------- | ------------------------------- |
| Model      | VO, DAO 클래스 | 자바 개발자                     |
| View       | JSP 페이지     | 웹 디자이너                     |
| Controller | Servlet 클래스 | 자바 개발자 또는 MVC 프레임워크 |

## 3.4 글 목록 검색 기능 구현하기

1. DispatcherServelt이 클라이언트로부터 "/getBoardList.do" 요청을 받으면,
2. DispatcherServelt은 BoardDAO 객체를 이용하여 글 목록을 검색한다.
3. 검색된 글 목록을 세션에 등록하고
4. getBoardList.jsp 화면을 요청하면,
5. getBoardList.jsp는 세션에 저장된 글 목록을 꺼내어 목록 화면을 구성한다.
6. 마지막으로 이 응답 화면이 브라우저에 전송된다.



# 04 MVC 프레임워크 개발

## 4.1 MVC 프레임워크 구조

- Controller를 서블릿 클래스 하나로 구현하는 것은 복잡하며 유지보수가 어렵다.
- Struts 나 Spring(MVC) 같은 MVC 프레임워크는 효율적인 Controller를 제공해준다.
- ***Spring(MVC)를 사용하기 전에 동일한 구조의 프레임워크를 구현하여 적용해 본다.***

| 클래스              | 기능                                                         |
| ------------------- | ------------------------------------------------------------ |
| DispatcherServerlet | 유일한 서블릿 클래스로서 모든 클라이언트의 요청을 가장 먼저 처리하는 Front Controller |
| HandlerMapping      | 클라이언트의 요청을 처리할 Controller 매핑                   |
| Controller          | 실질적인 클라이언트의 요청 처리                              |
| ViewResolver        | Controller가 리턴한 View 이름으로 실행될 JSP 경로 완성       |

## 4.2 MVC 프레임워크 구현

### 로그인 기능의 처리 순서

1. 클라이언트가 로그인버튼을 클릭하여 "/log.do"요청을 전송하면 DispatcherServerlet이 요청을 받는다.
2. DispatcherServerlet은 HandlerMapping객체를 통해 로그인 요청을 처리할 LoginController를 검색하고,
3. 검색된 LoginController의 HandRequest() 메소드를 호출하면 로그인 로직이 처리된다.
4. 로그인 처리 후에 이동할 화면 정보가 리턴되면
5. DispatcherServerlet은 ViewResolver를 통해 접두사와 접미사가 붙은 JSP 파일의 이름과 경로를 리턴받는다.
6. 그리고 최종적으로 JSP를 실행하고 실행결과가 브라우저에 응답된다.



# 05 Spring MVC 구조

## 5.1 Spring MVC 수행 흐름

1. 클라이언트로부터의 모든 ".do"요청을 DispatcherServerlet이 받는다.
2. DispatcherServerlet은 HandlerMapping객체를 통해 요청을 처리할 Controller를 검색하고,
3. DispatcherServerlet는 검색된 Controller를 실행하여 클라이언트의 요청을 처리한다.
4. Controller는 비즈니스 로직의 수행결과오 얻어낸 Model의 정보와 Model을 보여중 View 정보를 ModelAndView 객체에 저장하여 리턴한다.
5. DispatcherServerlet은 ModelAndView 로부터 View 정보를 추출하고, ViewResolver를 이용하여 사용할 View를 얻어낸다.
6. DispatcherServerlet은  ViewResolver를 통해 찾아낸 View를 실행하여 응답을 전송한다.

## 5.2 DispatcherServerlet 등록 및 스프링 컨테이너 구동

### 스프링 컨테이너의 구동 과정

1. 서블릿 컨테이너가 DispatcherServerlet 객체를 생성하고 나면 재정의된 init() 메소드가 자동으로 실행된다. 

2. 그러면 init() 메소드는 스프링 설정파일(action-servlet.xml)을 로딩하여 XmlWebApplicationContext를 생성한다. 즉, 스프링 컨테이너가 구동되는 것이다.

3. 스프링 설정 파일(action-servlet.xml)에 DispatcherServerlet이 사용할 HandMapping, Controller, ViewResolver 클래스를 \<bean>에 등록하면 스프링 컨테이너가 해당 객체들을 생성해준다.



# 06-07 Spring MVC 적용

## 6.3 글 목록 검색 기능 구현

1. 클라이언트로부터 "/getBoardList.do" 요청을 전송하려면 DispatcherServelt이 요청을 받고,
2. SimpleUlrHandlerMapping을 통해 요청을 처리할 GetBoardListController를 검색한다.
3. DispatcherServelt은 검색된 GetBoardListController를 실행하여 요청을 처리한다.
4. GetBoardListController는 검색 결과인 List\<BoardVO>와 getBoardList.jsp이름을 ModelAndView 객체에 저장하여 리턴한다.
5. DispatcherServelt은 ModelAndView로부터 View 정보를추출하고 ViewResolver을 이용하여 응답으로 사용할 getBoardList.jsp를 검색한다.
6. DispatcherServelt은 getBoardList.jsp를 실행하여 글 목록 화면을 전송한다.