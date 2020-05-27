# 一、servlet

## 1、Servlet简介

### 1）、Servlet简单介绍

1、Servlet （Server Applet） 服务器端小程序

2、狭义：javax.servlet.Servlet接口及其子接口

​	  广义：指实现了Servlet接口的实现类

3、对象由`Servlet容器`创建



### 2）、Servlet使用

使用一个接口的传统方式

- 创建一个类实现接口
- new 实现类对象
- 调用类的方法等



使用`Servlet接口`的方式

- 创建一个类实现Servlet接口

```java
package com.servlet;

import java.io.IOException;

import javax.servlet.Servlet;
import javax.servlet.ServletConfig;
import javax.servlet.ServletException;
import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;

public class HelloServlet implements Servlet {

	@Override
	public void destroy() {
		// TODO Auto-generated method stub
		
	}

	@Override
	public ServletConfig getServletConfig() {
		// TODO Auto-generated method stub
		return null;
	}

	@Override
	public String getServletInfo() {
		// TODO Auto-generated method stub
		return null;
	}

	@Override
	public void init(ServletConfig config) throws ServletException {
		// TODO Auto-generated method stub
		
	}
	
	/**
	 * 处理请求
	 */
	@Override
	public void service(ServletRequest req, ServletResponse res) throws ServletException, IOException {
		// TODO Auto-generated method stub
		System.out.println("service方法是处理请求的方法");
		
	}

}
```



- 在web.xml中`注册`这个实现类

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://java.sun.com/xml/ns/javaee" xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd" version="2.5">
  <display-name>ServletTest</display-name>
  <welcome-file-list>
    <welcome-file>index.html</welcome-file>
    <welcome-file>index.htm</welcome-file>
    <welcome-file>index.jsp</welcome-file>
    <welcome-file>default.html</welcome-file>
    <welcome-file>default.htm</welcome-file>
    <welcome-file>default.jsp</welcome-file>
  </welcome-file-list>
  
  <!-- 
  	注册Servlet
  		1、加载类的全路劲
  		2、url
   -->
   <servlet>
   		<servlet-name>HelloServlet</servlet-name>
   		<servlet-class>com.servlet.HelloServlet</servlet-class>
   </servlet>
   
   <!-- url-pattern里面必须加/，只有加/才表示路径 -->
   <servlet-mapping>
   		<servlet-name>HelloServlet</servlet-name>
   		<url-pattern>/HelloServlet</url-pattern>
   </servlet-mapping>
</web-app>
```



- Tomcat（Servlet容器）会创建实现类的对象



界面请求index.xml，因为Servlet默认是在webapp目录下，而index.html也在webapp目录下，所以直接写成<form action="HelloServlet" method="post">

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	<form action="HelloServlet" method="post">
		<table>
			<tr>
				<td>用户名：</td>
				<td><input type="text" name="username" id="username"></td>
			</tr>
			<tr>
				<td>用户名：</td>
				<td><input type="password" name="password" id="password"></td>
			</tr>
			<tr>
				<td>
					<td><input type="submit" value="登录"></td>
				</td>
			</tr>
		</table>
	</form>
</body>
</html>
```

结果

```
service方法是处理请求的方法
```



### 3）、Servlet的执行流程（工作原理）

- 请求
- web.xml中的url-pattern
- servlet-name
- servlet-class,找到执行的Servlet
- 默认执行service(),处理请求,做出响应



## 2、Servlet生命周期

```java
package com.servlet;

import java.io.IOException;

import javax.servlet.Servlet;
import javax.servlet.ServletConfig;
import javax.servlet.ServletException;
import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;

public class HelloServlet implements Servlet {
	
	public HelloServlet() {
		System.out.println("HelloServlet对象创建");
	}
	
	/**
	 * Servlet消亡时运行
	 */
	@Override
	public void destroy() {
		// TODO Auto-generated method stub
		System.out.println("HelloServlet destroy()");
		
	}

	@Override
	public ServletConfig getServletConfig() {
		// TODO Auto-generated method stub
		return null;
	}

	@Override
	public String getServletInfo() {
		// TODO Auto-generated method stub
		return null;
	}
	
	/**
	 * 创建对象后执行init
	 */
	@Override
	public void init(ServletConfig config) throws ServletException {
		// TODO Auto-generated method stub
		System.out.println("HelloServlet初始化");
	}
	
	/**
	 * 处理请求
	 */
	@Override
	public void service(ServletRequest req, ServletResponse res) throws ServletException, IOException {
		// TODO Auto-generated method stub
		System.out.println("service方法是处理请求的方法");
		
	}

}
```

启动tomcat，发现无Servlet实例创建

![image-20200509211606485](D:\notes\notes\typora\java\javaweb\images\image-20200509211606485.png)

第一次调用，发现走了构造器-->init()-->service()

<img src="D:\notes\notes\typora\java\javaweb\images\image-20200509211705747.png" alt="image-20200509211705747" style="zoom:80%;" />

第二次调用，发现仅仅调用了service(),由此可以看出Servlet是单实例的

![image-20200509211809629](D:\notes\notes\typora\java\javaweb\images\image-20200509211809629.png)

