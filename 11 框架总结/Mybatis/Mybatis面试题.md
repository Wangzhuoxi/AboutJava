### 1 JDBC编程有哪些不足之处，MyBatis是如何解决这些问题的？

① 数据库链接创建、释放频繁造成系统资源浪费从而影响系统性能，如果使用数据库链接池可解决此问题。

解决：在SqlMapConfig.xml中配置数据链接池，使用连接池管理数据库链接。

  ② Sql语句写在代码中造成代码不易维护，实际应用sql变化的可能较大，sql变动需要改变java代码。

解决：将Sql语句配置在XXXXmapper.xml文件中与java代码分离。

  ③ 向sql语句传参数麻烦，因为sql语句的where条件不一定，可能多也可能少，占位符需要和参数一一对应。

解决： Mybatis自动将java对象映射至sql语句。

  ④ 对结果集解析麻烦，sql变化导致解析代码变化，且解析前需要遍历，如果能将数据库记录封装成pojo对象解析比较方便。

解决：Mybatis自动将sql执行结果映射至java对象。

### 2 MyBatis编程步骤是什么样的？

1. 创建SqlSessionFactory 
2. 通过SqlSessionFactory创建SqlSession 
3. 通过sqlsession执行数据库操作 
4. 调用session.commit()提交事务 
5. 调用session.close()关闭会话

### 3 MyBatis与Hibernate有哪些不同？

Mybatis和hibernate不同，它不完全是一个ORM框架，因为MyBatis需要程序员自己编写Sql语句，不过mybatis可以通过XML或注解方式灵活配置要运行的sql语句，并将java对象和sql语句映射生成最终执行的sql，最后将sql执行的结果再映射生成java对象。

Mybatis学习门槛低，简单易学，程序员直接编写原生态sql，可严格控制sql执行性能，灵活度高，非常适合对关系数据模型要求不高的软件开发，例如互联网软件、企业运营类软件等，因为这类软件需求变化频繁，一但需求变化要求成果输出迅速。但是灵活的前提是mybatis无法做到数据库无关性，如果需要实现支持多种数据库的软件则需要自定义多套sql映射文件，工作量大。

Hibernate对象/关系映射能力强，数据库无关性好，对于关系模型要求高的软件（例如需求固定的定制化软件）如果用hibernate开发可以节省很多代码，提高效率。但是Hibernate的缺点是学习门槛高，要精通门槛更高，而且怎么设计O/R映射，在性能和对象模型之间如何权衡，以及怎样用好Hibernate需要具有很强的经验和能力才行。

总之，按照用户的需求在有限的资源环境下只要能做出维护性、扩展性良好的软件架构都是好架构，所以框架只有适合才是最好。 

### 4 使用MyBatis的mapper接口调用时有哪些要求？

1. Mapper接口方法名和mapper.xml中定义的每个sql的id相同 
2. Mapper接口方法的输入参数类型和mapper.xml中定义的每个sql 的parameterType的类型相同 
3. Mapper接口方法的输出参数类型和mapper.xml中定义的每个sql的resultType的类型相同 
4. Mapper.xml文件中的namespace即是mapper接口的类路径。

### 5 SqlMapConfig.xml中配置有哪些内容？

SqlMapConfig.xml中配置的内容和顺序如下： 

- properties（属性）
- settings（配置）
- typeAliases（类型别名）
- typeHandlers（类型处理器）
- objectFactory（对象工厂）
- plugins（插件）
- environments（环境集合属性对象）
- environment（环境子属性对象）
- transactionManager（事务管理）
- dataSource（数据源）
- mappers（映射器）

### 6 简单的说一下MyBatis的一级缓存和二级缓存？

Mybatis首先去缓存中查询结果集，如果没有则查询数据库，如果有则从缓存取出返回结果集就不走数据库。Mybatis内部存储缓存使用一个HashMap，key为hashCode+sqlId+Sql语句。value为从查询出来映射生成的java对象

Mybatis的二级缓存即查询缓存，它的作用域是一个mapper的namespace，即在同一个namespace中查询sql可以从缓存中获取数据。二级缓存是可以跨SqlSession的。

