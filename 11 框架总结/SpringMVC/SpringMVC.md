### 三层架构

**表现层**：也就是我们常说的web层。它负责接收客户端请求，向客户端响应结果，通常客户端使用http协议请求web 层，web 需要接收 http 请求，完成 http 响应。表现层包括展示层和控制层：控制层负责接收请求，展示层负责结果的展示。表现层依赖业务层，接收到客户端请求一般会调用业务层进行业务处理，并将处理结果响应给客户端。表现层的设计一般都使用 MVC 模型。（MVC 是表现层的设计模型，和其他层没有关系）

**业务层**：也就是我们常说的 service 层。它负责业务逻辑处理，和我们开发项目的需求息息相关。web 层依赖业务层，但是业务层不依赖 web 层。业务层在业务处理时可能会依赖持久层，如果要对数据持久化需要保证事务一致性。（也就是我们说的，事务应该放到业务层来控制）

**持久层**：也就是我们是常说的 dao 层。负责数据持久化，包括数据层即数据库和数据访问层，数据库是对数据进行持久化的载体，数据访问层是业务层和持久层交互的接口，业务层需要通过数据访问层将数据持久化到数据库中。通俗的讲，持久层就是和数据库交互，对数据库表进行曾删改查的。

### MVC 模型

MVC 全名是 Model View Controller，是模型(model)－视图(view)－控制器(controller)的缩写，是一种用于设计创建 Web 应用程序表现层的模式。MVC 中每个部分各司其职：

**Model**(模型)：通常指的就是我们的数据模型。作用一般情况下用于封装数据。

**View**(视图)：通常指的就是我们的 jsp 或者 html。作用一般就是展示数据的。通常视图是依据模型数据创建的。

**Controller**(控制器)：是应用程序中处理用户交互的部分。作用一般就是处理程序逻辑的。它相对于前两个不是很好理解，这里举个例子：例如：我们要保存一个用户的信息，该用户信息中包含了姓名，性别，年龄等等。这时候表单输入要求年龄必须是 1~100 之间的整数。姓名和性别不能为空。并且把数据填充到模型之中。此时除了 js 的校验之外，服务器端也应该有数据准确性的校验，那么校验就是控制器的该做的。当校验失败后，由控制器负责把错误页面展示给使用者。如果校验成功，也是控制器负责把数据填充到模型，并且调用业务层实现完整的业务求。

### SpringMVC 是什么

Spring MVC是一个基于Java的实现了MVC设计模式的请求驱动类型的轻量级Web框架，通过把Model，View，Controller分离，将web层进行职责解耦，把复杂的web应用分成逻辑清晰的几部分，简化开发，减少出错，方便组内开发人员之间的配合。

### SpringMVC 的优势

1. 可以支持各种视图技术,而不仅仅局限于JSP；
2. 与Spring框架集成（如IoC容器、AOP等）；
3. 清晰的角色分配：前端控制器(dispatcherServlet) , 请求到处理器映射（handlerMapping), 处理器适配器（HandlerAdapter), 视图解析器（ViewResolver）。
4. 支持各种请求资源的映射策略。

### SpringMVC的流程

1. 服务器启动，应用被加载。读取到 web.xml 中的配置创建 spring 容器并且初始化容器中的对象。
2. 浏览器发送请求，被 DispatherServlet 捕获，该 Servlet 并不处理请求，而是把请求转发出去。转发
   的路径是根据请求 URL，匹配@RequestMapping 中的内容。
3. 匹配到了后，执行对应方法。该方法有一个返回值。
4. 根据方法的返回值，借助 InternalResourceViewResolver 找到对应的结果视图。
5. 渲染结果视图，响应浏览器。

------

