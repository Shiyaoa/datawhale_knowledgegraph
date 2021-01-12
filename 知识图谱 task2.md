### 参考资料
https://github.com/zhihao-chen/QASystemOnMedicalGraph

今天内容比较简单，需要注意版本问题：

1.原始模型文件是较老版本sklearn生成的，无法直接用joblib来加载，解决方法是：
新建一个old虚拟环境，安装scikit-learn==0.21.0。先用import sklearn.externals.joblib as oldjoblib来load老模型，再import joblib，用joblib来再次dump模型覆盖旧模型。
这样就可以直接用joblib直接load，无需回退sklearn版本了。

2.tf_idfmodel.transform()是一个比较老的用法，会报warning。但我还没看0.23的sklearn应该用什么，明天解决。

还需要解决的问题：
1.多进程并行导入数据，如果数据有重复或者矛盾，会不会导致数据库冲突？     13号实验一下
2.查询语言的意图识别，可以写个更新的出来吗？训练新模型的话如何训练？     14号的任务
