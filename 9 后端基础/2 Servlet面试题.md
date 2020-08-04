## 1 Tomcat的缺省端口是多少，怎么修改

缺省端口是8080

到tomcat主目录下的conf/server.xml文件中修改，把8080端口改成是8088或者是其他的。

## 2 Tomcat 有哪几种Connector运行模式(优化)？

1. bio(blocking I/O)
2. nio(non-blocking I/O)
3. apr(Apache Portable Runtime/Apache可移植运行库)

相关解释:

- bio: 传统的Java I/O操作，同步且阻塞IO。
- nio: JDK1.4开始支持，同步阻塞或同步非阻塞IO
- aio(nio.2): JDK7开始支持，异步非阻塞IO
- apr: Tomcat将以JNI的形式**调用Apache HTTP服务器的核心动态链接库**来处理**文件读取或网络传输操作**，从而大大地 提高Tomcat对静态文件的处理性能

## 3 Tomcat有几种部署方式



```
1. 直接把Web项目放在webapps下，Tomcat会自动将其部署
2. 在server.xml文件上配置 `<Context>`节点，设置相关的属性即可
3. 通过Catalina来进行配置：进入到conf\Catalina\localhost文件下，创建一个xml文件，该文件的名字就是站点的名字。编写XML的方式来进行设置。
```

(web项目放在webapps下，tomcat自动部署。或者server.xml中配置。)

部署方式第二点：

1. 在其他盘符下创建一个web站点目录，并创建WEB-INF目录和一个html文件。

2. 找到Tomcat目录下/conf/server.xml文件

3. 在server.xml中的节点下添加如下代码。path表示的是访问时输入的web项目名，docBase表示的是站点目录的绝对路径。

   ```xml
   <Context path="/web1" docBase="D:\web1"/>
   ```

4. 访问配置好的web站点

部署方式第三点：

1. 进入到confCatalinalocalhost文件下，创建一个xml文件，该文件的名字就是站点的名字。

2. xml文件的代码如下，docBase是你web站点的绝对路径。

   ```xml
   <Context docBase="D:\web1" reloadable="true"> 
   </Context> 
   ```

3. 访问web站点下的html资源

## 4 Servlet生命周期

Servlet生命周期可分为5个步骤

（加载servlet（实例化），初始化，处理请求（service（）） 销毁 卸载）

1. **加载Servlet**。当Tomcat第一次访问Servlet的时候，Tomcat会负责创建Servlet的实例
2. **初始化**。当Servlet被实例化后，Tomcat会调用init()方法初始化这个对象
3. **处理服务**。当浏览器访问Servlet的时候，Servlet 会调用service()方法处理请求
4. **销毁**。当Tomcat关闭时或者检测到Servlet要从Tomcat删除的时候会自动调用destroy()方法，让该实例释放掉所占的资源。一个Servlet如果长时间不被使用的话，也会被Tomcat自动销毁
5. **卸载**。当Servlet调用完destroy()方法后，等待垃圾回收。如果有需要再次使用这个Servlet，会重新调用init()方法进行初始化操作。

简单总结：只要访问Servlet，service()就会被调用。init()只有第一次访问Servlet的时候才会被调用。destroy()只有在Tomcat关闭的时候才会被调用。

## 5 get方式和post方式有何区别

数据携带上:

- GET方式：在URL地址后附带的参数是有限制的，其数据容量通常不能超过1K。
- POST方式：可以在请求的实体内容中向服务器发送数据，传送的数据量无限制。

请求参数的位置上:

- GET方式：请求参数放在URL地址后面，以?的方式来进行拼接
- POST方式:请求参数放在HTTP请求包中

用途上:

- GET方式一般用来获取数据
- POST方式一般用来提交数据
  - 原因:
    - 首先是因为GET方式携带的数据量比较小，无法带过去很大的数量
    - POST方式提交的参数后台更加容易解析(使用POST方式提交的中文数据，后台也更加容易解决)
    - GET方式比POST方式要快

## 6 Servlet相关 API

### 6.1 doGet与doPost方法的两个参数是什么？

1. HttpServletRequest：封装了与请求相关的信息
2. HttpServletResponse：封装了与响应相关的信息

### 6.2 获取页面的元素的值有几种方式？

1. request.getParameter() 返回客户端的请求参数的值（接收一般变量）
2. request.getParameterNames() 返回所有**可用属性名的枚举**
3. request.getParameterValues() 返回包含参数的所有值的数组（接受数组变量）

### 6.3 request.getAttribute()和request.getParameter()区别？

用途上:

- request.getAttribute()一般用于获取request域对象的数据(在跳转之前把数据使用setAttribute来放到request对象上)
- request.getParameter()一般用于获取客户端提交的参数

存储数据上:

- request.getAttribute()可以获取Objcet对象
- request.getParameter()只能获取字符串(这也是为什么它一般用于获取客户端提交的参数)

## 7 forward和redirect的区别