1. 用户发送请求至前端控制器DispatcherServlet；
2. DispatcherServlet收到请求后，调用HandlerMapping处理器映射器，请求获取Handle；
3. 处理器映射器根据请求url找到具体的处理器，生成处理器对象及处理器拦截器(如果有则生成)一并返回给DispatcherServlet；
4. DispatcherServlet 调用 HandlerAdapter处理器适配器；
5. HandlerAdapter 经过适配调用 具体处理器(Handler，也叫后端控制器)；
6. Handler执行完成返回ModelAndView；
7. HandlerAdapter将Handler执行结果ModelAndView返回给DispatcherServlet；
8. DispatcherServlet将ModelAndView传给ViewResolver视图解析器进行解析；
9. ViewResolver解析后返回具体View；
10. DispatcherServlet对View进行渲染视图（即将模型数据填充至视图中）
11. DispatcherServlet响应用户。

![img](https://img-blog.csdn.net/20180708224853769?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2E3NDUyMzM3MDA=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### Spring MVC的主要组件

**DispatcherServlet：前端控制器**

用户请求到达前端控制器，它就相当于 mvc 模式中的 c，dispatcherServlet 是整个流程控制的中心，由
它调用其它组件处理用户的请求，dispatcherServlet 的存在降低了组件之间的耦合性。

**HandlerMapping：处理器映射器**

HandlerMapping 负责根据用户请求找到 Handler 即处理器，SpringMVC 提供了不同的映射器实现不同的
映射方式，例如：配置文件方式，实现接口方式，注解方式等。

**Handler：处理器**

它就是我们开发中要编写的具体业务控制器。由 DispatcherServlet 把用户请求转发到 Handler。由
Handler 对具体的用户请求进行处理。

**HandlAdapter：处理器适配器**

通过 HandlerAdapter 对处理器进行执行，这是适配器模式的应用，通过扩展适配器可以对更多类型的处理
器进行执行。

**View Resolver：视图解析器**

View Resolver 负责将处理结果生成 View 视图，View Resolver 首先根据逻辑视图名解析成物理视图名
即具体的页面地址，再生成 View 视图对象，最后对 View 进行渲染将处理结果通过页面展示给用户。

**View：视图**

SpringMVC 框架提供了很多的 View 视图类型的支持，包括：jstlView、freemarkerView、pdfView
等。我们最常用的视图就是 jsp。
一般情况下需要通过页面标签或页面模版技术将模型数据通过页面展示给用户，需要由程序员根据业务需求开
发具体的页面。

**\<mvc:annotation-driven>说明**

在 SpringMVC 的各个组件中，处理器映射器、处理器适配器、视图解析器称为 SpringMVC 的三大组件。
使用自动加载RequestMappingHandlerMapping(处理映射器)和RequestMappingHandlerAdapter(处理适配 器)，可用在SpringMVC.xml 配置文件中使用替代注解处理器和适配器的配置。

### RequestMapping注解

作用：用于建立请求 URL 和处理请求方法之间的对应关系。

出现位置：
类上：请求 URL 的第一级访问目录。此处不写的话，就相当于应用的根目录。写的话需要以/开头。它出现的目的是为了使我们的 URL 可以按照模块化管理:
方法上：请求 URL 的第二级访问目录。

属性：以下四个属性只要出现 2 个或以上时，他们的关系是与的关系。

1. **value**：用于指定请求的 URL。它和 path 属性的作用是一样的。
2. **method**：用于指定请求的方式。
3. **headers**：用于指定限制请求消息头的条件。
4. **params**：用于指定限制请求参数的条件。它支持简单的表达式。要求请求参数的 key 和 value 必须和
   配置的一模一样。例如：params = {"accountName"}，表示请求参数必须有 accountName；params = {"moeny!100"}，表示请求参数中 money 不能是 100。

### 请求参数的绑定

**绑定的机制**：表单中请求参数都是基于 key=value 的。SpringMVC 绑定请求参数的过程是通过把表单提交请求参数，作为控制器中方法参数进行绑定的。

**支持的数据类型**

1. 基本类型参数：包括基本类型和 String 类型
   要求：参数名称必须和控制器中方法的形参名称保持一致。(严格区分大小写)
2. POJO 类型参数：包括实体类，以及关联的实体类
   要求：表单中参数名称和 POJO 类的属性名称保持一致。并且控制器方法的参数类型是 POJO 类型。
3. 数组和集合类型参数：包括 List 结构和 Map 结构的集合（包括数组）
   第一种：要求集合类型的请求参数必须在 POJO 中。在表单中请求参数名称要和 POJO 中集合属性名称相同。给 List 集合中的元素赋值，使用下标。给 Map 集合中的元素赋值，使用键值对。
   第二种：接收的请求参数是 json 格式数据。需要借助一个注解实现。

SpringMVC 绑定请求参数是自动实现的，但是要想使用，必须遵循使用要求。

**请求参数乱码问题**

post 请求方式：在 web.xml 中配置一个过滤器；

get 请求方式：tomacat 对GET和POST请求处理方式是不同的，GET请求的编码问题，要改tomcat 的 server.xml

**自定义类型转换器**

情景：前端提交的是字符串格式的日期(2019-9-18)，控制器方法的参数是Date类型，需要自定义类型转换器；

使用步骤：

1. 定义一个类，实现Converter接口，该接口有两个泛型，S:表示接受的类型，T：表示目标类型；

```java
public interface Converter<S, T> {//S:表示接受的类型，T：表示目标类型
    //实现类型转换的方法
    @Nullable
    T convert(S source);
}
```

2. 在spring配置文件中配置类型转换器。spring 配置类型转换器的机制是，将自定义的转换器注册到类型转换服务中去；

```xml
<!-- 配置类型转换器工厂 -->
<bean id="converterService"
      class="org.springframework.context.support.ConversionServiceFactoryBean">
    <!-- 给工厂注入一个新的类型转换器 -->
    <property name="converters">
        <array>
            <!-- 配置自定义类型转换器 -->
            <bean class="com.itheima.web.converter.StringToDateConverter"></bean>
        </array>
    </property>
</bean>
```

3. 在annotation-driven标签中引用配置的类型转换服务；

```xml
<!-- 引用自定义类型转换器 -->
<mvc:annotation-driven conversion-service="converterService"></mvc:annotation-driven>
```

**使用 ServletAPI 对象作为方法参数**

SpringMVC 还支持使用原始 ServletAPI 对象作为控制器方法的参数，我们可以把上述对象，直接写在控制的方法参数中使用。常用的ServletAPI对象有：

- HttpServletRequest
- HttpServletResponse
- HttpSession

### 常用注解

**RequestParam**

作用：把请求中指定名称的参数给控制器中的形参赋值(放在方法形参前面)。

属性：

- value：请求参数中的名称。
- required：请求参数中是否必须提供此参数。默认值：true。表示必须提供，如果不提供将报错。

**RequestBody**

作用：用于获取请求体内容。直接使用得到是 key=value&key=value...结构的数据。get 请求方式不适用。

属性：

- required：是否必须有请求体。默认值是:true。当取值为 true 时,get 请求方式会报错。如果取值为 false，get 请求得到是 null。

**PathVaribale**

作用：用于绑定 url 中的占位符。例如：请求 url 中 /delete/{id}，这个{id}就是 url 占位符。url 支持占位符是 spring3.0 之后加入的。是 springmvc 支持 rest 风格 URL 的一个重要标志。

属性：

- value：用于指定 url 中占位符名称。
- required：是否必须提供占位符。

**RequestHeader**

作用：用于获取请求消息头。(一般不用)

属性：

- value：提供消息头名称
- required：是否必须有此消息头

**CookieValue**

作用：用于把指定 cookie 名称的值传入控制器方法参数。

属性：

- value：指定 cookie 的名称。
- required：是否必须有此 cookie。

**ModelAttribute**

作用：该注解是 SpringMVC4.3 版本以后新加入的。它可以用于修饰**方法**和**参数**。出现在方法上，表示当前方法会在控制器的方法执行之前，先执行。它可以修饰没有返回值的方法，也可以修饰有具体返回值的方法。出现在参数上，获取指定的数据给参数赋值。

属性：

- value：用于获取数据的 key。key 可以是 POJO 的属性名称，也可以是 map 结构的 key。

应用场景：

当表单提交数据不是完整的实体类数据时，保证没有提交数据的字段使用数据库对象原来的数据。例如：我们在编辑一个用户时，用户有一个创建信息字段，该字段的值是不允许被修改的。在提交表单数据是肯定没有此字段的内容，一旦更新会把该字段内容置为 null，此时就可以使用此注解解决问题。

**SessionAttribute**

作用：用于多次执行控制器方法间的参数共享。

属性：

- value：用于指定存入的属性名称
- type：用于指定存入的数据类型。

### 响应数据和结果视图

**返回值分类**

1. 字符串：controller 方法返回字符串可以指定逻辑视图名，通过视图解析器解析为物理视图地址。

2. void：Servlet 原始 API 可以作为控制器中方法的参数，在 controller 方法形参上可以定义 request 和response，使用 request 或 response 指定响应结果：

```java
// 1.使用 request 转向页面，如下：
request.getRequestDispatcher("/WEB-INF/pages/success.jsp").forward(request,response);
// 2.通过 response 页面重定向：
response.sendRedirect("testRetrunString")
// 3.通过 response 指定响应结果，例如响应 json 数据：
response.setCharacterEncoding("utf-8");
response.setContentType("application/json;charset=utf-8");
response.getWriter().write("json 串");
```

3. ModelAndView：SpringMVC 为我们提供的一个对象，该对象也可以用作控制器方法的返回值。该对象有两个方法：

```java
// 添加键值对属性（前端通过${attributeName}方式获取）
public ModelAndView addObject(String attributeName,Object attributeValue);
// 设置逻辑视图名称，视图解析器会根据名称前往执行的视图
public void setViewName(String viewName);
```

**转发和重定向**

forward 转发：controller 方法在提供了 String 类型的返回值之后，默认就是请求转发。

`return "forward:/WEB-INF/pages/success.jsp";`

需要注意的是，如果用了 formward：则路径必须写成实际视图 url，不能写逻辑视图。它相于 `request.getRequestDispatcher("url").forward(request,response)`

使用请求转发，既可以转发到 jsp，也可以转发到其他的控制器方法。

Redirect 重定向：contrller 方法提供了一个 String 类型返回值之后，它需要在返回值里使用:redirect:

`return "redirect:testReturnModelAndView";`

它相当于“response.sendRedirect(url)”。需要注意的是，如果是重定向到 jsp 页面，则 jsp 页面不
能写在 WEB-INF 目录中，否则无法找到。

### 异常处理

系统中异常包括两类：**预期异常**和**运行时异常**RuntimeException，前者通过捕获异常从而获取异常信息，后者主要通过规范代码开发、测试通过手段减少运行时异常的发生。

系统的 dao、service、controller 出现都通过 throws Exception 向上抛出，最后由 springmvc 前端控制器交由异常处理器进行异常处理；

### 拦截器

**拦截器的作用**

Spring MVC 的处理器拦截器类似于 Servlet 开发中的过滤器 Filter，用于对处理器进行预处理和后处理。

用户可以自己定义一些拦截器来实现特定的功能。

谈到拦截器，还要向大家提一个词——拦截器链（Interceptor Chain）。拦截器链就是将拦截器按一定的顺序联结成一条链。在访问被拦截的方法或字段时，拦截器链中的拦截器就会按其之前定义的顺序被调用。

拦截器和过滤器的区别：

- 过滤器是 servlet 规范中的一部分，任何 java web 工程都可以使用。
- 拦截器是 SpringMVC 框架自己的，只有使用了 SpringMVC 框架的工程才能用。
- 过滤器在 url-pattern 中配置了/*之后，可以对所有要访问的资源拦截。
- 拦截器它是只会拦截访问的控制器方法，如果访问的是 jsp，html,css,image 或者 js 是不会进行拦截的。

它也是 AOP 思想的具体应用。我们要想自定义拦截器， 要求必须实现：HandlerInterceptor 接口。

**拦截器的放行**

放行的含义是指，如果有下一个拦截器就执行下一个，如果该拦截器处于拦截器链的最后一个，则执行控制器中的方法。

**拦截器中方法的说明**

```java
default boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)throws Exception {
    return true;
}
```

如何调用：按拦截器定义顺序调用

何时调用：只要配置了都会调用

有什么用：如果程序员决定该拦截器对请求进行拦截处理后还要调用其他的拦截器，或者是业务处理器去进行处理，则返回 true。如果程序员决定不需要再调用其他的组件去处理请求，则返回 false。

```java
default void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler,@Nullable ModelAndView modelAndView) throws Exception {
}
```

如何调用：按拦截器定义逆序调用

何时调用：在拦截器链内所有拦截器返成功调用

有什么用：在业务处理器处理完请求后，但是 DispatcherServlet 向客户端返回响应前被调用，在该方法中对用户请求 request 进行处理。

```java
default void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler,@Nullable Exception ex) throws Exception {
}
```

 如何调用：按拦截器定义逆序调用

何时调用：只有 preHandle 返回 true 才调用

有什么用：在 DispatcherServlet 完全处理完请求后被调用，可以在该方法中进行一些资源清理的操作。

**多个拦截器的执行顺序**

多个拦截器是按照配置的顺序决定的。

------

### SpringMvc的控制器是不是单例模式,如果是,有什么问题,怎么解决？

是单例模式,所以在多线程访问的时候有线程安全问题,不要用同步,会影响性能的

解决方案是在控制器里面不能写字段。

### SpringMVC常用的注解有哪些？

- **@RequestMapping**：用于处理请求 url 映射的注解，可用于类或方法上。用于类上，则表示类中的所有响应请求的方法都是以该地址作为父路径。
- **@RequestBody**：注解实现接收http请求的json数据，将json转换为java对象。
- **@ResponseBody**：注解实现将conreoller方法返回对象转化为json对象响应给客户。

### SpingMvc中的控制器的注解一般用那个,有没有别的注解可以替代？

一般用@Conntroller注解,表示是表现层,不能用别的注解代替。

### 如果在拦截请求中，我想拦截get方式提交的方法,怎么配置？

可以在@RequestMapping注解里面加上method=RequestMethod.GET。

### 怎样在方法里面得到Request,或者Session？

直接在方法的形参中声明request,SpringMvc就自动把request对象传入。

### 如果想在拦截的方法里面得到从前台传入的参数,怎么得到？

直接在方法中声明这个对象,SpringMvc就自动会把属性赋值到这个对象里面。

### 如果前台有很多个参数传入,并且这些参数都是一个对象的,那么怎么样快速得到这个对象？

直接在方法中声明这个对象,SpringMvc就自动会把属性赋值到这个对象里面。

### SpringMvc中函数的返回值是什么？

返回值可以有很多类型,有String, ModelAndView。ModelAndView类把视图和数据都合并的一起的，但一般用String比较好。

### SpringMvc用什么对象从后台向前台传递数据的？

通过ModelMap对象,可以在这个对象里面调用put方法,把对象加到里面,前台就可以通过el表达式拿到。

### 怎么样把ModelMap里面的数据放入Session里面？

可以在类上面加上@SessionAttributes注解,里面包含的字符串就是要放入session里面的key。

### SpringMvc里面拦截器是怎么写的：

有两种写法,一种是实现HandlerInterceptor接口，另外一种是继承适配器类，接着在接口方法当中，实现处理逻辑；然后在SpringMvc的配置文件中配置拦截器即可：

```xml
<!-- 配置SpringMvc的拦截器 -->
<mvc:interceptors>
    <!-- 配置一个拦截器的Bean就可以了 默认是对所有请求都拦截 -->
    <bean id="myInterceptor" class="com.zwp.action.MyHandlerInterceptor"></bean>
    <!-- 只针对部分请求拦截 -->
    <mvc:interceptor>
        <mvc:mapping path="/modelMap.do" />
        <bean class="com.zwp.action.MyHandlerInterceptorAdapter" />
    </mvc:interceptor>
</mvc:interceptors>
```

### 注解原理：

注解本质是一个继承了Annotation的特殊接口，其具体实现类是Java运行时生成的动态代理类。我们通过反射获取注解时，返回的是Java运行时生成的动态代理对象。通过代理对象调用自定义注解的方法，会最终调用AnnotationInvocationHandler的invoke方法。该方法会从memberValues这个Map中索引出对应的值。而memberValues的来源是Java常量池。