### 7 Mapper编写有哪几种方式？

#### 1. 接口实现类继承SqlSessionDaoSupport

使用此种方法需要编写mapper接口，mapper接口实现类、mapper.xml文件

1、在sqlMapConfig.xml中配置mapper.xml的位置

```xml
<mappers>
    <mapper resource="mapper.xml文件的地址" />
    <mapper resource="mapper.xml文件的地址" />
</mappers>
```

2、定义mapper接口

3、实现类集成SqlSessionDaoSupport

mapper方法中可以this.getSqlSession()进行数据增删改查。

4、spring 配置

```xml
<bean id=" " class="mapper接口的实现">
    <property name="sqlSessionFactory" ref="sqlSessionFactory"></property>
</bean>
```

#### 2. 使用org.mybatis.spring.mapper.MapperFactoryBean

1、在sqlMapConfig.xml中配置mapper.xml的位置

如果mapper.xml和mappre接口的名称相同且在同一个目录，这里可以不用配置

```xml
<mappers>
    <mapper resource="mapper.xml文件的地址" />
    <mapper resource="mapper.xml文件的地址" />
</mappers>
```

2、定义mapper接口

**注意**

- mapper.xml中的namespace为mapper接口的地址
- mapper接口中的方法名和mapper.xml中的定义的statement的id保持一致

3、 Spring中定义

```xml
<bean id="" class="org.mybatis.spring.mapper.MapperFactoryBean">
    <property name="mapperInterface"   value="mapper接口地址" /> 
    <property name="sqlSessionFactory" ref="sqlSessionFactory" /> 
</bean>
```

#### 3. 使用mapper扫描器

1、mapper.xml文件编写，

**注意：**

- mapper.xml中的namespace为mapper接口的地址
- mapper接口中的方法名和mapper.xml中的定义的statement的id保持一致
- 如果将mapper.xml和mapper接口的名称保持一致则不用在sqlMapConfig.xml中进行配置

2、定义mapper接口

注意mapper.xml的文件名和mapper的接口名称保持一致，且放在同一个目录

3、配置mapper扫描器

```xml
<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
    <property name="basePackage" value="mapper接口包地址"></property>
    <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"/> 
</bean>
```

4、使用扫描器后从spring容器中获取mapper的实现对象 

扫描器将接口通过代理方法生成实现对象，要spring容器中自动注册，名称为mapper接口的名称。

### 8 xml 转义

方法一

| 符号   | 符号 |  转义  |
| ------ | ---: | :----: |
| 大于   |    > |  $gt;  |
| 小于   |    < |  $lt;  |
| 和     |    & | $amp;  |
| 单引号 |   '' | $apos; |
| 双引号 |   "" | $quot; |

方法二

以 `<![CDATA[ ]]>` 包裹

```xml
<if test="startDate!=null and startDate!='' ">
    AND sr.record_date <![CDATA[>=]]> #{startDate}
</if> 
```

### 9 通过 || 进行sql 拼接

```xml
<if test="personName!=null and personName!='' ">
    AND sr.person_name LIKE '%'||#{personName}||'%'
</if>
```

### 10 通用sql 定义和引用

- 定义

```xml
<sql id="Base_Column_List">
    SR.RECORD_ID, SR.PERSON_CODE,SR.PERSON_NAME, SR.DEPT_Id, SR.SCHEDULE_ID, SR.RECORD_DATE, SR.ATTEND_STATUS, SR.LEAVE_TYPE,
    SR.ATTEND_NUM, SR.DUTY_NUM, SR.HOLIDAY_NUM, SR.PUNCH_NUM, SR.OVERTIME_NUM, SR.DAY_WEEK, SR.PAID_LEAVE_FLAG,
    SR.ACCOUNT_DAYS, SR.TODAY_HOLIDAY_NUM, SR.MORROW_HOLIDAY_NUM, SR.CREATE_BY, SR.CREATE_AT, SR.UPDATE_BY,
    SR.UPDATE_AT, SR.DELETE_FLAG
</sql>
```

