## 1 Tomcat工作机制

![img](https://img-blog.csdnimg.cn/2018120522281643.gif)

## 2 什么是Servlet

Servlet是用Java编写的服务器端程序。其主要功能在于交互式地浏览和修改数据，生成动态Web内容。狭义的Servlet是指Java语言实现的一个接口，广义的Servlet是指任何实现了这个Servlet接口的类，一般情况下，人们将Servlet理解为后者。

Servlet运行于支持Java的应用服务器中。从实现上讲，Servlet可以响应任何类型的请求，但绝大多数情况下Servlet只用来扩展基于HTTP协议的Web服务器。

## 3 Servlet的工作模式

- 客户端发送请求至服务器
- 服务器启动并调用Servlet，Servlet根据客户端请求生成响应内容并将其传给服务器
- 服务器将响应返回客户端

## 4 Servlet API概览

Servlet API 包含以下4个Java包：

1. javax.servlet   其中包含定义servlet和servlet容器之间契约的类和接口。

2. javax.servlet.http   其中包含定义HTTP Servlet 和Servlet容器之间的关系。
3. javax.servlet.annotation   其中包含标注servlet，Filter，Listener的标注。它还为被标注元件定义元数据。

4. javax.servlet.descriptor   其中包含提供程序化登录Web应用程序的配置信息的类型。

## 5 Servlet 的主要类型

![img](https://img-blog.csdn.net/20180512170931739?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzE5NzgyMDE5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

## 6 Servlet 的使用方法

Servlet技术的核心是Servlet，它是所有Servlet类必须直接或者间接实现的一个接口。在编写实现Servlet的Servlet类时，直接实现它。在扩展实现这个这个接口的类时，间接实现它。

## 7 Servlet 的工作原理

Servlet接口定义了Servlet与servlet容器之间的契约。这个契约是：Servlet容器将Servlet类载入内存，并产生Servlet实例和调用它具体的方法。但是要注意的是，在一个应用程序中，每种Servlet类型只能有一个实例。

用户请求致使Servlet容器调用Servlet的Service()方法，并传入一个ServletRequest对象和一个ServletResponse对象。ServletRequest对象和ServletResponse对象都是由Servlet容器（例如TomCat）封装好的，并不需要程序员去实现，程序员可以直接使用这两个对象。

ServletRequest中封装了当前的Http请求，因此，开发人员不必解析和操作原始的Http数据。ServletResponse表示当前用户的Http响应，程序员只需直接操作ServletResponse对象就能把响应轻松的发回给用户。

对于每一个应用程序，Servlet容器还会创建一个ServletContext对象。这个对象中封装了上下文（应用程序）的环境详情。每个应用程序只有一个ServletContext。每个Servlet对象也都有一个封装Servlet配置的ServletConfig对象。

## 8 Servlet 接口中定义的方法

```java
public interface Servlet {
    void init(ServletConfig var1) throws ServletException;
    ServletConfig getServletConfig();
    void service(ServletRequest var1, ServletResponse var2) throws ServletException, IOException;
    String getServletInfo();
    void destroy();
}
```

## 9 Servlet 的生命周期

其中，init( ),service( ),destroy( )是Servlet生命周期的方法。代表了Servlet从“出生”到“工作”再到“死亡 ”的过程。Servlet容器（例如TomCat）会根据下面的规则来调用这三个方法：

1. init( )：当Servlet第一次被请求时，Servlet容器就会开始调用这个方法来初始化一个Servlet对象出来，但是这个方法在后续请求中不会在被Servlet容器调用，就像人只能“出生”一次一样。我们可以利用init()方法来执行相应的初始化工作。调用这个方法时，Servlet容器会传入一个ServletConfig对象进来从而对Servlet对象进行初始化。

2. service( )方法：每当请求Servlet时，Servlet容器就会调用这个方法。就像人一样，需要不停的接受老板的指令并且“工作”。第一次请求时，Servlet容器会先调用init( )方法初始化一个Servlet对象出来，然后会调用它的service( )方法进行工作，但在后续的请求中，Servlet容器只会调用service方法了。
3. destory：当要销毁Servlet时，Servlet容器就会调用这个方法，就如人一样，到时期了就得死亡。在卸载应用程序或者关闭Servlet容器时，就会发生这种情况，一般在这个方法中会写一些清除代码。

## 10 Servlet 的其它两个方法

1. getServletInfo()：这个方法会返回Servlet的一段描述，可以返回一段字符串。
2. getServletConfig()：这个方法会返回由Servlet容器传给init（ ）方法的ServletConfig对象。

## 11 ServletRequset接口

Servlet容器对于接受到的每一个Http请求，都会创建一个ServletRequest对象，并把这个对象传递给Servlet的Sevice( )方法。其中，ServletRequest对象内封装了关于这个请求的许多详细信息。

```java
public interface ServletRequest {
    int getContentLength();//返回请求主体的字节数
    String getContentType();//返回主体的MIME类型
    String getParameter(String var1);//返回请求参数的值
}
```

其中，getParameter是在ServletRequest中最常用的方法，可用于获取查询字符串的值。

## 12 ServletResponse接口

javax.servlet.ServletResponse接口表示一个Servlet响应，在调用Servlet的Service( )方法前，Servlet容器会先创建一个ServletResponse对象，并把它作为第二个参数传给Service( )方法。ServletResponse隐藏了向浏览器发送响应的复杂过程。

```java
public interface ServletResponse {
    String getCharacterEncoding();
    String getContentType();
    ServletOutputStream getOutputStream() throws IOException; 
    PrintWriter getWriter() throws IOException; 
    void setCharacterEncoding(String var1); 
    void setContentLength(int var1);
    void setContentType(String var1); 
    void setBufferSize(int var1);
    int getBufferSize();
    void flushBuffer() throws IOException;
    void resetBuffer();
    boolean isCommitted();
    void reset();
    void setLocale(Locale var1); 
    Locale getLocale();
}
```

其中的getWriter方法，它返回了一个可以向客户端发送文本的的Java.io.PrintWriter对象。默认情况下，PrintWriter对象使用ISO-8859-1编码（该编码在输入中文时会发生乱码）。

在向客户端发送响应时，大多数都是使用该对象向客户端发送HTML。

还有一个方法也可以用来向浏览器发送数据，它就是getOutputStream，从名字就可以看出这是一个二进制流对象，因此这个方法是用来发送二进制数据的。

在发送任何HTML之前，应该先调用setContentType()方法，设置响应的内容类型，并将“text/html”作为一个参数传入，这是在告诉浏览器响应的内容类型为HTML，需要以HTML的方法解释响应内容而不是普通的文本，或者也可以加上“charset=UTF-8”改变响应的编码方式以防止发生中文乱码现象。

## 13 ServletConfig接口

当Servlet容器初始化Servlet时，Servlet容器会给Servlet的init( )方式传入一个ServletConfig对象。

```java
String getServletName();	// 获得Servlet在web.xml中配置的name的值
String getInitParameter();	// 获得Servlet的初始化参数
Enumeration getInitParaterNames();	// 获得所有Servlet的初始化参数的名称
```

## 14 ServletContext对象

ServletContext对象表示Servlet应用程序。每个Web应用程序都只有一个ServletContext对象。在将一个应用程序同时部署到多个容器的分布式环境中，每台Java虚拟机上的Web应用都会有一个ServletContext对象。

通过在ServletConfig中调用getServletContext方法，也可以获得ServletContext对象。

那么为什么要存在一个ServletContext对象呢？存在肯定是有它的道理，因为有了ServletContext对象，就可以共享从应用程序中的所有资料处访问到的信息，并且可以动态注册Web对象。前者将对象保存在ServletContext中的一个内部Map中。保存在ServletContext中的对象被称作属性。

ServletContext中的下列方法负责处理属性：

```java
Object getAttribute(String var1);
Enumeration<String> getAttributeNames();
void setAttribute(String var1, Object var2);
void removeAttribute(String var1);
```

## 15 GenericServlet抽象类 

前面我们编写Servlet一直是通过实现Servlet接口来编写的，但是，使用这种方法，则必须要实现Servlet接口中定义的所有的方法，即使有一些方法中没有任何东西也要去实现，并且还需要自己手动的维护ServletConfig这个对象的引用。因此，这样去实现Servlet是比较麻烦的。

幸好，GenericServlet抽象类的出现很好的解决了这个问题。本着尽可能使代码简洁的原则，GenericServlet实现了Servlet和ServletConfig接口。

GenericServlet抽象类相比于直接实现Servlet接口，有以下几个好处：

为Servlet接口中的所有方法提供了默认的实现，则程序员需要什么就直接改什么，不再需要把所有的方法都自己实现了。

提供方法，包围ServletConfig对象中的方法。

将init()方法中的ServletConfig参数赋给了一个内部的ServletConfig引用从而来保存ServletConfig对象，不需要程序员自己去维护ServletConfig了。

```java
public void init(ServletConfig config) throws ServletException {
    this.config = config;
    this.init();
}
```

但是，我们发现在GenericServlet抽象类中还存在着另一个没有任何参数的Init()方法：

```java
public void init() throws ServletException {
}
```

设计者的初衷到底是为了什么呢？在第一个带参数的init（）方法中就已经把ServletConfig对象传入并且通过引用保存好了，完成了Servlet的初始化过程，那么为什么后面还要加上一个不带任何参数的init（）方法呢？这不是多此一举吗？

当然不是多此一举了，存在必然有存在它的道理。我们知道，抽象类是无法直接产生实例的，需要另一个类去继承这个抽象类，那么就会发生方法覆盖的问题，如果在类中覆盖了GenericServlet抽象类的init()方法，那么程序员就必须手动的去维护ServletConfig对象了，还得调用super.init(servletConfig）方法去调用父类GenericServlet的初始化方法来保存ServletConfig对象，这样会给程序员带来很大的麻烦。GenericServlet提供的第二个不带参数的init()方法，就是为了解决上述问题的。

这个不带参数的init()方法，是在ServletConfig对象被赋给ServletConfig引用后，由第一个带参数的init(ServletConfig servletconfig)方法调用的，那么这意味着，当程序员如果需要覆盖这个GenericServlet的初始化方法，则只需要覆盖那个不带参数的init( )方法就好了，此时，servletConfig对象仍然有GenericServlet保存着。

说了这么多，通过扩展GenericServlet抽象类，就不需要覆盖没有计划改变的方法。因此，代码将会变得更加的简洁，程序员的工作也会减少很多。

然而，虽然GenricServlet是对Servlet一个很好的加强，但是也不经常用，因为他不像HttpServlet那么高级。HttpServlet才是主角，在现实的应用程序中被广泛使用。那么我们接下来就看看传说中的HttpServlet到底厉害在哪里吧。

## 16 javax.servlet.http包内容

之所以所HttpServlet要比GenericServlet强大，其实也是有道理的。HttpServlet是由GenericServlet抽象类扩展而来的，HttpServlet抽象类的声明如下所示：

```java
public abstract class HttpServlet extends GenericServlet implements Serializable 
```

HttpServlet之所以运用广泛的另一个原因是现在大部分的应用程序都要与HTTP结合起来使用。这意味着我们可以利用HTTP的特性完成更多更强大的任务。Javax.servlet.http包是Servlet API中的第二个包，其中包含了用于编写Servlet应用程序的类和接口。Javax.servlet.http中的许多类型都覆盖了Javax.servlet中的类型。

![img](https://img-blog.csdn.net/20180513104757248?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzE5NzgyMDE5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

## 17 HttpServlet抽象类

HttpServlet抽象类是继承于GenericServlet抽象类而来的。使用HttpServlet抽象类时，还需要借助分别代表Servlet请求和Servlet响应的HttpServletRequest和HttpServletResponse对象。

HttpServletRequest接口扩展于javax.servlet.ServletRequest接口，HttpServletResponse接口扩展于javax.servlet.servletResponse接口。

```java
public interface HttpServletRequest extends ServletRequest
public interface HttpServletResponse extends ServletResponse
```

HttpServlet抽象类覆盖了GenericServlet抽象类中的Service( )方法，并且添加了一个自己独有的Service(HttpServletRequest request，HttpServletResponse方法。

首先来看GenericServlet抽象类中是如何定义service方法的：

```java
public abstract void service(ServletRequest var1, ServletResponse var2) throws ServletException, IOException;
```

我们看到是一个抽象方法，也就是HttpServlet要自己去实现这个service方法，我们在看看HttpServlet是怎么覆盖这个service方法的：

```java
public void service(ServletRequest req, ServletResponse res) throws ServletException, IOException {
    HttpServletRequest request;
    HttpServletResponse response;
    try {
        request = (HttpServletRequest)req;
        response = (HttpServletResponse)res;
    } catch (ClassCastException var6) {
        throw new ServletException("non-HTTP request or response");
    }
    this.service(request, response);
}
```

我们发现，HttpServlet中的service方法把接收到的ServletRequsest类型的对象转换成了HttpServletRequest类型的对象，把ServletResponse类型的对象转换成了HttpServletResponse类型的对象。之所以能够这样强制的转换，是因为在调用Servlet的Service方法时，Servlet容器总会传入一个HttpServletRequest对象和HttpServletResponse对象，预备使用HTTP。因此，转换类型当然不会出错了。

转换之后，service方法把两个转换后的对象传入了另一个service方法，那么我们再来看看这个方法是如何实现的：

```java
protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    String method = req.getMethod();
    long lastModified;
    if (method.equals("GET")) {
        lastModified = this.getLastModified(req);
        if (lastModified == -1L) {
            this.doGet(req, resp);
        } else {
            long ifModifiedSince = req.getDateHeader("If-Modified-Since");
            if (ifModifiedSince < lastModified) {
                this.maybeSetLastModified(resp, lastModified);
                this.doGet(req, resp);
            } else {
                resp.setStatus(304);
            }
        }
    } else if (method.equals("HEAD")) {
        lastModified = this.getLastModified(req);
        this.maybeSetLastModified(resp, lastModified);
        this.doHead(req, resp);
    } else if (method.equals("POST")) {
        this.doPost(req, resp);
    } else if (method.equals("PUT")) {
        this.doPut(req, resp);
    } else if (method.equals("DELETE")) {
        this.doDelete(req, resp);
    } else if (method.equals("OPTIONS")) {
        this.doOptions(req, resp);
    } else if (method.equals("TRACE")) {
        this.doTrace(req, resp);
    } else {
        String errMsg = lStrings.getString("http.method_not_implemented");
        Object[] errArgs = new Object[]{method};
        errMsg = MessageFormat.format(errMsg, errArgs);
        resp.sendError(501, errMsg);
    }
}
```

我们发现，这个service方法的参数是HttpServletRequest对象和HttpServletResponse对象，刚好接收了上一个service方法传过来的两个对象。

接下来我们再看看service方法是如何工作的，我们会发现在service方法中还是没有任何的服务逻辑，但是却在解析HttpServletRequest中的方法参数，并调用以下方法之一：doGet,doPost,doHead,doPut,doTrace,doOptions和doDelete。这7种方法中，每一种方法都表示一个Http方法。doGet和doPost是最常用的。所以，如果我们需要实现具体的服务逻辑，不再需要覆盖service方法了，只需要覆盖doGet或者doPost就好了。

总之，HttpServlet有两个特性是GenericServlet所不具备的：

1. 不用覆盖service方法，而是覆盖doGet或者doPost方法。在少数情况，还会覆盖其他的5个方法。
2. 使用的是HttpServletRequest和HttpServletResponse对象。

## 18 HttpServletRequest接口

HttpServletRequest表示Http环境中的Servlet请求。它扩展于javax.servlet.ServletRequest接口，并添加了几个方法。

```java
String getContextPath();	//返回请求上下文的请求URI部分
Cookie[] getCookies();		//返回一个cookie对象数组
String getHeader(String var1);//返回指定HTTP标题的值
String getMethod();			//返回生成这个请求HTTP的方法名称
String getQueryString();	//返回请求URL中的查询字符串
HttpSession getSession();	//返回与这个请求相关的会话对象
```

## 19 HttpServletRequest内封装的请求

因为Request代表请求，所以我们可以通过该对象分别获得HTTP请求的请求行，请求头和请求体。

![img](https://img-blog.csdn.net/20180513130638615?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzE5NzgyMDE5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

## 20 通过request获得请求行

假设查询字符串为：

```
username=zhangsan&password=123
```

获得客户端的请求方式：

```java
String getMethod()
```

获得请求的资源：

```java
String getRequestURI()
StringBuffer getRequestURL()
String getContextPath() --- web应用的名称
String getQueryString() ---- get提交url地址后的参数字符串
```

## 21 通过request获得请求头

```java
long getDateHeader(String name)
String getHeader(String name)
Enumeration getHeaderNames()
Enumeration getHeaders(String name)
int getIntHeader(String name)
//referer头的作用：执行该此访问的的来源，做防盗链
```

## 22 通过request获得请求体

请求体中的内容是通过post提交的请求参数，格式是：

```
username=zhangsan&password=123&hobby=football&hobby=basketball
key --------value
username	[zhangsan]
password 	[123]
hobby     	[football，basketball]                    
```

以上面参数为例，通过以下方法获得请求参数：

```java
String getParameter(String name)
String[] getParameterValues(String name)
Enumeration getParameterNames()
Map<String,String[]> getParameterMap()
```

注意：get请求方式的请求参数上述的方法一样可以获得。

## 23 Request乱码问题的解决方法

在前面我们讲过，在service中使用的编码解码方式默认为：ISO-8859-1编码，但此编码并不支持中文，因此会出现乱码问题，所以我们需要手动修改编码方式为UTF-8编码，才能解决中文乱码问题，下面是发生乱码的具体细节：

![img](https://img-blog.csdn.net/20180513195355316?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzE5NzgyMDE5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

解决post提交方式的乱码：request.setCharacterEncoding("UTF-8");

解决get提交的方式的乱码：parameter = newString(parameter.getbytes("iso8859-1"),"utf-8");

## 24 HttpServletResponse接口

在Service API中，定义了一个HttpServletResponse接口，它继承自ServletResponse接口，专门用来封装HTTP响应消息。    由于HTTP响应消息分为状态行，响应消息头，响应消息体三部分，因此，在HttpServletResponse接口中定义了向客户端发送响应状态码，响应消息头，响应消息体的方法。

## 25 HttpServletResponse内封装的响应

![img](https://img-blog.csdn.net/20180513203525684?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzE5NzgyMDE5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

## 26 通过Response设置响应

```java
void addCookie(Cookie var1);//给这个响应添加一个cookie
void addHeader(String var1, String var2);//给这个请求添加一个响应头
void sendRedirect(String var1) throws IOException;//发送一条响应码，讲浏览器跳转到指定的位置
void setStatus(int var1);//设置响应行的状态码

void addHeader(String name, String value)
void addIntHeader(String name, int value)
void addDateHeader(String name, long date)
void setHeader(String name, String value)
void setDateHeader(String name, long date)
void setIntHeader(String name, int value)	//其中add表示添加，set表示设置
```

```java
PrintWriter getWriter()
```

获得字符流，通过字符流的write(String s)方法可以将字符串设置到response缓冲区中，随后Tomcat会将response缓冲区中的内容组装成Http响应返回给浏览器端。

```java
ServletOutputStream getOutputStream()
```

获得字节流，通过该字节流的write(byte[] bytes)可以向response缓冲区中写入字节，再由Tomcat服务器将字节内容组成Http响应返回给浏览器。

该方法所获取的字节输出流对象为ServletOutputStream类型。由于ServletOutputStream是OutputStream的子类，它可以直接输出字节数组中的二进制数据。因此，要想输出二进制格式的响应正文，就需要使用getOutputStream()方法。

注意：虽然response对象的getOutSream()和getWriter()方法都可以发送响应消息体，但是他们之间相互排斥，不可以同时使用，否则会发生异常。

## 27 Response的乱码问题

由于计算机中的数据都是以二进制形式存储的，因此，当传输文本时，就会发生字符和字节之间的转换。字符和字节之间的转换是通过查码表完成的，将字符转换成字节的过程成为编码，将字节转换成字符的过程成为解码，如果编码和解码使用的码表不一致，就会导致乱码问题。

原因：response缓冲区的默认编码是iso8859-1，此码表中没有中文。所以需要更改response的编码方式：

```java
response.setCharacterEncoding("utf-8");
```

![img](https://img-blog.csdn.net/20180513205620262?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzE5NzgyMDE5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

通过更改response的编码方式为UTF-8，任然无法解决乱码问题，因为发送端服务端虽然改变了编码方式为UTF-8，但是接收端浏览器端仍然使用GB2312编码方式解码，还是无法还原正常的中文，因此还需要告知浏览器端使用UTF-8编码去解码。

```java
// 通知浏览器使用utf-8解码
respone.setHeader("Content-Type","text/html;charset=utf-8");
```

上面通过调用两个方式分别改变服务端对于Response的编码方式以及浏览器的解码方式为同样的UTF-8编码来解决编码方式不一样发生乱码的问题。

```java
response.setContentType("text/html;charset=UTF-8");	//使用这个
```

这个方法包含了上面的两个方法的调用，因此在实际的开发中，只需要调用一个方法即可。

![img](https://img-blog.csdn.net/20180513204459256?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzE5NzgyMDE5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

## 28 Response的工作流程

![img](https://img-blog.csdn.net/20180513204718428?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzE5NzgyMDE5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

## 29 Servlet的工作流程

![img](https://img-blog.csdn.net/20180513204808318?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzE5NzgyMDE5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

Html页面通过提交表单数据到相应的Servlet进行处理，此时浏览器的地址栏会发生变化。多了后面的“?username=root&password=123&提交=提交”字符串，这其实就是我们开始填写的参数，以Get的方法发送过去，所以查询字符串会直接加在链接后面，如果采用的是Post方式则不会出现在链接中，因此，登录表单为了安全性大多采用Post方式提交。

## 30 Servlet的局限性

Servlet如果需要给客户端返回数据，比如一个HTML文件，Servlet内部需要一行一行的把HTML语句给用Writer输出，早期简单的网页还能应付得住，但是随着互联网的不断发展，网站的内容和功能越来越强大，一个普通的HTML文件可能就达到好几百行，如果在采用使用Servlet去一行一行的输出HTML代码的话，将会非常的繁琐并且浪费大量的时间，且在当时，出现了PHP这种可以内嵌到HTML文件的动态语言，使得制作动态网页变得异常的简单和轻松，因此大量的程序员转上了PHP语言的道路，JAVA的份额急剧减小，当时JAVA的开发者Sun公司为了解决这个问题，也开发出了自己的动态网页生成技术，使得同样可以在HTML文件里内嵌JAVA代码，这就是现在的JSP技术。

## 31 ServletContextListener（Servlet全局监听器）

首先要说明的是，ServletContextListener是一个接口，我们随便写一个类，只要这个类实现了ServletContextListener接口，那么这个类就实现了【监听ServletContext】的功能。那么，这个神奇的接口是如何定义的呢？我们来看一下这个接口的内部情况：

```java
package javax.servlet;

import java.util.EventListener;

public interface ServletContextListener extends EventListener {
    void contextInitialized(ServletContextEvent var1);
    void contextDestroyed(ServletContextEvent var1);
}
```

我们发现，在这个接口中只声明了两个方法，分别是void contextInitialized(ServletContextEvent var1)和void contextDestroyed(ServletContextEvent var1)方法，所以，我们很容易的就能猜测到，ServletContext的生命只有两种，分别是：

1. ServletContext初始化。（应用start时）---------->Servlet容器调用void contextInitialized(ServletContextEvent var1)
2. ServletContext销毁。（应用stop时）---------->Servlet容器调用 void contextDestroyed(ServletContextEvent var1)

因此，我们大概能够猜到ServletContextListener的工作机制了，当应用启动时，ServletContext进行初始化，然后Servlet容器会自动调用正在监听ServletContext的ServletContextListener的void contextInitialized(ServletContextEvent var1)方法，并向其传入一个ServletContextEvent对象。当应用停止时，ServletContext被销毁，此时Servlet容器也会自动地调用正在监听ServletContext的ServletContextListener的void contextDestroyed(ServletContextEvent var1)方法。

