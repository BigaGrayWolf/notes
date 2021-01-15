# Oracle数据库

## 特点

支持多用户、大事务量的事务处理

数据安全性和完整性控制

支持分布式数据处理

可移植性

## 体系结构                                                                

 只有一个数据库

#### 实例：

一个Oracle实例有一系列后台进程和内存结构组成，一个数据库可以有n个实例

#### 数据文件：

数据库的物理存储单位

#### 表空间：

数据文件的逻辑映射

底下单位：段->区->数据块->磁盘块

#### 用户：

用户是在表空间下建立的，用户只能看到自己的表

 

## 连接到Oracle数据库

命令行输入如下命令：

sqlplus system/密码

退出，输入quit

#### 远程连接

有一个instantclient工具，把连接部分功能抽取出来了

输入如下命令：sqlplus system/密码@192.168.80.10：1521/orcl

orcl:默认的实例名

1521：oracle默认端口

#### 可视化工具

PLSQL

安装路径不能带中文和空格

还需要在环境变量里配置一下编码

 

## 项目

### 表结构设计

### 创建表空间

```sql
create tablespace waterboss
datafile 'C:\waterboss.dbf'//对应数据文件
size 100m//初始大小
autoextend on//打开自动扩展
next 10m//满后每次增长
```

### 创建用户

```sql
create user wateruser//用户名
identified by itcast//密码
default tablespace waterboss
grant dba to wateruser//给wateruser赋予dba权限
```

### 表的创建修改删除

#### 创建表

```
create table 表名称(
字段名 类型（长度） primary key,
字段名 类型（长度）,
.....
);
```

#### 数据类型

#### 1、字符型

(1)CHAR:固定长度的字符类型，最多存储2000个字节

(2)VARCHAR2:可变长度的字符类型，最多存储4000个字节

(3)LONG:大文本类型，最大2个G

#### 2、数值型

NUMBER:数值类型，用长度来设定存储大小

#### 3、日期型

(1)DATE:日期时间型，精确到秒

(2)TIMESTAMP:精确到秒的小数点后9位

#### 4、二进制型(大数据类型)

(1)CLOB:存储字符，最大可存4个G

(2)BLOB:存储图像、声音、视频等二进制数据，最多可存4个G

#### 修改表

```sql
ALTER TABLE T_OWNERS ADD(//追加字段
    REMARK VARCHAR2(20),
    OUTDATE DATE
)
ALTER TABLE T_OWNERS MODIFY(//修改字段
   REMARK CHAR(30)
)
//修改字段名
ALTER TABLE T_OWNERS RENAME COLUMN A TO B
//删除字段名
ALTER TABLE T_OWNERS DROP COLUMN REMARK

```

#### 删除表

```sql
DROP TABLE TABEL_NAME
```

### 数据的增删改

#### 插入数据

```sql
insert into 表名[(列名,....)] VALUES(...);
```

必须要commit 事务才能查到数据

#### 修改语句

```sql
update 表名 set ... where ...
```

#### 删除语句

```sql
delete from 表名 where ...//可能会有碎片
```

```sql
truncate table 表名//不需要commit，同时不能回滚，先摧毁表结构，再重建
```

### JDBC连接Oracle

 加载驱动

获取数据库连接

关闭数据库

### 数据的导入和导出

#### 整库的导出和导入

##### 导出

exp system/密码 full=y

导出到EXPDAT.DMP文件，纯二进制

用”file=文件名“来指定导出文件名

##### 导入

imp system/密码 full=y

#### 按用户导入导出

##### 导出

exp system/密码 owner=用户名 file=文件名

##### 导入

imp system/密码 file=文件名 fromuser=用户名

#### 按表导入导出

##### 导出

exp 用户名/密码 file=文件名 tables=表1,表2

##### 导入

imp 用户名/密码 file=文件名 tables=表1,表2

### 单表查询

#### 简单条件查询

##### 精确查询

```sql
select ... from ... where ...
```

##### 模糊查询

```sql
select ... from ... where name like '%刘%'
```

##### 去掉重复记录

```sql
select distinct addressid from T_OWNERS
```

#####  排序

```sql
select * from .. order by ...//升序
select * from .. order by ... desc //降序
```

##### 伪列rowid、rownum

```sql
select rowid,t.* from t_owners t//t为别名
```

#### 聚合统计

```sql
sum
max
avg
count(*)
```

##### 分组聚合

```sql
select areaid,sum(money) from t_account group by areaid
```

select后面必须是分组聚合条件或者是聚合函数

###### 分组后的条件查询

```sql
select areaid,sum(money) from t_account group by areaid having sum(money)>100000
```

### 多表连接查询

#### 内连接