- 引用

```xml
select s.schedule_name,s.start_time,s.end_time,
<include refid="Base_Column_List" />
from ATTEND_SCHEDULE_RECORD SR
```

------

- collection

ofType 是对象的所属类型 javaType ：collection 的类型 property 是实体类中collection的属性名

```xml
<resultMap type="map" id="getQuestionCrosswiseByTableNameTwoMap">
    <result column="table_name" property="tableName"/>
    <result column="survey_table_id" property="surveyTableId"/>
    <collection property="questionGrooup" ofType="map" javaType="list">
        <result column="question_crosswise_id" property="questionCrosswiseId"/>
        <result column="question_crosswise_name" property="questionCrosswiseName"/>
        <collection property="questions"  fetchType="eager" column="question_crosswise_id" select="com.yikangyiliao.pension.dao.QuestionUnitDao.getQuestionUnitAnswerMapByQuetionCrosswiseId">      
        </collection>
        </resultMap>
```

### 11 #和$的区别是什么？

- \#{}解析传递进来的参数数据, ${}对传递进来的参数原样拼接在SQL中
- \#{}是预编译处理，${}是字符串替换。 使用#可以有效的防止SQL注入，提高系统安全性。

#{}是sql的参数占位符，Mybatis会将sql中的#{}替换为?号,#{}可以有效防止sql注入，可自动进行java类型和jdbc类型转换。如果parameterType传输单个简单类型值，#{}括号中可以是value或其它名称。

\${}是Properties文件中的变量占位符，通过​\${}可以将parameterType 传入的内容拼接在sql中且不进行jdbc类型转换，如果parameterType传输单个简单类型值，${}括号中只能是value。

### 12 如何获取自动生成的(主)键值?

- Mysql：通过LAST_INSERT_ID()获取刚插入记录的自增主键值，在insert语句执行后，执行select LAST_INSERT_ID()就可以获取自增主键。

```xml
<insert id="insertUser" parameterType="User">
    <selectKey keyProperty="id" order="AFTER" resultType="int">
        select LAST_INSERT_ID()
    </selectKey>
    INSERT INTO USER(username,birthday,sex,address) VALUES(#{username},#{birthday},#{sex},#{address})
</insert>
```

- Oracle：先查询序列得到主键，将主键设置到user对象中，将user对象插入数据库。

```xml
<!-- oracle
	在执行insert之前执行select 序列.nextval() from dual取出序列最大值，将值设置到user对象 的id属性
-->
<insert id="insertUser" parameterType="User">
    <selectKey keyProperty="id" order="BEFORE" resultType="int">
	    select 序列.nextval() from dual
	</selectKey>
    INSERT INTO USER(id,username,birthday,sex,address) VALUES( 序列.nextval(),#{username},#{birthday},#{sex},#{address})
</insert> 
```

### 13 在mapper中如何传递多个参数?

**第一种：使用占位符的思想**

- **在映射文件中使用#{0},#{1}代表传递进来的第几个参数**
  - **经@冬马党测试，如果使用的是JDK8的话，那么会有Bug**
- **使用@param注解:来命名参数**
- #{0}、#{1}方式

```
//对应的xml,#{0}代表接收的是dao层中的第一个参数，#{1}代表dao层中第二参数，更多参数一致往后加即可。
<select id="selectUser"resultMap="BaseResultMap">  
    select *  fromuser_user_t   whereuser_name = #{0} anduser_area=#{1}  
</select>  
```

- @param注解方式

```java
public interface usermapper { 
    user selectuser(@param(“username”) string username, 
                    @param(“hashedpassword”) string hashedpassword); 
}
```

```xml
<select id=”selectuser” resulttype=”user”> 
    select id, username, hashedpassword 
    from some_table 
    where username = #{username} 
    and hashedpassword = #{hashedpassword} 
</select>
```

**第二种：使用Map集合作为参数来装载**

