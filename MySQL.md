### Mysql

#### 启动|停止|查看mysql

service mysql start|stop|status

##### mysql登录

mysql -u root -p +输入密码

set password = password("pass");//设置密码



#### 索引

一种数据结构，功能：高效获取数据，同时会指向数据

类型:BTREE,HASH,R-tree,Full-text

 BTREE -> B+tree的优化，增加叶子节点横向指针，便于进行一定范围的查找

索引分类：单值索引、唯一索引、复合索引

主键默认存在主键索引

创建索引

create index index_name on 表名(字段名);

查看索引

show index from 表名

删除索引

drop index 索引名 on 表名;



#### 视图

创建视图

create view view_name as select语句

不建议更新视图，因为视图是用来简化查询操作的

删除视图

drop view view_name;

查看视图

show view 



#### 存储过程和函数

事先经过编译并存储在数据库中的一段SQL语句的集合

好处：减少应用程序和数据库之间的交互，提高效率

##### 创建存储过程

```sql
CREATE PROCEDURE procedure_name([in/out/inout] 参数名 参数类型)
begin
//sql语句集合
end;
```

##### 声明分隔符

delimeter 新分隔符

##### 调用存储过程

call procedure_name();

##### 查看procedure

show procedure status;

##### 查看存储过程定义

show create procedure procedure_name;

##### 删除

drop procedure procedure_name;



##### 变量

```sql
begin
declare num int default 10;//声明变量，赋予默认值10
select concat("num的值为：",num);
end$//此时$为分隔符
```

变量赋值

```
//方案一
set num = num+10;
//方案2 select into
select count(*) into num from city
```

##### if条件判断

```sql
if search_condition then statement_list
[elseif search_condition then statement_list]...
[else statement_list]
end if;
```

当有out(输出变量时)

调用

call pro_test(180,@description)$ //@开头的变量表示用户会话变量，当前会话中变量有效，会话结束会释放

select @description$//$为分隔符

//如果变量有两个@开头，为系统变量



#### case结构：

```sql
方式1：
CASE case_value
WHEN value1 THEN statement_list
[WHEN value1 THEN statement_list]...
[ELSE statement_list]
END CASE;

方式2：
CASE 
WHEN search_condition THEN statement_list
[WHEN search_condition THEN statement_list]...
[ELSE statement_list]
END CASE;
```

#### while循环：

```sql
while search_condition do
    statement_list
end while;
```

#### REPEAT循环：

满足条件，退出循环

```sql
REPEAT
 statement_list
 UNTIL search_condition//条件后没有分号
END REPEAT;
```

#### LOOP循环：

退出条件通过LEAVE语句实现

```
[begin_label:]LOOP
statement_list
//退出语句实例
if n <= 0 then 
     leave begin_label;
end if;
END LOOP[end_label]
```

#### 游标||光标

用来存储查询结果集的类型

##### 声明

DECLARE CURSOR_NAME CURSOR FOR select_Statment;

##### OPEN光标

OPEN CURSOR_NAME;

##### FETCH光标

FETCH CURSOR_NAME INTO var_name[,var_name].....

##### CLOST光标

CLOST CURSOR_NAME;

##### 句柄机制

DECLARE has_data int default 1;

DECLEARE EXIT HANDLER FOR NOT FOUND set has_data = 0;//需要写在游标声明之后，功能，在没有找到数据时退出当前程序，同时设置has_data =0



#### 存储函数

有返回值

```sql
CREATE FUNCTION function_name([param type ...])
RETURNS type
BEGIN

END;
```

##### 调用

select fun1(2);



#### 触发器

###### 创建

```sql
CREATE TRIGGER trigger_name
before/after insert/update/delete
on TABLE_NAME
[for each row] //行级触发器，mysql只支持行级触发器
BEGIN
    trigger_stmt;
END;
```

new对象记录新增的行对象，old对象记录之前的对象

###### 查看

show triggers;

show triggers\G;//改变显示样式

###### 删除

drop trigger [schema_name.]trigger_name;



#### 存储引擎

##### InnoDB

存在事务transaction

外键约束

锁机制：行锁（适合高并发）

##### MyISAM

不支持事务和外键

其优势是访问的速度快

##### Memory

数据存储在内存中，每个MEMORY表对应一个磁盘文件，格式是.frm,该文件中只存储表的结构。

因数据存放在内存中，并且默认使用HASE索引，服务一旦关闭，表中的数据就会丢失。

##### MERGE

一组MyISAM表的组合，这些MyISAM表的结构必须结构完全相同，MERGE表本身并没有存储数据，对MERGE表进行操作，实际是去MyISAM表去处理。与视图类似。



