## 数据源 c3p0 druid DBCP

Java的数据库连接性能对比
JDBC:
jdbc - 全名是 Java data base connectivity；翻译为 Java数据库连接
它是一个面向对象的程序接口（API）;可以通过它访问到各类的 关系型数据库[注意：关系型数据库]
它不属于某一个数据库的接口，而是可以用于定义程序与数据库连接规范，通过一整套接口，由各个不同的数据库厂商去完成所对应的实现类，由sun公司提出！
步骤:

1. 类加载

2. 获取连接

3. 书写SQL

4. 执行语句

5. 处理结果集





为什么会有连接池的存在？
因为***建立数据库连接是一个非常耗时、耗资源的行为，所以通过连接池预先同数据库建立一些连接***，放在内存中，应用程序需要建立数据库连接时直接**到连接池中申请一个就行，**用完后再放回去，极大的提高了数据库连接的性能问题，节省了资源和时间。

数据源
什么是数据源
JDBC2.0 提供了javax.sql.DataSource接口，它负责建立与数据库的连接，当在应用程序中访问数据库时 不必编写连接数据库的代码，直接引用DataSource获取数据库的连接对象即可。**用于获取操作数据Connection对象**。

数据源与数据库连接池组件
***数据源建立多个数据库连接***，这些数据库连接会保存在数据库连接池中，当需要访问数据库时，只需要从数据库连接池中
获取空闲的数据库连接，当程序访问数据库结束时，数据库连接会放回数据库连接池中。

![image-20200703084003638](C:\Users\wangdamei\AppData\Roaming\Typora\typora-user-images\image-20200703084003638.png)

常用的数据库连接池技术：
C3P0、DBCP、Proxool和Druid

1. C3P0、DBCP和Druid是什么？
c3p0是一个开放源代码的JDBC连接池，它在lib目录中与Hibernate一起发布,包括实现了数据源和JNDI绑定，

hibernate开发组推荐使用c3p0;
c3p0所需jar：c3p0-0.9.2.1.jar mchange-commons-java-0.2.3.4.jar

DBCP是 apache 上的一个 java 连接池项目,是一个依赖Jakarta commons-pool对象池机制的数据库连接池.DBCP可以直接的在应用程序中使用，Tomcat的数据源使用的就是DBCP

dbcp所需jar：commons-dbcp.jar和commons-pool.jar
Druid是阿里巴巴出品的数据源，而且是淘宝和支付宝专用数据库连接池，但它不仅仅是一个数据库连接池，它还包含一个ProxyDriver，一系列内置的JDBC组件库，一个 SQL Parser。

Druid是阿里开源的连接池，可以说是Java语言中最好的数据库连接池.Druid能够提供**强大的日志监控和扩展功能，是为监控而生**的数据库连接池！【主要是监控DB池连接和SQL的执行情况】
Druid支持所有JDBC兼容的数据库，包括Oracle、MySql、Derby、Postgresql、SQL Server、H2等等。
Druid针对Oracle和MySql做了特别优化，比如Oracle的PS Cache内存占用优化，MySql的ping检测优化。Druid提供了MySql、Oracle、Postgresql、SQL-92的SQL的完整支持，这是一个手写的高性能SQL Parser，支持Visitor模式，使得分析SQL的抽象语法树很方便
简单SQL语句执行耗时10微秒以内，复杂SQL耗时30微秒
通过Druid提供的SQL Parser可以在JDBC层拦截SQL做相应处理，比如说分库分表、审计等。
Druid防御SQL注入攻击的WallFilter就是通过Druid的SQL Parser分析语义实现的。
在Maven中的依赖为:

<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.0.9</version>
</dependency>

DRUID连接池使用的jar包：druid-1.0.9.jar


2. C3P0与DBCP的区别？
c3p0有自动回收空闲连接功能

dbcp没有自动回收空闲连接功能

两者主要是对数据连接的处理方式不同！

C3P0提供**最大空闲时间**，当连接超过最大空闲连接时间时，当前连接就会被断掉
DBCP提供了**最大连接数**，当连接数超过最大连接数时，所有连接都会被断开

