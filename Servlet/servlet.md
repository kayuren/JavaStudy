기본적인 웹 애플리케이션의 구조 이해
----------
### 서블릿
* 모든 자바 웹 애플리케이션의 핵심컴포넌트로서 HTTP요청을 수신하고 응답하는 역활을 하는 자바 클래스


### 필터
* 서블릿에 대한 요청을 가로챌 수 있는 컴포넌트로서 대한 고민이 데이터 서식 지정과 응답 압축, 인증 및 권한부여등 다양한 용도로 사용 가능하다.


### 리스너
* 애플리케이션의 시작 및 종료 HTTP세션 생성, 세션 삭제와 같은 이벤트를 코드에 알리는 역활을 한다.


###JSP
* HTML의 String을 OutputStream또는 PrintWriter로 직접 출력하는 작업 없이 웹 애플리케이션을 위한 동적인 HTML기반 사용자 인터페이스를 만들 수 있다.


### 디렉터리 구조와 WAR파일
* 표준자바 웹 애플리케이션은 WAR파일 또는 압축 해제된 웹 애플리케이션 디렉터리로 배포된다. JAR파일이 JVM이 인식하는 표준 디렉터리 구조를 압축한 ZIP형식의 압축파일인 것처럼 WAR파일은 자바 웹 애플리케이션을 위한 아카이브 파일이다.


### 배포설명자
* 웹 애플리케이션을 설명하고 자바 웹 애플리케이션 서버에 웹 애플리케이션을 배포 및 실행하기 위한 지침을 제공하는 메타데이터
* 일반적으로 이 메타데이터는 배포 설명자 파일은 web.xml파일에 있다.


### 클래스로더 아키텍쳐
* 일반적인 애플리케이션에서 java 클래스는 재 정의할 수 없는 특수한 경로에 로드된다. (ClassLoader)
* 루트 ClassLoader > jre설치 디렉터리의 확장 jar에서 클래스를 로드하는 확장 ClassLoader > 애플리케이션의 다른 모든 클래스를 로드하는 애플리케이션 ClassLoader 순서의 계층 구조를 가지고있다.
* 하위 ClassLoader는 클래스 로드 요청을 받으면 항상 부모 ClassLoader로부터 로드를 위임하며, 루트 ClassLoader가 확인될 때까지 계층을 거슬러 올라간다. 루트 ClassLoader를 제외하고 부모 ClassLoader가 먼저 찾지 못한 경우에만 해당 jar컬렉션과 디렉터리에서 해당 Class를 로드한다.
위와같은 클래스 로딩방식을 Parent-first위임모델이라고 하며, 여러 애플리케이션의 유형에 적합하지만 자바 웹 애플리케이션에는 부적합하다.
* 자바 웹 애플리케이션은 복잡하고 여러가지의 라이브러리들이 사용되므로 버전충돌이 일어날 수 있기때문에 Parent-last위임모델을 사용하는 것이 바람직하다. 이는 각 웹 애플리케이션에 공통버 ClassLoader를 상속하는 격리된 각각의 ClassLoader를 할당하므로 다른 애플리케이션의 클래스에 접근할 수 없다.
* 일반적으로 웹 애플리케이션 ClassLoader는 자신이 먼저 클래스를 로드할 수 없는 경우에만 부모에 클래스를 로드하도록 요청하기 때문에 부모가 나중이 되도록 클래스로딩을 위임하므로서 웹 애플리케이션 클래스와 라이브러리가 서버의 클래와 라이브러리보다 높은 우선순위를 가질 수 있게 한다.

 
### 엔터프라이즈 아카이브
엔터프라이즈 아카이브 EAR
* JAR, WAR파일과 구성파일을 배포 가능한 단일 아카이브(JAR또는 WAR와 같은 ZIP형식)로 압축한것

웹 컨테이너 이용하기
======================
웹컨테이너 선택
-----------
* 서블릿컨테이너라고도 하며 JAVA EE사양에는 다수의 하위사양이 포함되 있지만 대부분의 웹 컨테이너는 서블릿, JSP, JSTL사양만 구연하여 자바 애플리케이션 서버와는 차이가 있다.
* 서블릿의 수명주기관리, 요청 URL을 서블릿 코드로 매핑, HTTP요청 수신 및 응답, 필터체인관리등의 역활을 한다.


