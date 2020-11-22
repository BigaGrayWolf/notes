本地用户名 wangshaofeng

密码 常用密码



#### Neo4j

##### win10安装neo4j

注：安装server版本，同时最新社区版需要jdk11

https://blog.csdn.net/lihuaqinqwe/article/details/80314895

https://www.jianshu.com/p/dc620ca59d19



##### PowerShell因为在此系统中禁止执行脚本解决方法

注：

PS F:\neo4j-community-4.1.3\bin> neo4j
neo4j : 无法加载文件 F:\neo4j-community-4.1.3\bin\neo4j.ps1，因为在此系统上禁止运行脚本。有关详细信息，请参阅 https:/go
.microsoft.com/fwlink/?LinkID=135170 中的 about_Execution_Policies。
所在位置 行:1 字符: 1

+ neo4j
+ ~~~~~
    + CategoryInfo          : SecurityError: (:) []，PSSecurityException
    + FullyQualifiedErrorId : UnauthorizedAccess
    ~~~~~

https://www.cnblogs.com/zhaozhan/archive/2012/06/01/2529384.html



###### 批量导入数据

在neo4j安装目录下执行以下命令，(先终止neo4j服务)

 .\bin\neo4j-admin import --database=graph --relationships import/links.csv --nodes import/actor.csv --nodes import/movie.csv

不同导入方法参考http://weikeqin.com/2017/04/14/neo4j-import-data/

neo4j-import 详细记录https://blog.csdn.net/oTianShiZaiChangGe1/article/details/105095947

import后数据查看问题 https://blog.csdn.net/oTianShiZaiChangGe1/article/details/105097563

节点处必须要有  :ID 

链接边处必须要有 :START_ID :END_ID :TYPE















### neo4j 3.x 入门经典 阅读笔记

数据结构：节点、关系、属性、标签（主要用于索引和一些有限的模式约束，节点可设置多个标签|关系只能由一个标签，我觉得基本相当于节点的类型属性）

 节点粒度：图模型中倾向于创建瘦节点和关系，也就是节点和关系有尽可能少的属性。将节点属性转化为新的节点：遍历节点通常比评估每个路径更简单快捷。

 图内索引：多用于时间数据，建立时间树，而非时间轴，将图数据连接到此树。

##### 图数据库建模陷阱

1、使用丰富的属性

2、多概念节点

【以上两点其实都是避免单一节点属性过多】

3、未连接的图

4、密集的节点模式

【以上两点分别为节点度的两种极端情况】

##### Cypher入门

###### 创建数据

CREATE (remeo:Person{name:"Remeo"})-[:LOVES]->(Juliet:Person{name:"Juliet"})

remeo:节点名；Person：标签名

###### 读取数据

MATCH (n:Person)-[:LOVES]-()

WHERE toLower(n.name)="juliet"

RETURE n ORDER BY n.age ASC

SKIP 2 LIMIT 5;

###### 更新数据

MATCH (n:Person{name:"Juliet"})

WHERE n.age = 13

SET n.age = 15

RETURN n;

把age>18的节点的Teen移除

MATCH (n:Teen:Person)

WHERE n.age >18

REMOVE n:Teen

RETURN n; 

###### 删除数据

原则：该节点不再连接到其他任何节点

MATCH (r"Person{lastName:"Montague"})

DETACH DELETE r;

##### 语法约定

1、节点别名用小写驼峰（小写字母开头）

2、标签用大写驼峰（大写字母开头）

3、关系用蛇形大写（类似IS_A）

4、属性名用小写驼峰

5、关键词全部大写



##### 如何标识一个节点

唯一标识符的确定|解除

(CREATE|DROP) CONSTRAINT ON (p:Person) ASSERT p.identifier IS UNIQUE



##### Awesome Procedures On Cypher (APOC)

APOC is an add-on library for Neo4j that provides hundreds of procedures and functions adding a lot of useful functionality.

|      | 函数                             | 过程                               |
| ---- | -------------------------------- | ---------------------------------- |
| 目的 | 读取数据库数据并计算返回一个结果 | 对数据库数据进行修改并返回多个结果 |
| 调用 | 直接应用到Cypher查询中           | 通过CALL命令来调用                 |

##### 通过Cypher扩展（Java编程）

可以自定义函数|过程|聚合器（如count,sum,min,max） 

形式上，会在类|函数上加注解，如@UserAggregationFunction

非托管扩展：代码运行在服务器上，为数据提供服务

JSON响应流式化：目的保持一个较低的内存占用。HTTP调用返回的结果会导致在服务器端存储大量的对象，它会使得JVM触发比实际情况更多的垃圾回收。



##### 查询性能优化

Explain指令和Profile指令，加在命令的开头

Explain会给出查询的执行计划，但不会执行查询

Profile执行查询并返回结果

###### 执行计划的决定

不是由数据库的命中数决定，计划器Planner的Cyther组件定义

有两种计划器，成本计划器（默认）和规则计划器。

##### 数据导入

###### 导入CSV文件

使用LOAD CSV命令

实例1:有列名

LOAD CSV WITH HEADERS FROM

'https://neo4j.com/docs/cyphter-refcard/3.2/csv/artists-with-headers.csv' AS

line

CREATE (:Artist {name: line.`Name`, year: toInt(line.`Year`)})

#注释  用反向引号 `括住列名 防止列名中含有空格

实例2：无列名

LOAD CSV FROM

'https://neo4j.com/docs/cyphter-refcard/3.2/csv/artists-with-headers.csv' AS

line

CREATE (:Artist {name: line[1], year: toInt(line[2])})

#注释 列索引从0开始，且较为容易出错，不推荐

实例3：导入本地文件

步骤1： $NEO_HOME/conf/neo4j.conf配置文件并取消一行代码注释（我查看的时候已经取消了）

dbms.directories.import = import

需要重启服务

步骤2：将需要导入的文件放入到$NEO_HOME/import/level1/

现在，文件路径这块需要改写成‘file:///level1/artists-with-headers.csv’ AS line.......

#### 错误说明：OutOfMemoryException(内存溢出错误)

原因：CSV表格行数、列数过多

可以进行周期性事务提交

代码

USING PERIODIC COMMIT n#注释 n表示每次十五提交包含的行数

LOAD CSV .....

###### 导入JSON源

1、需要使用（安装）APOC

2、需要在neo4j.conf里设置下列内容，并重启服务

apoc.import.file.enabled=true

3、代码

创建约束条件

CREATE CONSTRAINT ON (c:Country) ASSERT c.cca2 IS UNIQUE

创建节点

WITH "file:////home.../import/countries.json" AS url

CALL apoc.load.json(url） YIELD value

MERGE (c:Country{cca3:value.cca3})

ON CREATE SET c.cca2 = value.cca2,c.cca3=value.cca3;

###### 导入JDBC源

采用APOC，以及JAVA驱动程序

CALL apoc.load.driver("com.mysql.jdbc.Driver")

###### 导入XML源

采用APOC 

#####  图数据库可视化

###### 开源可视化组件库

D3.js

www.d3js.org

GraphViz

www.graphviz.org

Sigma.js

www.sigmajs.org

Vivagraph.js

https://github.com/anvaka/ngraph

www.yasiv.com

yWorks





​                                                                                                                                                                                                                                                                                                                                          

