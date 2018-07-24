[toc] <!-- 목차 생성 -->

## 첫번째 서블릿 작성
{:toc}
실습을 위한 메이븐을 주입  
```java
<dependency>
	<groupId>javax.servlet</groupId>
	<artifactId>javax.servlet-api</artifactId>
	<version>3.1.0</version>
	<scope>provided</scope>
</dependency>
```

### 서블릿 클래스 작성
{:toc}
* 자바EE에서 `서블릿은 최종 사용자로부터 요청을 수신하고 응답하는 역활`을 하며, 자바 EE API사이트에서는 다음과 같이 정의하고 있다.
* - 서블릿은 작은 자바프로그램이며, 일반적으로 HTTP를 통해 웹 클라이언트로부터 요청을 수신하고 이에 응답한다.
* 서블릿은 모든 웹 애플리케이션의 핵심 클래스이며 `요청을 수신`하고 `작업을 수행`하거나 애플리케이션의 `다른부분으로 위임`할 수 있는 유일한 클래스이다.
* 필터로 `중간에 요청을 종료하지 않는이상 애플리케이션에 대한 모든 요청은 서블릿을 통하게 되어있다.`
* 웹 컨테이너는 하나이상의 기본 서블릿을 가지며 서블릿은 JSP에 서비스제공, 디렉터리 목록표시, HTML페이지 및 그래픽등의 정적자원 접근과 같은 작업을 수행한다.
* 모든 서블릿은 javax.servlet.Servlet인터페이스를 구현하지만, 일반적으로 직접 구현하지는 않으며 Servlet은 서블릿 초기화 및 삭제와 요청에대한 서비스 제공을 위한 간단한 메서드를 포함하는 인터페이스이다.

##### 서블릿이 확장하는 대상
{:toc}
* 서블릿은 대부분 javax.servlet.GenericServlet을 상속하며 GenericServlet은 프로토콜에 독립적인 서블릿이다.
* HttpServlet은 다양한 형식의 HTTP요청을 선택적으로 수신하고 응답하는 데 필요한 모든 도구를 제공한다.
* 메서드가 javax.servlet.htpp.HttpServletRequest alc HttpServletResponse형식의 인자를 받으므로 서블릿이 서비스하는 요청의 HTTP전용 속성에 쉽게 접근할 수 있다.
* javax.servlet.htpp.HttpServlet은 HTTP전용 요청에 응답하기 위해 GenericServlet을 확장하여 HTTP요청만 수신하는 service메서드를 구현하며 아래의 방식에 해당하는 메서드의 비어있는 구현을 제공한다.  
|메서드|서블릿 메서드|용도|
|:---|:---|:---|
|GET|doGet()|지정된 URP의 자원 검색|
|HEAD|doHead()|GET과 동일하지만 헤더만 반환|
|POST|doPost()|일반적으로 우베 폼 제풀에 사용됨|
|PUT|doPut()|제공된 엔터티를 URL에 저장|
|DELETE|doDelete()|URL로 지정된 자원을 삭제|
|OPTIONS|doOptions()|허용되는 HTTP메서드 형식을 반환|
|TRACE|doTrace()|진단용도로 사용됨|

##### 서블릿이 응답하는 HTTP 방식 제어
{:toc}
```java
import javax.servlet.http.HttpServlet;

public class HelloServlet extends HttpServlet {
	
}
```
> 서블릿이 응답하는 HTTP 방식 제어를 하기 위해서는 먼저 이렇게 시작해야 한다.
> 아무 처리도 하지 않은 서블릿은 소용이 없으므로 goGet에 대한 메서드를 재 정의하여 HTTP방식에 대한 지원을 추가해본다.  

```java
import java.io.IOException;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

@SuppressWarnings("serial")
public class HelloServlet extends HttpServlet {

	@Override
	protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		
		resp.getWriter().println("Hello, World!");
	}
}
```
> HTTP 요청이나 응답을 처리하는 세부적인 부분은 웹 컨테이너의 역활이기 때문에 신경 쓸 필요가 없다.

##### 이니셜라이저와 디스트로이어
{:toc}
* 웹 컨테이너가 처음 서블릿을 시작하면 해당 서블릿의 `init`메서드를 실행한다. 이 메서드는 `애플리케이션이 배포 될 때 종종 호출되며 매번 호출되지는 않는다.`
* 이 메서드는 자바 생성자 및 파이널라이저(finalizer)와 다르며 생성자 및 파이널라이저와 동시에 호출되지 않는다.
* 보통 이 메서드에서는 아무 작업도 수행하지 않지만 메서드를 재 정의해서 특정한 작업을 수행하게 할 수 있다.  
```java
@Override
public void init() throws ServletException {
	System.out.println("Servlet" + this.getServletName() + " has Started!");
}

@Override
public void destroy() {
	System.out.println("Servlet" + this.getServletName() + " has stopped!");
}
```
> 이러한 두 메서드를 이용하여 다양한 일을 할 수 있는데, 중요한것은 init이 호출되는 시점이 서블릿이 생성된 후 첫번째 응답에 요청하기 전이라는 점이다. (서블릿이 시작할때 호출된다.)
> 생성자가 호출되는 시점과 달리 init이 호출되는 시점에는 모든 프로퍼티가 설정되고 객체에 접근할 수 있는 상태이기 때문에 이 메서드로 프로퍼티 파일을 읽거나 JDBC를 통해 데이터베이스에 연결하는 등의 작업을 할 수 있다.  