### 아파치 톰캣
* 현재 가장 일반적이고 많이 사용되는 웹 컨테이너로서, 주요 장점으로는 작은 사용곤간, 간단한 구성, 오랜기간에 걸친 커뮤니티 참여가 있다.
* 세부적인 튜닝을 거치면 고부하 및 고가용성의 프로덕션 환경에서도 우수한 성능을 보이나, 서버를 구성하기 위해 제공하는 것과 같은 정교한 웹 관리 인터페이스를 제공하지 않고 애플리케이션 배포 및 배포취소같은 기본적인 작업을 위한 단순한 인터페이스만 제공한다.


### 글래스피시
* 웹컨테이너로서 아파치 톰캣에서 파생됐지만, 공통점은 거의 없다. 
* 그래픽 웹 사용자 인터페이스와 명령줄 인터페이스, 서버내의 모든 사항을 구성할 수 있는 구성 파일을 제공하는 관리 인터페이스라는것이 장점이다.


커스텀 JSP 컴파일러 구성
-------------
### 톰캣에 애플리케이션 배포 및 배포취소
* 톰캣에 자바 웹 애플리케이션을 배포하는 방법은 두가지가 있다.
1. 애플리케이션을 webapp디렉터리로 직접 배포하는 방법
2. 톰캣 관리자 어플리케이션을 이용하는 방법

#### 수동 배포 및 배포취소
* 톰캣에서 애플리케이션을 수동 배포하려면 WAR파일을 톰캣의 webapp디렉터리로 이동하면 된다.
* 해당 디렉터리로 이동하면 톰캣이 실행중(실행될)때 WAR확장자만 뺀 동일한 이름의 디렉터리에 압축을 푼다.
* 배포를 취소할경우 해당 WAR파일을 삭제하면 되며 배포 취소하기 위해 톰캣을 종료할 필요는 없다.

#### 톰캣 관리자 사용
* 또 다른 배포방법으로 톰캣 관리자 인터페이스를 사용 하여 배포할 수 있는데, 방법은 다음과 같다.
1. http://localhost:8080/manager/html으로 이동
2. 사용자명과 비밀번호를 입력 (admin/admin)
3. Deploy의 WAR file to deploy폼의 Select War file to uploade에서 업로드할 WAR파일을 선택한다.
* 작업이 완료되면 톰캣은 애플리케이션의 목록을 반환해 배포 목록을 확인할 수 있게 해준다.
* 배포취소의 경우 관리자 항목에서 배포 취소할 애플리케이션의 오른쪽에 있는 Undeploy버튼을 클릭하면 된다.


### IDE에서 톰캣 디버깅하기
* 자바 IDE에서 톰캣으로 애플리케이션을 배포하고 디버그하는 기술은 개발자에게 매우 중요한 기술 중 하나로, 이는 애플리케이션이 제대로 실행되지 않는 이유나 고객이 보고하는 버그가 발생하는 이유는 밝히는데 매우 유용하다.
* 이 책에는 2가지의 방법이 다뤄지고 있다.

#### 인텔리J IDE 사용
1. 톰캣 서버 추가
Settings > IDE Settings하단 Application Server > 새로운 서버를 추가하기 위해 녹색의 더하기 버튼 클릭 > 톰캣 홈 디렉터리 설정 후 OK버튼 
2. 톰캣에 프로젝트 추가
도구모음에서 Run/Debug Configurations아이콘(아래화살표)를 누른 후 Edit Configuration 버튼 > 새로운 서버를 추가하기 위해 녹색의 더하기 버튼 클릭 > Add New Configuration 하단에 서버 선택 후 OK버튼

#### 이클립스 IDE 사용
1. 톰캣 서버 추가
Windows - Preferences > Server - Environments - Add > Apach - Create new local server > 사용할 서버의 버전명 선택 후 Next > 톰캣 홈 디렉터리 지정 > 로컬에 설치된 JRE 버전 선택 후 OK 버튼
2. 프로젝트에서 톰캣 서버사용
Project - Propertise - Server > 사용하려는 톰캣 서버로 변경



