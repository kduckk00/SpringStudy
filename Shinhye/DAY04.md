# CLASS01 어노테이션 기반 MVC개발

- 스프링은 어노테이션 기반 설정을 제공함으로써 과도한 XML설정으로 인한 문제 해결
- Spring MVC도 스프링 설정 파일에 HandlerMapping, Controller, ViewResolver같은 여러 클래스 등록 필요
  - 어노테이션을 최대한 활용해 XML설정 최소화하기



## 어노테이션 관련 설정

- `<beans>`루트 엘리먼트에 context네임스페이스 추가
  - 스프링 MVC에서 어노테이션 사용가능
  - HandlerMapping, Controller, ViewResolver 클래스에 대한 `<bean>`등록 모두 삭제
  - `<context:component-scan>`엘리먼트 사용
- `presentation-layer.xml`
  - 모든 Controller 클래스가 스캔 범위에 포함되도록 base-package속성에 Controller클래스들이 있는 가장 상위 패키지 등록

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context
		http://www.springframework.org/schema/context/spring-context-4.2.xsd
	">
	<context:component-scan base-package="com.springbook.view"></context:component-scan>
</beans>
```



## @Controller 사용하기

- 기존 : Controller클래스들을 스프링 설정 파일에 `<bean>` 등록 필요
- 어노테이션 이용시 : 기존처럼 할 필요 없이 `@Controller`만 붙이면 됨
  
- 스프링 설정파일의 `<context:component-scan>`으로 스프링 컨테이너가 컨트롤러 객체들을 자동으로 생성하기 때문
  
- @Component를 상속한 @Controller

  - <u>**@Controller가 붙은 클래스의 객체를 메모리에 생성하는 기능 제공**</u>

    `+` <u>**DispactherServlet이 인식하는 Controller객체로 만들어줌**</u>

- POJO스타일

  - `@Controller`선언 이용

- 만약 다른 Controller가 등록되어 있으면 지우고 다시 해보기



## @RequestMapping 사용하기

- HandlerMapping설정을 대신함
  - <u>**클라이언트의 요청에 대해서 매핑하는 역할**</u>
  - ex) `/insertBoard.do`요청이 오면 insertBoard()메소드가 실행되게끔 만듦



- `InsertBoardController.java`
  - 클라이언트로부터 `/insertBoard.do`요청이 오면 insertBoard()메소드 매핑하다는 설정
  - value속성은 생략 가능
  - 대부분 생략

```java
package com.springbook.view.board;

import javax.servlet.http.HttpServletRequest;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

import com.springbook.biz.board.BoardVO;
import com.springbook.biz.board.impl.BoardDAO;

@Controller
public class InsertBoardController {
	
	@RequestMapping(value="/insertBoard.do")
	public void insertBoard(HttpServletRequest request) {
		System.out.println("글 등록 처리");
		
		// 1. 사용자 입력 정보 추출
		String title = request.getParameter("title");
		String writer = request.getParameter("writer");
		String content = request.getParameter("content");
		
		// 2. DB연동 처리
		BoardVO vo = new BoardVO();
		vo.setTitle(title);
		vo.setWriter(writer);
		vo.setContent(content);
		
		BoardDAO boardDAO = new BoardDAO();
		boardDAO.insertBoard(vo);	
	}
}

```



## 클라이언트 요청처리

- 대부분 Controller는 사용자의 입력 정보를 추출하여 VO객체에 저장
  - 비즈니스 컴포넌트의 메소드를 호출할 때 VO객체를 인자로 전달
- 사용자 입력정보는 HttpServletRequest의 getParameter()메소드를 사용하여 추출
  - 위와 같이 작성하면 사용자가 입력하는 정보가 많거나 변경되면 코드 수정필요함
  - Command객체 이용시 이런 문제 해결
- `InsertBoardController`클래스의 insertBoard()메소드를 Command객체를 이용하여 구분

- `InsertBoardController.java`
  - `insertBoard()`메소드의 매개변수로 사용자가 입력한 값을 매핑할 BoardVO클래스 선언
  - 스프링 컨테이너가 insertBoard()메소드 실행시, 사용자가 입력한 값들을 Command객체에 세팅까지 해서 생성해 넘겨줌

```java
package com.springbook.view.board;

