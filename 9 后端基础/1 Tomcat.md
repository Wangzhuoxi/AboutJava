一、概念
    Tomcat 服务器是一个开源的轻量级Web应用服务器，在中小型系统和并发量小的场合下被普遍使用，是开发和调试Servlet、JSP 程序的首选。

二、原理
    Tomcat结构图：

​                

    Tomcat主要组件：服务器Server，服务Service，连接器Connector、容器Container。连接器Connector和容器Container是Tomcat的核心。
    
    一个Container容器和一个或多个Connector组合在一起，加上其他一些支持的组件共同组成一个Service服务，有了Service服务便可以对外提供能力了，但是Service服务的生存需要一个环境，这个环境便是Server，Server组件为Service服务的正常使用提供了生存环境，Server组件可以同时管理一个或多个Service服务。

三、两大组件
1、Connector

    一个Connecter将在某个指定的端口上侦听客户请求，接收浏览器的发过来的 tcp 连接请求，创建一个 Request 和 Response 对象分别用于和请求端交换数据，然后会产生一个线程来处理这个请求并把产生的 Request 和 Response 对象传给处理Engine(Container中的一部分)，从Engine出获得响应并返回客户。 
Tomcat中有两个经典的Connector，**一个直接侦听来自Browser的HTTP请求，另外一个来自其他的WebServer请求。**HTTP/1.1 Connector在端口8080处侦听来自客户Browser的HTTP请求，AJP/1.3 Connector在端口8009处侦听其他Web Server（其他的HTTP服务器）的Servlet/JSP请求。 
Connector 最重要的功能就是**接收连接请求然后分配线程让 Container 来处理这个请求**，所以这必然是多线程的，多线程的处理是 Connector 设计的核心。

2、Container

​                Container是容器的父接口，该容器的设计用的是典型的责任链的设计模式，它由**四个自容器组件构成**，分别是E**ngine、Host、Context、Wrapper**。这四个组件是负责关系，存在包含关系。通常一个Servlet class对应一个Wrapper，如果有多个Servlet定义多个Wrapper，如果有多个Wrapper就要定义一个更高的Container，如Context。 

