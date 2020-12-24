CSV数据格式说明



### ES

格式CSV文件,以"es"+下划线开头

一条记录为一行，该行为ES中的一条数据，需要有明确的列名

目前导入的属性类型默认为string类型

#### Neo4j

式为CSV文件

###### 文件名要求

分为节点类型和边类型，目前节点类型文件以"node"+下划线+该节点对应的label，边类型文件以“relation”+下划线+起始节点label+下划线+终止节点label+下划线+边对应label。

例子： ”node_actor.csv“,"relation_actor_movie_ACT_IN.csv"

###### 文件中列名要求

节点类型必须有ID列，唯一标识节点

边类型必须有SID和EID列，分别唯一标识起始节点的ID和终止节点的ID

###### 注意点：

节点类型的ID会输入到数据库中，但边类型中的SID和EID不会输入到数据库中