import javax.servlet.http.HttpServletRequest;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

import com.springbook.biz.board.BoardVO;
import com.springbook.biz.board.impl.BoardDAO;

@Controller
public class InsertBoardController {
	
	@RequestMapping(value="/insertBoard.do")
	public void insertBoard(BoardVO vo) {
		System.out.println("글 등록 처리");
		
		BoardDAO boardDAO = new BoardDAO();
		boardDAO.insertBoard(vo);
	
	}
}

```



### 서블릿 객체의 service()메소드가 호출되는 과정(p359)

1. 서블릿 컨테이너는 클라이언트의 HTTP요청이 서버에 전달되는 순간
2. HttpServletRequest객체를 생성하고 HTTP프로토콜에 설정된 모든 정보 추출하여 HttpServletRequest객체에 저장
3. service()메소드를 호출할 때, 이 HttpServletRequest객체를 인자로 전달

---

- 서블릿 컨테이너가 **생성**
  - 서블릿 객체
  - HttpServletRequest, HttpServvletResponse 객체 
- 서블릿 컨테이너가 **호출**
  - service(), doGet(), doPost()메소드
  - 정상적으로 호출되려면 HttpServletRequest, HttpServvletResponse객체 필요
    - 이 객체들도 서블릿 컨테이너가 생성해서 넘겨줌

---

- 클라이언트가 글 등록 정보를 입력하고 서버에 `insertBoard.do`요청 전달
  - 스프링컨테이너는 @Controller가 붙은 모든 컨트롤러 객체 생성
  - InsertBoardController가 가지고 있는 insertBoard()메소드를 실행
    - 여기에 @RequestMapping이 써져 있기 때문
    - 이때, 매개변수에 해당하는 BoardVO객체를 스프링 컨테이너가 생성하여 전달



### Controller개체의 메소드가 호출되는 과정(p360)

1. 매개변수에 해당하는 BoardVO객체 생성
2. 사용자가 입력한 파라미터(title, writer, content)값들을 추출해 BoardVO객체에 저장
   - 이때, BoardVO클래스의 Setter메소드들이 호출됨
3. insertBoard()메소드 호출 시, 사용자 입력값들이 설정된 BoardVO객체가 인자로 전달
   - Form태그 안의 파라미터 이름과 Command객체의 Setter메소드 이름이 반드시 일치해야 함
   - 각 파라미터 이름에 해당하는 setTitle(), setWriter(), setContent()메소드가 있어야 Setter인젝션에 의해 자동으로 사용자 입력값이 저장됨



# CLASS02 어노테이션으로 게시판 프로그램 구현하기

## 글 등록 기능 구현하기

- 글 등록 처리 성공 후, 글 목록 출력
- `InsertBoardController.java`
  - GetBoardListController 실행시키기 위해 리턴타입을 String으로 수정
  - `"getBoardList.do"`리턴
  - 사용자 입력값을 Command객체로 받기 위해 BoardVO클래스를 매개변수로 선언
  - DB연동처리를 위해 BoardDAO도 매개변수로 선언
    - DAO객체 역시 매개변수로 선언화면 스프링 컨테이너가 해당 객체 생성하여 전달해줌

```java
package com.springbook.view.board;

import javax.servlet.http.HttpServletRequest;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

import com.springbook.biz.board.BoardVO;
import com.springbook.biz.board.impl.BoardDAO;

@Controller
public class InsertBoardController {
	