![img](https://img-blog.csdn.net/2018030817251496?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxNDIzMTY0Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

Context 还可以定义在父容器 Host 中，Host 不是必须的，但是要运行 war 程序，就必须要 Host，因为 war 中必有 web.xml 文件，这个文件的解析就需要 Host 了，如果要有多个 Host 就要定义一个 top 容器 Engine 了。而 Engine 没有父容器了，一个 Engine 代表一个完整的 Servlet 引擎。

Engine 容器 
Engine 容器比较简单，它只定义了一些基本的关联关系
Host 容器 
Host 是 Engine 的子容器，**一个 Host 在 Engine 中代表一个虚拟主机**，这个虚拟主机的作用就是**运行多个应用，**它负责安装和展开这些应用，并且标识这个应用以便能够区分它们。它的子容器通常是 Context，它除了关联子容器外，还有就是保存一个主机应该有的信息。
Context 容器 
Context 代表 Servlet 的 Context，**它具备了 Servlet 运行的基本环境，**理论上只要有 Context 就能运行 Servlet 了。简单的 Tomcat 可以没有 Engine 和 Host。Context 最重要的功能就是管理它里面的 Servlet 实例，Servlet 实例在 Context 中是以 Wrapper 出现的，还有一点就是 Context 如何才能找到正确的 Servlet 来执行它呢？ Tomcat5 以前是通过一个 Mapper 类来管理的，Tomcat5 以后这个功能被移到了 request 中，在前面的时序图中就可以发现获取子容器都是通过 request 来分配的。
Wrapper 容器 
**Wrapper 代表一个 Servlet，它负责管理一个 Servlet，包括的 Servlet 的装载、初始化、执行以及资源回收。**Wrapper 是最底层的容器，它没有子容器了，所以调用它的 addChild 将会报错。 
Wrapper 的实现类是 StandardWrapper，StandardWrapper 还实现了拥有一个 Servlet 初始化信息的 ServletConfig，由此看出 StandardWrapper 将直接和 Servlet 的各种信息打交道。
  (wrapper:代表一个servlet context：servlet的环境，管理servlet实例。 host 一个虚拟主机负责运行多个应用。engine 定义基本关系)



3、其他组件

  Tomcat 还有其它重要的组件，如安全组件 security、logger 日志组件、session、mbeans、naming 等其它组件。这些组件共同为 Connector 和 Container 提供必要的服务。

## Tomcat Server处理一个HTTP请求的过程

1、用户点击网页内容，请求被发送到本机端口8080，被在那里监听的Coyote HTTP/1.1 **Connector**获得。 
2、Connector把该请求交给它所在的Service的**Engine**来处理，并等待Engine的回应。 
3、Engine获得请求localhost/test/index.jsp，匹配所有的虚拟主机Host。 
4、Engine匹配到名为localhost的Host（即使匹配不到也把请求交给该Host处理，因为该Host被定义为该Engine的默认主机），名为localhost的Host获得请求/test/index.jsp，匹配它所拥有的所有的Context。Host匹配到路径为/test的Context（如果匹配不到就把该请求交给路径名为“ ”的Context去处理）。 
5、path=“/test”的Context获得请求/index.jsp，在它的mapping table中寻找出对应的Servlet。Context匹配到URL PATTERN为*.jsp的Servlet,对应于JspServlet类。 
6、构造HttpServletRequest对象和HttpServletResponse对象，作为参数调用JspServlet的doGet（）或doPost（）.执行业务逻辑、数据存储等程序。 
7、Context把执行完之后的HttpServletResponse对象返回给Host。 
8、Host把HttpServletResponse对象返回给Engine。 
9、Engine把HttpServletResponse对象返回Connector。 
10、Connector把HttpServletResponse对象返回给客户Browser。

(borwer->connector监听->engine（匹配host）->host->context->servlet->建立httpservletrequest/response ,执行业务->response返回host、engine、connector、browser)

# 1 什么是Tomcat

Tomcat简单的说就是一个运行JAVA的**网络服务器**，**底层是Socket的一个程序，它也是JSP和Serlvet的一个容器。**

通俗一点说，tomcat是一个web服务器，我们访问的网站，其实就是一些文件和程序，他们存储和运行在一台主机上，这个主机就是服务器，只有把**网站发布到服务器**上别的主机才能访问，所以我们在开发网站用tomcat在自己的电脑上建立一个服务器，把网站发布上去就能通过浏览器输入地址访问了

# 2 为什么我们需要用到Tomcat

如果你学过html，css，你会知道你写的页面只能自己访问，**别人不能远程访问你写的页面**，

Tomcat就是**提供能够让别人访问自己写的页面的一个程序**

![img](https://segmentfault.com/img/remote/1460000013228164?w=694&h=483)

# 3 相关术语介绍

![img](https://segmentfault.com/img/remote/1460000013228167?w=522&h=317)

# 4 Tomcat结构目录

![img](https://segmentfault.com/img/remote/1460000013228168?w=728&h=388)

1. bin：启动和关闭tomcat的bat文件
2. conf：配置文件
   - `server.xml`该文件用于配置server相关的信息，比如tomcat启动的端口号，配置主机(Host)
   - `web.xml`文件配置与web应用（web应用相当于一个web站点）
   - `tomcat-user.xml`配置用户名密码和相关权限.
3. lib：该目录放置运行tomcat运行需要的jar包
4. logs：存放日志，当我们需要查看日志的时候，可以查询信息
5. webapps：放置我们的web应用
6. work工作目录：该目录用于存放**jsp被访问后生成对应的server文件和.class文件**



**web站点的目录是有规范的**

![img](https://segmentfault.com/img/remote/1460000013228170?w=926&h=628)

# 5 Tomcat体系结构

![img](https://segmentfault.com/img/remote/1460000013228179?w=983&h=593)

# 6 浏览器访问WEB资源的流程图

![img](https://segmentfault.com/img/remote/1460000013228180?w=953&h=561)

