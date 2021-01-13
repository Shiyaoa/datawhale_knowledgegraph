## task03 数据导入

采用py2neo包将csv数据导入到neo4j ，相比于通过import直接导入，操作上更加便捷（？有吗）

参考资料：

py2neo的官方文档：https://py2neo.readthedocs.io/en/latest/index.html（有点混乱，例子清奇，当字典查）

比较全面的：https://www.jianshu.com/p/febe8a248582

### py2neo的数据类型包括node，relationship，subgraph

首先要生成graph实例，在graph实例上进行create（node/relationship/subgraph)或者run(cypher_query)。

- node数据类型，参数是(*labels, **properties*)
- relationship数据类型，参数是（*start_node*, *type*, *end_node*, ***properties*）
- subgraph数据类型，参数是（*nodes*, *relationships*），不必同时包含，nodes或者relationships组成list后传入，更简单的做法是直接用  | 来union多个subgraph或relationships

 创建node和edge时是先实例*node* **a**,**b**，再实例*relationship* **ab** ，graph.create(**ab**)即可生成节点和边，但是如果先create(**a**和**b**)以后，再次create(**ab**)就会重复建node，问题也就是如果数据库中已经存在了node，该如何去创建节点？

此时要用match操作找出目标节点，示意如下：

```
a_have=graph.nodes.match("Person",name="Alice").first()
b_have=graph.nodes.match("Person",name="Bob").first()
rel_a=Relationship(a_have,"likes",b_have)
graph.create(rel_a)
```

 注意match的结果类型是“py2neo.matching.NodeMatch”，用first()取出类型才是“py2neo.data.Node”

参考资料：https://blog.csdn.net/michaelguangg/article/details/100704303

### 批处理操作

#### 批量生成节点：

不要每次遍历节点生成node后立即create，可以先组成nodes_list，生成subgraph实例后再集体create，速度快。

```
tx=self.graph.begin()
dnodes=[]
for node_name in nodes:
	node = Node(label, name=node_name)
	dnodes.append(node)
	count += 1
	print(f"{label}:",count,"/", len(nodes),sep="")
dnodes=Subgraph(dnodes)    
tx.create(dnodes)
tx.commit()
```

#### 批量生成关系边：

1.如果是还没有节点，同上

直接生成node实例，再创建relationship实例，遍历生成list，添加至subgraph，最后create



2.如果是给已经存在的节点创建关系边：用run(cypher_query)

```
for edge in set(set_edges):
	edge = edge.split('###')
	p = edge[0]
	q = edge[1]
	query = "match(p:%s),(q:%s) where p.name='%s'and q.name='%s' create (p)-[rel:%s{name:'%s'}]->(q)" % (start_node, end_node, p, q, rel_type, rel_name)
	try:
		self.graph.run(query)
		count += 1
		print(rel_type, count, all)
	except Exception as e:
		print(e)
```

cypher_query要比用py2neo的match，relationship，create三步走要快



### 多进程

直接根据不同节点建立多个process分别执行的操作不可行。。猜测涉及数据库的操作都会有事务锁一类吧，有大佬说可以关闭py2neo的事务机制。



### 其他问题

导入数据的程序interrupt以后再次执行会重复建点，关系边也会重复建，不能像关系数据库一样自己指定primary key还是有些麻烦的，该如何去重？



