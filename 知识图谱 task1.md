## 知识图谱 task1 

### Cypher查询语言的熟悉

参考https://www.jianshu.com/p/53e2a67e9f40

```css
### 节点语法
() 匿名节点
(matrix)  为节点添加一个ID
(:Movie) Movie label标签，声明的是节点类型。noe4j的索引使用label，每个索引由标签和属性组成
(matrix:Movie)
(matrix:Movie {title: "The Matrix"}) 节点属性(如：title)代表一个key\value 的List
(matrix:Movie {title: "The Matrix", released: 1997})

### 关系的语法
--  表示一个无指向的关系
--> 表示一个有指向的关系
[] 能够添加ID，属性，类型等信息
-[role]->
-[:ACTED_IN]->
-[role:ACTED_IN]->
-[role:ACTED_IN {roles: ["Neo"]}]->

### pattern语法
(keanu:Person:Actor   {name: "Keanu Reeves"} )
-[role:ACTED_IN   {roles: ["Neo"] } ]->
(matrix:Movie    {title: "The Matrix"} )
acted_in = (:Person)-[:ACTED_IN]->(:Movie)
这个是实例化，方便重复使用pattern

### 增
## 增加节点
CREATE (n:Person {name:'John'}) RETURN n
## 增加关系
MATCH (a:Person {name:'Shawn'}), 
	(b:Person {name:'Sally'}) 
MERGE (a)-[:FRIENDS {since:2001}]->(b)
或者也可以用create 方式按照pattern来创建
CREATE (JamesThompson)-[:FOLLOWS]->(JessicaThompson)

多行输入的问题？用; 注意enable multi statement query editor

###查
用match找pattern
可以指定属性、关系的值，或者只要关系存在即可

### 删和改
match ... set  
match ... delete 
可以删属性，节点，关系

能改节点或者关系的label吗？还没试


```

通过Python操作Neo4j

neo4j的包：

`driver = GraphDatabase.driver("bolt://localhost:7687", auth=("neo4j", "123456"))`

py2neo的包：

`g = Graph("bolt://localhost:7687", auth=("neo4j", "123456"))`

<u>需要注意的问题是，及时进行neo4j stop ，避免进程冲突！</u>



import csv数据

数据参考https://neo4j.com/docs/stable/re03.html

方法参考https://blog.csdn.net/weixin_43986252/article/details/92829827

官方只给出了linux下的命令，Windows下大同小异

首先是数据集构建：

nodes的csv文件需要列名如下：

node_ID:ID(就是主键), node_properties(可自定义), :LABEL(多label用;来分隔)

relationships的csv文件需要列名如下：

:START_ID, relation-properties(可自定义), :END_ID, :TYPE

把csv文件全部放在/root/import 文件夹以下

把/root/bin添加至环境变量，cmd执行命令如下

```
neo4j-admin import --nodes=import/movies.csv --nodes=import/actors.csv --relationships=import/roles.csv   --delimiter=, --database=movie.db
```

还需要注意的问题：import之前要先关闭数据库；Neo4j社区版只默认使用一个数据库，需要在conf文件里去修改

```
dbms.active_database=movie.db
```

如果用桌面版的话，注意端口