첫번째 서블릿 작성
===================
* 실습을 위해 필요한 메이븐을 주입해준다.
```java
<dependency>
	<groupId>javax.servlet</groupId>
	<artifactId>javax.servlet-api</artifactId>
	<version>3.1.0</version>
	<scope>provided</scope>
</dependency>
```


서블릿 클래스 작성
-----------
* 서블릿은 최종 사용자로부터 요청을 수신하고 응답하는 역활을 한다.
* 모든 웹 애플리케이션의 핵심 클래스이며, 요청을 수신하고 작업을 수행하거나 애플리케이션의 다른부분으로 위임할 수 있는 유일한 클래스이기도 하다.
* 필터로 중간에 요청을 중단하지 않는이상 애플리케이션에 대한 모든 요청은 서블릿을 통하게 되어있다.
* 애플리케이션을 실행하는 웹 컨테이너는 하나이상의 서블릿을 가지며 이러한 서블릿은 JSP에 서비스 제공, 디렉터리 목표 표시, HTML페이지 및 그래픽등의 정적자원 접근과같은 일을 처리한다.
* 모든 서블릿은 javax.servlet.Servlet 인터페이스를 구현하지만 일반적으로 직접 구현하지는 않는다. Servlet은 서블릿 초기화 및 삭제 와 요청에 대한 서비스 제공을 위한 메서드를 포함하는 간단한 인터페이스이며, 모든 형식의 요청에 대해 호출된다.


### 서블릿이 확장하는 대상
* 서블릿은 대부분 javax.servlet.GenericServlet을 상속하며 GenericServlet은 프로토콜에 독립적인 서블릿이다.
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

* HttpServlet은 다양한 형식의 HTTP요청을 선택적으로 수신하고 응답하는 데 필요한 모든 도구를 제공한다.
* 메서드가 javax.servlet.htpp.HttpServletRequest alc HttpServletResponse형식의 인자를 받으므로 서블릿이 서비스하는 요청의 HTTP전용 속성에 쉽게 접근할 수 있다.

* 서블릿이 응답하는 HTTP 방식 제어를 하기 위해서는 먼저 이렇게 시작해야 한다.
```java
import javax.servlet.http.HttpServlet;

public class HelloServlet extends HttpServlet {
	
}
```

* 아무 처리도 하지 않은 서블릿은 소용이 없으므로 goGet에 대한 메서드를 재 정의하여 HTTP방식에 대한 지원을 추가해본다.
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
* HTTP 요청이나 응답을 처리하는 세부적인 부분은 웹 컨테이너의 역활이기 때문에 신경 쓸 필요가 없다.


### 이니셜라이저와 디스트로이어
* 웹 컨테이너가 처음 서블릿을 시작하면 해당 서블릿의 init메서드를 실행한다. 이 메서드는 애플리케이션이 배포 될 때 종종 호출되며 매번 호출되지는 않는다.
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
* 이러한 두 메서드를 이용하여 다양한 일을 할 수 있는데, 중요한것은 init이 호출되는 시점이 서블릿이 생성된 후 첫번째 응답에 요청하기 전이라는 점이다. (서블릿이 시작할때 호출된다.)
* 생성자가 호출되는 시점과 달리 init이 호출되는 시점에는 모든 프로퍼티가 설정되고 객체에 접근할 수 있는 상태이기 때문에 이 메서드로 프로퍼티 파일을 읽거나 JDBC를 통해 데이터베이스에 연결하는 등의 작업을 할 수 있다.

