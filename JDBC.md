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

提升性能



#### Spring JDBC:JDBC Template











