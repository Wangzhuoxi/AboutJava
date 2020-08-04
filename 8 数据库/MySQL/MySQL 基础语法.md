## 1 概述

本文对常用SQL语法进行总结，包括以下部分:

1. 数据库创建、删除、查看语法
2. 表创建、删除、查看语法
3. 视图创建、删除、查看语法
4. 触发器创建、删除、查看语法
5. 各种查询操作语法
6. 索引创建、删除、查看语法
7. 权限创建、删除、查看语法

## 2 数据库语法

```mysql
mysql -u root -p password 
```

登入数据库，没有切换到任何数据库，才可以执行操作数据库的操作。

```mysql
// 删除数据库
DROP DATABASE IF EXISTS `ihbs`;
// 创建数据库
CREATE DATABASE `ihbs` CHARACTER SET 'utf8' COLLATE 'utf8_general_ci';
// 显示数据库
show databases;
```

## 3 表语法

### 3.1 创建表

```mysql
create table 表名(
  列名1 列2类型 [完整性约束条件],
  列名2 列2类型 [完整性约束条件],
  ……
)ENGINE = myisam|innodb DEFAULT charset = utf8|gbk|latin1;
```

### 3.2 查看表结构

```mysql
desc 表名
show create table 表名
```

### 3.3 设置表的主键

单字段主键

```mysql
create table table2(
  stu int PRIMARY key,
  name VARCHAR(30)
)
```

多字段主键

```mysql
create table  table2(
  stu int ,
  name VARCHAR(30),
  primary key (stu,name)
)
```

### 3.4 完整性约束条件

#### 1 主键：primary key

#### 2 外键：foreign key

```mysql
// 依赖数据库中已存在表的主键，可以为空
constraint 外键别名 foreign key(属性1,属性2,…) references 表名(属性1，属性2) 
```

删除表的外键约束

```mysql
alter table 表名 drop foreign key 外键别名
```

#### 3 非空：not null

```mysql
属性名 数据类型 not null
```

#### 4 唯一性：unique

```mysql
属性名 数据类型 unique
```

#### 5 自增：Auto_increment 

```mysql
// 一个表只能有一个字段使用auto_increment，且为主键一部分任何整数(默认从1开始自增)
属性名 数据类型 auto_increment 
```

#### 6 默认值：default 

```mysql
属性名 default 默认值
```

完整demo

```mysql
create table if not exists test(
    id int PRIMARY key auto_increment COMMENT '主键，自增',
    name VARCHAR(30) UNIQUE COMMENT '唯一性约束',
    sex int DEFAULT 1 COMMENT '设置默认值',
    dsc VARCHAR(256) not null  COMMENT '非空',
    forid int COMMENT '和table2表的stu进行外键约束',
    constraint forkey FOREIGN KEY(forid) references table2(stu)
)
```

### 3.5 修改表

```mysql
// 修改表名
alter table 旧表名 RENAME [to] 新表名
// 添加列
alter table 表名 add 属性名 属性类型 [完整性约束条件] [First | After 属性名]
alter table 表名 add 属性名 属性类型 after 某列名(指定添加到某列后)
alter table 表名 add 属性名 属性类型 first;(把新列加到最前面)
// 修改列
alter table 表名 modify 属性名 属性类型;
// 修改列名及列类型：
alter table 表名 change 旧属性名 新属性名 新类型;
// 修改字段的排列顺序
alter table 表名 modify 属性1 数据类型 first | after 属性2
// 修改表的存储引擎
alter table 表名 engine = 存储引擎名
// 删除列
alter table 表名 drop 属性名
```

注意：字段改名后，完整性约束条件丢失

```mysql
// 以下修改后，约束条件还在
alter table teacher change num t_id not null unique 
// 以下修改后，t_id约束条件丢失
alter table teacher change num t_id
结论：属性改名时，必须在语句中加上原来的完整性约束条件
```

### 3.6 删除表

```mysql
// 删除没有被关联的表
drop table 表名
// 删除被其他表关联的表
先删除外键约束： alter table 表名2 drop foreign key 外键别名
再删除表：drop table 表名
```

## 4 插入、更新与删除数据

```mysql
// 插入数据
insert into 表名(属性名1,属性名2,...) values(值1，值2,...),[(值1,值2,...)...]
// 将查询结果插入到表中
insert into 表名1(属性列表1) select 属性列表2 from 表名 where 条件表达式
//  更新数据
update 表名  set 属性1=值1, 属性2=值2,.... where 表达式
//  删除数据
delete from 表名[ where 表达式]
```

## 5 视图语法

### 5.1 概述

**视图简介:**

- 虚拟表
- 数据库只存放定义，并没有存放视图中数据，存放在原来表中;使用视图查询数据时，数据库系统会从原来表中取出对应数据

