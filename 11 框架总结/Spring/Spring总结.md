# 1 IOC和DI

**IOC: 控制反转**

即控制权的转移，将我们创建对象的方式反转了，以前对象的创建是由我们开发人员自己维护，包括依赖关系也是自己注入。使用了spring之后，对象的创建以及依赖关系可以由spring完成创建以及注入，反转控制就是反转了对象的创建方式，从我们自己创建反转给了程序创建(spring)

**DI:  Dependency Injection  依赖注入**

spring这个容器中，替你管理着一系列的类，前提是你需要将这些类交给spring容器进行管理，然后在你需要的时候，不是自己去定义，而是直接向spring容器索取，当spring容器知道你的需求之后，就会去它所管理的组件中进行查找，然后直接给你所需要的组件。

实现IOC思想需要DI做支持：

- 注入方式:   
  1. set方式注入    
  2. 构造方法注入   
  3. 字段注入
- 注入类型:  
  1. 值类型注入    
  2. 引用类型注入

好处: 

1. 降低组件之间的耦合度，实现软件各层之间的解耦. 
2. 可以使容器提供众多服务如事务管理消息服务处理等等。当我们使用容器管理事务时，开发人员就不需要手工 控制事务，也不需要处理复杂的事务传播

3.  容器提供单例模式支持，开发人员不需要自己编写实现代码.
4.  容器提供了AOP技术，利用它很容易实现如权限拦截，运行期监控等功能 

5. 容器提供众多的辅佐类，使这些类可以加快应用的开发.如jdbcTemplate HibernateTemplate

# 2 applicationContext & BeanFactory区别

**BeanFactory接口**

1. spring的原始接口，针对原始接口的实现类功能较为单一
2. BeanFactory接口实现类的容器，特点是每次在获得对象时才会创建对象

**ApplicationContext接口**

1. 每次容器启动时就会创建容器中配置的所有对象

2. 提供了更多功能

3. 从类路径下加载配置文件: ClassPathXmlApplicationContext

   从硬盘的绝对路径下加载配置文件:FileSystemXmlApplication

# 3 spring配置详解

## 3.1 元素属性

- bean元素:使用该元素描述需要spring容器管理对象
- name属性:给被管理的对象起个名字,获得对象时getBean("name值")
- class属性:被管理对象的完整类名
- id属性:与name属性一模一样，名称不可重复，不能使用特殊字符

**name和id的注意点：**

- 配置两个相同的 id 或者 name 都不能通过。
- 如果既配置了 id ，也配置了 name ，则两个都生效。如果id和name都没有指定，则用类全名作为name，如\<bean class="com.stamen.BeanLifeCycleImpl">,则你可以通过getBean("com.stamen.BeanLifeCycleImpl")返回该实例。
- 如果配置基本类的时候，注解和配置文件都使用的时候，注解和配置文件中 name 相同的时候， 则两个冲突，配置文件生效。
- 如果配置基本类的时候，注解和配置文件都使用的时候，注解和配置文件中 name 不相同的时候， 则两个不冲突，都能够生效。

## 3.2 bean元素进阶( scope属性生命周期属性)——单例多例

### 3.2.1 scope属性

1. singleton（默认值）单例对象：被标识为单例的对象在spring容器中只会存在一个实例
2. prototype 多例原型：被标识为多例的对象,每次在获得才会被创建,每次创建都是新的对象
3. request：Web环境下,对象与request生命周期一致    
4. session：Web环境下,对象与session生命周期一致

总结：绝大多数情况下，使用单例singleton(默认值)，但是在与struts整合时候，务必要用prototype多例，因为struts2在每次请求都会创建一个新的Action，若为单例，在多请求情况下，每个请求找找spring拿的都是同一个action。

### 3.2.2 生命周期属性(了解)——初始化和销毁

配置一个方法作为生命周期初始化方法,spring会在对象创建之后立刻调用 init-method，对应注解为@PostConstruct

配置一个方法作为生命周期的销毁方法,spring容器在关闭并销毁所有容器中的对象之前调用destory-method，对应注解为@PreDestory

```xml
<bean init-method=“init”  destory-method=“destory”></bean>        
```

### 3.2.3 模块化配置,即分模块配置(导入其他spring配置文件)

```xml
<beans>
    <import resource = “spring配置文件的全路径名” />
</beans>
```

## 3.3 spring三种对象的创建方式

### 3.3.1 空参数构造(重要)

```xml
<bean name="hello" class="cn.itcasts.spring.hello.HelloWord"></bean>
```

