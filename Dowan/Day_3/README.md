# **Day 3** 

# CLASS 0102 Model 1 아키텍처로 게시판 개발

## 1.1 Model 1 아키텍처 구조

~~사진~~

Model 1 아키텍처에서 우선 살펴볼 것이 Model 기능의 JavaBeans다. 자바에서 Bean이라는 용어는 객체를 의미하므로 JavaBean하면 자바 객체를 의미한다. 즉, Java Beans는 데이터베이스 연동에 사용되는 자바 객체들이다.

Controller 기능은 JSP 파일에 작성된 자바 코드를 의미하지만 JSP에 작성된 모든 자바 코드를 Controller 라고 하지는 않는다. 일반적으로 Controller는 사용자의 요청 처리와 관련된 자바 코드를 의미하며 대부분 밑의 코드들로 구성된다.

| 기능                  | 사용 예                                                      |
| --------------------- | ------------------------------------------------------------ |
| 사용자 입력 정보 추출 | String id = request.getParameter("userId")                   |
| DB 연동 처리          | UserVO vo = new UserVO();<br />vo.setId(id);<br />UserDAO userDAO = new UserDAO();<br />UserVO user = userDAO.getUser(vo); |
| 화면 내비게이션       | if(user != null) { // 로그인 성공<br /> response.sendRedirect("getBoardList.jsp");<br />} else {              // 로그인 실패<br />   response.sendRedirect("login.jsp");<br />} |



Model 1 구조는 JSP파일에서 Controller 기능과 View 기능을 모두 처리한다는 특징이 있다.

JSP 파일에 자바 코드와 마크업 관련 코드들이 뒤섞여 있어서 역활 구분이 명확하지 않고, JSP 파일에 대한 디버깅과 유지보수에 많은 어려움이 생기기 때문에 Model 1구조는 적은 개발 인력으로 간단한 프로젝트를 수행하는 때라면 사용할 수 있지만 엔터프라이즈급의 복잡한 시스템에는 부적절한 모델이라고 할 수 있다.

Model 2를 일반적으로 MVC라고 부르는데, 이는 Model, View, Controller 요소로 기능을 분리하여 개발하기 때문에 MVC는 Model 1 구조의 단점을 보완하기 위해 만들어진 구조이다.





## 1.2 로그인 기능 구현

   화면 내비게이션 방법에는 포워드(Forward) 방식과 리다이렉트(Redirect) 두 가지 방법이 있지만, 당분간은 로직의 단순화를 위해서 리다이렉트 방식만을 사용해서 개발한다.

~~사진~~



글 등록 처리 로직에서 가장 먼저 작성한 것은 인코딩 관련 코드이다. 사용자 입력 데이터에 한글이 포험되어 있으면 사용자 입력 정보를 getParameter() 메소드로 추출했을 때 한글이 깨진다. 따라서 사용자 입력 정보를 추출하기 직전에 반드시 setCharacterEncoding() 메소드로 한글 인코딩을 처리해야한다.



# CLASS 03 Model 2 아키텍처로 게시판 개발

## 3.1 Model 2 아키텍처 구조

  Model 1 아키텍처가 엔터프라이즈 시스템에 적합하지 않은 이유는 자바 로직과 화면 디자인이 통합되어 유지보수가 어렵기 때문이다. 자바 개발자 입장에서 JSP 파일에 자바 로직과 화면 디자인이 통합되어 있으면, 우선 수정할 자바 로직을 찾기부터가 쉽지않다. 그리고 디자이너가 디자인을 변경할 때도 복잡한 자바코드들 때문에 어려움을 느낄 수 밖에없다.

  Model 2 아키텍처에서 가장 중요한 특징은 Controller의 등장이며, 이 Controller는 서블릿 클래스를 중심으로 구현한다.

~~사진~~

결과적으로 Controller 로직이 사라진 JSP에는 View와 관련된 디자인만 남게 되어 디자이너는 JSP 파일을 관리하고 자바 개발자는 Controller와 Model만 관리하면 된다.

| 기능       | 구성 요소      | 개발 주체                       |
| ---------- | -------------- | ------------------------------- |
| Model      | VO, DAO클래스  | 자바 개발자                     |
| View       | JSP 페이지     | 웹 디자이너                     |
| Controller | Servlet 클래스 | 자바 개발자 또는 MVC 프레임워크 |



 

## 3.2  Controller 구현하기

   DispatcherServlet에는 GET 방식 요청을 처리하는 doGet() 메소드와 POST 방식 요청을 처리하는 doPost() 메소드가 재정의 되어있다. 하지만 어떤 방식으로 요청하든 process() 메소드를 통해 클라이언트의 요청을 처리한다.

  POST 방식의 요청에 대해서 doPost() 메소드가 호출되는데, 이때 한글이 깨지지 않도록 인코딩 처리를 추가한다. DispatcherServlet에서 가장 중요한 process() 메소드에서는 가장 먼저 클라이언트의 요청 URI로부터 path 정보를 추출하고 있는데, 이때 추출된 path는  URI 문자열에서 마지막 "/xxx.do" 문자열 이다. 그리고 추출된 path 문자열에 따라서 복잡한 분기 처리 로직이 실행된다.




​	

## 3.3 포인트컷 표현식

  검색 결과를 JSP에 공유하기 위해 세션에 저장하는 것은 문제가 있다. 세션은 브라우저당 서버 메모리에 하나씩 유지되는 객체이므로 사용자가 많을수록 많은 세션이 생성되고 세션에 정보가 많이 저장될수록 서버 입장에서는 부담스러울 수밖에 없다. 따라서 검색 결과는 세션이 아닌 HttpServletReqeust 객체에 저장하여 공유해야한다. 

HttpServletReqeust 객체는 클라이언트가 서버에 요청을 전송할 때마다 매번 새롭게 생성되며, 응답 메시지가 브라우저에 전송되면 바로 삭제되는 1회성 객체이므로 공유할데이터를 HttpServletReqeust에 저장하면 서버에는 전혀 부담이 되지 않는다. 하지만 당분간 코드의 간결함을 유지하기 위해서 세션을 이용할 것이며, 나중에 Spring MVC로 변환하면서 HttpSession을 HttpServletReqeust로 수정할 것이다.

~~그림~~



~~그림~~

MVC 아키텍처상에서 각 소스의 역활



먼저 Model 기능의 VO, DAO 클래스는 재사용되었고 DispatcherServlet 이라는 Controller 기능의 서블릿 클래스가 추가되었다. 가장 큰 변화는 View 기능의 JSP파일인데 우선 Controller 기능의 자바 로직을 DispatcherServlet 클래스로 이동했다.

즉, MVC 아키텍처를 적용한 결과 JSP에서 Controller 로직에 해당하는 자바 코드 모두 제거 되었다.

**Controller 로직은 사용자 입력 정보 추출, Model을 이용한 DB 연동 처리, 화면 내비게이션에 해당하는 자바 코드를 의미한다.**





# CLASS 04 MVC 프레임워크 개발

## 4.1 MVC 프레임워크 구조

  이전까지 개발한 게시판 프로그램은 MVC 아키텍처를 적용하긴 했지만, DispatcherServlet 클래스 하나로 Controller 기능을 구현했다. 하지만 이렇게 하나의 서블릿으로 Controller를 구현하면 클라이언트의 모든 요청을 하나의 서블릿이 처리하게 된다. 따라서 수많은 분기 처리 로직을 가질 수 밖에 없고, 이는 오히려 개발과 유지보수를 어렵게 만든다.

MVC 프레임워크 구조

~~사진~~

| 클래스            | 기능                                                         |
| ----------------- | ------------------------------------------------------------ |
| DispatcherServlet | 유일한 서블릿 클래스로서 모든 클라이언트의 요청을 가장 먼저 처리하는 Front Controller |
| HandlerMapping    | 클라이언트의 요청을 처리할 Controller 매핑                   |
| Controller        | 실질적인 클라이언트의 요청 처리                              |
| ViewResolver      | Controller가 리턴한 View 이름으로 실행될 JSP 경로 완성       |







## 4.2 MVC 프레임워크 구현

Controller를 구성하는 요소 중에서 DispatcherServlet은 클라이언트의 요청을 가장 먼저 받아들이는 Front Controller이다. 하지만 클라이언트의 요청을 처리하기 위해 DispatcherServlet이 하는 일은 거의 없으며, 실질적인 요청 처리는 각 controller에서 담당한다.

Controller 인터페이스의 handleRequest() 메소드를 재정의 했으므로 로그인 처리 기능의 마지막은 이동할 화면을 리다이렉트하지 않고 리턴하는 것으로 처리한다. handleRequest() 메소드가 확장자 없는 문자열을 리턴하면, 자동으로 '.jsp' 확장자가 붙어서 처리된다는 것으로만 이해하고 넘어간다.

  HandlerMapping은 모든 Controller 객체들을 저장하고 있다가 클라이언트의 요청이 들어오면 요청을 처리할 특정 Controller 를 검색하는 기능을 제공한다.

  먼저 클라이언트의 요청 path에 해당하는 Controller를 검색하기 위해 HandlerMapping 객체의 getController() 메소드를 호출한다. 그러고 나서 검색된 Controller의 handleRequest() 메소드를 호출하여 요청에 해당하는 로직을 처리하고 나면 리이동할 화면 정보가 리턴 된다.

~~사진~~





## 4.3 MVC 프레임워크 적용

  InsertBoardController 역시 DispatcherServlet 소스를 복사해서 구현했으므로 기능은 같다. 다만 handleRequest() 메소드가 글 등록 작업을 처리한 후에 getBoardlist.do 문자열을 리턴하는 부분이 중요한데, 글등록을 성공하면 등록된 글이 포함된 글 목록을 다시 검색해야한다. 따라서 getBoardList.do 문자열을 리턴하여 리다이렉트 처리한 것이다.





## 4.4 EL/JSTL 이용한 JSP 화면 처리

  Controller 로직은 사용자 입력 정보 추출, DB 연동 처리, 화면 내비게이션 같은 자바 코드를 의미하기 때문에 현재 JSP 파일에 남아있는 자바 코드는 Controller 로직은 아니다. 만일 이런 자바코드 조차 JSP 파일에서 제거하고 싶다면, JSP에 제공하는 EL과 JSTL를 이용하면 된다.

~~사진~~

게시글의 상세 정보를 출력하는 getBoard.jsp 파일에 EL을 적용하여 자바코드를 제거한다. BoardVO와 BoardDAO 클래스에 대한 import 선언이 사라진 것이다. 그리고 세션에 저장된 BoardVO 객체를 꺼내는 자바 코드도 삭제되었다. 그리고 EL표현식을 이용하여 세션에 board라는 이름으로 저장된 BoardVO 객체의 값들을 "${}" 구문을 이용하여 출력하고 있다.

게시 목록을 출력하는 getBoardList.jsp 파일은 EL과 함께 for루프로 처리하기 위한 JSTL도 같이 사용해야한다. getBoard.jsp 파일과 동일하게 import 선언은 모두 삭제되었다. 그리고 JSTL 사용을 위한 taglib 지시문이 추가되었으며 글 목록을 반복해서 출력하기 위해 사용했던 for 루프는 JSTL에서 제공하는 < c:forEach > 태그로 대체되었다. 



# CLASS 0607 Spring MVC 적용

## 6.3 글 목록 검색 기능 구현

세션이라는 것은 클라이언트 브라우저 하나당 하나씩 서버 메모리에 생성되어 클라이언트의 상태정보를 유지하기 위해서 사용한다. 따라서 세션에 많은 정보가 저장되면 될수록 서버에 부담을 줄 수 밖에 없다. 

~~게시글 글 목록 검색 과정 그림~~





## 6.9 ViewResolver 활용하기

   스프링 설정 파일인 presentation-layer.xml에 HandlerMapping, Controller 클래스들을 < bean > 등록하여 Spring 컨테이너가 객체 생성하도록 하였다. 아직 적용하지 않은 한 가지 요소가 있는데 바로 ViewResolver다. ViewResolver를 이용하면 클라이언트로부터의 직접적인 JSP 호출을 차단할 수 있어서 대부분 웹 프로젝트에서 ViewResolver 사용은 거의 필수이다. ViewResolver 역시 여러 가지가 있지만 JSP를 View로 사용하는 경우에는 InternalResourceViewResolver를 사용한다.

WEB-INF 폴더는 절대 브라우저에서 접근할 수 없다. 이제 WEB-INF 폴더로 이동한 JSP 파일들은 절대 클라이언트 브라우저에서 접근할 수 없다. 하지만 InternalResourceViewResolver 를 다음과 같이 설정하면 WEB-INF 폴더에 있는 JSP 파일을 View 화면으로 사용할 수 있다.