**视图作用**

- 操作简便
- 增加数据安全性
- 提高表的逻辑独立性

### 5.2 视图和表区别和联系

**区别**

- 视图是按照sql语句生成的一个虚拟的表
- 视图不占用实际的物理空间，而表中的记录需要占用物理空间
- 建立和删除视图只影响视图本身，不影响实际记录，而建立和删除表会影响实际记录

**联系**

- 视图是在基本表上建立的表，其字段和记录都来自基本表，其依赖基本表而存在
- 一个视图可以对应一个基本表，也可以对应多个基本表
- 视图是基本表的抽象，也可以对应多个基本表

### 5.3 创建视图

必须拥有创建视图权限(Create View权限)

```mysql
// 语法
create [Algorithm={Undefined|Merge|Temptable}] 
View 视图名[(属性列表)]
As select 语句
[With [Cascaded | Local]] check option];
Algorithm:
    Undefined：mysql默认算法
    Merge：将使用视图的语句与视图定义合并起来，使得视图定义某一部分取代语句对应部分
    TempTable：将视图结果存入临时表，然后临时表执行语句
With：
    cascaded：更新视图时要满足所有相关视图和表条件(默认)
    Local：更新视图时需要满足视图本身的定义条件即可

// 在单表上创建视图
create view v_appuser As SELECT * from qappuser

// 在多表上创建视图
create ALGORITHM=MERGE view v_appuser2
As SELECT a.* from qappuser as a, qappfunction as b
WITH LOCAL check option;

// 查看视图
DESC v_appuser;
describe v_appuser2;
show table status like 'v_appuser';
show create view v_appuser;
SELECT * from information_schema.VIEWS;
```

### 5.4 修改视图

```mysql
// 修改视图:Create or Replace语法
Create or Replace [Algorithm={Undefined|Merge|Temptable}] 
View 视图名[(属性列表)]
As select 语句
[With [Cascaded | Local]] check option];
说明：不存在时创建视图，存在时，则修改视图

// 修改视图：Alter语法
Alter [Algorithm={Undefined|Merge|Temptable}] 
View 视图名[(属性列表)]
As select 语句
[With [Cascaded | Local]] check option];
说明：使用alter时，视图名必须存在
```

### 5.5 更新视图数据

```mysql
// 创建测试视图
CREATE  
View  v_appuser
As  SELECT * from qappuser where iAppuserId = 1430224175105

// 更新视图记录
// 通过视图更新时，只能更新权限范围内数据
update v_appuser set sUsername="11"
说明：以上语句只更新iAppUserId=1430224175105的记录，而其他记录不更新

// 不能更新的视图
    ○ 视图中包含sum、count、max、min
    ○ 视图中包含union、union all、distinct、group by、having
    ○ 常量视图：create view t_view as select 'name' as name
    ○ 视图中select中包含子查询：create view t_view7(name) as select (select name from qwork)
    ○ 由不可更新视图导出的视图
    ○ 创建视图时，Algorithm为Temptable类型
视图对应表上存在没有设置默认值的列，而且该列没有包含在视图中
```

### 5.6 删除视图

```mysql
drop view if exists 视图名列表 [Restrict | cascade]
```

## 6 触发器语法

```mysql
// 触发事件
Insert、update、delete
// 创建一个只有一个执行语句的触发器
create trigger 触发器名  before | after
触发事件
on 表名 for each row  执行语句

// 创建多个执行语句触发器
create trigger 触发器 before | after
触发事件
on 表名 for each row
before
  执行语句列表
end
// 查看触发器
show triggers \G;

// information_schema 下的triggers表
SELECT * from information_schema.TRIGGERS;
// 触发器使用顺序
Before触发器  操作(insert,update,delete) After触发器
// 删除触发器
Drop trigger 触发器名
```

创建触发器demo

```mysql
create trigger updatename after update on user for each row   //建立触发器，  
begin  
        //old,new都是代表当前操作的记录行，你把它当成表名，也行;  
        if new.name!=old.name then   //当表中用户名称发生变化时,执行  
        update comment set comment.name=new.name where comment.u_id=old.id;  
end if;  
```

## 7 查询数据

### 7.1 基本查询语句

```mysql
select 属性列表
    from 表名和视图列表
      [where 条件表达式]
      [Group by 属性1 [having 条件表达式2] ]
      [Order by 属性2 [ASC|DESC]]
带like查询
      [Not] like
      带"%"通配符，多个字符
      带"_"通配符，一个字符
带in关键字查询
      [Not] in (元素1，元素2，...)
带between and 查询
      [Not] between 值1 and 值2
空值查询
      is [not] null
带and、or查询条件
带distinct查询
带比较条件：
      =、<、<=、>、>=、!=、<>、!>、!<
使用limit限制查询结果数目(从0开始)
      不指定初始位置，只指定显示N个
      select * from employ limit 2;
      指定初始位置
      select * from employ limit 0,2;
```