> 비슷하게 `destroy`는 일반적으로 `웹 애플리케이션이 중지 또는 배포취소되거나 웹 컨테이너가 종료되어 서블릿이 더는 요청을 수신할 수 없을 때 호출`된다.
> 배포취소 또는 종료즉시 호출되므로 가비지컬렉션이 파이널라이저를 트리거해서 임시파일이나 더는 사용되지 않는 데이터베이스연결을 해제하는등의 자원정리 작업을 기다릴 필요가 없다.
> 이것이 중요한 이유는 애플리케이션이 배포취소 되더라도 서버는 계속 실행되며 가비지컬렉션이 실행되려면 몇분에서 몇시간이 걸릴수도 있기 때문이다.
> destroy메서드가 아니라 파이널라이저에서 자원을 정리하게되면 애플리케이션이 부분적으로 배포취소되거나 배초취소가 실패할 수 있어 요청간에 서블릿이 사용했던 자원을 정리하는데는 반드시 destroy 메서드를 사용하는것이 좋다.

### 배포를 위한 서블릿 구성
{:toc}

##### 설명자에 서블릿 추가
{:toc}
배포 설명자는 애플리케이션과 함께 배포할 리스너, 서블릿, 필터와 이를 위해 애플리케이션이 사용할 설정을 정의한다.  
```java
<?xml version="1.0" encoding="UTF-8"?>
<web-app version="2.5" xmlns="http://java.sun.com/xml/ns/javaee"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd">

	<display-name>Hello World Application</display-name>
</web-app>
```
> 상단은 web.xml의 예시로 display-name에 지정된 이름은 설치된 애플리케이션을 모두 나열하는 톰캣 관리자 화면에서 표시되며, 열린 web-app태그의 version속성은 이 애플리케이션에서 사용하는 서블릿 및 API버전을 지정한다.
> 이전에 작성한 서블릿을 사용하기 위해 하단의 설정을 추가한다.  

```java
<servlet>
	<servlet-name>HelloServlet</servlet-name>
	<servlet-class>src.main.java.HelloServlet</servlet-class>
	<load-on-startup>1</load-on-startup>
</servlet>
```
> 이전에 작성한 서블릿의 인스턴스를 생성하도록 한다.
> 이렇게 작성할경우 init메서드는 웹 애플리케이션이 시작되고 서블릿이 첫번째 요청을 수신하면 호출되는데 만약 init메서드가 복잡한 작업을 수행하는 경우 서블릿이 시작되는데 오래걸릴수가 있어, 아래와 같이 서블릿 구성을 수정하여 웹 애플리케이션이 시작할 때 즉시 서블릿이 시작되도록 지정하면 해당 문제를 예방할 수 있다.
> 코드 하단의 load-on-startup부분은 웹 애플리케이션이 시작하는 즉시 서블릿을 시작하도록 컨테이너에 지시한다. "1"이 제일 우선순위가 높으며 값이 클수록 나중에 실행된다. 만약 순위가 동일할경우 설명자 파일에 나온순서대로 실행된다.


##### 서블릿과 URL매핑
{:toc}
이제까지 서블릿을 시작하도록 구성했다면, 하단부터는 어떤 URL에 반응할 것인지 지정해준다.  
```java
<servlet-mapping>
	<servlet-name>HelloServlet</servlet-name>
	<url-pattern>/greeting</url-pattern>
</servlet-mapping>
```
> 상단의 구성은 '/greeting'라는 URL구성에 대한 모든 요청을 HelloServlet에서 하도록 구성한다.
> 상단에 servlet에서 작성한 servlet-name과 servlet-mapping의 servlet-name이 일치하게 구성해야 한다.
> 매핑은 1:1로만 구성되는것이 아니라 아래와같이 1:n으로도 구성될 수 있다.  