### 3.3.2 静态工厂创建(调用静态方法创建)

调用UserFactory类的静态createUser方法创建名为user的对象,放入容器

```xml
<bean name="user" class="cn.itcats.UserFactory" factory-method="createUser"></bean>
```

### 3.3.3 实例工厂创建(调用非静态方法创建)

需要配置两个bean，因为无法通过类名调用非静态方法

```xml
<bean name="user2" factory-bean="userFactory" factory-method="createUser"></bean>
<bean name="userFactory" class="cn.itcats.UserFactory"></bean>
```

## 3.4 spring注入方式

### 3.4.1 set方式注入(重点)

**值类型用value注入 ，引用类型用ref注入**

```xml
<bean name="student" class="cn.itcast.spring.domain.Student">
    <!--值类型注入：为Student对象中名为name属性注入fengce作为值-->
    <property name="name" value="fengce"></property>
    <property name="grade" value="60"></property>
    <!--引用类型注入：为teacher属性注入下方配置的teacher对象-->
    <property name="teacher" value="teacher"></property>
</bean>

<bean name="teacher" class="cn.itcast.spring.domain.Teacher">
    <property name="name" value="wanglaoshi"></property>
    <property name="className" value="cs1"></property>
</bean>
```

### 3.4.2 构造方法注入

```xml
<bean name="student2" class="cn.itcast.spring.domain.Student">
	<constructor-arg name="name" index="1" type="java.lang.String" value="wangzhuoxi"></constructor-arg>
    <constructor-arg name="grade" index="0" type="java.lang.Integer" value="20"></constructor-arg>
    <constructor-arg name="teacher" index="2" ref="teacher"></constructor-arg>
</bean>
```

### 3.4.3 函数注入

#### 1 p名称空间注入

实际上就是set注入，spring特有，为了简化\<property>写法

1. applicationContext.xml中\<beans>标签头部导入p命名空间

```xml
xmlns:p="http://www.springframework.org/schema/p"
```

2. 书写格式：

   值类型注入——  p:属性名="值"      

   引用类型注入——  p:属性名-ref="引用的\<bean> name属性"

```xml
<bean name="run2" class="cn.itcats.thread.Run" p:name="haha" p:age="20" p:hello-ref="hello"></bean>
```

#### 2 spel注入

spel：spring Expression Language spring表达式语言

```xml
<bean name="runSpel" class="cn.itcats.thread.Run">
    <!-- 取bean标签中name为"user"中property为"name"中的value值 -->
    <property name="name" value="#{user.name}"></property>
</bean>
```

SpEL特性：

1. 使用Bean的ID来引用Bean；
2. 调用方法和访问对象的属性；
3. 对值进行算术、关系和逻辑运算；
4. 正则表达式匹配；
5. 集合操作

### 3.4.4 复杂类型注入

#### 1 array数组的注入

```xml
<bean name="cb" class="cn.itcast.spring.domain.CollectionBean">
    <!--如果数组中只准备注入一个值（对象），直接使用value|ref即可-->
    <property name="arr">
    	<array>
            <!--array数组中值类型注入方式-->
        	<value>value1</value>
            <value>value2</value>
            <!--arrray数组中引用类型注入方式-->
            <ref bean="teacher"></ref>
        </array>
    </property>
</bean>
```

#### 2 list集合的注入

```xml
<property name="list">
	<list>
    	<value>listV1</value>
        <value>listV2</value>
        <ref bean="teacher"></ref>
    </list>
</property>
```

#### 3 map集合的注入

```xml
<property name="map">
	<map>
    	<entry key="mapK1" value="mapV1"></entry>
        <entry key="mapK1" value-ref="teacher"></entry>
        <entry key-ref="student" value-ref="teacher"></entry>
    </map>
</property>
```

#### 4 properties的注入

```xml
<property>
	<props>
    	<prop key="driverClass">com.jdbc.mysql.Driver</prop>
        <prop key="username">root</prop>
        <prop key="password">1234</prop>
    </props>
</property>
```

# 4 web.xml

防止创建多个applicationContext取值，并指定记载spring配置文件的位置——web.xml

1. 需要导入包spring-web
2. 在web.xml中配置**监听器**

