#### Neo4j和ES数据导入

建立全新的数据库（第一次导入），采用python脚本方式

增量更新情况（后续导入），es：python脚本，neo4j: apoc或load csv方式



### ES

格式CSV文件,以"es"开头

一条记录为一行，该行为ES中的一条数据，需要有明确的列名

导入方法：写python脚本导入

备注：目前导入数据类型均为string类型，可以进一步完善。



### neo4j

#### case1:数据库未建立：neo4j-admin

格式为CSV文件

分为节点类型和边类型，目前节点类型文件以"node"开头，边类型文件以“relation”开头

##### 节点类型必须有名为:ID列（说明：ID前加英文冒号），列的内容不一定是数字，但必须唯一标识该节点。例如：”node1“，建议有:LABEL列（说明：LABEL前加英文冒号），列的内容标识节点种类，例如：“movie”、“actor”

##### 边类型必须有名为:START_ID,:END_ID,:TYPE列（说明：在START_ID,END_ID,TYPE前加英文冒号），这三列分别说明：起始点ID,终止点ID，边类型ID

START_ID,END_ID，ID可以设定作用域，例如节点文件中 “:ID(Person_ID)”，同时在边类型文件中可以有“:START_ID(Person_ID)”作为列名。

除了以上规定的列名，其它列名均无特殊要求。其它列名格式为 "列名:列类数据格式" ,例如 “node1:int”，如果不指定也可以，但默认为string类型，

数据类型：Use one of `int`, `long`, `float`, `double`, `boolean`, `byte`, `short`, `char`, `string`, `point`, `date`, `localtime`, `time`, `localdatetime`, `datetime`, and `duration`

需要特别注意的是，CSV文件默认以英文逗号为分隔符，数据中应避免使用英文逗号，防止格式出错。

导入方法参考：

https://blog.csdn.net/oTianShiZaiChangGe1/article/details/105095947

各种导入方法比较：

http://weikeqin.com/2017/04/14/neo4j-import-data/

官方文档：

https://neo4j.com/docs/operations-manual/current/tools/import/file-header-format/



#### case2:数据库已建立

##### case2.1:batch-import

因为适用于Neo4j 3.x版本，暂时放弃这种方案。

参考链接

https://my.oschina.net/u/2538940/blog/883829

**报错**： *java*.*lang*.*IllegalAccessException*: *class org.mapdb.Volume*$*ByteBufferVol* cannot access class jdk.internal.ref.Cleaner (in module java.base)

解决方案：jdk版本过高了，使用jdk8

**报错**：按照参考链接，报unknown label type

解决方案：博主的配置文件的几行注释得打开

**报错**：Exception in thread "main" org.neo4j.kernel.impl.storemigration.StoreUpgrader$UnexpectedUpgradingStoreVersionException: 'neostore' has a store version 'SF4.0.0' that we cannot upgrade from.

解决方案：这个没有办法，必须得回退版本



##### case2.2用apoc导入

APOC导入方法 csv文件header不能有冒号，不要有START和END这种保留字，应该把原本的:ID列，:START_ID列和:END_ID列给换掉，:ID列不需要存在了，:START_ID和:END_ID需要同时换掉列名和内容。因为在用neo4j-admin时，这几列都不会放入数据库的，但apoc为了创建关系，必须把唯一标识加入数据库。

提交时使用 "cypher-shell -f 1.cypher"命令提交

###### 数据导入教程

https://blog.csdn.net/haveanybody/article/details/103493329?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-4.control&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-4.control

###### 大数据量时，可以批量提交：CALL apoc.periodic.iterate

###### 批量提交官方文档

https://neo4j-contrib.github.io/neo4j-apoc-procedures/3.4/cypher-execution/commit-batching/

###### neo4j apoc使用总结一（安装）

https://blog.csdn.net/timefisher/article/details/79893826





##### 脚本代码