```java
<servlet-mapping>
	<servlet-name>HelloServlet</servlet-name>
	<url-pattern>/greeting</url-pattern>
	<url-pattern>/salutation</url-pattern>
	<url-pattern>/wazzup</url-pattern>
</servlet-mapping>
```
> 위와 같은 방법이 그냥 URL을 서블릿 클래스에 매핑하는 것보다 불편하고 번거롭다고 느낄 수도 있는데, 예를들어 온라인 쇼핑애플리케이션에서 완전히 동일한 로직을 사용하지만 서로 다른 데이터 베이스에 연결해야 하는 경우 아래와 같이 간단하게 해결할 수 있다는 장점이 있다.  

```java
<servlet>
	<servlet-name>Store1</servlet-name>
	<servlet-class>src.main.java.Store1</servlet-class>
</servlet>

<servlet>
	<servlet-name>Store2</servlet-name>
	<servlet-class>src.main.java.Store2</servlet-class>
</servlet>
	
<servlet-mapping>
	<servlet-name>Store1</servlet-name>
	<url-pattern>/Store1</url-pattern>
</servlet-mapping>		

<servlet-mapping>
	<servlet-name>Store2</servlet-name>
	<url-pattern>/Store2</url-pattern>
</servlet-mapping>
```
> 여러개의 서블릿을 사용할 때 서블릿 코드에서 this.getServletName()을 호출하면 어떤 인스턴스인지에 따라 사용된 서블릿의 이름이 반환되어 각 인스턴스에서 사용되는 서블릿을 알 수 있다.  
> 아래는 지금까지 나온 내용을 종합하여 만들어진 web.xml파일이다.  
```java
<?xml version="1.0" encoding="UTF-8"?>
<web-app version="2.5" xmlns="http://java.sun.com/xml/ns/javaee"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd">

	<display-name>Hello World Application</display-name>

	<servlet>
		<servlet-name>HelloServlet</servlet-name>
		<servlet-class>src.main.java.HelloServlet</servlet-class>
		<load-on-startup>1</load-on-startup>
	</servlet>

	<servlet-mapping>
		<servlet-name>HelloServlet</servlet-name>
		<url-pattern>/greeting</url-pattern>
	</servlet-mapping>
</web-app>
```

##### 서블릿 실행 및 디버깅
{:toc}
* 애플리케이션을 저장하고 컴파일을 하면 실행구성이 정확하게 설정되었는지 확인해야 한다.
* 디버그 모드로 실행을 하고 중단점을 찍어서 확인한다.

### doGet(), doPost()및 다른 메서드
{:toc}

##### 서비스 메서드 실행중 수행해야 하는 작업
{:toc}
* servlet클래스의 `service메서드`는 모든 들어오는 `요청에 대한 서비스를 제공하는 역활`을 한다.
* 궁극적으로는 사용되는 프로토콜을 바탕으로 들어오는 요청의 데이터구문을 분석 및 처리하고 프로토콜에 적합한 응답을 클라이언트로 전송해야 한다. service메서드가 응답데이터를 소켓에 전달하지 않고 반환하면 클라이언트 에서는 "연결 재설정"과 같은 네트워크 오류가 발생할 가능성이 높은데, `HttpServlet을 확장하면 이러한 세부 작업을 직접 처리할 필요가 없다는 장점`을 가지고 있다.

##### HttpServletRequest 사용
{:toc}
* HttpServletRequest인터페이스는 수신한 요청에 대한 추가적인 HTTP프로토콜 전용정보를 제공하는 ServletRequest의 확장 인터페이스이다.
* HttpServletRequest의 가장 중요한 기능은 `클라이언트가 전송한 요청 매개변수를 얻는 기능`이다. 요청매개변수는 쿼리매개변수또는 application/x-www-form-urlencoded, multipart/form-data로 인코딩된 요청본문(post변수)으로 전달된다.

##### Request의 주요메서드 기능
{:toc}
|메서드|기능|
|:---|:---|
|getParameter|매개변수의 단일값을 반환하며, 매개변수에 여러개의 값이 포함된경우 첫번째 값을 반환한다.|
|getParameterValues|매개변수 값의 배열을 반환한다.|
|getParameterMap|모든 매개변수와 값의 쌍을 Map<String, String[]>형태로 반환한다.|
|getParameterNames|사용가능한 모든 매개변수의 이름을 열거형으로 반환한다.|

##### 요청 컨텐츠에 대한 정보 확인
{:toc}
|메서드|기능|
|:---|:---|
|getContentType|요청의 MIME콘텐츠 형식을 반환한다.|
|getContentLength(Long)|요청 본문의 바이트수를 반환한다. (길이가 길경우 Long메서드를 사용한다.)|
|getCharacterEncoding|컨텐츠의 문자 인코딩을 반환한다.|
|getReader, getInputStream|컨텐츠가 문자 인코딩 데이터일경우 Reader를, 이진 데이터일경우 InputStream을 사용하며 동일 요청에 대하여 두 메서드를 모두 사용할 수는 없다.|