![img](https://img-blog.csdn.net/20180807154046934?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2l0Y2F0c19jbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

# 5 使用注解方式代替配置文件

## 5.1 在applicationContext.xml中书写指定扫描注解

```xml
<!--指定扫描cn.itcast.spring.domian包下所有类的注解-->
<!--扫描包时，会扫描指定包下的所有子孙包-->
<context:component-scan base-package="cn.itcast.spring.domain"></context:component-scan>
```

## 5.2 在类中书写Component

```java
//相当于<bean name="student" class="cn.itcast.spring.domain.Student">
@Component("student")
@Service("student")		//service层
@Controller("student")	//web层
@Repository("student")	//dao层
```

注意：假如不写括号内的值(即name或id)，默认使用类名首字母小写作为搜索，什么意思呢？

比如Student类中使用了@Component   没有书写括号和值，那么默认搜索id或name为student。

## 5.3 指定对象的作用范围Scope

```java
//指定对象的作用范围
@Scope(scopeName="prototype")	//声明Student类对象为多例
```

singleton作用域：当把一个Bean定义设置为singleton作用域是，Spring IoC容器中只会存在一个共享的Bean实例，并且所有对Bean的请求，只要id与该Bean定义相匹配，则只会返回该Bean的同一实例。值得强调的是singleton作用域是Spring中的缺省作用域。

prototype作用域：prototype作用域的Bean会导致在每次对该Bean请求（将其注入到另一个Bean中，或者以程序的方式调用容器的getBean()方法）时都会创建一个新的Bean实例。根据经验，对有状态的Bean应使用prototype作用域，而对无状态的Bean则应该使用singleton作用域。对于具有prototype作用域的Bean，有一点很重要，即Spring不能对该Bean的整个生命周期负责。具有prototype作用域的Bean创建后交由调用者负责销毁对象回收资源。简单的说：

- singleton：只有一个实例，也即是单例模式。

- prototype：访问一次创建一个实例，相当于new。

## 5.4 值类型的注入

实际通过反射field赋值

```java
@Value("tom")
private String name;
```

实际通过set方式赋值

```java
@Value("tom")
public void setName(String name){
    this.name=name;
}
```

## 5.5 引用类型的注入

面试题: @AutoWired和@Resource的区别?

@AutoWired默认以类型进行查找，@Resource默认以名称进行查找

@AutoWired(required=false)    +   @Qualifier("user")    ==   @Resource(name="user")

其中@Resource注解是jdk1.6后才有的

```java
@Autowired //自动装配
// 问题：如果匹配多个类型一致的对象，将无法选择具体注入哪个对象
@Qualifier("teacher")	//使用@Qualifier注解告诉spring容器自动装配哪个名称的对象
private Teacher teacher;
```

```java
@Resource(name="tacher")	//手动注入，指定注入哪个名称的对象
private Teacher teacher;
```

## 5.6 创建与销毁方法

```java
@PostConstruct	//在对象被创建后调用.init-method
public void init(){
    System.out.println("初始化方法");
}
```

```java
@PreDestory		//在对象销毁之前调用.destory-method
public void destory(){
    System.out.println("销毁方法");
}
```

## 5.7 spring整合junit测试

```java
@RunWith(SpringJUnit4ClassRunner.class)	//帮我们创建容器
@ContextConfiguration("classpath:applicationContext.xml")//指定创建容器时使用哪个配置文件
public class AnnotationTest{
    //将名为student的对象注入到student中
    @Resource(name="student")
    private Student student;
    
    @Test
    public void testFindAll(){
        //...
    }
}
```

# 6 spring中AOP名词解释

**JoinPoint(连接点)**

目标对象中,所有可以增强的方法，就是spring允许你是通知（Advice）的地方，那可就真多了，基本每个方法的前、后（两者都有也行），或抛出异常是时都可以是连接点，spring只支持方法连接点。

**Pointcut(切入点)**

目标对象中,已经被增强的方法。调用这几个方法之前、之后或者抛出异常时干点什么，那么就用切入点来定义这几个方法。

**Advice(通知/增强)** 

增强方法的代码、想要的功能。

**Target(目标对象)**

被代理对象，被通知的对象，被增强的类对象。

**Weaving(织入)**

将通知应用到连接点形成切入点的过程

**Proxy(代理)**

将通知织入到目标对象之后形成的代理对象

**aspect(切面)**

切入点+通知——通知(Advice)说明了干什么的内容(即方法体代码)和什么时候干（什么时候通过方法名中的before，after，around等就能知道），二切入点说明了在哪干（指定到底是哪个方法），切点表达式等定义。

虽然现在都用Maven项目构建，但是不能忘记，使用aop需要用到的包：

- spring-aop
- spring-aspects 
- springsource.org.aopalliance
- springsource.org.aspectj.weaver 

**AOP实例**

## 6.1 准备目标对象

目标对象：被代理对象，被通知的对象，被增强的类对象

![img](https://img-blog.csdn.net/20180807164638982?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2l0Y2F0c19jbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

## 6.2 准备通知

通知/增强：被增强方法的代码，想要实现功能的方法代码

![img](https://img-blog.csdn.net/20180807164847918?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2l0Y2F0c19jbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

## 6.3 配置 applicationContext.xml

1. 导入aop(约束)命名空间
2. 配置目标对象
3. 配置通知对象
4. 配置将通知织入目标对象

![img](https://img-blog.csdn.net/20180807165310404?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2l0Y2F0c19jbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

## 6.4 测试

![img](https://img-blog.csdn.net/20180807170443480?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2l0Y2F0c19jbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

总结：通知的几种类型

1. 前置通知——目标方法运行之前调用
2. 后置通知——目标方法运行之后调用(如果出现异常不调用)
3. 环绕通知——目标方法之前和之后都调用
4. 异常拦截通知——如果出现异常，就会调用
5. 最终通知——目标方法运行之后调用(无论是否出现异常都会调用)

# 7 spring中的aop使用注解配置

## 7.1 applicationContext.xml配置

applicationContext.xml中配置目标对象，通知对象，开启使用注解完成织入

![img](https://img-blog.csdn.net/20180807172145664?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2l0Y2F0c19jbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

## 7.2 通知类注解

@Aspect注解代表该类是个通知类，书写切点表达式@Pointcut("execution(返回值 全类名.方法名(参数))")

![img](https://img-blog.csdn.net/20180807172350874?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2l0Y2F0c19jbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

注意环绕通知需要这么写：

```java
public void around(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {
    //环绕方法执行前
    //proceedingJoinPoint.proceed();表示对拦截的方法进行放行
    //若注释proceedingJoinPoint.proceed()则不会执行被AOP匹配的方法
    proceedingJoinPoint.proceed();
    //环绕方法执行后
}
```

AOP注解解析：

@Before 前置通知（Before advice）

- 在某连接点（JoinPoint）——核心代码（类或者方法）之前执行的通知，但这个通知不能阻止连接点前的执行。为啥不能阻止线程进入核心代码呢？因为@Before注解的方法入参不能传ProceedingJoinPoint，而只能传入JoinPoint。要知道从aop走到核心代码就是通过调用ProceedingJionPoint的proceed()方法。而JoinPoint没有这个方法。 
- 这里牵扯区别这两个类：Proceedingjoinpoint 继承了 JoinPoint 。是在JoinPoint的基础上暴露出 proceed 这个方法。proceed很重要，这个是aop代理链执行的方法。暴露出这个方法，就能支持 aop:around 这种切面（而其他的几种切面只需要用到JoinPoint，这跟切面类型有关）， 能决定是否走代理链还是走自己拦截的其他逻辑。建议看一下 JdkDynamicAopProxy的invoke方法，了解一下代理链的执行原理。这样你就能明白 proceed方法的重要性。

@After 后通知（After advice）

- 当某连接点退出的时候执行的通知（不论是正常返回还是异常退出）。

@AfterReturning 返回后通知（After return advice）

- 在某连接点正常完成后执行的通知，不包括抛出异常的情况。

@Around 环绕通知（Around advice）

- 包围一个连接点的通知，类似Web中Servlet规范中的Filter的doFilter方法。可以在方法的调用前后完成自定义的行为，也可以选择不执行。这时aop的最重要的，最常用的注解。用这个注解的方法入参传的是ProceedingJionPoint pjp，可以决定当前线程能否进入核心方法中——通过调用pjp.proceed();

@AfterThrowing 抛出异常后通知（After throwing advice）

- 在方法抛出异常退出时执行的通知。

# 8 spring整合jdbc

spring中提供了一个可以操作数据库的对象，对象封装了jdbc技术  ————JDBCTemplate JDBC模板对象，而JdbcDaoSupport则对JdbcTemplate进行了封装，所以要操作JdbcTemplate，或只需要继承JdbcDaoSupport即可。

![img](https://img-blog.csdn.net/20180807174204755?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2l0Y2F0c19jbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![img](https://img-blog.csdn.net/2018080717424540?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2l0Y2F0c19jbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

**依赖关系配置：**

![img](https://img-blog.csdn.net/20180807175348552?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2l0Y2F0c19jbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

**测试：**

![img](https://img-blog.csdn.net/20180807175618666?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2l0Y2F0c19jbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

# 9 spring中的aop事务

![img](https://img-blog.csdn.net/20180807195216838?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2l0Y2F0c19jbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

## 9.1 事务的四大基本特性

### 9.1.1 原子性（Atomicity）

原子性是指事务包含的所有操作要么全部成功，要么全部失败回滚，因此事务的操作如果成功就必须要完全应用到数据库，如果操作失败则不能对数据库有任何影响。

### 9.1.1 一致性（Consistency）

一致性是指事务必须使数据库从一个一致性状态变换到另一个一致性状态，也就是说一个事务执行之前和执行之后都必须处于一致性状态。

拿转账来说，假设用户A和用户B两者的钱加起来一共是5000，那么不管A和B之间如何转账，转几次账，事务结束后两个用户的钱相加起来应该还得是5000，这就是事务的一致性。

### 9.1.1 隔离性（Isolation）

隔离性是当多个用户并发访问数据库时，比如操作同一张表时，数据库为每一个用户开启的事务，不能被其他事务的操作所干扰，多个并发事务之间要相互隔离。

即要达到这么一种效果：对于任意两个并发的事务T1和T2，在事务T1看来，T2要么在T1开始之前就已经结束，要么在T1结束之后才开始，这样每个事务都感觉不到有其他事务在并发地执行。

关于事务的隔离性数据库提供了多种隔离级别，稍后会介绍到。

### 9.1.1 持久性（Durability）

持久性是指一个事务一旦被提交了，那么对数据库中的数据的改变就是永久性的，即便是在数据库系统遇到故障的情况下也不会丢失提交事务的操作。

例如我们在使用JDBC操作数据库时，在提交事务方法后，提示用户事务操作完成，当我们程序执行完成直到看到提示后，就可以认定事务以及正确提交，即使这时候数据库出现了问题，也必须要将我们的事务完全执行完成，否则就会造成我们看到提示事务处理完毕，但是数据库因为故障而没有执行事务的重大错误。

## 9.2 spring中事务的分类

spring中事务可以分为编程式事务控制和声明式事务控制。

### 9.2.1 编程式事务控制

自己手动控制事务，就叫做编程式事务控制。   

Jdbc代码：

```java
Conn.setAutoCommit(false);  // 设置手动控制事务
```

   Hibernate代码：

```java
Session.beginTransaction();    // 开启一个事务
```

细粒度的事务控制： 可以对指定的方法、指定的方法的某几行添加事务控制

比较灵活，但开发起来比较繁琐： 每次都要开启、提交、回滚.

### 9.2.2 声明式事务控制

Spring提供了对事务的管理, 这个就叫声明式事务管理。

Spring提供了对事务控制的实现。用户如果想用Spring的声明式事务管理，只需要在配置文件中配置即可； 不想使用时直接移除配置。这个实现了对事务控制的最大程度的解耦。

Spring声明式事务管理，核心实现就是基于Aop。

粗粒度的事务控制： 只能给整个方法应用事务，不可以对方法的某几行应用事务。

因为aop拦截的是方法

Spring声明式事务管理器类：

- Jdbc技术：**DataSourceTransactionManager**
- Hibernate技术：**HibernateTransactionManager**

有一点需要注意的：若为**编程式事务控制**，则开启事务后一定要手动释放(提交或回滚)，否则长期占用内存，有可能报事务异常。

spring封装了事务管理的代码(打开，提交，回滚事务)

- 事务操作对象,因为在不同平台，操作事务的代码各不相同。spring提供了一个接口——PlatformTransactionManager接口
- 在不同平台,实现不同的接口即可
- 注意：在spring中玩事务管理.最为核心的对象就是TransactionManager对象

![img](https://img-blog.csdn.net/20180807234619190?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2l0Y2F0c19jbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

spring管理事务的属性介绍

1. 事务的隔离级别
2. 是否只读
3. 事务的传播行为

配置事务的核心管理器，它封装了所有事务，依赖于连接池(DataSourceTransactionManager)

![img](https://img-blog.csdn.net/2018080723485451?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2l0Y2F0c19jbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

xml中配置通知

![img](https://img-blog.csdn.net/201808072350066?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2l0Y2F0c19jbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

配置将通知织入目标

![img](https://img-blog.csdn.net/20180808000556851?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2l0Y2F0c19jbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

# 10 spring中aop管理事务 注解使用步骤

![img](https://img-blog.csdn.net/20180808000840926?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2l0Y2F0c19jbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

在需要管理的方法或者类中声明配置事务管理。

```java
@Transactional(isolation=Isolation.REPEATABLE_READ,readOnly=false,propagation=Propagation.REQUIRED)
```

![img](https://img-blog.csdn.net/20180808001009352?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2l0Y2F0c19jbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