```java
try{
    //映射文件的命名空间.SQL片段的ID，就可以调用对应的映射文件中的SQL
    /**
     * 由于我们的参数超过了两个，而方法中只有一个Object参数收集
     * 因此我们使用Map集合来装载我们的参数
     */
    Map<String, Object> map = new HashMap();
    map.put("start", start);
    map.put("end", end);
    return sqlSession.selectList("StudentID.pagination", map);
}catch(Exception e){
    e.printStackTrace();
    sqlSession.rollback();
    throw e;
}finally{
    MybatisUtil.closeSqlSession();
}
```

```xml
<!--分页查询-->
<select id="pagination" parameterType="map" resultMap="studentMap">
    /*根据key自动找到对应Map集合的value*/
    select * from students limit #{start},#{end};
</select>
```

### 14 Mybatis动态sql是做什么的？都有哪些动态sql？简述动态sql的执行原理？

- 动态sql可以让我们在Xml映射文件内，以**标签**的形式编写动态sql，完成**逻辑判断**和**动态拼接**sql的功能。
- Mybatis提供了9种动态sql标签：trim|where|set|foreach|if|choose|when|otherwise|bind。
- 其执行原理为，使用**OGNL**从sql参数对象中计算表达式的值，根据表达式的值动态拼接sql，以此来完成动态sql的功能。

### 15 通常一个Xml映射文件，都会写一个Dao接口与之对应，这个Dao接口的工作原理是什么？Dao接口里的方法，参数不同时，方法能重载吗？

Dao接口，就是人们常说的Mapper接口，接口的全限名，就是映射文件中的namespace的值，接口的方法名，就是映射文件中MappedStatement的id值，接口方法内的参数，就是传递给sql的参数。

Mapper接口是没有实现类的，当调用接口方法时，接口全限名+方法名拼接字符串作为key值，可唯一定位一个MappedStatement即 一个 select、insert、update、delete标签

Dao接口里的方法，是不能重载的，因为是全限名+方法名的保存和寻找策略。

Dao接口的工作原理是JDK动态代理，Mybatis运行时会使用JDK动态代理为Dao接口生成代理proxy对象，代理对象proxy会拦截接口方法，转而执行**MappedStatement**所代表的sql，然后将sql执行结果返回。

### 16 Mybatis是如何进行分页的？分页插件的原理是什么？

Mybatis使用**RowBounds对象**进行分页，它是针对**ResultSet**结果集执行的内存分页，而非物理分页，可以在sql内直接书写带有物理分页的参数来完成物理分页功能，也可以使用分页插件来完成物理分页。

分页插件的基本原理是使用Mybatis提供的**插件接口**，实现自定义插件，**在插件的拦截方法内拦截待执行的sql，然后重写sql**，根据dialect方言，添加对应的物理分页语句和物理分页参数。

```mysql
举例：
    select * from student，拦截sql后重写为：
    select t.* from （select * from student）t limit 0，10
```

### 17 简述Mybatis的插件运行原理，以及如何编写一个插件

Mybatis仅可以编写针对**ParameterHandler**、**ResultSetHandler**、**StatementHandler**、**Executor**这4种接口的插件，Mybatis使用**JDK的动态代理**，为需要拦截的接口生成代理对象以实现接口方法拦截功能，每当执行这4种接口对象的方法时，就会进入拦截方法，具体就是**InvocationHandler的invoke()方法**，当然，只会拦截那些你指定需要拦截的方法。

实现Mybatis的**Interceptor接口**并复写**intercept()方法**，然后在给插件编写注解，**指定要拦截哪一个接口的哪些方法**即可，记住，别忘了在配置文件中配置你编写的插件。

### 18 Mybatis是否支持延迟加载？如果支持，它的实现原理是什么？

Mybatis仅支持association关联对象和collection关联集合对象的延迟加载，association指的就是一对一，collection指的就是一对多查询。在Mybatis配置文件中，可以配置是否启用延迟加载lazyLoadingEnabled=true|false。

它的原理是，使用CGLIB创建目标对象的代理对象，当调用目标方法时，进入拦截器方法，比如调用a.getB().getName()，拦截器invoke()方法发现a.getB()是null值，那么就会单独发送事先保存好的查询关联B对象的sql，把B查询上来，然后调用a.setB(b)，于是a的对象b属性就有值了，接着完成a.getB().getName()方法的调用。这就是延迟加载的基本原理。

