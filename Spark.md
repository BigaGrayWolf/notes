### Spark深度之眼课程



###### 大数据4V：

VOLUME(海量的)

VARIETY(多样性的)   结构性数据|非结构性数据|半结构性数据

VELOCITY(流转速度) 数据处理具有时效性

VALUE（数据价值）



###### 集群架构Cluster Architecture

将单机服务复制成几份+负载均衡服务器

缺点：服务端每个服务器（节点）功能相同，无法将任务切割成小任务，任何一个功能出现问题会影响整体

-----------------》》》

###### 分布式系统Distributed System

每个子系统是一个服务，独立部署，服务之间可以相互通信

优势：独立部署的各个子系统（各子系统之间不受影响|子系统之间通信便捷|整个系统更加稳定）

可扩展性（可以无限扩展系统性能|性能不再是瓶颈）



##### 分布式存储系统Distributed Storage System

###### 中间控制节点架构

DEF:以单独元数据服务器为中间控制，具体数据存储服务器为分布式存储的架构存储

REPRESENTATION:Hadoop Distributed File System(HDFS)

元数据：描述数据的数据（包含具体数据的路径，以及相关信息）

###### 完全无中心架构

客户端通过设备映射关系计算出具体数据的位置

REPRESENTATION:Ceph

#####  分布式计算系统Distributed Compute System

###### Hadoop Map Reduce

Map+Reduce-》理念-》分而治之

特点：分布式计算|中间结果都存放在硬盘，无法查询上一个步骤的结果-》效率一般

支持语言：JAVA,C++

###### Spark

DEF:基于内存优化的分布式大数据计算框架

理念-》分而治之，同时引入RDD概念（弹性分布式数据集）

应用场景：批处理+流处理

支持语言：JAVA,SCALA,PYTHON,R

###### Flink

DEF:分布式大数据处理框架，对流数据可以进行计算

理念-》实时处理

应用场景：流处理

支持语言：JAVA,SCALA,PYTHON,R

##### 分布式消息队列系统Distributed Message Queue System

###### Kafka

特征：高性能|持久化|多副本备份|横向扩展能力

理念-》生产者消费者模型

##### 分布式机器学习系统Distributed Mechine Learning System

###### Spark ML

DEF:以Spark为计算引擎的分布式机器学习框架

特征：提供一个分布式的模型训练环境|提供一个训练数据集分布式处理的环境25

###### 分布式TensorFlow



##### scala入门

| 面向对象语言   | 纯面向对象               |
| -------------- | ------------------------ |
| 函数式编程     | 函数也能当值进行参数传递 |
| 静态类型       | 编译时检查类型           |
| 扩展性和并发性 | Actor作为其并发模型      |

# 

##### sbt（Simple Build Tool）

scala的项目构建工具，拥有依赖管理【增加第三方依赖库】、构建过程管理和打包【变成可执行文件】等功能。

注意sbt和Scala版本匹配关系 sbt1.x--scala2.12.x



##### 使用sbt

###### 方案一：

在项目路径下依次键入以下命令：

sbt—》update—》compile—》run

进入sbt控制台，更新依赖，编译，运行

###### 方案二：

在项目路径下键入以下命令：

sbt run



###### sbt assembly

构建整个项目，构成fat jar (依赖关系全部完成)



#### 分布式文件系统

###### GFS(Google File System)

###### HDFS(Hadoop Distributed File System)

设计原则：

1、硬件故障

检测故障、快速和自动恢复数据

2、流数据访问

支持高的吞吐量

3、大数据集

高带宽、可扩展的上百个节点、文件大小应该在GB以及TB以上

4、简单一致性原则

不支持在文件任意位置进行修改

5、移动计算比移动数据更划算

6、轻便的跨异构的软硬件平台



HDFS架构(主从架构的分布式文件系统)

##### NameNode

HDFS集群必须拥有至少一个Namenode

存储元数据信息

管理HDFS的命名空间（文件的存储结构or目录）

管理客户端的访问权限

管理Datanode中的文件块映射

接收Datanode的心跳信息和文件块报告

##### DataNode

