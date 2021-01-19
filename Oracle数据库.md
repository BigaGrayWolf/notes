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

### 视图

#### 创建或修改视图

```sql
CREATE [OR REPLACE] [FORCE] VIEW view_name --OR REPLACE表示view已存在的话就替代
--FORCE 表示subquery中不一定需要存在基表
AS subquery
[WITH CHECK OPTION]--设立一个带检查的视图，插入或修改的数据行必须满足视图定义的约束
[WITH READ ONLY]--只读视图
```

#### 删除视图

```sql
drop view view_name
```

#### 复杂视图

键保留表可以做修改，但其它表只能做查询。

因为键保留表保留了原本表的主键，所以可以对应到特定的行数据上。



### 物化视图

#### 会建数据的副本，牺牲空间换取查询时间

```sql
CREATE METERIALIZED VIEW view_name
[BUILD IMMEDIATE | BUILD DEFERRED]--是否创建后立即生成数据,默认 IMMEDIATE
--DEFERRED 默认创建时没有数据，需要手动执行刷新
REFRESH [FAST|COMPLETE|FORCE]--采取何种方式与基表保持同步,默认 FORCE，自动选择是增量更新还是完全更新
--FAST:增量刷新的物化视图 前提1、是必须创建物化视图日志：记录基表发生了哪些变化，用这些记录去更新物化视图
--2、创建物化视图的语句中，查询结果中必须有基表的唯一标识（对应日志）
[
    ON [COMMIT|DEMAND] | START WITH (START_TIME) NEXT (NEXT_TIME)
    --ON COMMIT在基表做提交操作时更新视图
    --ON DEMAND手动刷新，默认值
]
AS 
SUBQUERY
```



#### 执行下列语句进行手动刷新

```sql
begin--PLSQL
DBMS_MVIEW.refreash('物化视图名','C')--C指Complete 完全刷新
end
```

#### 创建物化视图日志

```sql
create materialized view log on 表名 with rowid --根据rowid进行更新物化视图
```

### 序列

```sql
create sequence 序列名
```

在插入时：序列名.nextval 作为值进行插入，这是一个伪列

当前值：序列名.currval

#### 复杂序列

```sql
CREATE SEQUENCE sequence_name
[INCREAMENT BY n] --递增的序列值是n,每次加n
[START WITH n]
[{MAXVALUE n | NOMAXVALUE}]--最大值
[{MINVALUE n | NOMINVALUE}]
[{CYCLE|NOCYCLE}] --循环|非循环
[{CACHE n|NOCACHE}] --分配并存入内存中
```

#### 修改序列

```sql
ALTER SEQUENCE 序列名称 maxvalue 5000 cycle;
```

不能更改序列的START WITH参数

#### 删除序列

```sql
DROP SEQUENCE 序列名称
```

### 同义词

别名

创建同义词

```sql
create [public] SYNONYM synoonym for object;--object表示表、视图、序列等我们要创建同义词对象的名称
--public表示共有同义词，未隔离不同用户
```

### 索引

#### 创建索引

```sql
CREATE [UNIQUE] INDEX 索引名 on 表名(列名);
```

在INDEX前加UNIQUE关键字-->唯一索引

##### 复合索引

```sql
CREATE INDEX 索引名 on 表名(列名1，列名2);
```

#### 反向键索引

当某个字段的值为连续增长的值-->建普通索引会是一棵歪脖子树

为了随机化->转化成二进制以后再倒置，最后返回10进制

```sql
CREATE  INDEX 索引名 on 表名(列名) reverse;
```

#### 位图索引

```sql
CREATE bitmap index 索引名 on 表名(列名);
```

低基数（值是有限的）时适合建位图索引

不能按范围查询



### ORACLE编程

#### PL/SQL

基本语法结构

```sql
[declare 
--声明变量
]
begin
--代码逻辑
[exception
--异常处理
]
end;
```

##### 变量

声明变量

```
变量名 类型(长度);
```

变量赋值

```sql
变量名:=变量值

select ... into .... where ...
```

##### 属性类型

###### 引用型 从数据库中提取列的类型

```sql
变量名 表名.列名%type
```

###### 记录型 把整个行的记录赋给变量

```sql
变量名 表名%rowtype
--取出变量时
变量名.列名
```

##### 异常

```sql
exception
when 异常类型
then
逻辑处理
```

##### 条件判断

```sql
if then
endif;

if then 
else
endif;

if then
elsif
else
endif;
```

##### 循环

###### 无条件循环

```sql
loop
--循环语句 通过exit跳出循环
end loop;
```

###### 有条件循环

```sql
while 条件 --条件成立，则循环
loop

end loop;
```

###### for循环

```sql
for 变量 in 1...100 --变量从1到100
loop
end loop;
```

##### 游标

存放查询结果的结果集

###### 在声明区声明游标

```sql
cursor 游标名称 is SQL语句;
```

###### 使用游标语法

```sql
open 游标名称
loop
   fetch 游标名称 into 变量
   exit when 游标名称%notfound
end loop;
close 游标名称
```

###### 带参数的游标

```sql
cursor 游标名称(参数名 类型) is SQL语句;--SQL语句带有未定参数
```

###### for循环游标

```sql
for 变量 in 游标名称[(参数值)]--参数项可选
loop
执行代码
end loop;
```

##### 存储函数

```sql
CREATE [OR REPLACE] FUNCTION 函数名称
(参数名称 类型,参数名称 类型)--不写参数长度
RETURN 结果变量数据类型
IS
变量声明
BEGIN
函数体
END;
```

##### 存储过程

可以返回多个值，通过传出参数来返回

不能在select语句中调用，多数时被应用程序所调用

```sql
CREATE [OR REPLACE] PROCEDURE 存储过程名称
(参数名 类型，参数名 类型，参数名 类型)--参数只指定类型，不指定长度
IS|AS
变量声明
BEGIN
逻辑部分
[EXCEPTION
异常处理部分
]
END;
--参数模式 IN ||OUT  ||IN OUT 在参数类型前加

```

调用

```sql
call 存储过程名称(); --不带传出参数

--带传出参数
declare
   v_id number;
begin
存储过程名称(v_id); --执行完毕后v_id就有值了
end;
```

##### 触发器

###### 应用

数据确认

复杂的安全性检查

做审计，跟踪表上所作的数据操作

数据的备份与同步

###### 触发器语法

```sql
CREATE [OR REPLACE] TRIGGER 触发器名
BEFORE | AFTER --前置触发器or后置触发器，commit前触发还是commit后触发
[DELETE][[OR] INSERT][[OR]UPDATE [OF 列名]]
ON 表名
[FOR EACH ROW][WHEN(条件)]--是否为行级触发器
declare 
...
begin
...
end;
```

伪记录变量

:old,:new 