### 19 Mybatis都有哪些Executor执行器？它们之间的区别是什么？

Mybatis有三种基本的Executor执行器，SimpleExecutor、ReuseExecutor、BatchExecutor。

**SimpleExecutor**：每执行一次update或select，就开启一个Statement对象，用完立刻关闭Statement对象。

**ReuseExecutor**：执行update或select，以sql作为key查找Statement对象，存在就使用，不存在就创建，用完后，不关闭Statement对象，而是放置于Map<String, Statement>内，供下一次使用。简言之，就是重复使用Statement对象。

**BatchExecutor**：执行update（没有select，JDBC批处理不支持select），将所有sql都添加到批处理中（addBatch()），等待统一执行（executeBatch()），它缓存了多个Statement对象，每个Statement对象都是addBatch()完毕后，等待逐一执行executeBatch()批处理。与JDBC批处理相同。

作用范围：Executor的这些特点，都严格限制在SqlSession生命周期范围内。

### 20 介绍一下Mybatis框架

Mybatis是一个优秀的持久层框架。它和Hibernate一样，对jdbc操作数据库的过程进行了封装。它本来叫iBatis，是apache公司的一个开源项目，但在2010年的时候迁移到了Google公司，并改名为Mybatis。

### 21 使用jdbc操作数据库存在的缺点？

1. 频繁的创建连接和释放资源，造成系统资源浪费。
2. sql语句在代码中硬编码。
3. 使用preparedStatement向占位符传参数存在硬编码。
4. 对结果集解析存在硬编码。

### 22 介绍一下Mybatis的架构

首先加载配置文件，mybatis有两个配置文件：

- SqlMapConfig.xml，此文件作为mybatis的全局配置文件，配置了mybatis的运行环境等信息。

- mapper.xml文件即sql映射文件，文件中配置了操作数据库的sql语句。此文件需要在SqlMapConfig.xml中加载。

执行过程：

1. 通过mybatis环境等配置信息构造SqlSessionFactory即会话工厂
2. 由会话工厂创建sqlSession即会话，操作数据库需要通过sqlSession进行。

3. mybatis底层自定义了Executor执行器接口操作数据库，Executor接口有两个实现，一个是基本执行器、一个是缓存执行器。

4. Mapped Statement也是mybatis一个底层封装对象，它包装了mybatis配置信息及sql映射信息等。mapper.xml文件中一个sql对应一个Mapped Statement对象，sql的id即是Mapped statement的id。

5. Mapped Statement对sql执行输入参数进行定义，包括HashMap、基本类型、pojo，Executor通过Mapped Statement在执行sql前将输入的java对象映射至sql中，输入参数映射就是jdbc编程中对preparedStatement设置参数。

6. Mapped Statement对sql执行输出结果进行定义，包括HashMap、基本类型、pojo，Executor通过Mapped Statement在执行sql后将输出结果映射至java对象中，输出结果映射过程相当于jdbc编程中对结果的解析处理过程。

### 23 mybatis的核心配置文件叫什么？除了核心配置文件，还需要其它的配置文件吗？

sqlMapConfig.xml（官方提供的名称）。

需要，针对数据库中的每张表都要有一个对应的映射配置文件，如mapper.xml.

### 24 mybatis的核心配置文件sqlMapConfig.xml和每张表格的映射配置文件之间存在什么关系呢？