3. C3P0的底层运行机制？
c3p0所引用的类是：ComboPooledDataSource
ComboPooledDataSource会从pool里获取到的connection，而这个是connection的根本应该是proxy包装的connection，会对connection的释放或者重用，是pool的管理责任：初始化池大小，维护池的大小
4. DBCP的底层运行机制？
dbcp可以采用数据源的方式进行获取连接，进行管理；

dbcp中的BasicDataSourceFactory类实现了DataSource接口，自然可以获取到数据库连接

BasicDataSourceFactory中有三种方法：getOjectInstance、createDataSource以及getProperties

通常情况，采用的是createDataSource方法读取数据库连接参数，连接事务参数 数据池连接参数等。





## JNDI

JNDI是 Java 命名与目录接口（Java Naming and Directory Interface）


要了解JNDI的作用，我们可以从“如果不用JNDI我们怎样做？用了JNDI后我们又将怎样做？”这个问题来探讨。

没有JNDI的做法：
程序员开发时，知道要开发访问MySQL数据库的应用，于是将一个对 MySQL JDBC 驱动程序类的引用进行了编码，并通过使用适当的 JDBC URL 连接到数据库。
就像以下代码这样：

- Java code

  ```Connection conn=null; try { 	Class.forName("com.mysql.jdbc.Driver", true, Thread.currentThread().getContextClassLoader()); 	conn=DriverManager.getConnection("jdbc:mysql://MyDBServer?user=xxx&password=xxx"); 	...... 	conn.close(); } catch(Exception e) { 	e.printStackTrace(); } finally { 	if(conn!=null) { 		try { 			conn.close(); 		} catch(SQLException e) {} 	}}` 


这是传统的做法，也是以前非Java程序员（如Delphi、VB等）常见的做法。这种做法一般在小规模的开发过程中不会产生问题，只要程序员熟悉Java语言、了解JDBC技术和MySQL，可以很快开发出相应的应用程序。

没有JNDI的做法存在的问题：
1、数据库服务器名称MyDBServer 、用户名和口令都可能需要改变，由此引发JDBC URL需要修改；
2、数据库可能改用别的产品，如改用DB2或者Oracle，引发JDBC驱动程序包和类名需要修改；
3、随着实际使用终端的增加，原配置的连接池参数可能需要调整；
4、......

**解决办法：**
程序员应该不需要关心“具体的数据库后台是什么？JDBC驱动程序是什么？JDBC URL格式是什么？访问数据库的用户名和口令是什么？”等等这些问题***，程序员编写的程序应该没有对 JDBC 驱动程序的引用，没有服务器名称，没有用户名称或口令 —— 甚至没有数据库池或连接管理。***而是把这些问题交给J2EE容器来配置和管理，程序员只需要对这些配置和管理进行引用即可。

由此，就有了JNDI。

**用了JNDI之后的做法：**
1.定义一个数据源，也就是JDBC引用参数，给这个数据源设置一个名称；

```xml
<?xml version="1.0" encoding="UTF-8"?>
<datasources>
<local-tx-datasource>
  <jndi-name>MySqlDS</jndi-name>
  <connection-url>jdbc:mysql://localhost:3306/lw</connection-url>
  <driver-class>com.mysql.jdbc.Driver</driver-class>
  <user-name>root</user-name>
  <password>rootpassword</password>
<exception-sorter-class-name>org.jboss.resource.adapter.jdbc.vendor.MySQLExceptionSorter</exception-sorter-class-name>
  <metadata>
  <type-mapping>mySQL</type-mapping>
  </metadata>
</local-tx-datasource>
</datasources>
```

**2、在程序中引用数据源：**

- Java code
- 

```java
Connection conn=null; 



try { 



	Context ctx = new InitialContext(); 



	Object datasourceRef = ctx.lookup("java:MySqlDS"); 



	//引用数据源 



	DataSource ds = (Datasource) datasourceRef; 



	conn = ds.getConnection(); 



	...... 



	c.close(); 



} catch(Exception e) { 



	e.printStackTrace(); 



} finally { 



	if(conn!=null) { 



		try { 



			conn.close(); 



		} catch(SQLException e) {} 



	} 



}
```