HDFS中真正存储数据的节点，通常来说一个Datanode指的就是整个集群中的一个节点

管理存储在Datanode中的数据和文件块

对文件块进行创建，删除和复制（指令来自于Namenode）

向Namenode汇报文件块信息和节点状态

可以和客户端进行数据交互



###### NameNode存储机制

edit logs:运行后的编辑日志

fsimage:当前文件系统的NameNode快照

在启动时读取faimage和edit logs合并

###### Secondary Namenode备份

查询NameNode上的edit logs，更新到fsimage,可更新到NameNode上



###### The File System Namespace 文件系统命名空间

etc.  hdfs://namenodehost/parent/child

###### File Block and Data Replication文件块和数据复制

一个文件可以划分成很多个数据块，默认值为128MB，文件的所有Block都是同样大小的，除了最后一个

使存储更加均匀，更容易进行故障恢复、并行读写，可以设置副本数目

###### Data Replication 数据复制原理

同一个机架（Rack）的Datanode数据传输速度会好过跨机架传输。

副本要放在不同机架中，防止整个机架损坏后，数据仍然不会丢失。

###### 节点交流协议

TCP/IP基础之上，RPC（远程过程调用）



###### HDFS特点

经济高效（数据集中存储）

大存储高吞吐（分布式存储）

容错性可靠性（多个副本、智能的存储方式）

可扩展性（添加Datanode）

数据完整性（分布式存储，Datanode定时汇报数据块状态）

高可用性（数据恢复）

耐用性（故障自我修复）



##### 元数据（Metadata）管理

描述数据属性的数据，etc. 数据存储位置

最常见还是中心节点去管理元数据

###### 元数据的持久化存储

fsimage + edit file

namenode启动时做整合，更新fsimage 。Second namenode会定期查看edit file，并且

构造自己的fsimage，并更新namenode的fsimage。避免namenode的edit file过大。

Standby Namenode:接收Datanode报告+与Namenode共享相关编辑日志，当namenode出现

问题时，自动切换。



#### 分布式计算与Map Reduce

##### 分布式计算重要特点

可扩展、高性能、容错性好、可用性好

##### Map Reduce

分而治之

Map 拆分初始任务

Reduce 结果归纳

Map和Reduce之间会存在Shuffling步骤（洗牌），对数据进行整理，例如排序。

###### 系统架构

Job Client: 用户编写的MapReduce 程序通过Job Client提交给JobTracker

Job Tracer: 负责资源监控和作业调度，并监控所有TaskTracker与作业健康状况

Task Tracer: 负责监控任务进度，并汇报给Job Tracer



### Hive

关系式数据存储

查询语言HQL

数据存储HDFS/cloud FileSystem

执行引擎Map Reduce

##### 架构

###### 客户端：

Client CLI

JDBC/ODBC

Web UI

Thrift

###### 服务端：

Driver:解析器、编译器、优化器、执行器->MapReduce任务

Metastore:一个组件，提供元数据服务

MapReduce:经过转换和优化，形成一个mapreduce任务，可以提交到Hadoop集群

###### 存储层：

HDFS for Storage

可扩展

#### Metastore

Metadata DB

Metastore 服务

#### Hive特点

可扩展

关系式

SQL语言为查询接口

为OLAP设计(Online Analytical Processing)

数据更新即使某个版本支持也非常不建议

##### Hive数据单元

###### Databases||Schema：

命名空间的功能，防止名字重复

权限控制

###### Tables:

内部表：数据和表的定义绑定

外部表：数据和表的定义分开，删除表，数据仍然存在

###### Views

###### Columns

###### Partitions:分区

###### Buckets:桶













#### Spark生态系统

基础层Spark Core、衍生层GraphX等

抽象架构：构建DAG（有向无环图）









##### The Google File System论文解读

摘要重点：

特点：可扩展、分布式

作用：为大的分布式数据交互作用提供文件系统

优点：在便宜的硬件上运行且提供容错性，为大量的用户提供很好的性能









##### Pregel 

https://io-meter.com/2018/03/23/pregel-in-graphs/

##### Neo4j和spark结合

https://www.jianshu.com/p/8bef80b9acfc