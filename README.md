<h1>SERVLET</h1>
서블릿은 주로 **주로 웹 서버에서 동작**하는 애플리케이션을 구현할 때 사용합니다.
서블릿을 만드는 목적은 클라이언트에 서비스하기 위함이며, 서버에 서블릿이 준비되어 있어야 클라이언트가 서블릿에 접근할 수 있습니다.

<h3>목차</h3>
<a href="#servlet-start">servlet구현 및 실행</a><p>
<a href="#req-resp">응답 정보 처리</a><p>
<a href="#question">질의 문자열</a><p>


<h2 name="serlvet-start">servlet구현 및 실행</h2>
가장 기본적인 코드 소스를 작성해보겠습니다.

- FirstSerlvet.java

```java
package ..
import javax.servlet.http.*;

public class FirstServlet extends HttpServlet {

}
```
HttpServlet는 서블릿이 웹 상에서 HTTP프로토콜을 이용해 서비스를 처리하기 위해 **반드시 상속**받아야 하는 클래스입니다. 즉, 모든 서블릿 클래스는 HttpServlet을 상속받습니다.

```java
import java.io.*;  
import javax.servlet.ServletException;  
import javax.servlet.ServletRequest;  
import javax.servlet.ServletResponse;  
import javax.servlet.http.*;  
  
public class FirstServlet extends HttpServlet {  
    @Override  
  public void init() throws ServletException {  
        super.init();  
  }  
  
    @Override  
  public void service(ServletRequest req, ServletResponse res) throws ServletException, IOException {  
        super.service(req, res);  
  }  
}
```
`init( )` 메소드는 서블릿이 최초로 호출되었을 때, 서블릿 컨테이너가 자동으로 실행되는 것이며,
`service( )` 메소드는 클라이언트의 요청이 있을 때마다 매번 서블릿 컨테이너가 자동으로 실행되는 것입니다.

<h3>객체의 생명주기</h3>
HttpServlet 클래스를 상속받은 다음 재정의한 init( ), serviet( )는 콜백 메소드에 속합니다. 이 외에도 destroy( ) 메소드가 존재하며, 이는 객체의 삭제부분에서 활용됩니다.

서블릿 객체의 생성은 서버 입장에서 클라이언트가 최초로 서블릿 요청이 있을 때 객체가 생성되며, init( ) -> service( ) 순으로 실행됩니다. 요청이 있을 때마다 서블릿 객체를 생성하는 것이 아닌, 최초 요청 때 생성한 서블릿 객체를 계속 사용한다는 부분에서 서블릿의 장점을 볼 수 있습니다.

또한 서블릿 객체의 삭제되는 시점은 서버를 중지시키거나 웹 애플리케이션 서비스를 중지할 때이며, 어떤 상황이든지 서블릿 객체가 삭제되는 시점은 웹 서버에서 웹 애플리케이션 서비스가 중지되는 시점입니다. 이 때에는 destroy( )가 실행되며 서블릿 객체의 소멸이 이루어집니다.

<h3>실행 접근 방법</h3>

- web.xml을 통해 접근
web.xml안에 있는 web-app태그 안에 다음을 집어넣는 방법입니다.
```html
<servlet>  
  <servlet-name>loginCheck</servlet-name>  
  <servlet-class>com.example.Servlet.LoginCheckServlet</servlet-class>  
  <load-on-startup>1</load-on-startup>  
</servlet>  
  
<servlet-mapping>  
  <servlet-name>loginCheck</servlet-name>  
  <url-pattern>/login</url-pattern>  
</servlet-mapping>
```

servlet-name은 서블릿에 대한 이름을 제시한 후 servlet-class에서 서블릿의 실제 클래스 이름을 지정합니다. 또한 위의 과정이 이루어지고, servlet-mapping작업을 통하여 실행할 서블릿의 이름을 servlet-name에 명시해주고, url-pattern태그로 서블릿을 실행할 때 사용할 URL을 지정하게 됩니다.