核心配置文件sqlMapConfig.xml主要用来连接数据库，在命名空间\<environments>里面嵌套\<environment>标签，在\<environment>标签里面配置jdbc事物管理、数据库连接池等。除此之外，在核心配置文件sqlMapConfig.xml中要配置映射配置文件的地址，

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!-- 引入properties配置文件-->
    <properties resource="jdbc.properties"/>
    <!-- 别名 包以其子包下所有类   头字母大小都行-->
    <typeAliases>
        <!-- <typeAlias type="com.itheima.mybatis.pojo.User" alias="User"/> -->
        <package name="com.itheima.mybatis.pojo"/>
    </typeAliases>
    <!-- 和spring整合后 environments配置将废除    -->
    <environments default="development">
        <environment id="development">
            <!-- 使用jdbc事务管理 -->
            <transactionManager type="JDBC" />
            <!-- 数据库连接池 -->
            <dataSource type="POOLED">
                <property name="driver" value="${jdbc.driver}" />
                <property name="url"
                          value="jdbc:mysql://localhost:3306/mybatis?characterEncoding=utf-8" />
                <property name="username" value="root" />
                <property name="password" value="root" />
            </dataSource>
        </environment>
    </environments>
    <!-- Mapper的位置  Mapper.xml 写Sql语句的文件的位置 -->
    <mappers>
        <!-- <mapper resource="sqlmap/User.xml" class="" url=""/> -->
        <!-- <mapper resource="sqlmap/User.xml" class="" url=""/> -->
        <!-- <mapper class="com.itheima.mybatis.mapper.UserMapper" /> -->
        <!-- <mapper url="" /> -->
        <package name="com.itheima.mybatis.mapper"/>
    </mappers>
</configuration>
```

注意：如果只使用mybatis时，我们是这样配的，但如果用spring来整合mybatis的话，\<environment>标签就不用了，spring框架可以帮助mybatis框架连接数据库。在大多数情况下，映射配置文件也不需要我们来配，我们可以使用逆向工程自动生成，但是逆向工程只针对单表，在一些复杂的情况下满足不了需求，所以对于映射配置文件的配置我们一定要熟稔于心。

### 25 当你配置完sqlMapConfig.xml主配置文件和映射配置文件之后，要对数据库进行操作，在测试类中分几步呢？

1. 加载核心配置文件
2. 创建SqlSessionFactory会话工厂
3. 由SqlSessionFactory会话工厂生成SqlSession会话对象
4. 通过SqlSession对象执行sql语句

代码如下：

```java
@Test
public void testMybatis() throws Exception {
    //加载核心配置文件
    String resource = "sqlMapConfig.xml";
    InputStream in = Resources.getResourceAsStream(resource);
    //创建SqlSessionFactory
    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(in);
    //创建SqlSession
    SqlSession sqlSession = sqlSessionFactory.openSession();
    //执行Sql语句
    User user = sqlSession.selectOne("test.findUserById", 10);
    System.out.println(user);
}
```

### 26 \<select>、\<insert>等标签中常用的属性有哪些？

- id 指定名称
- parameterType   传入参数的类型
- resultType   返回值的类型

### 27 mybatis和Hibernate的区别？

Mybatis和Hibernate框架都是持久层框架，底层封装的都是jdbc。它们的区别是：

- Mybatis是一个不完全的ORM框架，需要程序员自己编写Sql语句；Mybatis无法做到数据库无关性，适合对关系数据模型要求不高的软件开发。

- Hibernate是一个完全的ORM框架，不需要程序员自己编写Sql语句；Hibernate数据库无关性好，适合于对关系数据模型要求高的软件开发。

- Mybatis和Hibernate框架各自都有非常广泛的应用领域，本身并无好坏之分，适合就是最好的。

### 28 当实体类中的属性名和表中的字段名不一样，怎么办？

两种方法。

1. 通过在查询的sql语句中定义字段名的别名，让字段名的别名和实体类的属性名一致。
2. 通过\<resultMap>来映射字段名和实体类属性名的一一对应的关系（推荐使用）

### 29 模糊查询like语句该怎么写？

1. 在java代码中添加sql通配符

2. 在sql语句中拼接统配符，防sql语句注入。

```xml
<select id="findUserByUsername" parameterType="String" resultType="com.itheima.mybatis.pojo.User">
    select * from user where username like "%"#{haha}"%"