### 7.2 连接查询

内连接：两边都有才被查询

- 表名1 inner join 表名2 on 表名1.属性 = 表名2.属性
- from 表名1,表名2 where表名1.属性 = 表名2.属性

外连接：

- 表名1 left|right join 表名2 on 表名1.属性 = 表名2.属性
- 左连接查询：左边表显示所有记录，右边表只有匹配的才显
- 右连接查询：右边表显示所有记录，左边表只有匹配的才显示

### 7.3 子查询

将一个查询语句嵌套在另一个查询语句中。内层查询语句的查询结果可以为外层查询语句提供查询条件。

**带 [Not] in 关键字的子查询** 

```mysql
select * from employ where id in (select d_id from department)
```

**带比较运算符子查询**

=、<、<=、>、>=、!=、<>、!>、!<

```mysql
select * from employ where id >= (select d_id from department)
```

**带 [Not] exists 关键字查询**

使用exists关键字，内层查询语句不返回查询的记录，而是返回一个真假值。如果内层查询语查询到满足条件的记录，就返回一个真值，否则返回一个假值。只有当返回的值为真时，外层查询语句才进行查询并获取记录。

```mysql
select * from employ where exists (select d_name from department where d_id=1001)
```

**SQL中 EXISTS 的用法**

EXISTS与IN的使用效率的问题，通常情况下采用exists要比in效率高，因为IN不走索引，但要看实际情况具体使用：**IN适合于外表大而内表小的情况；EXISTS适合于外表小而内表大的情况。**

**带 any 关键字子查询**

满足其中任一条件。只要满足内层查询语句返回结果中的任何一个，就可以通过该条件执行外层查询语句。

```mysql
select * from employ where id >= any(select d_id from department)
```

**带 all 关键字子查询**

满足所有条件。只有满足内层查询语句返回所有结果，才可以执行外层查询语句。

```mysql
select * from employ where id >= all(select d_id from department)
```

**合并查询结果**

将多个select语句查询结果合并到一起

- union：将所有查询结果合并到一起，然后去除掉相同记录
- union all：简单合并到一起

**表和字段取别名**

表名或者字段名 [AS] 别名。

### 7.4 分组查询group by

分组查询：带有GROUP BY的查询，也叫组合查询。

特征：

- GROUP BY是SELECT语句的从句，用来指定查询分组条件，主要用来对查询的结果进行分组，相同组合的分组条件在结果集中只显示一行记录。
- 和其配合的聚合函数有：count()、sum()、avg()、max()、min()
- GROUP BY在做组合查询的时候，会对NULL的分组单独形成一行
- 可以在SELECT … GROUP BY 分组后筛选数据。筛选的关键字是HAVING。HAVING的作用和WHERE类似。都是用来过滤查询的中间记录。但是，HAVING从句指定的每个列规范必须出现在一个聚合函数内，或者出现在GROUP BY从句命名的列中。与WHERE不同的是：**WHERE是在分组前（查询后）筛选数据；HAVING是在分组后筛选数据。**

例如：

```mysql
SELECT
  SUBSTR(A.HYLB_DM,1,2),
  COUNT(*),
  SUM(A.ZCZB)
FROM DJ_ZT A
  GROUP BY SUBSTR(A.HYLB_DM,1,2)
  HAVING MAX(YEAR(A.CJRQ))<>2007;
```

```mysql
带有WHERE和HAVING的SELECT语句执行过程：
    § 执行 WHERE 筛选数据
    § 执行 GROUP BY 分组形成中间分组表
    § 执行 WITH ROLLUP/CUBE 生成统计分析数据记录并加入中间分组表
    § 执行 HAVING 筛选中间分组表
    § 执行 ORDER BY 排序
```

## 8 索引

### 8.1 查看索引

```mysql
// 查看某个表的索引
show index from 表名;
```
### 8.2 普通索引

可以创建在任何数据类型上，不附件任何限制条件

```mysql
// 创建表时创建索引
drop table if EXISTS test;
CREATE table test(
id int,
name VARCHAR(20),
sex TINYINT,
INDEX(id)
);

// 在已存在的表上创建索引
Create [Unique | Fulltext | spatial] index 索引名 on 表名(属性名[(长度)][ASC|DESC], ...);
create INDEX index_id on test(id);

// 用Alter table创建索引，其他也是类似，不在一一举例
Alter table 表名 add [Unique | Fulltext | spatial] Index 索引名 (属性名[(长度)][ASC|DESC],...)
ALTER TABLE test add UNIQUE INDEX index_id4(id);

// 删除索引，所有类型的索引的删除语句都是一样的
drop index index_id2 on test;
```