关闭tomcat服务器，发现调用了destroy()

![image-20200509212209096](D:\notes\notes\typora\java\javaweb\images\image-20200509212209096.png)

从Servlet的创建到消亡的过程

- 构造器
  - 执行次数，在整个生命周期中执行一次(Servlet是单例的)
  - 执行时机，第一次接受请求时执行
- init()
  - 执行次数，在整个生命周期中执行一次
  - 执行时机，第一次接受请求时执行
- service()
  - 执行次数，在整个生命周期中执行多次
  - 执行时机，每次接受请求时执行
- destroy()
  - 执行次数，在整个生命周期中执行一次
  - 执行时机，关闭服务器时执行





## 3、ServletConfig和ServletContext

ServletConfig

- 定义：代表了Servlet的配置对象
- 作用：
  - 获取Servlet初始化参数(作用域：当前Servlet)
  - 获取ServletContext对象
  - 获取Servlet名(作用域：当前Servlet)

配置Servlet初始化参数,在web.xml的servlet标签下增加init-param

```xml
<servlet>
   		<servlet-name>HelloServlet</servlet-name>
   		<servlet-class>com.servlet.HelloServlet</servlet-class>
   		<!-- 配置Servlet初始化参数 -->
   		<init-param>
   			<param-name>encode</param-name>
   			<param-value>utf-8</param-value>
   		</init-param>
   </servlet>
```

在init()中给ServletConfig赋值，在service()中执行操作

```java
package com.servlet;

import java.io.IOException;

import javax.servlet.Servlet;
import javax.servlet.ServletConfig;
import javax.servlet.ServletContext;
import javax.servlet.ServletException;
import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;

public class HelloServlet implements Servlet {
	
	private ServletConfig config ; 
	
	public HelloServlet() {
		System.out.println("HelloServlet对象创建");
	}
	
	/**
	 * Servlet消亡时运行
	 */
	@Override
	public void destroy() {
		// TODO Auto-generated method stub
		System.out.println("HelloServlet destroy()");
		
	}

	@Override
	public ServletConfig getServletConfig() {
		// TODO Auto-generated method stub
		return null;
	}

	@Override
	public String getServletInfo() {
		// TODO Auto-generated method stub
		return null;
	}
	
	/**
	 * 创建对象后执行init
	 */
	@Override
	public void init(ServletConfig config) throws ServletException {
		// TODO Auto-generated method stub
		System.out.println("HelloServlet初始化");
		this.config = config ; 
	}
	
	/**
	 * 处理请求
	 */
	@Override
	public void service(ServletRequest req, ServletResponse res) throws ServletException, IOException {
		// TODO Auto-generated method stub
		System.out.println("service方法是处理请求的方法");
		
		String initParameter = config.getInitParameter("encode");
		System.out.println("initParameter:"+initParameter);
		
		ServletContext context = config.getServletContext();
		System.out.println("ServletContext:"+context);
		
		String name = config.getServletName();
		System.out.println("ServletName:"+name);
		
		
	}

}
```

结果

![image-20200509214720214](D:\notes\notes\typora\java\javaweb\images\image-20200509214720214.png)



ServletContext

- 定义：代表了Servlet的上下文对象
- 作用：
  - 获取初始化参数(当前Servlet上下文的)
  - 获取真实路径
  - 域对象



配置全局初始化参数context-param

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://java.sun.com/xml/ns/javaee" xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd" version="2.5">
  <display-name>ServletTest</display-name>
  <welcome-file-list>
    <welcome-file>index.html</welcome-file>
    <welcome-file>index.htm</welcome-file>
    <welcome-file>index.jsp</welcome-file>
    <welcome-file>default.html</welcome-file>
    <welcome-file>default.htm</welcome-file>
    <welcome-file>default.jsp</welcome-file>
  </welcome-file-list>
  
  <!-- 配置全局初始化参数 -->
  <context-param>
  		<param-name>age</param-name>
  		<param-value>18</param-value>
  </context-param>
  
  <!-- 
  	注册Servlet
  		1、加载类的全路劲
  		2、url
   -->
   <servlet>
   		<servlet-name>HelloServlet</servlet-name>
   		<servlet-class>com.servlet.HelloServlet</servlet-class>
   		<!-- 配置Servlet初始化参数,作用域当前Servlet -->
   		<init-param>
   			<param-name>encode</param-name>
   			<param-value>utf-8</param-value>
   		</init-param>
   </servlet>
   
   <!-- url-pattern里面必须加/，只有加/才表示路径 -->
   <servlet-mapping>
   		<servlet-name>HelloServlet</servlet-name>
   		<url-pattern>/HelloServlet</url-pattern>
   </servlet-mapping>