</select>
```

### 30 Mybatis是如何将sql执行结果封装为目标对象并返回的？都有哪些映射形式？

第一种是使用\<resultMap>标签，逐一定义列名和对象属性名之间的映射关系，第二种是使用sql列的别名功能，将列别名书写为对象属性名。

有了列名和属性名的映射关系后，Mybatis通过反射创建对象，同时使用反射给对象的属性逐一赋值并返回，那些找不到映射关系的属性，是无法完成赋值的。

### 31 使用mybatis开发Dao层时，我们会选择使用Mapper动态代理方式，这种方式只需要写Mapper接口，而不需要写实现类，那么，Mapper接口开发需要遵循什么规范呢？

Mapper接口开发需要遵循以下规范：（4大规范）

1. Mapper.xml文件中的namespace与mapper接口的类路径相同。
2. Mapper接口方法名和Mapper.xml中定义的每个statement的id相同

3. Mapper接口方法的输入参数类型和mapper.xml中定义的每个sql 的parameterType的类型相同

4. Mapper接口方法的输出参数类型和mapper.xml中定义的每个sql的resultType的类型相同

### 32 Mapper动态代理生成的动态代理对象到底是调用selectOne还是selectList是根据什么决定的呢？

动态代理对象调用sqlSession.selectOne()和sqlSession.selectList()是根据mapper接口方法的返回值决定，如果返回list则调用selectList方法，如果返回单个对象则调用selectOne方法。

mybatis官方推荐使用mapper代理方法开发mapper接口，程序员不用编写mapper接口实现类，使用mapper代理方法时，输入参数可以使用pojo包装对象或map对象，保证dao的通用性。

### 33 Mybatis的核心配置文件叫什么名字？

sqlMapConfig.xml

### 34 sqlMapConfig.xml的配置一般包含哪些内容呢？

SqlMapConfig.xml中配置的内容和顺序如下：（顺序不能颠倒）

- properties（属性）

- settings（全局配置参数）

- typeAliases（类型别名）
- typeHandlers（类型处理器）
- objectFactory（对象工厂）
- plugins（插件）
- environments（环境集合属性对象）
- environment（环境子属性对象）
- transactionManager（事务管理）
- dataSource（数据源）
- mappers（映射器）

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <properties resource="jdbc.properties"/>
    <!-- 别名 包以其子包下所有类   头字母大小都行-->
    <typeAliases>
        <!-- <typeAlias type="com.itheima.mybatis.pojo.User" alias="User"/> -->
        <package name="com.itheima.mybatis.pojo"/>
    </typeAliases>
    <!-- 和spring整合后 environments配置将废除    -->
    <environments default="development">
        <environment id="development">
            <!-- 使用jdbc事务管理 -->
            <transactionManager type="JDBC" />
            <!-- 数据库连接池 -->
            <dataSource type="POOLED">
                <property name="driver" value="${jdbc.driver}" />
                <property name="url"
                          value="jdbc:mysql://localhost:3306/mybatis?characterEncoding=utf-8" />
                <property name="username" value="root" />
                <property name="password" value="root" />
            </dataSource>
        </environment>
    </environments>
    <!-- Mapper的位置  Mapper.xml 写Sql语句的文件的位置 -->
    <mappers>
        <!-- <mapper resource="sqlmap/User.xml" class="" url=""/> -->
        <!-- <mapper resource="sqlmap/User.xml" class="" url=""/> -->
        <!-- <mapper class="com.itheima.mybatis.mapper.UserMapper" /> -->
        <!-- <mapper url="" /> -->
        <package name="com.itheima.mybatis.mapper"/>
    </mappers>
</configuration>
```

### 35 parameterType和resultType

parameterType：指定输入参数类型，mybatis通过ognl从输入对象中获取参数值拼接在sql中。

resultType：指定输出结果类型，mybatis将sql查询结果的一行记录数据映射为resultType指定类型的对象。如果有多条数据，则分别进行映射，并把对象放到容器List中

### 36 Mybatis中的输入类型和输出类型分别用什么属性表示？

parameterType和resultType.

### 37 Mybatis中的Mapper接口遵循的四大原则？

1. 接口的类路径与mapper.xml中的namespace一致
2. 接口中的方法名与mapper.xml中的方法名一致
3. 接口中的方法的输入参数类型与mapper.xml中的parameterType一致
4. 接口中的方法的返回值类型与mapper.xml中的resultType一致