```python


from elasticsearch import Elasticsearch
from elasticsearch import helpers
import pandas as pd
import os
import sys

es = Elasticsearch()


# return list of es_*.csv ,list of neo4j_*.csv
def list_file(_file_dir):
    _list_es = []
    _list_neo_node = []
    _list_neo_rela = []
    for file_name in os.listdir(_file_dir):
        file_path = os.path.join(_file_dir, file_name)
        if file_name[0] == 'e':
            _list_es.append(file_path)
        elif file_name[0] == 'n':
            _list_neo_node.append(file_path)
        else:
            _list_neo_rela.append(file_path)
    return _list_es, _list_neo_node, _list_neo_rela


# csv --> index
def import_data(ind, file_name, _index_name):
    actions = []
    df_iterator = pd.read_csv(file_name, chunksize=1000)
    for data in df_iterator:
        columns_name_list = data.columns.values.tolist()
        for index, row in data.iterrows():
            d = {}
            for columns_name in columns_name_list:
                d[columns_name] = row[columns_name]
            action = {"_index": _index_name, "_id": ind, "_source": d}
            ind += 1
            actions.append(action)

        helpers.bulk(es, actions)
        del actions[0:len(actions)]

    return ind


# 自定义的id从0开始
def es_load(file_list, _index_name, is_new=True):
    if is_new:
        ind = 0
        for file in file_list:
            ind = import_data(ind, file, _index_name)
    else:
        ind = es.count(index=_index_name)['count']
        for file in file_list:
            ind = import_data(ind, file, _index_name)


def deal_attribute(filename):
    column_names = pd.read_csv(filename, index_col=0, nrows=1).columns.tolist()
    s = ""
    for name in column_names:
        if name == "SID" or name == "EID":
            continue
        else:
            s += "link."+name + "= links." + name + ","
    s = s[:-1]
    return s
# os.system(cmd)
# if is_new  neo4j-admin
# else batch import
def neo4j_load(node_file_list, rela_file_list, _database_name, _is_new=True):
    if _is_new:
        s = "neo4j-admin import --database="+_database_name+" "
        for file in node_file_list:
            s = s + " --nodes " + '"'+file+'"'+" "
        for file in rela_file_list:
            s = s + " --relationships " + '"'+file+'"'+" "
        os.system(s)
    else:
        ## filename node_object.csv
        content = ""
        for file in node_file_list:

            filename = file.split("\\")[-1]
            ind = filename.split(".")[0].find("_")
            ind2 = filename.find(".")
            subject = filename[ind+1:ind2]
            content += "CALL apoc.periodic.iterate('CALL apoc.load.csv(\"" + filename\
                       + "\") yield map as A return A','CREATE (m:" + subject \
                       + ") SET m= A', {batchSize:10000, iterateList:true, parallel:true});"
            content += "\n"
        ## filename relation_object1_object2_ACT_IN
        for file in rela_file_list:
            attr = deal_attribute(file)
            filename = file.split("\\")[-1]
            l = filename.split(".")[0].split("_")
            object1 = l[1]
            object2 = l[2]
            rela = "_".join(l[3:])

            content += "CALL apoc.periodic.iterate('CALL apoc.load.csv(\""+filename \
                       + "\") yield map as links return links','MERGE(a:" \
                       + object1 + "{ID:links.SID}) MERGE(m:" + object2 + "{ID:links.EID}) MERGE (a)-[link:" +\
                       rela+"]->(m) SET " + attr + "', {batchSize:10000, iterateList:true, parallel:true});"
            content += "\n"
        with open("1.cypher", "w") as f:
            f.write(content)

        os.system("cypher-shell -f 1.cypher")
        #os.remove("1.cypher")



def read_sys():
    if len(sys.argv) != 5:
        print("the expected number of argvs is 4 and get "+str(len(sys.argv)))
        print("the example is: python es.py [csv数据所在路径] [ES索引名] [Neo4j数据库所在路径] [1表示是增量插入|0表示新创建]")
        exit(0)
    else:
        _file_dir = sys.argv[1]
        _index_name = sys.argv[2]
        _database_name = sys.argv[3]
        already_exist = sys.argv[4]
        if already_exist == "1":
            _is_new = False
        else:
            _is_new = True
        return _file_dir, _index_name, _database_name, _is_new


if __name__ == "__main__":
    file_dir, index_name, database_name, is_new = read_sys()
    #file_dir = "F:\\neo4j-community-4.1.3\\import"
    # database_name = "graph.db"
    # is_new = False

    # 文件路径例子：'.\\actor.csv'
    list_es, list_neo_node, list_neo_rela = list_file(file_dir)

    es_load(list_es, index_name, is_new)
    neo4j_load(list_neo_node, list_neo_rela, database_name, is_new)
```





APOC导入数据实例：

CALL apoc.periodic.iterate('
CALL apoc.load.csv("movie.csv") yield map as movie return movie
','
CREATE (m:movie) SET m= movie
', {batchSize:10000, iterateList:true, parallel:true});



CALL apoc.periodic.iterate('
CALL apoc.load.csv("actor.csv") yield map as actor return actor
','
CREATE (a:actor) SET a= actor
', {batchSize:10000, iterateList:true, parallel:true});



CALL apoc.periodic.iterate('
CALL apoc.load.csv("links.csv") yield map as links return links
','

MERGE(a:actor{ID:links.SID})

MERGE(m:movie{ID:links.EID})
MERGE (a)-[act_in:ACT_IN]->(m)
', {batchSize:10000, iterateList:true, parallel:true});

