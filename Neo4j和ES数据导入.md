#### Neo4j和ES数据导入

##### ES

格式CSV文件

一条记录为一行，该行为ES中的一条数据，需要有明确的列名

导入方法：写python脚本导入，脚本范例见最后



##### neo4j

格式为CSV文件

分为节点类型和边类型

##### 节点类型必须有名为:ID列（说明：ID前加英文冒号），列的内容不一定是数字，但必须唯一标识该节点。例如：”node1“

##### 边类型必须有名为:START_ID,:END_ID,:TYPE列（说明：在START_ID,END_ID,TYPE前加英文冒号），这三列分别说明：起始点ID,终止点ID，边类型ID

除了以上规定的列名，其它列名均无特殊要求。

需要特别注意的是，CSV文件默认以英文逗号为分隔符，数据中应避免使用英文逗号，防止格式出错。

导入方法参考：

https://blog.csdn.net/oTianShiZaiChangGe1/article/details/105095947

各种导入方法比较：

http://weikeqin.com/2017/04/14/neo4j-import-data/



###### ES导入脚本范例

```
from elasticsearch import Elasticsearch
from elasticsearch import helpers
es = Elasticsearch()
actions = []

i=3
f = [["1","2"],["3","4"]]

for line in f:
    action={
    "_index":"image",
    "_id":i,
    "_source":{
    "name":line[0],
    "content":line[1],
    }
    }
    i+=1
    actions.append(action)
    if(len(actions)==500):
        helpers.bulk(es, actions)
        del actions[0:len(actions)]
    if (len(actions) > 0):
        helpers.bulk(es, actions)
```