**实际发生位置不同，地址栏不同**

- 转发是发生在服务器的

  转发是由服务器进行跳转的，细心的朋友会发现，在转发的时候，浏览器的地址栏是没有发生变化的，在我访问Servlet111的时候，即使跳转到了Servlet222的页面，浏览器的地址还是Servlet111的。也就是说浏览器是不知道该跳转的动作，转发是对浏览器透明的。通过上面的转发时序图我们也可以发现，实现转发只是一次的http请求**，**一次转发中request和response对象都是同一个。这也解释了，为什么可以使用request作为域对象进行Servlet之间的通讯。

- 重定向是发生在浏览器的

- 重定向是由浏览器进行跳转的，进行重定向跳转的时候，浏览器的地址会发生变化的。曾经介绍过：实现重定向的原理是由response的状态码和Location头组合而实现的。这是由浏览器进行的页面跳转实现重定向会发出两个http请求，request域对象是无效的，因为它不是同一个request对象。

**用法不同**

很多人都搞不清楚转发和重定向的时候，资源地址究竟怎么写。有的时候要把应用名写上，有的时候不用把应用名写上。很容易把人搞晕。记住一个原则： **给服务器用的直接从资源名开始写，给浏览器用的要把应用名写上**

- request.getRequestDispatcher("/资源名 URI").forward(request,response)

  转发时"/"代表的是本应用程序的根目录【zhongfucheng】

- response.send("/web应用/资源名 URI");

  重定向时"/"代表的是webapps目录

**能够去往的URL的范围不一样**

- 转发是服务器跳转只能去往当前web应用的资源
- 重定向是服务器跳转，可以去往任何的资源

**传递数据的类型不同**

- 转发的request对象可以传递各种类型的数据，包括对象
- 重定向只能传递字符串

**跳转的时间不同**

- 转发时：执行到跳转语句时就会立刻跳转
- 重定向：整个页面执行完之后才执行跳转

那么转发(forward)和重定向(redirect)使用哪一个？

根据上面说明了转发和重定向的区别也可以很容易概括出来。转发是带着转发前的请求的参数的。重定向是新的请求。

典型的应用场景：

1. 转发: 访问 Servlet 处理业务逻辑，然后 forward 到 jsp 显示处理结果，浏览器里 URL 不变
2. 重定向: 提交表单，处理成功后 redirect 到另一个 jsp，防止表单重复提交，浏览器里 URL 变了

## 8 tomcat容器是如何创建servlet类实例？用到了什么原理？

1. 当容器启动时，**会读取在webapps目录下所有的web应用中的web.xml文件，然后对 xml文件进行解析，并读取servlet注册信息。然后，将每个应用中注册的servlet类都进行加载，并通过 反射的方式实例化**。（有时候也是在第一次请求时实例化）

2. 在servlet注册时加上\<load-on-startup>1\</load-on-startup>如果为正数，则在一开始就实例化，如果不写或为负数，则第一次请求实例化。

   （tomcat启动时读取webapps下的web应用的web.xml中文件，进行解析，读取servlet注册信息并进行实例化）

## 9 什么是cookie？Session和cookie有什么区别？

在程序中，会话跟踪是很重要的事情。理论上，**一个用户的所有请求操作都应该属于同一个会话**，而另一个用户的所有请求操作则应该属于另一个会话，二者不能混淆。例如，用户A在超市购买的任何商品都应该放在A的购物车内，不论是用户A什么时间购买的，这都是属于同一个会话的，不能放入用户B或用户C的购物车内，这不属于同一个会话。

Cookie实际上是一小段的文本信息。客户端请求服务器，如果服务器需要**记录该用户状态**，就使用**response向客户端浏览器颁发一个Cookie。**客户端浏览器会把Cookie保存起来。当浏览器再请求该网站时，浏览器把**请求的网址连同该Cookie一同提交给服务 器**。服务器检查该Cookie，以此来辨认用户状态。服务器还可以根据需要修改Cookie的内容。

（服务端记录该用户的状态，response一个cookie。下一次直接网址+cookie一起提交。）

**应用：**

**1. 记录用户访问次数**

Java中把Cookie封装成了javax.servlet.http.Cookie类。每个Cookie都是该Cookie类的对象。服务器通过操作Cookie类对象对客户端Cookie进行操作。通过**request.getCookie()获取客户端提交的所有Cookie**（以Cookie[]数组形式返回），**通过response.addCookie(Cookiecookie)向客户端设置Cookie。**

Cookie对象使用key-value属性对的形式保存用户状态，一个Cookie对象保存一个属性对，一个request或者response同时使用多个Cookie。因为Cookie类位于包javax.servlet.http.*下面，所以JSP中不需要import该类。

 

**2 Cookie的不可跨域名性**

很多网站都会使用Cookie。例如，Google会向客户端颁发Cookie，Baidu也会向客户端颁发Cookie。那浏览器访问Google会不会也携带上Baidu颁发的Cookie呢？或者Google能不能修改Baidu颁发的Cookie呢？

