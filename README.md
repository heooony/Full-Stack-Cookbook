<h1>SERVLET</h1>
서블릿은 주로 <b>주로 웹 서버에서 동작</b>하는 애플리케이션을 구현할 때 사용합니다.
서블릿을 만드는 목적은 클라이언트에 서비스하기 위함이며, 서버에 서블릿이 준비되어 있어야 클라이언트가 서블릿에 접근할 수 있습니다.

<h3>목차</h3>

<a href="#servlet-start">servlet구현 및 실행</a><p>
<a href="#req-resp">응답 정보 처리</a><p>
<a href="#question">질의 문자열</a><p>
<a href="#download">파일 다운로드 기능</a><p>

<h2 name="servlet-start">servlet구현 및 실행</h2>
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

<h2>파일업로드</h2>
파일업로드에 필요한 컴포넌트
<a href="http://www.servlets.com/cos/">(라이브러리 다운로드)</a>를 가져옵니다.

다운로드한 라이브러리를 압축을 풀어 lib폴더에 있는
cos.jar를 파일을 이클립스 WEB-INf/lib폴더에 넣습니다.

<h3>파일업로드 폼</h3>

```html
<form method="post" action="Upload" enctype="multipart/form-data">  
	 이름 :<input type="text" name="name"/><p>  
	 제목: <input type="text" name="subject"/><p>  
	 파일첨부:<input type="file" name="file"/><p>  
	 <input type="submit" value="업로드하기"/>
</form>
```
method는 반드시 post 방식이어야 하며, <b>enctype="multipart/form-data"</b> 의 구문은 필수적으로 들어가야 합니다.

html에 다음과 같은 코드를 넣으셨다면, servlet에서도 마찬가지로 요청을 받아들일 준비를 해야합니다.
```java
String name = request.getParameter("name");  
String subject = request.getParameter("subject");  
String file = request.getParameter("file");  
  
System.out.println("name = " + name);  
System.out.println("subject = " + subject);  
System.out.println("file = " + file);
```

그러나, System.out.print로 인해 출력되는 것은 모두 null이며, 값들이 넘어오지 않게 됩니다. enctype="multipart/form-data는 파일을 서버측에 저장하기 위해 설정을 하는 것이며, 설정이 되었을 때에는 <b>request로 전송된 데이터를 받을 수 없게 됩니다.</b>


<h3>form request</h3>
form 형식으로부터 request를 받는 과정은 다음과 같습니다.

```java
MultipartRequest m = new MultipartRequest(request, 저장폴더, 최대용량, 한글인코딩, 같은이름파일처리방법 );
```

`m.getParameter("name");` //text박스의 값 가져오기
`m.getFilesystemName("file");` //file 박스의 첨부파일가져오기
`File f = m.getFile("file");` //첨부된 파일의 정보

그러면 다음과 같이 Servlet 파일안에 작성하여 값을 받아보도록 합니다.
```java
//WebContent폴더 혹은 Webapp폴더 안에 save라는 폴더가 존재해야 한다.
String saveDir = request.getServletContext().getRealPath("/save");  
int maxSize = 1024 * 1024 * 100;  
String encoding = "UTF-8";  
  
MultipartRequest m = new MultipartRequest(request, saveDir, maxSize, encoding, new DefaultFileRenamePolicy());  
  
//request.getParameter 으로 받는 것이 아닌
//m.getParameter 으로 받아야 한다. 여기서 m은 MultipartRequest의 객체이다.
String name = m.getParameter("name");  
String subject = m.getParameter("subject");  
String file = m.getParameter("file");  

String fileSystemName = m.getFilesystemName("file");  
String originalFileName = m.getOriginalFileName("file");  

System.out.println("name = " + name);  
System.out.println("subject = " + subject);
System.out.println("file = " + file);  
System.out.println("fileSystemName = " + fileSystemName);  
System.out.println("originalFileName = " + originalFileName);
```

```java
request.setAttribute("name", name);  
request.setAttribute("subject", subject);  
request.setAttribute("fileSystemName", fileSystemName);  
request.setAttribute("originalFileName", originalFileName);  
request.setAttribute("fileSize", m.getFile("file").length());  
request.setAttribute("saveDir", saveDir);  
  
//이동방식  
request.getRequestDispatcher("uploadResult.jsp").forward(request, response);
```
<h3 name="download">파일 다운로드 기능</h3>