- @WebServlet을 통해 접근
어노테이션은 자바 주석문 처럼 소스 안에 @ 기호와 함께 사용되며, 자바프로그램 실행에 관한 내용을 설정하는 용도로 사용됩니다.
```java
import java.io.*;  
import javax.servlet.ServletException;  
import javax.servlet.ServletRequest;  
import javax.servlet.ServletResponse;  
import javax.servlet.annotation.WebServlet;  
import javax.servlet.http.*;  
  
@WebServlet("/first")  
public class FirstServlet extends HttpServlet {  
     ...
}
```
이 때에는 접근 시 사용할 URI를 지정하면 됩니다. 이 방법을 사용하려면 web.xml에서 사용한 방법은 지우십시오.

<h2 name="req-resp">request, response</h2>
HttpServletRequest는 클라이언트가 서버에 보내는 요청정보를 처리하는 객체이며
HttpServletResponse는 서버가 클라이언트로 보내는 응답정보를 처리하는 객체입니다.

이 둘은, service메소드가 실행되기 전에 생성 되었다가 끝나면 소멸하는 것입니다. 따라서 service메소드가 실행되는 동안에만 메모리에 상주하고 있어 그 동안에만 사용할 수 잇습니다.

<h3>HttpServletResponse</h3>

다음은 HttpServletResponse객체를 활용하여 클라이언트 쪽으로 문자열을 전송한 다음, 웹 브라우저에 문자열을 출력하는 예제입니다.
```java
import javax.servlet.ServletException;  
import javax.servlet.annotation.WebServlet;  
import javax.servlet.http.*;  
import java.io.IOException;  
import java.io.PrintWriter;  
  
@WebServlet("/second")  
public class SecondServlet extends HttpServlet {  
    @Override  
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
	    //다음은 한글과 영어 문자셋을 지원하기 위해 사용합니다.
	    resp.setContentType("text/html; charset=UTF-8");
        System.out.println("SecondServlet!!");  
	    PrintWriter out = resp.getWriter();  
	    out.print("<html><head><title>Title</title></head>");  
	    out.print("<body><h1>have a nice day</h1></body>");  
	    out.print("</html>");  
	    out.close();  
    }
}
```

<h3>HttpServletRequest</h3>
서블릿은 주로 웹서버의 애플리케이션 기술로 활용되어 HTTP 프로토콜 기반의 애플리케이션이라고 할 수 있습니다. 또한 request는 **ServletRequest**(일반적인 네트워크 통신에서의 요청 관련 메소드 제공)와 **HttpServletRequest**(HTTP 통신 기반의 요청 관련 메소드를 확장하여 제공) 두 가지가 있습니다.

`req.getScheme()`
`req.getServerName()`
`req.getLocalAddr()`
`req.getServerPort()`
와 같은 여러가지 메소드를 통해서 네트워크 정보를 확인할 수 있으며, 그 외에도 다양한 메소드를 통해 모든 헤더 정보를 살펴볼 수도 있습니다.

<h2 name="#question">질의 문자열</h2>
웹에서의 질의 문자열을 사용하게 되면서 웹을 서비스 제공자와 이용자의 상호작용 매체로 활용할 수 있게 되었습니다. 질의 문자열 처리는 웹이 다양한 목적으로 사용되는 데 중요한 역할을 담당하는 기술이기 때문에 요청과 응답사이의 관계를 이해하기 위해 필수적입니다.

질의 문자열은 다음과 같은 규칙이 있습니다.
1. name=value의 형식으로 전달되며, 여러 개의 name=value 싸잉 있을 때 &를 구분자로 사용합니다.
2. 영문자, 숫자, 일부 특수문자는 그대로 전달되고, 이를 제외한 나머지 문자는 % 기호와 함께 16진수로 바뀌어 전달됩니다.
3. 공백문자는 + 기호로 변경되어 전달됩니다.

<h3>요청 방식에 따른 처리</h3>
클라이언트가 서버로 전달하기 위한 값들을 입력받는 화면을 만들기 위해서는 html과 css등의 문법을 알아야 하지만, 이전에서 진행했다는 가정하에 다음 대표적인 요청 방식은 GET과 POST방식에 대해 알아보도록 하겠습니다.