</web-app>
```

service()

```java
@Override
	public void service(ServletRequest req, ServletResponse res) throws ServletException, IOException {
		// TODO Auto-generated method stub
		System.out.println("service方法是处理请求的方法");
		
		ServletContext context = config.getServletContext();
		String initParameter = context.getInitParameter("age");
		String initParameter3 = context.getInitParameter("encode");
		
		String initParameter2 = config.getInitParameter("age");
		
		System.out.println("ServletContext获取初始化参数:"+initParameter);
		System.out.println("ServletContext获取Servlet初始化参数:"+initParameter3);
		System.out.println("ServletConfig获取初始化参数:"+initParameter2);
		
		String realPath = context.getRealPath("index.html");
		System.out.println("index.html的真实路径:"+realPath);
		
		
	}
```

`结果`

​		ServletConfig无法获取context-param全局初始化参数,ServletContext无法获取当前Servlet的初始化参数

```java
HelloServlet对象创建
HelloServlet初始化
service方法是处理请求的方法
ServletContext获取初始化参数:18
ServletContext获取Servlet初始化参数:null
ServletConfig获取初始化参数:null
index.html的真实路径:D:\eclipse_project\.metadata\.plugins\org.eclipse.wst.server.core\tmp0\wtpwebapps\ServletTest\index.html
```

## 4、Servlet接口扩展

因为直接实现Servlet接口会出现很多我们并不关注的方法，像init(),destroy()等，我们只需要处理请求的方法service()，所以真正的创建servlet的方法是`继承HttpServlet`

- MyServlet-->HttpServlet-->GenericServlet-->Servlet

  - HttpServlet

    - Override service()

      - 将ServletRequest,ServletResponse强转为HttpServletRequest,HttpServletResponse

      ```java
      @Override
          public void service(ServletRequest req, ServletResponse res)
              throws ServletException, IOException
          {
              HttpServletRequest  request;
              HttpServletResponse response;
              
              if (!(req instanceof HttpServletRequest &&
                      res instanceof HttpServletResponse)) {
                  throw new ServletException("non-HTTP request or response");
              }
      
              request = (HttpServletRequest) req;
              response = (HttpServletResponse) res;
      
              service(request, response);
          }
      ```

    - Overload service()

      - 根据请求类型执行doGet(),doPost()等方法

      ```java
      protected void service(HttpServletRequest req, HttpServletResponse resp)
              throws ServletException, IOException
          {
              String method = req.getMethod();
      
              if (method.equals(METHOD_GET)) {
                  long lastModified = getLastModified(req);
                  if (lastModified == -1) {
                      // servlet doesn't support if-modified-since, no reason
                      // to go through further expensive logic
                      doGet(req, resp);
                  } else {
                      long ifModifiedSince = req.getDateHeader(HEADER_IFMODSINCE);
                      if (ifModifiedSince < lastModified) {
                          // If the servlet mod time is later, call doGet()
                          // Round down to the nearest second for a proper compare
                          // A ifModifiedSince of -1 will always be less
                          maybeSetLastModified(resp, lastModified);
                          doGet(req, resp);
                      } else {
                          resp.setStatus(HttpServletResponse.SC_NOT_MODIFIED);
                      }
                  }
      
              } else if (method.equals(METHOD_HEAD)) {
                  long lastModified = getLastModified(req);
                  maybeSetLastModified(resp, lastModified);
                  doHead(req, resp);
      
              } else if (method.equals(METHOD_POST)) {
                  doPost(req, resp);
                  
              } else if (method.equals(METHOD_PUT)) {
                  doPut(req, resp);
                  
              } else if (method.equals(METHOD_DELETE)) {
                  doDelete(req, resp);
                  
              } else if (method.equals(METHOD_OPTIONS)) {
                  doOptions(req,resp);
                  
              } else if (method.equals(METHOD_TRACE)) {
                  doTrace(req,resp);
                  
              } else {
                  //
                  // Note that this means NO servlet supports whatever
                  // method was requested, anywhere on this server.
                  //
      
                  String errMsg = lStrings.getString("http.method_not_implemented");
                  Object[] errArgs = new Object[1];
                  errArgs[0] = method;
                  errMsg = MessageFormat.format(errMsg, errArgs);
                  
                  resp.sendError(HttpServletResponse.SC_NOT_IMPLEMENTED, errMsg);
              }
      ```

      

  - GenericServlet

    - getServletConfig()    getServletContext()
    - abstract service()   抽象化的方法在子类中必须重写

```java
package com.servlet;

import java.io.IOException;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * Servlet implementation class MyServlet
 */
public class MyServlet extends HttpServlet {
	private static final long serialVersionUID = 1L;
       
    /**
     * @see HttpServlet#HttpServlet()
     */
    public MyServlet() {
        super();
        // TODO Auto-generated constructor stub
    }

	/**
	 * @see HttpServlet#doGet(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
		response.getWriter().append("Served at: ").append(request.getContextPath());
	}

	/**
	 * @see HttpServlet#doPost(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
		doGet(request, response);
	}

}
```



## 5、请求与响应

请求与响应：

- 请求：客户端向服务器

  - 类型：HttpServletRequest
  - 定义：代表客户端向服务器端发送的请求报文，该对象由(web容器|Servlet容器)创建，同时将该对象传给service()方法
  - 作用：
    - 获取请求参数
      - request.getParameter(name)
      - request.getParameterNames()
    - 获取项目的虚拟路径
      - request.getContextPath()
    - 转发(路径<页面/Servlet>跳转)
      - 获取转发器
        - RequestDispatcher requestDispatcher = request.getRequestDispatcher(path);
      - 执行转发
        - requestDispatcher.forward(request, response)
    - 域对象

  ```java
  protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
  		// TODO Auto-generated method stub
  		System.out.println("doGet()");
  		
  		//测试request对象
  		
  		//1、通过name获取请求参数
  		String userName = request.getParameter("username");
  		System.out.println("username:"+userName);
  		
  		//2、获取项目的虚拟路径
  		String contextPath = request.getContextPath();
  		System.out.println("contextPath:"+contextPath);
  		
  		//3、转发
  			//1、获取转发器
  			RequestDispatcher requestDispatcher = request.getRequestDispatcher("login_success.html");
  			//2、执行转发
  			requestDispatcher.forward(request, response);
  	}
  ```

  

- 响应：服务器向客户端

  - 类型：HttpServletResponse

  - 定义：代表服务器向客户端发送的响应报文，该对象由(web容器|Servlet容器)创建，同时将该对象传给service()方法

  - 作用：

    - 服务器向客户端做出响应(文本/html)
      - 获取响应流
        - PrintWriter writer = response.getWriter();
      - 响应
        - writer.write("login success")

    - 重定向(路径跳转)
      - response.sendRedirect("login_success.html")

  ```java
  protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
  		// TODO Auto-generated method stub
  		System.out.println("doPost()");
  		
  		//服务器向客户端做出响应(文本/html)
  			//1、获取响应流
  			PrintWriter writer = response.getWriter();
  			//2、响应
  			writer.write("login success");
      
      	//重定向
  		response.sendRedirect("login_success.html");
  	}
  ```

## 6、转发与重定向

转发与重定向的区别：

1. 转发地址栏不变，重定向地址栏改变
2. 转发客户端发送一次请求，重定向客户端发送两次请求
3. 转发可以访问WEB-INF下的资源，重定向不能
   
   1. 因为WEB-INF目录属于web应用的私有目录(客户端无法直接访问)
4. 转发可以携带request对象，重定向不行

1. 表象

   1. 转发之后地址在servlet上

   ![image-20200511134519621](D:\notes\notes\typora\java\javaweb\images\image-20200511134519621.png)

      2.重定向的地址在html

   ![image-20200511134621061](D:\notes\notes\typora\java\javaweb\images\image-20200511134621061.png)

   2.实际

   1. 转发其实只做了一次请求(请求服务器的servlet，html结果是servlet返回的)，所以转发后的url是在servlet上
   2. 重定向是客户端做了两次请求
      1. 客户端请求服务器，服务器将资源地址html返回给客户端
      2. 客户端根据html的地址，访问资源(所以请求地址为html )

![image-20200511134336095](D:\notes\notes\typora\java\javaweb\images\image-20200511134336095.png)

## 7、servlet练习

伪登录/伪注册

LoginServlet

```java
package com.servlet;

import java.io.IOException;

import javax.servlet.RequestDispatcher;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * Servlet implementation class LoginServlet
 */
public class LoginServlet extends HttpServlet {
	private static final long serialVersionUID = 1L;
       
    /**
     * @see HttpServlet#HttpServlet()
     */
    public LoginServlet() {
        super();
        // TODO Auto-generated constructor stub
    }

	/**
	 * @see HttpServlet#doGet(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
		System.out.println("doGet()");
		//1、获取用户名密码
		String username = request.getParameter("username");
		String pwd = request.getParameter("password");
		if("admin".equals(username) && "admin".equals(pwd)) {
			//登陆成功进入成功界面
			RequestDispatcher requestDispatcher = request.getRequestDispatcher("login_success.html");
			requestDispatcher.forward(request, response);
		}else {
			response.sendRedirect("login.html");
		}
	}

	/**
	 * @see HttpServlet#doPost(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
		doGet(request, response);
	}

}
```

RegistServlet

```java
package com.servlet;

import java.io.IOException;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * Servlet implementation class RegistServlet
 */
public class RegistServlet extends HttpServlet {
	private static final long serialVersionUID = 1L;
       
    /**
     * @see HttpServlet#HttpServlet()
     */
    public RegistServlet() {
        super();
        // TODO Auto-generated constructor stub
    }

	/**
	 * @see HttpServlet#doGet(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
		System.out.println("RegisterServlet doGet()");
		//1、获取用户名
		String username = request.getParameter("username");
		if("admin".equals(username)) {
			//用户名已存在
			response.sendRedirect("register.html");
		}else {
			request.getRequestDispatcher("regist_success.html").forward(request, response);
		}
	}

	/**
	 * @see HttpServlet#doPost(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
		doGet(request, response);
	}

}
```

web.xml

```java
<servlet>
    <description></description>
    <display-name>LoginServlet</display-name>
    <servlet-name>LoginServlet</servlet-name>
    <servlet-class>com.servlet.LoginServlet</servlet-class>
  </servlet>
  <servlet-mapping>
    <servlet-name>LoginServlet</servlet-name>
    <url-pattern>/LoginServlet</url-pattern>
  </servlet-mapping>
  <servlet>
    <description></description>
    <display-name>RegistServlet</display-name>
    <servlet-name>RegistServlet</servlet-name>
    <servlet-class>com.servlet.RegistServlet</servlet-class>
  </servlet>
  <servlet-mapping>
    <servlet-name>RegistServlet</servlet-name>
    <url-pattern>/RegistServlet</url-pattern>
  </servlet-mapping>
```



### 1、web应用中的路径问题

1. 在web应用中，由于使用转发跳转路径时，地址栏不变，此时使用相对路(../)径存在安全隐患,所以使用绝对路径，解决web应用中的路径问题

1. 什么是绝对路径，以"/"开头的路径，称之为绝对路径
   1. “/”代表意思
      1. 由服务器解析，代表着当前项目路径,http://localhost:8080/ServletTest
         1. 以下两种情况由服务器解析
            1. web.xml中的url
            2. 转发
      2. 由浏览器解析，代表当前服务器路径，http://localhost:8080
         1. 以下两种情况由浏览器解析
            1. 重定向
            2. html文件中的路径问题 eg:  src:script|img  href:link|a  action:form 



各文件所在位置

![image-20200512220521299](D:\notes\notes\typora\java\javaweb\images\image-20200512220521299.png)

index.html

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>

	<a href="pages/login.html">我要登录</a>
	<a href="pages/register.html">我要注册</a>
</body>
</html>
```

通过浏览器访问index.html

![image-20200512220804479](D:\notes\notes\typora\java\javaweb\images\image-20200512220804479.png)

点击我要登录,进入到LoginServlet,登陆成功就转发到登陆成功界面，失败就重定向到login.html

```java
package com.servlet;

import java.io.IOException;

import javax.servlet.RequestDispatcher;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * Servlet implementation class LoginServlet
 */
public class LoginServlet extends HttpServlet {
	private static final long serialVersionUID = 1L;
       
    /**
     * @see HttpServlet#HttpServlet()
     */
    public LoginServlet() {
        super();
        // TODO Auto-generated constructor stub
    }

	/**
	 * @see HttpServlet#doGet(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
		System.out.println("doGet()");
		
		request.setCharacterEncoding("UTF-8");
		
		//1、获取用户名密码
		String username = request.getParameter("username");
		String pwd = request.getParameter("password");
		if("admin".equals(username) && "admin".equals(pwd)) {
			//登陆成功进入成功界面
			RequestDispatcher requestDispatcher = request.getRequestDispatcher("pages/login_success.html");
			requestDispatcher.forward(request, response);
		}else {
			String contextPath = request.getContextPath();
			response.sendRedirect(contextPath+"/pages/login.html");
		}
	}

	/**
	 * @see HttpServlet#doPost(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
		doGet(request, response);
	}

}
```

login.html

因为Servlet的路径是在webapp路径下，而login.html在pages下，所以得用../LoginServlet

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	<h1>GET登录</h1>
	<form action="../LoginServlet" method="get">
		<table>
			<tr>
				<td>用户名：</td>
				<td><input type="text" name="username" id="username"></td>
			</tr>
			<tr>
				<td>用户名：</td>
				<td><input type="password" name="password" id="password"></td>
			</tr>
			<tr>
				<td>
					<td><input type="submit" value="登录"></td>
				</td>
			</tr>
		</table>
	</form>
	<br><br><br>
	<h1>POST登录</h1>
	<form action="LoginServlet" method="post">
		<table>
			<tr>
				<td>用户名：</td>
				<td><input type="text" name="username" id="username"></td>
			</tr>
			<tr>
				<td>用户名：</td>
				<td><input type="password" name="password" id="password"></td>
			</tr>
			<tr>
				<td>
					<td><input type="submit" value="登录"></td>
				</td>
			</tr>
		</table>
	</form>
</body>
</html>
```

登陆成功到login_success.html,因为是转发，所以路径还在Servlet上

![image-20200512221254295](D:\notes\notes\typora\java\javaweb\images\image-20200512221254295.png)

login_success.html

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	<h1>登陆成功</h1>
	<h2><a href="index.html">返回</a></h2>
</body>
</html>
```

此时点击返回可以返回到index.html



但是，直接进入到login_success.html，点击返回则会到404，因为找到pages下了

![image-20200512221515406](D:\notes\notes\typora\java\javaweb\images\image-20200512221515406.png)

![image-20200512221545596](D:\notes\notes\typora\java\javaweb\images\image-20200512221545596.png)



现在修改login_success.html,直接进入到login_success.html可以直接返回到index.html

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	<h1>登陆成功</h1>
	<h2><a href="../index.html">返回</a></h2>
</body>
</html>
```

但是经过登陆成功进入成功界面却又不能返回了，直接找到以下地址了，所以现在login_success.html的路径是改也不对，不改也不对，所以对于web的路径问题，我们最好都用绝对路径

![image-20200512221900187](D:\notes\notes\typora\java\javaweb\images\image-20200512221900187.png)



1、将index.html改为绝对路径，因为浏览器的绝对路径是相对于localhost:8080的，所以得在路径后面加上项目名，又因为绝对路径是以/开头，所以把路径改为以下形式

index.html

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>

	<a href="/ServletTest/pages/login.html">我要登录</a>
	<a href="/Servlet/pages/register.html">我要注册</a>
</body>
</html>
```

login.html

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	<h1>GET登录</h1>
	<form action="/ServletTest/LoginServlet" method="get">
		<table>
			<tr>
				<td>用户名：</td>
				<td><input type="text" name="username" id="username"></td>
			</tr>
			<tr>
				<td>用户名：</td>
				<td><input type="password" name="password" id="password"></td>
			</tr>
			<tr>
				<td>
					<td><input type="submit" value="登录"></td>
				</td>
			</tr>
		</table>
	</form>
	<br><br><br>
	<h1>POST登录</h1>
	<form action="LoginServlet" method="post">
		<table>
			<tr>
				<td>用户名：</td>
				<td><input type="text" name="username" id="username"></td>
			</tr>
			<tr>
				<td>用户名：</td>
				<td><input type="password" name="password" id="password"></td>
			</tr>
			<tr>
				<td>
					<td><input type="submit" value="登录"></td>
				</td>
			</tr>
		</table>
	</form>
</body>
</html>
```

login_success.html

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	<h1>登陆成功</h1>
	<h2><a href="/ServletTest/index.html">返回</a></h2>
</body>
</html>
```

LoginServlet.java

```java
protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
		System.out.println("doGet()");
		
		request.setCharacterEncoding("UTF-8");
		
		//1、获取用户名密码
		String username = request.getParameter("username");
		String pwd = request.getParameter("password");
		if("admin".equals(username) && "admin".equals(pwd)) {
			//登陆成功进入成功界面
			RequestDispatcher requestDispatcher = request.getRequestDispatcher("/pages/login_success.html");
			requestDispatcher.forward(request, response);
		}else {
			String contextPath = request.getContextPath();
			response.sendRedirect(contextPath+"/pages/login.html");
		}
	}
```

之后不管怎样都是能找到界面



## 8、servlet乱码问题

乱码：编码与解码不一致时，出现乱码问题

编码：将字符转换为二进制的过程

解码：将二进制转化为字符



乱码分为请求乱码和响应乱码

请求乱码：客户端编码，服务器解码

响应乱码：服务器编码，客户端解码



默认客户端与服务器端的编码解码情况

1. 服务器编码与解码默认一致：ISO-8859-1(不支持中文)
2. 客户端(浏览器编码默认为:<meta charset="UTF-8">  解码默认为：GBK



解决web中的乱码问题

1. 请求乱码

   1. POST请求

      1. request.setCharacterEncoding("UTF-8");

   2. GET请求

      1. 在当前服务器的server.xml中的URIEncoding(当前服务器的server.xml并不是磁盘中tomcat安装目录下的server.xml)

         1. 通过项目中的server找到server.xml

         ![image-20200512140212600](D:\notes\notes\typora\java\javaweb\images\image-20200512140212600.png)

         可以看到当前server.xml的位置并不是安装路径的server.xml

         ![image-20200512140326225](D:\notes\notes\typora\java\javaweb\images\image-20200512140326225.png)

         在8080端口这一行加入URIEncoding="utf-8"(utf-8大小写都行）

         ```xml
         <Connector connectionTimeout="20000" port="8080" protocol="HTTP/1.1" redirectPort="8443" URIEncoding="UTF-8"/>
         ```

         

         2.响应乱码

         1. 直接将服务器编码给我GBK(不建议)
            1. response.setCharacterEncoding("GBK");  
         2. 将服务器编码和浏览器解码统一改为UTF-8 
            1. response.setContentType("text/html;charset=UTF-8");




# 二、Cookie

## 1、简介

1. Cookie实际上就是服务器保存在浏览器上的一段信息

## 2、Cookie运行原理

1. 请求
2. 服务器创建一个Cookie对象，该Cookie对象携带用户信息，服务器发送(响应)给客户端
3. 以后客户端再发送请求时，会携带该Cookie对象
4. 服务器会根据该Cookie对象(及信息)，`区分不同用户`

## 3、Cookie的创建与获取与修改

CookieDemo.jsp

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	<h1>cookiedemo</h1>
	<a href="/ServletTest/CreateCookieServlet">创建cookie对象</a>
	<a href="/ServletTest/GetCookieServlet">获取cookie对象</a>
</body>
</html>
```

CreateCookieServlet

```java
protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		//1、创建Cookie对象，并携带用户信息
		Cookie cookie = new Cookie("stuName","zhangsan");
		
		//响应给客户端
		response.addCookie(cookie);
	}
```

Cookie的获取

GetCookieServlet.java

```java
protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		Cookie[] cookies = request.getCookies();
		PrintWriter writer = response.getWriter();
		StringBuffer sb = new StringBuffer();
		for(Cookie cookie : cookies) {
			String cookieName = cookie.getName();
			String cookieValue = cookie.getValue();
			sb.append(cookieName+":"+cookieValue+"\r\n");
		}
		writer.write(sb.toString());
	}
```

Cookie在浏览器上的展示

![image-20200513132244153](D:\notes\notes\typora\java\javaweb\images\image-20200513132244153.png)

1. 创建

   1. Cookie cookie = new Cookie(String name,String value);
   2. response.addCookie(cookie);

2. 获取

   1. Cookie[] cookies = request.getCookies();
   2. cookie.getName()|getValue()

3. 修改

   1. 覆盖式修改

      1. Cookie cookie = new Cookie("stuName","lisi");
      2. response.addCookie(cookie);

   2. 直接式修改

      1. ```java
         Cookie[] cookies = request.getCookies();
         		for(Cookie cookie2 :cookies) {
         			if("stuName".equals(cookie2.getName())) {
         				cookie2.setValue("wangwu");
         				response.addCookie(cookie2);
         			}
         		}
         ```

```java
protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		//1、覆盖式修改
		
		//1、重新创建Cookie对象
		//2、将cookie响应给客户端
		Cookie cookie = new Cookie("stuName","lisi");
		response.addCookie(cookie);
		
		//直接式修改
		Cookie[] cookies = request.getCookies();
		for(Cookie cookie2 :cookies) {
			if("stuName".equals(cookie2.getName())) {
				cookie2.setValue("wangwu");
				response.addCookie(cookie2);
			}
		}
	}
```

![image-20200513134107813](D:\notes\notes\typora\java\javaweb\images\image-20200513134107813.png)

## 4、Cookie的键值问题

- name不可以为中文，value可以为中文，需要指定字符集问题，所以建议使用英文



## 5、Cookie的有效性问题

- 默认为会话级别(与浏览器有关，关闭或更换浏览器失效)
- 持久化
  - 创建Cookie对象
  - 设置Cookie最大存活时间
  - 将Cookie响应给客户端

```java
protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
		//持久化
		
		/* 
		 * 1、创建Cookie对象
		 * 2、设置Cookie最大存活时间
		 * 3、将Cookie响应给客户端
		 *  */
		Cookie cookie = new Cookie("age","12");
		cookie.setMaxAge(30);
		response.addCookie(cookie);
	}
```

- setMaxAge(ss:秒)
  - ss>0 :在ss秒后失效
  - ss=0 :立即失效
  - ss<0 :回到默认会话级别



## 6、Cookie的有效路径

cookie默认有效路径为项目下路径，可以通过setPath修改

- cookie.setPath(request.getContextPath()+"/aa");  表示在当前项目下的aa路径下才有效

```java
protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
		//持久化
		
		/* 
		 * 1、创建Cookie对象
		 * 2、设置Cookie最大存活时间
		 * 3、将Cookie响应给客户端
		 *  */
		Cookie cookie = new Cookie("age","12");
		
		//表示只有在当前项目下的aa路径才能有效
		cookie.setPath(request.getContextPath()+"/aa");
		response.addCookie(cookie);
	}
```

第一次直接访问项目下的路径时，age并不会加到cookie上

![image-20200515130852181](D:\notes\notes\typora\java\javaweb\images\image-20200515130852181.png)

至于访问项目下的aa路径，才会把cookie加到客户端

![image-20200515131004138](D:\notes\notes\typora\java\javaweb\images\image-20200515131004138.png)

## 7、cookie应用(记住密码)

login.jsp

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	<h1>GET登录</h1>
	<form action="/ServletTest/LoginServlet" method="get">
		<table>
			<tr>
				<td>用户名：</td>
				<td><input value="${cookie.username.value}" type="text" name="username" id="username"></td>
			</tr>
			<tr>
				<td>用户名：</td>
				<td><input value="${cookie.pwd.value}" type="password" name="password" id="password"></td>
			</tr>
			<tr>
				<td>记住密码：</td>
				<td><input type="checkbox" name="remenberPwd" id=""remenberPwd""></td>
			</tr>
			<tr>
				<td>
					<td><input type="submit" value="登录"></td>
				</td>
			</tr>
		</table>
	</form>
	<br><br><br>
	<h1>POST登录</h1>
	<form action="LoginServlet" method="post">
		<table>
			<tr>
				<td>用户名：</td>
				<td><input type="text" name="username" id="username"></td>
			</tr>
			<tr>
				<td>用户名：</td>
				<td><input type="password" name="password" id="password"></td>
			</tr>
			<tr>
				<td>
					<td><input type="submit" value="登录"></td>
				</td>
			</tr>
		</table>
	</form>
</body>
</html>
```

LoginServlet

```java
protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
		System.out.println("doGet()");
		
		request.setCharacterEncoding("UTF-8");
		
		//1、获取用户名密码
		String username = request.getParameter("username");
		String pwd = request.getParameter("password");
		String remenberPwd = request.getParameter("remenberPwd");
		
		if(remenberPwd != null) {
			//将数据存放到cookie中
			Cookie cookie = new Cookie("username",username);
			Cookie cookie1 = new Cookie("pwd",pwd);
			
			//将Cookie持久化
			cookie.setMaxAge(60*60*24*7);   //7天
			cookie1.setMaxAge(60*60*24*7);
			
			
			//将Cookie响应给客户端
			response.addCookie(cookie);
			response.addCookie(cookie1);
		}
		
		
		/*
		 * if("admin".equals(username) && "admin".equals(pwd)) { //登陆成功进入成功界面
		 * RequestDispatcher requestDispatcher =
		 * request.getRequestDispatcher("/pages/login_success.html");
		 * requestDispatcher.forward(request, response); }else { String contextPath =
		 * request.getContextPath();
		 * response.sendRedirect(contextPath+"/pages/login.html"); }
		 */
		response.setContentType("text/html;charset=UTF-8");
		response.sendRedirect(request.getContextPath()+"/pages/login.jsp");
		/*
		 * PrintWriter writer = response.getWriter(); writer.write("登陆成功login success");
		 */
	}
```

## 8、Cookie的缺陷

1、Cookie的value类型为String,不灵活

2、Cookie存放在浏览器中，不安全

3、Cookie过多，浪费内存流量

# 三、session

## 1、简介

- 简介
  - 类型HttpSession



## 2、工作原理

- Session工作原理
  - 请求
  - 服务器创建Session,同时创建一个特殊的Cookie,该Cookie的key为JSESSIONID,value为session的id
  - 服务器将该Cookie对象发送(响应)给客户端
  - 以后客户端再请求时，会携带该Cookie对象
  - 服务器会根据Cookie的value，找到相应的session，从而区分不同的用户

## 3、Session获取

- html（Servlet）:request.getSession()

  - ```jsp
    <%@ page language="java" contentType="text/html; charset=UTF-8"
        pageEncoding="UTF-8"%>
    <!DOCTYPE html>
    <html>
    <head>
    <meta charset="UTF-8">
    <title>Insert title here</title>
    </head>
    <body>
    	sessionid:<%=session.getId()%>
    	
    	<a href="/ServletTest/LoginServlet" >获取sessionid</a>
    </body>
    </html>
    ```

  - LoginServlet

  - ```java
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
    		// TODO Auto-generated method stub
    		System.out.println("doGet()");
    		
    		String sessionid = request.getSession().getId();
    		PrintWriter writer = response.getWriter();
    		writer.write("[LoginServlet]sessionid:"+sessionid);
    	}
    ```

  

- jsp:直接获取(session是jsp的隐含对象)

  - ```jsp
    <%@ page language="java" contentType="text/html; charset=UTF-8"
        pageEncoding="UTF-8"%>
    <!DOCTYPE html>
    <html>
    <head>
    <meta charset="UTF-8">
    <title>Insert title here</title>
    </head>
    <body>
    	sessionid:<%=session.getId()%>
    </body>
    </html>
    ```

  - ![image-20200515135952366](D:\notes\notes\typora\java\javaweb\images\image-20200515135952366.png)

  

## 4、Session的有效性

- 默认有效性：当前会话(因为特殊的Cookie是会话级别的)

- 持久化Session

  - Session的存活时间

    - 默认为30分钟(在当前服务器的web.xml中可以看到，可以在本地web.xml重新设置)

      - ```xml
          <session-config>
                //单位为分钟
                <session-timeout>30</session-timeout>
            </session-config>
        ```

    - session.setMaxInactiveInterval(30);  //单位为秒

      - ss > 0  在ss秒后失效
      - ss = 0 或者 ss<0  session永不失效（tomcat >=7）

    - session立即失效

      - session.invalidate();

  - 持久化特殊的Cookie

  ```java
  protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
  		// TODO Auto-generated method stub
  		/* 持久化Session 
  		 * 		1、获取Cookie
  		 * 		2、找到名字为JSESSIONID的Cookie值
  		 * 		3、设置该Cookie的超时时间
  		 * 		4、推送给客户端
  		 *
  		 * */
  		Cookie[] cookies = request.getCookies();
  		for(Cookie cookie :cookies) {
  			if("JSESSIONID".equals(cookie.getName())) {
  				cookie.setMaxAge(60);
  				response.addCookie(cookie);
  				break;
  			}
  		}
  		HttpSession session = request.getSession();
  		//单位为秒
  		session.setMaxInactiveInterval(30);
  	}
  ```

## 5、Session的钝化与活化

钝化：将session对象及其数据，一同从内存中序列化到硬盘的过程

- 时机:服务器关闭时触发

活化:将session及其数据从硬盘反序列化到内存的过程

- 时机:服务重启时触发

## 6、Session应用

### 1、表单重复提交



# 四、jsp

## 1、jsp简介

**其实就是运行在前端的java代码**

jsp运行过程

1. web服务器将jsp文件转化为*.java文件
2. 在将*.java转化为\*.class文件
3. 在web服务器中执行

## 2、Scriptlet

在jsp中，最重要的是Scriptlet(脚本小程序),所有嵌入在html中的java程序都必须用scriptlet标记出来，在java中一共有3中Scriptlet

- <%%>
- <%!%>
- <%=%>

1、<%%>可以定义`局部变量`、`编写语句`等