1. HttpServlet 상속받는다.
2. 기능
- 업로드된 파일의 경로 알아오기
- 한글파일 인코딩 설정(8859_1  -> euc-kr 변환)
- 요청된 파일이름을 File객체 변환
- ContentType설정
	- 서버가 클라이언트쪽으로 보낼때 인코딩 설정 , MIMEType설정
	- 브라우져가 해석가능한 파일을 열지못하도록 설정
		- 한글인코딩( euc-kr  -> 8859_1 변환)
		- 헤더설정(헤더란 http에게 자신의 파일의 정체를 알리는것)
```java
response.setContentType("application/actet-stream");
response.setHeader("Content-Disposition", "attachment;filename=파일이름");
```
 
<h3>다운로드</h3>

```html
 <a href="/프로젝트명/서블릿클리스이름?fileName=파일이름">파일이름</a>
```

<h2>JDBC 연동하기</h2>
<h3>시작</h3>
JDBC는 java와 DB를 연동하는 기술로, oracle 설치 폴더에 있는 오라클 드라이버 라이브러리가 필요합니다. 라이브러리는 C:\oracle\jdbc\lib 폴더안에 있는 ojdbc14.jar 파일을 사용하고 있는 프로그램 WEB-INF/lib에 넣습니다.

필자는 IntelliJ를 사용하고 있어 프로젝트 설정에서 library탭 안에 추가를 합니다.

`import java.sql.*; `으로 sql 사용에 대한 준비를 시작합니다.

<h3>DB연동 순서</h3>

1. 로드 : 사용할 DB종류를 로드합니다. - Class.forName("연결하려는 드라이브명");

```css
	<연결하려는 드라이브명>
	Oracle :  oracle.jdbc.driver.OracleDriver
	Ms_Sql :  sun.jdbc.odbc.JdbcOdbcDriver
	My_Sql :  org.git.mm.mysql.Driver
	일반적으로 로드 부분은 생성자에서 구현합니다. (객체가 생성되는 시점에 한번만 로드 함)
```
2. 연결(db에 접속하는 부분)
```java
	Connection con = DrvierManager.getConnection(String URL , String id ,String pwd);

	URL부분
	Oracle : jdbc:oracle:thin:@localhost:1521:ORCL
	Ms_Sql : jdbc:odbc:odbc설정을통해만든db원본명
	My_Sql : jdbc:mysql://localhost:3306/db명
```
3. 실행 

```java
	Statement st = con.createStatement();

	//insert/update/ delete/ create인경우는
	//result의 값이  0이면 실패 , 1이상이면 성공.
	int result = st.executeUpdate(String sql);
	int result = st.executeUpdate("delete test where no=1"); 

	//select문장인 경우는
	ResultSet rs = st.executeQuery(String sql);       
	Resultset rs = st.executeQuery("select * from test");

	//ResultSet 메소드
	boolean b =rs.next(); //커서를 앞으로 이동
	String s =rs.getString(int 컬럼index); //컬럼에해당하는 문자열얻기
	int i =rs.getInt(int 컬럼index); //컬럼에해당하는 숫자값 얻기
	        
	while(rs.next()){
		String id = rs.getString(1);//첫번째컬럼
		int age =rs.getInt(2);//두번째컬럼
	}
```
4. 닫기(사용한 객체 닫기)
```java
	주체.close();
```

<h3>DB연동의 3단계</h3>
- DB연동 부분의 3번 실행 단계 PreparedStatement 이용하기
  
  1. 로드
  2. 연결
  3. 실행
     PreparedStatement ps = con.prepareStatement(String sql);
       * sql인수는 insert/update/delete/select/create문장 모든문장가능함.
       ex) insert into test values(?,?);
           select * from test where id=?;

      * sql문장에 ? 가 있는경우 ?의 순서대로 값 넣는다.
       ps.setType(int index, Type value);
       ex) ps.setString(1, "장희정");
           ps.setInt(2, 20);
           ....

      * ?에 해당하는 값을 다 넣은 후에 최종적으로 실행함.
        int i = ps.executeUpdate(); => insert,update, delete, create인경우
        ResultSet rs = ps.executeQuery();=> select인경우
