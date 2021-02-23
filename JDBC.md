## JDBC

JAVA Database Connectivity  Java数据库连接

本质：定义了一套操作关系型数据库的接口，不同数据库有不同版本的实现类（数据库驱动）。

#### 快速入门步骤

1、导入驱动jar包

复制jar包到项目libs目录下，右键，选择Add As Library

2、注册驱动

```java
Class.forName("com.mysql.jdbc.Driver");
```

3、获取数据库连接对象Connection

```java
Connection conn = DriverManager.getConnection("jdbc....");
```

4、定义sql语句

5、获取执行sql语句的对象Statement

```sql
Statement stmt = conn.createStatement();
```

6、执行sql,接受返回结果

```java
int count = stmt.executeUpdate(sql);
```

7、处理结果

8、释放资源

```java
stmt.close();
conn.close();
```

#### 详解各个对象：

##### 1、DriverManager:驱动管理对象

功能：

###### 注册驱动

static void registerDriver(Driver driver)

注：mysql5之后的驱动jar包可以省略注册驱动的步骤

###### 获取数据库连接

```java
static Connection getConnection(String url,String user,String password)
/*url指定连接的路径
  jdbc:mysql://ip地址（域名）：端口号/数据库名称
  细节：若连接本机mysql服务器，且默认端口是3306，则可以简写为
  jdbc:mysql:///数据库名称
  */  
```



##### 2、Connection：数据库连接对象

功能：

###### 获取执行sql的对象

```java
Statement createStatement()
PreparedStatement prepareStatement(String sql)
```

###### 管理事务

```java
//开启事务
void setAutoCommit(boolean autoCommit)
//提交事务
 commit()
//回滚事务
 rollback()
```



##### 3、Statement：执行sql的对象

执行静态sql的对象

1、boolean execute(String sql) //可以执行给定的sql对象

2、int executeUpdate(String sql)//执行DML（insert\update\delete）语句，DDL(create\alter\drop)语句,返回值：影响的行数

3、ResultSet excuteQuery(String sql):执行select语句



##### 4、ResultSet：结果集对象

```
boolean next():游标向下移动一行 游标默认在第一行之前,如果当前位置为最后一行，返回false
getXxx():获取数据 Xxx代表数据类型 
     参数：int类型 列编号，从1开始
          String类型 列名称
          

```

##### 5、PreparedStatement：执行sql的对象

SQL注入问题：在拼接sql时，有一些特殊的sql的特殊关键字参与字符串拼接，会造成安全问题。

解决：PreparedStatement+预编译SQL[参数使用？作为占位符]

1、Connection.prepareStatement(String sql)//预编译sql,获取PreparedStatement对象

2、给sql中的？赋值，方法 setXxx(参数1，参数2)

参数1：？的位置编号，从1开始

参数2：？的值

#### jdbc控制事务

##### 操作：

开启事务

conn.setAutoCommit(false);

提交事务

conn.commit();

回滚事务(在catch代码块中)

conn.rollback()

使用Connection对象来管理事务



#### 数据库连接池

##### 好处

提升性能

用户访问高效

##### 实现：

标准接口：DataSource javax.sql包下

方法：

获取连接：getConnection()

归还连接：如果连接对象Connection是从连接池中获取的，那么调用Connection.close()方法，则不会再关闭连接了，而是归还连接

一般我们不去实现它，有数据库厂商来实现

1、C3P0:数据库连接池技术

2、Druid:数据库连接池技术，阿里巴巴

#### C3P0:

##### 步骤：

导入jar包(不要忘记导入数据库驱动jar包)

定义配置文件

 	名称：c3p0.properties 或者 c3p0-config.xml

​	路径：直接放在src文件夹下即可

创建核心对象 数据库连接池对象 ComboPooledDataSource//参数：使用特定名称的配置，若null，默认配置

获取连接：getConnection

#### Druid:

##### 步骤：

导入jar包

定义配置文件

 	 是properties形式的

​      可以叫任意名称，可以放在任意目录下

加载配置文件

```java
Properties pro = new Properties();
InputStream is = DruidDemo.class.getClassLoader().getResourceAsStream("druid.properties");
pro.load(is);
```

获取数据库连接对象：通过工厂类来获取 DruidDataSourceFactory

```java
DataSource ds = DruidDataSourceFactory.createDataSource(pro);
```

获取连接 getConnection

```java
Connection conn = ds.getConnection();
```

##### 定义工具类：

定义一个类JDBCUtils

提供静态代码块加载配置文件，初始化连接池对象

提供方法

1、获取连接方法：通过数据库连接池获取连接

2、释放资源

3、获取连接池方法

#### Spring JDBC:JDBC Template

Spring框架对jdbc开发的简化：提供了一个JDBCTemplate对象

步骤：

1、导入jar包

2、创建JDBCTemplate对象，依赖数据源DataSource

```java
JDBCTemplate template = new JDBCTemplate(ds);
```

3、调用JDBCTemplate的方法来完成CRUD操作

```java
update():执行DML语句，增、删、改
queryForMap():查询结果将结果封装为map集合,字段名当作键，字段值当作值（单条记录）
queryForList():查询结果将结果封装为list集合，查询多条记录，list的元素为map集合
query():查询结果，将结果封装为JavaBean对象
    方案一：第二个参数为RowMapper接口，实现一个mapRow方法完成封装
    方案二：new BeanPropertyRowMapper(Emp.class)//Emp为对应的类，封装类型如Double\Integer可以接收NULL
queryForObject():查询结果，将结果封装为对象//第二个参数为返回结果类型，如Long.class，一般用来执行聚合函数
```



#### Junit单元测试，可以让方法独立执行

@Test

public void test1(){

}