答案是否定的。**Cookie具有不可跨域名性**。根据Cookie规范，浏览器访问Google只会携带Google的Cookie，而不会携带Baidu的Cookie。Google也只能操作Google的Cookie，而不能操作Baidu的Cookie。

Cookie在客户端是由浏览器来管理的。浏览器能够保证Google只会操作Google的Cookie而不会操作 Baidu的Cookie，从而保证用户的隐私安全。浏览器判断一个网站是否能操作另一个网站Cookie的依据是域名。Google与Baidu的域名 不一样，因此Google不能操作Baidu的Cookie。

需要注意的是，虽然网站images.google.com与网站www.google.com同属于Google，但是域名不一样，二者同样不能互相操作彼此的Cookie。

 

注意：用户登录网站www.google.com之后会发现访问images.google.com时登录信息仍然有效，而普通的Cookie是做不到的。这是因为Google做了特殊处理。本章后面也会对Cookie做类似的处理。

 

**3 Unicode编码：保存中文**

中文与英文字符不同，**中文属于Unicode字符，在内存中占4个字符，而英文属于ASCII字符，内存中只占2个字节**。Cookie中使用Unicode字符时需要对Unicode字符进行编码，否则会乱码。

 

提示：Cookie中保存中文只能编码。一般使用UTF-8编码即可。不推荐使用GBK等中文编码，因为浏览器不一定支持，而且JavaScript也不支持GBK编码。

 

**1.1.5 BASE64编码：保存二进制图片**

Cookie不仅可以使用ASCII字符与Unicode字符，还可以使用二进制数据。例如在Cookie中使用数字证书，提供安全度。使用二进制数据时也需要进行编码。

%注意：本程序仅用于展示Cookie中可以存储二进制内容，并不实用。由于浏览器每次请求服务器都会携带Cookie，因此Cookie内容不宜过多，否则影响速度。Cookie的内容应该少而精。

 



Cookie是由W3C组织提出，最早由netscape社区发展的一种机制





- **网页之间的交互是通过HTTP协议传输数据的，而Http协议是无状态的协议**。无状态的协议是什么意思呢？一旦数据提交完后，浏览器和服务器的连接就会关闭，再次交互的时候需要重新建立新的连接。

- 服务器无法确认用户的信息，于是乎，W3C就提出了：**给每一个用户都发一个通行证，无论谁访问的时候都需要携带通行证，这样服务器就可以从通行证上确认用户的信息。通行证就是Cookie。**

  

  ## Session:服务器端记录客户端状态

  除了使用Cookie，Web应用程序中还经常使用Session来记录客户端状态。**Session是服务器端使用的一种记录客户端状态的机制**，使用上比Cookie简单一些，相应的也**增加了服务器的存储压力**。

  （存储：隐私：有效期：）

**从存储方式上比较**

- Cookie只能存储字符串，如果要存储非ASCII字符串还要对其编码。
- Session可以存储任何类型的数据，可以把Session看成是一个容器

**从隐私安全上比较**

- Cookie存储在浏览器中，对客户端是可见的。信息容易泄露出去。如果使用Cookie，最好将Cookie加密
- Session存储在服务器上，对客户端是透明的。不存在敏感信息泄露问题。

**从有效期上比较**

- Cookie保存在硬盘中，只需要设置maxAge属性为比较大的正整数，即使关闭浏览器，Cookie还是存在的
- Session的保存在服务器中，设置maxInactiveInterval属性值来确定Session的有效期。并且Session依赖于名为JSESSIONID的Cookie，该Cookie默认的maxAge属性为-1**。如果关闭了浏览器，该Session虽然没有从服务器中消亡，但也就失效了。**

**从对服务器的负担比较**

- Session是保存在服务器的，每个用户都会产生一个Session，如果是**并发访问的用户非常多**，是不能使用Session的，Session会消耗大量的内存。
- Cookie是保存在客户端的。不占用服务器的资源。像baidu、Sina这样的大型网站，一般都是使用Cookie来进行会话跟踪。

**从浏览器的支持上比较**

- 如果浏览器禁用了Cookie，那么Cookie是无用的了！
- 如果浏览器禁用了Cookie，Session可以通过URL地址重写来进行会话跟踪。

**从跨域名上比较**

- Cookie可以设置domain属性来实现跨域名
- Session只在当前的域名内有效，不可夸域名

## 10 Servlet安全性问题

由于**Servlet是单例**的，当多个用户访问Servlet的时候，服务器会为每个用户创建一个线程**。**当多个用户并发访问Servlet共享资源的时候就会出现线程安全问题。

原则：

1. 如果一个变量需要多个用户共享，则应当在访问该变量的时候，加同步机制synchronized (对象){}
2. 如果一个变量不需要共享，则直接在 doGet() 或者 doPost()定义.这样不会存在线程安全问题