#### 优化SQL步骤



##### 查看sql执行频率

查看当前连接各种命令使用

show status like 'Com_______'; //有七个占位符

查看全局各种命令使用

show global status like 'Com_______'; //有七个占位符

查看innodb各种命令使用

show global status like 'Innodb_rows_%'; 

定位低效执行sql

方案一：慢查询日志

方案二：show processlist//实时查看



##### explain指令：

用来查看sql语句执行计划

######  id项：

所有表id一样，表从上到下依次加载

id值不同，id越大，加载优先级越高

###### select_type项：

SIMPLE:简单select查询，不含子查询或UNION

PRIMARY:包含子查询的最外层标识

SUBQUERY:在SELECT或WHERE列表中包含子查询

DERIVED:在FROM列表中包含的子查询

UNION:第二个SELECT出现在UNION之后

UNION RESULT:从UNION表中获取结果的SELECT

###### type项：

NULL:不访问任何表、索引，直接返回数据

system:表只有一行数据，一般不出现

const：表示通过索引一次就找到了，通常根据主键或者唯一索引进行查询

eq_ref:多表关联查询，当关联出的数据只有一条

ref：非唯一性索引扫描，匹配到结果的所有行

range:范围查询，where 之后出现between,>,<,in等操作

index：查询整个索引树

all:遍历全表



###### possible_keys:

可能用到的索引

###### key:

实际用到的索引

如果为NULL，则没有使用索引

###### key_len:

key的长度

长度越短越好

###### rows:

扫描的行数

###### extra:

using filesort:文件排序，效率低

using temporary:使用了临时抱保存中间结果，常用于order by和group by

using index:避免访问数据行，效率不错



#### profile工具

查看当前系统是否支持profile工具

select  @@have_profiling;

查看当前系统是否开启profile工具

select @@profiling;

在当前会话开启

set profiling= 1;

查看所有操作耗时

show profiles;

查看操作耗时阶段

show profile for query QUERY_ID;

 查看所有信息

show profile all  for query QUERY_ID;



#### trace工具

分析优化器执行计划

第一步：打开trace，设置格式为json，并设置trace最大能够使用的内存大小

SET optimizer_trace="enabled=on",end_markers_in_json=on;

SET optimizer_trace_max_men_size = 1000000;

第二步：执行sql语句

第三步：select * from information_schema.optimizer_trace\G;



#### 索引的使用

#####  避免索引失效：

全值匹配：对索引中全部列均指定值

最左前缀法则：在复合索引中，查询需要从左到右不能跳过

##### 失效情况：

范围查询右边的列不能使用索引

不要在索引列上进行运算操作，比如substring()，否则索引失效

字符串不加单引号，索引失效

尽可能使用覆盖索引（查询字段在索引结构中已经记录），不用select *, 因为这种情况需要回表查询

使用or，且包含未进入索引的条件字段，则索引失效

用like模糊匹配，百分号加在前面，索引失效，解决方式：用覆盖索引的方式就使用索引了

如果Mysql评估使用索引比全表扫描更慢，则不使用索引

is NULL, is not NULL,有时索引失效，底层会判断走全表扫描还是走索引

in走索引，not in索引失效



#### 查看索引使用情况

show status like 'Handler_read%';

show global status like 'Handler_read%';



#### SQL优化

######  大批量插入数据

使用load data命令

对于InnoDB类型的表

1、建议以主键顺序插入

2、关闭唯一性校验 

导入数据前SET UNIQUE_CHECKS=0;

导入数据后 SET UNIQUE_CHECKS=1;

3、手动提交事务

SET AUTOCOMMIT=0;//关闭自动提交

SET AUTOCOMMIT=1;//开启自动提交，等导入数据后



#### 插入数据优化

```sql
INSERT INTO TB_TEST VALUES(1,"TOMS"),(2."CAT"),(3,"JERRY");
```

在事务中进行数据插入,数据量过大则分块插入

```sql
start transaction;
insert into tb_test values(1,"TOM");
....
commit;
```

数据有序插入



#### 优化order by语句

###### filesort排序

有两种排序算法：一次扫描算法，两次扫描算法

通过适当增加系统变量sort_buffer_size和max_length_for_sort_data，增大排序区的大小，提高排序的效率

###### using index排序（索引覆盖）

多个字段进行排序，如果一个升序，一个降序，那么还是会用到filesort

多个字段进行排序，排序的顺序需要和索引的顺序保持一致，不然还会用到filesort