* 비슷하게 destroy는 일반적으로 웹 애플리케이션이 중지 또는 배포취소되거나 웹 컨테이너가 종료되어 서블릿이 더는 요청을 수신할 수 없을 때 호출된다.
* 배포취소 또는 종료즉시 호출되므로 가비지컬렉션이 파이널라이저를 트리거해서 임시파일이나 더는 사용되지 않는 데이터베이스연결을 해제하는등의 자원정리 작업을 기다릴 필요가 없다.
* 이것이 중요한 이유는 애플리케이션이 배포취소 되더라도 서버는 계속 실행되며 가비지컬렉션이 실행되려면 몇분에서 몇시간이 걸릴수도 있기 때문이다.
* destroy메서드가 아니라 파이널라이저에서 자원을 정리하게되면 애플리케이션이 부분적으로 배포취소되거나 배초취소가 실패할 수 있어 요청간에 서블릿이 사용했던 자원을 정리하는데는 반드시 destroy 메서드를 사용하는것이 좋다.


배포를 위한 서블릿 구성
------------
### 설명자에 서블릿 추가
* 배포 설명자는 애플리케이션과 함께 배포할 리스너, 서블릿, 필터와 이를 위해 애플리케이션이 사용할 설정을 정의한다.

* 아래는 web.xml의 예시이다.
```java
<?xml version="1.0" encoding="UTF-8"?>
<web-app version="2.5" xmlns="http://java.sun.com/xml/ns/javaee"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd">

	<display-name>Hello World Application</display-name>
</web-app>
```
* display-name에 지정된 이름은 설치된 애플리케이션을 모두 나열하는 톰캣 관리자 화면에서 표시되며, 열린 web-app태그의 version속성은 이 애플리케이션에서 사용하는 서블릿 및 API버전을 지정한다.

* 이전에 작성한 서블릿을 사용하기 위해 설정을 추가한다.
```java
<servlet>
	<servlet-name>HelloServlet</servlet-name>
	<servlet-class>src.main.java.HelloServlet</servlet-class>
	<load-on-startup>1</load-on-startup>
</servlet>
```
* 코드 하단의 load-on-startup부분은 웹 애플리케이션이 시작하는 즉시 서블릿을 시작하도록 컨테이너에 지시한다. "1"이 제일 우선순위가 높으며 값이 클수록 나중에 실행된다. 만약 순위가 동일할경우 설명자 파일에 나온순서대로 실행된다.


### 서블릿과 URL매핑
* 이제까지 서블릿을 시작하도록 구성했다면, 하단부터는 어떤 URL에 반응할 것인지 지정해주는 작업이다.
```java
<servlet-mapping>
	<servlet-name>HelloServlet</servlet-name>
	<url-pattern>/greeting</url-pattern>
</servlet-mapping>
```
* 상단의 구성은 '/greeting'라는 URL구성에 대한 모든 요청을 HelloServlet에서 하도록 구성한다.
* 상단에 servlet에서 작성한 servlet-name과 servlet-mapping의 servlet-name이 일치하게 구성해야 한다.

* 매핑은 1:1로만 구성되는것이 아니라 아래와같이 1:n으로도 구성될 수 있다.
```java
<servlet-mapping>
	<servlet-name>HelloServlet</servlet-name>
	<url-pattern>/greeting</url-pattern>
	<url-pattern>/salutation</url-pattern>
	<url-pattern>/wazzup</url-pattern>
</servlet-mapping>
```
* 위와 같은 방법이 그냥 URL을 서블릿 클래스에 매핑하는 것보다 불편하고 번거롭다고 느낄 수도 있는데, 예를들어 온라인 쇼핑애플리케이션에서 완전히 동일한 로직을 사용하지만 서로 다른 데이터 베이스에 연결해야 하는 경우 아래와 같이 간단하게 해결할 수 있다는 장점이 있다.


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
* 여러개의 서블릿을 사용할 때 서블릿 코드에서 this.getServletName()을 호출하면 어떤 인스턴스인지에 따라 사용된 서블릿의 이름이 반환되어 각 인스턴스에서 사용되는 서블릿을 알 수 있다.


* 아래는 지금까지 나온 내용을 종합하여 만들어진 web.xml파일이다.
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


### 서블릿 실행 및 디버깅
* 애플리케이션을 저장하고 컴파일을 하면 실행구성이 정확하게 설정되었는지 확인해야 한다.
* 디버그 모드로 실행을 하고 중단점을 찍어서 확인한다.