<h4>GET 방식으로 처리</h4>
먼저 GET방식으로 처리하는 예제입니다.

```html
<form method="get" action="login">  
  ID : <input type="text" name="userId"/><br/>  
  PWD : <input type="password" name="userPwd"/></br/>  
  NAME : <input type="text" name="userName"/></br/>  
  
 <input type="submit" value="로그인"/>  
</form>
```
id와 password, name이 get방식으로 넘어가게 되어 실행될 수 있는 form입니다.
`.../Servlet/src/main/webapp/login?userId=userId=아이디&userPwd=1234&userName=이름`
다음과 같은 주소로 연결이 되는데, 폼에서 입력한 정보들이 모두 name=value&name=value의 값으로 연결되는 것을 알 수 있습니다. 이와 같은 GET방식에는 데이터의 크기 제한이 있습니다. 이는 URI 길이에 대한 서버 인식 능력이 제한적이기 때문에 255바이트 미만으로 정의되어 있습니다. 또한, 외부에 노출되어도 상관없는 데이터를 전달할 때 사용하는 요청방식입니다.

GET방식으로 요청되는 경우는 다음과 같습니다.
1. a태그를 클릭하여 요청
2. 브라우저 주소 줄에 URL을 입력하여 요청하는 경우
3. form태그에서 method 속성을 생략하여 요청하는 경우

<h4>POST방식으로 처리</h4>
이번에는 요청 방식이 POST인 경우를 알아보도록 하겠습니다.

```html
<form method="post" action="login">  
  ID : <input type="text" name="userId"/><br/>  
  PWD : <input type="password" name="userPwd"/></br/>  
  NAME : <input type="text" name="userName"/></br/>  
  
 <input type="submit" value="로그인"/>  
</form>
```
다음과 같이 요청하게 되면 GET방식과는 달리 질의 문자열이 보이지 않으며, POST방식은 질의 문자열이 요청정보의 몸체에 포함되기 때문에 클라이언트의 요청이 노출되지 않는 경우에 사용하거나, 데이터 크기의 제한을 원하지 않을 때 이 방식을 사용합니다.

POST 방식의 요청방식은 다음과 같은 특성이 있습니다.
1. 전달되는 질의 문자열이 요청정보의 몸체에 포함되어 전달됩니다.
2. 전달되는 질의 문자열이 외부에 노출되지 않습니다.
3. 전달되는 질의 문자열의 길이에 제한이 없습니다.
4. form태그를 사용해야만 요청할 수 있습니다.

<h3 name="question">질의 문자열 처리</h3>
GET, POST방식에 정답은 없습니다. 그렇기 때문에 자신이 사용하는 환경에 더 적합한 것을 사용하면 되며, 두 방식으로 전달되는 것을 처리하는 방법에 대해 소개합니다.

먼저 예제와 함께 다음을 살펴봅니다.
```java
@Override  
protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {  
      String id = req.getParameter("id");  
	  String password = req.getParameter("pwd");  
	  String name = req.getParameter("name");  
	  String [] hobbies = req.getParameterValues("hobby");  
	  String gender = req.getParameter("gender");  
	  String religion = req.getParameter("religion");  
	  String intro = req.getParameter("introduction");  
}
```
클래스 안에 있는 doGet메소드 입니다. req.getParameter으로 요청사항을 받으며, 폼으로 부터 연결된 value를 통해 값을 불러와 변수로 활용합니다.

여기서 살펴볼 것은 여러 가지가 선택될 수 있는 **checkBox**의 경우에는 값을 여러개 받을 수 있기 때문에 getParameterValues라는 메소드를 사용한다는 점과 받을 변수가 배열로 선언된다는 점입니다.

만약, GET형식이 아닌 POST형식으로 받게 된다면 무엇을 바꾸면 될까요?
바로, doGet에서 doPost으로 바꾸면 됩니다. 또한 입력하는 대상인 form에서도 method를 POST형식으로 바꾸어주어야 합니다.