```sql
select ow.id 业主编号,ot.id 业主名称,ot.name 业主类型
from t_owners ow,t_ownertype ot 
where ow.ownertypeid=ot.id
```

#### 左外连接

以左表为主，右表对应的项可能为null

----SQL1999语法

```sql
select ow.id,ow.name,year,month,money from t_owners ow left join t_account ac 
on ow.id=ac.owneruuid
```

----Oracle语法

```sql
select ow.id,ow.name,year,month,money from t_owners ow,t_account ac 
where ow.id=ac.owneruuid(+)//ac为右表
```

#### 右外连接

与左外连接类似

----SQL1999语法

```sql
select ow.id,ow.name,year,month,money from t_owners ow right join t_account ac 
on ow.id=ac.owneruuid
```

----Oracle语法

```sql
select ow.id,ow.name,year,month,money from t_owners ow,t_account ac 
where ow.id(+)=ac.owneruuid
```

### 子查询

#### where子句中的子查询

##### 单行子查询

操作符：=、>、<、<>....

##### 多行子查询

操作符： IN、ANY、ALL

#### from子句的子查询

为多行子查询

#### select子句的子查询

```sql
select id,name,(select name from t_address where id=addressid) addressname from t_owners
```

### 分页查询

应用rownum伪列，如果没有经过扫描，rownum后的运算符必须是小于或者小于等于

#### 简单分页

```sql
select * from (select rownum r,t.* from t_account t) 
where r<=20 and r>10
```

#### 基于排序的分页

```sql
select * from (select rownum r,t.* from (select * from t_account t order by usenum desc) t) 
where r<=20 and r>10
```

### 特殊函数

#### 字符函数

##### 字符串长度

```sql
select length('ABCD') FROM dual
```

dual是伪表，只是为了补充语法而存在的

##### 求字符串子串

```sql
select substr('ABCD',2,2) FROM DUAL
```

2->从B开始

2->截取多少字符

##### 字符串拼接

```sql
select concat('ABC','D') FROM DUAL
```

```sql
SELECT 'ABC' || 'D' FROM DUAL
```

#### 数值函数

##### 四舍五入

```sql
select round(100.26,1) from dual
```

1->保留一位小数

##### 数字截取

```sql
select trunc(100.567) from dual
```

##### 取模

```sql
select mod(10,4) from dual
```

####  日期函数

##### 加月

```sql
select add_months(sysdate,2) from dual;--sysdate系统变量
```

##### 求所在月的最后一天

```sql
select last_day(sysdate) from dual;
```

##### 日期截取

```sql
select trunc(sysdate) from dual;--按日截取(把时间截掉)
select trunc(sysdate,'mm') from dual--按月截取(把日截掉)
select trunc(sysdate,'yyyy') from dual--按年截取
--按小时“hh”
--按分钟“mi”
```

#### 转换函数

##### 数字转字符串

```sql
select to_char(100)||'分' from dual--||字符串拼接
```

##### 日期转字符串

```sql
select to_char(sysdate,'yyyy-mm-dd') from dual
```

##### 字符串转日期

```sql
select to_date('2016-03-10','yyyy-mm-dd') from dual
```

##### 字符串转数字

```sql
select to_number("100") from dual
```

#### 其它函数

##### 空值处理函数

当检测值为null时，自动转换为某个值（多为0）

```sql
select nvl(null,0) from dual;
```

```sql
nvl2(null,不为null显示的值，为null显示的值)
```

##### decode条件判断

```sql
select decode(100,1,2,3,4,100,200) from dual
```

第一个100和1、3、100比，串行向前比较，和100相等，返回100后的200，最后还可以加上一个缺省值

###### 和case when then效果一致

```sql
select (case ... when value1 then 'v1'
                 when value2 then 'v2'
                 when ....
                 else 'vn'
                 end) from ....
```

### 行列转换

```sql
select (select name form t_area where id=areaid) 区域,
sum( case when month>='01' and month<='03' then money else 0 end) 第一季度，
sum( case when month>='04' and month<='06' then money else 0 end) 第二季度，
sum( case when month>='07' and month<='09' then money else 0 end) 第三季度，
sum( case when month>='10' and month<='12' then money else 0 end) 第四季度
from t_account where year='2012' group by areaid
```

### 分析函数

#### 值相同，排名相同，序号跳跃

```sql
select rank() over(order by usenum desc) 排名, t.* from t_account t
```

#### 值相同，排名相同，序号连续

```sql
select dense_rank() over(order by usenum desc) 排名, t.* from t_account t
```

#### 序号连续，不管值是否相同

```sql
select row_number() over(order by usenum desc) 排名, t.* from t_account t
```

### 集合运算

并集union

union 包含重复记录

union all去掉重复记录

交集

intersect

差集

minus