##### URL, URI, 헤더와 같은 요청정보 받기.
{:toc}
|메서드|기능|
|:---|:---|
|getRequestURL|클라이언트가 요청하는데 사용한 전체 URL을 반환한다.|
|getRequestURI|URL의 서버 경로 부분만 반환한다.|
|getServletPath|서블릿 매핑에 사용되는 URL부분을 반환한다.|
|getHeader|지정한 이름의 헤더값을 반환한다.|
|getHeaderNames|요청한 모든 헤더 이름을 열거형으로 반환한다.|

### HttpServletResponse사용
{:toc}
ServletResponse를 확장한 인터페이스로 `응답에 HTTP프로토콜 관련 프로퍼티에 접근`할 수 있도록 한다.

##### 응답본문에 컨텐츠 기록
{:toc}
* 응답본문에 컨텐츠를 기록하는 방법은 javax.servlet.SetvletOutputStream을 반환하는 getOutputStream()과java.io.PrintWriter를 반환하는 getWriter()메서드를 이용하여 기록한다.
* Request와 마찬가지로 HTML또는 다른 문자 인코딩된 텍스트를 클라이언트에 반환할경우 getWriter를 사용하는게 좋으며, 바이너리 데이터를 전송할 경우 getOutputStream를 이용해 응답 바이트를 전송하는 것이 좋다. 단, 동일한 응답에 두개의 메서드를 동시에 사용할 경우 오류가 발생한다.
* 응답본문에 기록하는 동안 인코딩이나 컨텐츠 형식을 지정해야 하는경우 setContentType() 과 setCharacterEncoding()을 사용할 수 있고 해당 메서드가 여러번에 걸쳐 중복 사용 되었을경우 마지막에 사용된 인코딩 및 컨텐츠 형식으로 지정된다. 또한 getWriter와 함께 사용하는 경우 getWriter의 사용시점보다 이전에 컨텐츠타입과 인코딩이 지정되야 올바른 문자 인코딩을 하도록 구성된다.

##### 헤더 및 다른 응답 프로퍼티 설정
{:toc}
* Response는 Request에 있는 메서드와 비슷하게 헤더 및 다른 응답 프로퍼티를 재 설정 할 수 있다.
* 설정하려는 헤더와 같은 이름의 헤더가 이미 기존의 응답헤더에 있는경우 해당 해더값이 재 정의된다.  
|메서드|기능|
|:---|:---|
|setHeader|헤더값 설정|
|addHeader|새로운 헤더 추가|
|getHeader, getHeaders|응답에 이미 설정되어있는 헤더 조사|
|setStatus|HTTP응답 상태코드 설정|
|getStatus|응답의 현재상태 확인|
|sendError|설정된 상태코드를 이용하여 클라이언트로 오류응답을 전송하고 버퍼를 삭제|
|sendRedirect|클라이언트를 다른 URL로 리디렉션|

##### 매개변수 사용 및 폼 제출 처리
{:toc}
```java
@WebServlet (
		name = "HelloServlet",
		urlPatterns = {"/greeting", "/salutation", "wazzup"},
		loadOnStartup = 1		
)
public class HelloServlet extends HttpServlet {
	
	private static final String DEFAULT_USER = "Gest";

	@Override
	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		
		String user = request.getParameter("user");
		
		if(user == null) {
			user = HelloServlet.DEFAULT_USER;							// user가 입력되지 않았을경우 Gest로 사용한다.
		}
		
		response.setContentType("text/html");				// 응답의 컨텐츠 형식 지정
		response.setCharacterEncoding("UTF-8");		// 응답의 인코딩 형식 지정
		
		PrintWriter writer = response.getWriter();
		
		writer.append("<!DOCTYPE html> \r\n")
			.append("<html> \r\n")
			.append("<head> \r\n")
			.append("<title>hellow user!</title> \r\n")
			.append("<head> \r\n")
			.append("<body> \r\n")
			.append(" Hello, ").append(user).append("!<br /><br /> \r\n")
			.append(" <form action='greeting' method='POST'>\r\n")
			.append("Enter your name : <br /> \r\n")
			.append(" <input type='text' name='user'><br />\r\n")
			.append(" <input type='submit' value='Submit'><br />\r\n")
			.append(" </form>\r\n")
			.append(" </body>\r\n")
			.append(" </html>\r\n");
	}

	@Override
	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		this.doGet(request, response);				// POST로 요청한 방식을 GET으로 실행한다.
	}
}
```
> 예제 코드 상단의 어노테이션은 web.xml파일에서의 서블릿 선언과 매핑과 같이 애플리케이션이 시작할 때 이를 함께 시작하며 지정된 URL로 매핑한다. 
> 해당 애플리케이션을 실행하고 http://localhost:8080/hello-world/greeting으로 이동하면 해당 코드로 작성된 페이지를 볼 수있다.