### 8.3 唯一性索引

只能在唯一值数据上创建索引，用Unique参数设置

```mysql
// 创建表时创建索引，Mysql中不用加unique index(id)，只要某个字段是unique就自动建立唯一索引。
drop table if EXISTS test;
CREATE table test(
id int unique,
name VARCHAR(20),
sex TINYINT,
// id_index表示索引名称,(id asc)表示索引升序排列
unique INDEX id_index(id asc)
// unique INDEX (id)
);
// 在已存在的表上创建索引，其他在已有的表建立索引，语法相似就不一一举例
create UNIQUE INDEX index_id2 on test(id);
// 用Alter table创建索引
```

### 8.4 全文索引

只能在char、varchar和text上建立索引，用fulltext设置，只限于MyIsam存储引擎

```mysql
// 创建表时创建索引
drop table if EXISTS test;
CREATE table test(
    id int unique,
    name VARCHAR(20),
    sex TINYINT,
    FULLTEXT INDEX name_index(name )
)ENGINE=myisam;
```

### 8.5 单列索引

在表的单个字段上建立索引

```mysql
// 创建表时创建索引
drop table if EXISTS test;
CREATE table test(
    id int unique,
    name VARCHAR(20),
    sex TINYINT,
    // 只取前10个前缀建立索引
    INDEX name_index(name(10) )
);
```

### 8.6 多列索引

在表的多个字段上建立索引。只有查询条件中使用了这些字段中的第一个字段时，索引才被使用。 
例子：如在id，name和sex上建立了多列索引，只有在查询条件中使用了id字段，该索引才被使用

```mysql
// 创建表时创建索引
drop table if EXISTS test;
CREATE table test(
    id int unique,
    name VARCHAR(20),
    sex TINYINT,
    INDEX name_index(name(10),sex)
);
```

### 8.7 空间索引

只能在空间数据类型上建立索引

## 9 账号管理

### 9.1 新建用户

```mysql
// 为Entry数据库创建一个remote_entry账号，密码为password，拥有select、insert、delete、create、drop权限
grant select,insert,update,delete,create,drop on entry.* to 'remote_entry'@'%' identified by 'password';
// 不用刷新权限，就可以登录；如果通过insert、update对mysql.user进行操作，则需要进行刷新操作
// flush privileges;
```

### 9.2 删除用户

```mysql
DROP USER 'remote_entry'@'%';
```

### 9.3 查询数据库的用户

```mysql
SELECT * from mysql.user
```

### 9.4 修改密码

```mysql
// 登录后,修改自己密码
set Password = Password("new_password");
// 账号拥有Super_priv的权限，才可以执行以下操作，如root用户
mysqladmin -umysql -ppassword password "password2"
// 拥有修改mysql表权限的用户(如root用户)修改自己或者别人密码，并刷新权限
update mysql.user set password=Password("password") where user='remote_entry' and host='localhost';
flush privileges;
// 修改别人密码
set Password for 'remote_entry'@'%' = Password("password") ;
```

### 9.5 修改用户host：是否可以远程访问

```mysql
// 修改用户只能本地访问，如果要任何IP都可以访问，则设置host为%；需要刷新权限
update mysql.user set host = 'localhost' where user='mysql';
flush privileges;
```

### 9.6 忘记root密码，找回root密码(mysql忘记密码)

```mysql
// 1 打开命令行界面，使用 --skip-grant-tables方式启动服务，在命令行中执行，程序会停留在界面上
mysqld  --skip-grant-tables
// 2 打开新命令行界面，使用root无密码登陆，并执行密码修改语句，并刷新权限
mysql -uroot
update mysql.user set password=Password("password2") where user = "root";
flush privileges;
```

## 10 权限管理

### 10.1 授权

```mysql
// with grant option(未测试，是否可以给其他用户授权)表示对A用户进行的授权，A可以授予给其他用户，当收回对A的授权时，A授予给其他用户的权限不会被级联收回
grant select,update on *.* to 'entry'@'localhost' identified by 'entry' with grant option;
// 为一个用户赋予全部的权限
grant all privileges on ab.* to hwalk1@'%' identified by 'hwalk1';
```

### 10.2 收回权限

```mysql
// 收回所有用户或者某个特定数据库的权限 
Revoke all privileges on *.* from 'entry'@'localhost';
```

### 10.3 查看权限

```mysql
// 获取用户在所有数据库上的权限
select * from mysql.user \G;
// 查看用户所有权限，在所有数据库和特定数据库上的权限
show grants for 'root'@'localhost';
```

