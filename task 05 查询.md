查询

cypher语句：

```
MATCH (n)<-[:DEPARTMENT_IS]-(d:Disease)-[:HAS_COMPLICATION]->(c:Complication) WHERE c.name='{0}' return d.name,n.name"
```

一个问题：

查询后回答结果中会有重复值：

如糖尿病怎么治疗

小豪： 疾病 糖尿病 的治疗方法有：药物治疗、饮食疗法、运动治疗、心理治疗 ；可用药品包括：盐酸二甲双胍片,糖脉康颗粒,降糖宁胶囊,糖尿乐胶囊,盐酸二甲双胍片,糖脉康颗粒,降糖宁胶囊,糖尿乐胶囊

经试验，发现这是因为原始数据中存在重复但不完全一致的记录导致的。

![image](https://github.com/Shiyaoa/datawhale_knowledgegraph/blob/main/pic/1.png)
![image](https://github.com/Shiyaoa/datawhale_knowledgegraph/blob/main/pic/2.png)
![image](https://github.com/Shiyaoa/datawhale_knowledgegraph/blob/main/pic/3.png)



但很搞笑的是，read_csv时，一个是“糖尿病”，一个是“ 糖尿病”,多了一个空格。在生成节点时用了strip()又删除了空格，所以在Neo4j里查询“糖尿病”会有两个记录，在dataframe里面只有一个记录
