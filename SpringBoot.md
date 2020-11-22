#### bug：

Cannot resolve symbol xxx intellij idea with lombok

解决方案：https://blog.csdn.net/oxp7085915/article/details/76816717



##### SpringBoot整合ES教程

https://blog.csdn.net/chengyuqiang/article/details/102938266

##### @Query查询语法文档

https://docs.spring.io/spring-data/elasticsearch/docs/current/reference/html/#elasticsearch.query-methods.criterions



##### Maven导入依赖以后出无法找到Dependency问题

项目中右键->Maven->Reload Project



##### Neo4j启动出错：Caused by: java.lang.ClassNotFoundException: org.neo4j.ogm.drivers.http.driver.HttpDriver

解决方案：Neo4j有两种启动方式，需要增加依赖

https://blog.csdn.net/teenwolf_man/article/details/102724726



##### 访问Neo4j验证失败

报错：The client is unauthorized due to authentication failure

解决方案：修改Neo4j配置文件，去掉访问验证

修改neo4j.conf配置文件，取消验证机制，修改如下：

dbms.security.auth_enabled=false

https://blog.csdn.net/weixin_39198406/article/details/85068102





##### spark集成在Springboot里



###### Springboot2.X 集成 spark2.X 实现WordCount

项目代码来源：https://blog.csdn.net/weixin_40461281/article/details/105381514

##### 报错：java.io.IOException: Could not locate executable null\bin\winutils.exe in the Hadoop binaries.

问题：缺少winutils.exe文件

解决方案

https://blog.csdn.net/love666666shen/article/details/78812622

##### 报错：java.lang.IllegalArgumentException: Unsupported class file major version 55

问题：版本问题

解决方案：修改pom.xml文件

https://stackoverflow.com/questions/55923943/how-to-fix-unsupported-class-file-major-version-55-while-executing-org-apache