	@RequestMapping(value="/insertBoard.do")
	public String insertBoard(BoardVO vo, BoardDAO boardDAO) {
		return "getBoardList.do";
           
        ...
```



### 리다이렉트로 넘기기

- Controller메소드가 실행되고 View경로 리턴시, 기본이 포워딩 방식
  - 글 등록 후에 목록화면이 출력돼도 브라우저의 URL은 변경되지 X
  - 따라서, 리다이렉트 필요
    - `redirect:`라는 접두사 붙여주기
    - `return "redirect:getBoardList.do";`



## 글 목록 검색 구현하기

- 게시글을 등록한 후에 실행될 GetBoardListController
  - @Controller, @RequestMapping이용
- `GetBoardListController.java`
  - getBoardList()메소드는 매개변수가 3개
  - `public ModelAndView getBoardList(BoardVO vo, BoardDAO boardDAO, ModelAndView mav)`
    - 첫 번째 매개변수 : 사용자 입력값 받기 위한 BoardVo클래스
    - 두 번째 매개변수 : DB연동 처리 위한 BoardDAO클래스
    - 세 번재 매개변수 : 검색 결과와 화면 정보를 저장하여 리턴하기 위한 ModelAndView

```java
package com.springbook.view.board;

import java.util.List;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.servlet.ModelAndView;

import com.springbook.biz.board.BoardVO;
import com.springbook.biz.board.impl.BoardDAO;

@Controller
public class GetBoardListController {
	@RequestMapping("/getBoardList.do")
	public ModelAndView getBoardList(BoardVO vo, BoardDAO boardDAO, ModelAndView mav) {
        
		mav.addObject("boardList", boardDAO.getBoardList(vo)); // Model 정보 저장
		mav.setViewName("getBoardList"); // View 정보 저장
		return mav;
	}
}

```



## 글 상세 보기 구현하기

- `GetBoard.java`

- GetBoardListController.java와 비슷



## 글 수정 기능 구현하기

- `UpdateBoardController.java`
  - 클라이언트로부터 `/.updateBoard.do`요청이 들어오면 updateBoard()메소드가 실행
  - 수정에 필요한 데이터는 매개변수로 선언된 BoardVO객체를 통해 받아냄
  - BoardDAO객체의 update()메소드를 호출하여 글 등록 처리 후, getBoardList.do를 리턴하여 글 목록으로 이동
- InsertBoardController.java와 비슷



## 글 삭제 기능 구현하기

- InsertBoardController.java와 비슷
- 클라이언트로부터 `/deleteBoard.do`요청이 들어오면 deleteBoard()메소드가 실행
  - 이때 삭제에 필요한 게시글 번호는 매개변수로 선언된 BoardVO객체를 통해 받아냄
  - BoardDAO객체의 deleteBoard()메소드를 호출하여 글 삭제를 처리한 후 getBoardList.do 리턴해 글 목록으로 이동



## 로그인 기능 구현하기

- `loginController.java`

```java
package com.springbook.view.user;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

import com.springbook.biz.user.UserVO;
import com.springbook.biz.user.impl.UserDAO;

@Controller
public class LoginController {
	@RequestMapping("/login.do")
	public String login(UserVO vo, UserDAO userDAO) {

		System.out.println("로그인 처리");
		if(userDAO.getUser(vo) != null) return "getBoardList.do";
		else return "login.jsp";
		
	}
	
}

```



- 클라이언트의 `/login.do`요청에 대해서 실행되는 login()메소드
  - 사용자가 입력한 아이디와 비밀번호를 받아내기 위해 UserVo클래스를 매개변수로 선언
- DB연동을 위한 UserDAO 클래스도 매개변수로 받음
- 로그인 성공과 실패에 대해 적절한 화면 정보 리턴



## 로그아웃 기능 구현하기

- 세션 객체 이용
- `LogoutController.java`

```java
package com.springbook.view.user;

import javax.servlet.http.HttpSession;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
public class LogoutController  {
	@RequestMapping("/lougout.do")
	public String logout(HttpSession session) {
		System.out.println("로그아웃 처리");
		session.invalidate();
		return "login.jsp";

	}

}

```



- `/logout.do`요청으로 실행되는 logout메소드
  - 로그아웃 처리를 위해 세션 객체 필요
  - HttpSession 매개변수로 선언
  - 스프링 컨테이너가 로그아웃 요청한 브라우저와 매핑된 세션 객체를 찾아 넘겨줌
  - 매개변수로 받은 세션 객체를 강제 종료하기



## 컨트롤러 통합하기

- 어노테이션을 이용하여 Controller 클래스를 구현하면 4~5줄 내외로 간단하게 구현 가능
  - 따라서, 하나의 클래스로 묶어 처리하면 관리가 편리
  - 용도에 맞게 Controller생성하여 관련 Controller는 모두 모아두기
- `BoardController.java`
  - 기존의 컨트롤러 클래스들 전부 합침
    - InsertBoardController, UpdateBoardController, DeleteController, GetBoardController, GetBoardListController
  - 이 컨트롤러 클래스를 제외한 나머지 전부 삭제가능

```java
package com.springbook.view.board;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.servlet.ModelAndView;

import com.springbook.biz.board.BoardVO;
import com.springbook.biz.board.impl.BoardDAO;

@Controller
public class BoardController {
	// 글 등록
	@RequestMapping("/insertBoard.do")
	public String insertBoard(BoardVO vo, BoardDAO boardDAO) {
		boardDAO.insertBoard(vo);
		return "getBoardList.do";
	}
	
	// 글 수정
	@RequestMapping("/updateBoard.do")
	public String updateBoard(BoardVO vo, BoardDAO boardDAO) {
		boardDAO.updateBoard(vo);
		return "getBoardList.do";
	}
	
	// 글 삭제
	@RequestMapping("/deleteBoard.do")
	public String deleteBoard(BoardVO vo, BoardDAO boardDAO) {
		boardDAO.deleteBoard(vo);
		return "getBoardList.do";
	}
	
	// 글 상세 조회
	@RequestMapping("/getBoard.do")
	public ModelAndView getBoard(BoardVO vo, BoardDAO boardDAO, ModelAndView mav) {
		mav.addObject("board", boardDAO.getBoard(vo));
		mav.setViewName("getBoard.jsp");
		return mav;
	}
	
	// 글 목록 검색
	@RequestMapping("/getBoardList.do")
	public ModelAndView getBoardList(BoardVO vo, BoardDAO boardDAO, ModelAndView mav) {
		mav.addObject("boardList", boardDAO.getBoardList(vo));
		mav.setViewName("getBoardList.jsp");
		return mav;
	}
}

```

- BoardController클래스를 구현하면 스프링 컨테이너가 BoardController객체를 생성
  - 클라이언트의 요청 패스에 따라 @RequestMapping 설정된 메소드 실행



## 요청방식에 따른 처리

### method속성

- @RequestMapping이용하면 Servlet처럼 <u>클라이언트의 요청 방식(GET/POST)에 따라 수행될 메소드 다르게 설정 가능</u>
  - 주로, **클라이언트가 GET방식으로 입력 폼 요청**하면 **입력화면 보여주고,**
  - 입력 화면에서 **submit버튼 클릭해 POST방식으로 요청**하면 클라이언트의 요청을 적절히 **처리하고자 할 때 사용**

- LoginController 클래스에 loginView()메소드 추가
- `LoginController.java`

```java
package com.springbook.view.user;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;

import com.springbook.biz.user.UserVO;
import com.springbook.biz.user.impl.UserDAO;

@Controller
public class LoginController {
	@RequestMapping(value="/login.do", method=RequestMethod.GET)
	public String loginView(UserVO vo) {
		System.out.println("로그인 화면으로 이동");
		vo.setId("test");
		vo.setPassword("test123");
		return "login.jsp";
	}
	
	@RequestMapping(value="/login.do", method=RequestMethod.POST)
	public String login(UserVO vo, UserDAO userDAO) {
		System.out.println("로그인 인증 처리");
		if(userDAO.getUser(vo) != null) return "getBoardList.do";
		else return "login.jsp";
    }	
}

```

- 둘 다 `/login.do`요청에 실행되도록 설정
  - 이렇게만 있으면 에러
  - 하지만 method속성 추가로 에러 발생X
    - 두 메소드가 각기 다른 메소드로 처리되도록 함
    - GET방식의 요청이 오면 스프링 컨테이너는 loginView() 메소드 실행
      - 로그인 화면 이동
    - POST방식의 요청이 오면 login()메소드 실행
      - 실질적 로그인 인증작업 처리
- loginView()메소드는 로그인 화면으로 이동할 때 실행되는 메소드
  - 따라서, 사용자가 입력할 값이 아무것도 없는 상태
  - 매개변수로 UserVO객체 받아들이도록 매개변수 설정
  - 매개변수로 받은 Command객체에 적절한 데이터 설정하면 리턴된 JSP파일에어 이 데이터 사용가능



## JSP에서 Command객체 사용

- Command객체에 저장된 데이터를 JSP에서 사용하려면 `${}`구문 이용
- 클라이언트가 직접 URL을 입력하거나 하이퍼링크를 클릭하여 요청할 시
  - 기본은 GET방식
- `index.jsp`
  - 로그인 링크 클릭 시 서버에 `login.do`요청 전달됨
    - 이때, GET방식의 요청 전달
    - loginVIew()메소드 실행
    - login.jsp화면이 브라우저에 출력될 대, UserVO객체에 저장한 id,password정보가 자동으로 설정됨
  - 로그인 버튼 클릭시 (`login.jsp`) `login.do`요청 전달됨
    - 이때, POST방식의 요청
    - login()메소드 실행

```java
<%@ page language="java" contentType="text/html; charset=EUC-KR"
    pageEncoding="EUC-KR"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD// HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=EUC-KR">
<title>Main Page</title>
</head>
<body>
<center>
	<h1>게시판 프로그램/h1>
	<hr>
		<a href="login.do">로그인/a><br><br><br>
		<a href="getBoardList.do">글 목록 바로가기</a>
	</h1>
</center>

</body>
</html>
```



## @ModelAttribute 사용

- 스프링 컨테이너가 생성하는 Command 객체의 이름은 클래스 이름의 첫 글자를 소문자로 변경한 이름이 자동으로 설정됨
  - 그래서 login.jsp에서 UserVO객체의 변수에 접근할 때, `${userVO.변수명}`으로 접근했던 것
  - 하지만, Command객체의 이름을 변경하려면 @ModelAttribute사용해야 함

```java
	@RequestMapping(value="/login.do", method=RequestMethod.GET) // GET방식의 요청이 들어올 때, loginView호출
	public String loginView(@ModelAttribute("use") UserVO vo) {
		System.out.println("로그인 화면으로 이동");
		vo.setId("test");
		vo.setPassword("test123");
		return "login.jsp";
	}
	...
        
```

```jsp
...

<form action="login.do" method="post">
<table border="1" cellpadding="0" cellspacing="0">
<tr>
    <td bgcolor="skyblue">아이디</td>
    <td><input type="text" name="id" value="${user.id}" /></td>
</tr>
...
```



## Servlet API사용

- Controller메소드에서 사용자가 입력한 정보를 추출하기 위해 HttpServletRequest 대신 Command객체 사용
  - But, HttpServletRequest 객체는 입력값 추출할 때 뿐 아니라 더 다양한 메소드 제공
  - 이 메소드들을 이용해 Controller 구현할 때, HttpServletRequest 객체를 매개변수로 받아야 함
  - 스프링 MVC에서는 COntroller메소드 매개변수로 다양한 Servlet API를 사용할 수 있도록 지원
- `LoginController.java`
  - 로그인 성공시 사용자 이름을 세션에 저장
  - 꺼내 사용할 땐, `userName`을 가지고 사용

```java
	@RequestMapping(value="/login.do", method=RequestMethod.POST)
	public String login(UserVO vo, UserDAO userDAO, HttpSession session) {
		System.out.println("로그인 인증 처리");
		UserVO user = userDAO.getUser(vo);
		if(user != null) {
			session.setAttribute("userName", user.getName()); // 로그인 성공시 사용자 이름을 세션에 저장
			return "getBoardList.do";
		}
		else return "login.jsp";
```

- `getBoardList.jsp`
  - 세션에 저장된 사용자 이름 출력
  - 로그인에 성공한 사용자의 이름이 글 목록 화면에 출력

```jsp
<h1>글 목록</h1>
<h3>${userName}님 환영합니다~<a href="logout.do">Log-out</a></h3>
```

![image-20210216022402598](images/image-20210216022402598.png)



## Controller의 리턴타입

- Controller 메소드 정의할 때, 리턴타입은 개발자 마음대로 결정 가능
- **리턴타입을 String으로 설정시**
  - 완벽한 View이름을 문자열로 리턴
  - 이때, 스프링 컨테이너는 리턴된 JSP파일 찾아 실행
- 리턴타입을 ModelAndView로 설정시
  - 검색된 Model데이터와 View이름을 모두 저장하여 리턴

- 실행 결과는 같지만 대부분 프로젝트는 일관성 있는 코드를 중요케 생각
  - 따라서, 하나로 통일하여 사용
  - String이 좀 더 간결하므로 String으로 리턴타입 통일
- 기존에 ModelAndView에 저장했던 검색 결과는 이제 Model에 저장해야 함
  - 리턴된 JSP 화면에서 검색 결과를 출력하려면 검색결과를 Model에 저장



## 기타 어노테이션

### @RequestParam 사용하기

- Command객체 이용하면 클라이언트에서 넘겨준 요청 파라미터 정보 받아낼 수 있음
  - 이를 위해, **요청 파라미터와 매핑될 변수, Setter메소드가 Command클래스에 선언되어 있어야 함**
  - HTTP요청 파라미터 정보 추출위한 `@RequestParam`제공
    - Command클래스에는 없는 파라미터 정보 추출 가능
- 사용자가 글 목록 화면에서 검색 조건과 검색 키워드를 입력하고 검색 버튼을 클릭하면 `/getBoardList.do`와 매핑된 getBoardList()메소드가 실행됨
  - BUT, BoardVO라는 Command객체에는 searchCondition, searchKeyword라는 변수와 Setter메소드가 없음
  - 따라서, BoardVO를 Command객체로 사용할 수는 없음
- `@RequestParam` 사용하면 검색과 관련된 파라미터 정보 추출 할 수 있음
  - HttpServletRequest에서 제공하는 getParameter()메소드와 같은 기능의 어노테이션



```java
@RequestParam(value="searchCondition", 
	defaultValue="TITLE", 
	required=false) String condition,
```

- value : 화면(`.jsp`)으로부터 전달될 파라미터이름
  - 파라미터 정보가 전달된다면, 해당 값을 추출해서 condition변수에 할당
- defaultValue : 화면(`.jsp`, value)으로부터 전달될 파라미터 정보가 없을 때, 설정할 기본 값
  - 파라미터 정보가 전달되지 않았다면, 기본값으로 TITLE문자열 할당
- required : 파라미터의 생략 여부



---

- `@RequestParam`을 사용하기 싫다면 `BoardVO.java` 클래스에 searchCondition, searchKeyword 변수 추가
  - Getter/Setter 메소드만 생성해 간단하게 처리 가능



#### Command객체란?

> HttpServletRequest를 통해 들어온 요청 파라미터들을 setter메서드를 이용하여 객체에 정의되어있는 속성에 바인딩되는 객체

- 보통 VO나 DTO를 의미
- HttpServletRequest로 받아오는 요청 파라미터의 key값과 동일한 이름의 속성들과 setter메서드를 가지고 있어야 함
- [참고](https://medium.com/webeveloper/modelattribute-%EC%99%80-%EC%BB%A4%EB%A7%A8%EB%93%9C-%EA%B0%9D%EC%B2%B4-command-object-42c14f268324)



### @ModelAttribute 사용하기

- Command 객체의 이름을 변결할 목적으로도 사용 가능
- View(JSP)에서 사용할 데이터를 설정하는 용도로도 사용 가능

- @ModelAttribute가 설정된 메소드는 @RequestMapping어노테이션이 적용된 메소드보다 먼저 호출
  - `@ModelAttribute`메소드의 실행 결과로 리턴된 객체는 자동으로 Model에 저장
  - 그 객체를 View페이지에서 사용가능

- `BoardController.java`
  - seacrchConditionMap() 메소드위에 @ModelAttribute선언
  - getBoardList()메소드(@RequestMapping선언) 실행되기 전에 먼저 실행
  - searchConditionMap()메소드는 다양한 검색 조건이 저장된 conditionMap을 리턴
    - 이 리턴 결과를 다음에 실행된 getBoardList()메소드가 리턴한 JSP에서 사용 가능

```java
...

@Controller
public class BoardController { // 이 컨트롤러 하나로 나머지 컨트롤러 대체가능, 나머지 삭제해도 무방
	// 검색 조건 목록 설정
	@ModelAttribute("conditionMap")
	public Map<String, String> searchConditionMap() {
		Map<String, String> conditionMap = new HashMap<String, String>();
		conditionMap.put("제목", "TITLE");
		conditionMap.put("내용", "CONTENT");
		return conditionMap;
	}
	
    ...
        
	// 글 상세 조회
	@RequestMapping("/getBoard.do")
	public String getBoard(BoardVO vo, BoardDAO boardDAO, Model model) {
		model.addAttribute("board", boardDAO.getBoard(vo)); // Model정보 저장
		return "getBoard.jsp"; // View이름 리턴
	}
    
	...
```





#### 맵(Map)이란?

> 대응관계를 쉽게 표현할 수 있게 해주는 자료형

- ex) 이름 = 홍길동, 생일 = 몇 월 몇 일
- Associative array, Hash라고도 불림
- 사전(ditionary)와 비슷
  - Key, Value라는 것을 한 쌍으로 가짐
  - 순서에 상관없이 Key를 통해 value를 얻어냄
    - Map에 입력된 순서대로 데이터를 가지고 오고 싶은 경우 LinkedHashMap, TreeMap사용
    - LinkedHashMap : 입력된 순서대로 데이터 출력
    - TreeMap : 입력된 key의 소트순으로 데이터 출력

```java
import java.util.HashMap;

public class TestMap {
    public static void main(String[] args) {
        HashMap<String, String> map = new HashMap<String, String>();
        map.put("people", "사람");
        map.put("baseball", "야구");

        System.out.println(map.get("people"));
        System.out.println(map.containsKey("people"));
        System.out.println(map.remove("people"));
        System.out.println(map.size());
    }
}
```



### @SessionAttributes 사용하기

- 수정 작업 처리시 유용하게 사용할 수 있는 어노테이션

#### null 업데이트

#### null 업데이트 방지

- @SessionAttributes이용해 writer컬럼 값이 null로 업데이트 되지 않도록 처리하기

- `BoardController.java`
  - `model.addAttribute("board", boardDAO.getBoard(vo));`
    - 사용자가 상세화면 요청(/getBoard.do)하면 getBoard()메소드는 검색 결과를 BoardVO객체를 board라는 이름으로 Model에 저장
  - `@SessionAttributes("board")`
    - Model에 board라는 이름으로 저장되는 데이터가 있다면, 
    - 그 데이터를 세션(HttpSession)에도 자동으로 저장하라는 설정

```java
...

@Controller
@SessionAttributes("board")
public class BoardController { // 이 컨트롤러 하나로 나머지 컨트롤러 대체가능, 나머지 삭제해도 무방

    ...
        
    // 글 수정
	@RequestMapping("/updateBoard.do")
	public String updateBoard(@ModelAttribute("board") BoardVO vo, BoardDAO boardDAO) {
		System.out.println("번호 : " + vo.getSeq());
		System.out.println("제목 : " + vo.getTitle());
		System.out.println("작성자 : " + vo.getWriter());
		System.out.println("내용 : " + vo.getContent());
		System.out.println("등록일 : " + vo.getRegDate());
		System.out.println("조회수 : " + vo.getCnt());
		boardDAO.updateBoard(vo);
		return "getBoardList.do";
	}


    ...

	// 글 상세 조회
	@RequestMapping("/getBoard.do")
	public String getBoard(BoardVO vo, BoardDAO boardDAO, Model model) {
		model.addAttribute("board", boardDAO.getBoard(vo)); // Model정보 저장
		return "getBoard.jsp"; // View이름 리턴
	}
    
    ...
```



- getBoard()메소드가 실행되어 상세화면 출력되면, Model에 board라는 이름으로 BoardVO객체가 저장
  - 세션에도 board이름의 BoardVO객체 저장
  - 이 BoardVO객체에는 상세화면에 출력된 모든 정보(seq, title, writer, content, regDate, cnt)가 저장
  - 사용자가 글 수정 버튼 클릭시 updateBoard()메소드 실행
    - updateBoard()메소드엔 `@ModelAttribute("board")`가 매개변수로 선언
      - 스프링 컨테이너는 이 어노테이션을 우선 해석
      - 세션에 board라는 이름으로 저장된 데이터 있는지 확인
      - 있다면 해당 객체를 세션에서 꺼내 매개변수로 선언된 vo변수에 할당
        - 이때, 사용자가 입력한 수정정보인 title, content값만 새롭게 할당되고, 
        - 나머지는 상세 보기를 했을 때 세션에 저장된 데이터가 유지됨



# CLASS03 프레젠테이션 레이어와 비즈니스 레이어 통합

+