### 38 where标签的作用？

where标签可以自动添加where关键字，同时去掉sql语句中的第一个and关键字。

### 39 一对一、一对多的关联查询？

一对一关联查询会用到\<association>标签，一对多关联查询会用到\<collection>标签。

### 40 如何用Spring框架整合Mybatis框架？

创建SqlMapConfig.xml配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
</configuration>
```

Spring整合mybatis

创建applicationContext-dao.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"       xmlns:context="http://www.springframework.org/schema/context" xmlns:p="http://www.springframework.org/schema/p"       xmlns:aop="http://www.springframework.org/schema/aop" xmlns:tx="http://www.springframework.org/schema/tx"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.2.xsd
                           http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.2.xsd
                           http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.2.xsd http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-4.2.xsd
                           http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util-4.2.xsd">
    <!-- 数据库连接池 -->
    <!-- 加载配置文件 -->
    <context:property-placeholder location="classpath:conf/db.properties" />
    <!-- 数据库连接池 -->
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource"
          destroy-method="close">
        <property name="url" value="${jdbc.url}" />
        <property name="username" value="${jdbc.username}" />
        <property name="password" value="${jdbc.password}" />
        <property name="driverClassName" value="${jdbc.driver}" />
        <property name="maxActive" value="10" />
        <property name="minIdle" value="5" />
    </bean>
    <!-- 让spring管理sqlsessionfactory 使用mybatis和spring整合包中的 -->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <!-- 数据库连接池 -->
        <property name="dataSource" ref="dataSource" />
        <!-- 加载mybatis的全局配置文件 -->
        <property name="configLocation" value="classpath:mybatis/SqlMapConfig.xml" />
    </bean>
    <!-- Mapper动态代理开发 扫描-->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="cn.e3mall.mapper" />
    </bean>
</beans>
```

db.properties

```properties
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/e3mall?characterEncoding=utf-8
jdbc.username=root
jdbc.password=root
```

### 41 Mybatis的Xml映射文件中，不同的Xml映射文件，id是否可以重复？

如果配置了namespace那么当然是可以重复的，因为我们的Statement实际上就是namespace+id

如果没有配置namespace的话，那么相同的id就会导致覆盖了。

### 42 为什么说Mybatis是半自动ORM映射工具？它与全自动的区别在哪里？

- Hibernate属于全自动ORM映射工具，使用Hibernate查询关联对象或者关联集合对象时，可以根据对象关系模型直接获取，所以它是全自动的。
- 而Mybatis在查询关联对象或关联集合对象时，需要手动编写sql来完成，所以，称之为半自动ORM映射工具。

### 43 Mybatis比IBatis比较大的几个改进是什么

- **有接口绑定,包括注解绑定sql和xml绑定Sql** ,
- 动态sql由原来的节点配置变成OGNL表达式,
- 在一对一,一对多的时候引进了association,在一对多的时候引入了collection节点,不过都是在resultMap里面配置

### 44 接口绑定有几种实现方式,分别是怎么实现的?

接口绑定有两种实现方式：

- 一种是通过注解绑定,就是在接口的方法上面加上@Select@Update等注解里面包含Sql语句来绑定
- 另外一种就是通过xml里面写SQL来绑定,在这种情况下,要指定xml映射文件里面的namespace必须为接口的全路径名.

### 45 简述Mybatis的插件运行原理，以及如何编写一个插件?

Mybatis仅可以**编写针对ParameterHandler、ResultSetHandler、StatementHandler、Executor这4种接口的插件，Mybatis使用JDK的动态代理，为需要拦截的接口生成代理对象以实现接口方法拦截功能**，每当执行这4种接口对象的方法时，就会进入拦截方法，具体就是InvocationHandler的invoke()方法，当然，只会拦截那些你指定需要拦截的方法。

实现Mybatis的Interceptor接口并复写intercept()方法，**然后在给插件编写注解，指定要拦截哪一个接口的哪些方法即可，记住，别忘了在配置文件中配置你编写的插件。**

