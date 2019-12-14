培训
===
# Hadoop&Spark

## Hadoop

### HDFS
HDFS(Hadoop Distributed File System):分布式文件系统，将数据和存储分离为NameNode和DataNode

#### NameNode
名字节点，master角色，负责管理文件系统名称空间和控制外部客户机的访问

关于重要数据备份三副本是标配，两个副本放在不同机架位上，另一个副本位于另一机房

![avatar](/img/hdfs.jpg)

引入standby NameNode与active NameNode的架构设计，从而进行交互保证信息一致


#### DataNode
数据节点，小弟角色，可以随时删减，响应来自HDFS客户机的读写请求，用于存储文件块

#### HDFS的读写操作
NameNode响应客户端的请求，客户端再与DataNode进行通信进行后续操作，如果是写多副本则依次由上一个DataNode通知下一个DataNode进行数据写入

![avatar](/img/fangwen.jpg)
![avatar](/img/datatrans.jpg)

### Hadoop MapReduce
MapReduce:为海量的数据提供计算，采用分而治之的思想降低资源的开销

* Map:Map Task，对输入的每条记录执行map操作
* Shuffle:对数据进行分组后传输到Reduce，类比洗牌
* Reduce:Reduce Task一次只处理一个任务，规约出结果集或部分结果集，最后写入HDFS
* Combine:可选操作，减少传输给reducer的数据量
* Split:数据分片，分配到不同mapper，降低数据传输成本

Hive可以把SQL翻译成Map Reduce

![avatar](/img/hive&mapreduce.jpg)

## Spark
### Why Spark
Map Reduce的两大不足
* 不支持流数据，只能处理批数据
* 速度慢(每一步操作都要写磁盘)

Spark Adventages
* Spark Core:支持批处理
* Spark SQL:支持SQL
* Spark Streaming:流数据的近实时处理，最大优势
* MLlib:机器学习算法的Spark实现
* GraphX:图API的Spark实现
快，易用，通用，兼容

# Hive
Hive用来管理和查询结构化数据的数据仓库系统

Hive系统构建原则
* 可扩展性
* 延展性:自定义
* 良好的容错性

![avatar](/img/hive.jpg)

## HiveSQL特性(主要考点)
HveSQL与SQL的区别:不支持update和delete

Hive建表一般都会建分区(Pt=2019-12-01)是为了减少查询时返回的数据量，建表都会指定数据存放路径也同样能够提高查询效率

## Hive数据模型
### 建表语句
#### 外部表(EXTERNAL)
```sql
create EXTERNAL TABEL wifi_active_device(
  imei string,
  mac  string
)
```
![avatar](/img/externaltable.jpg)

#### 插入语句
#### 加载数据
#### 更新数据
INSERT OVERWRITE TABLE

### Join
与普通的join语句不同，它是解析成mapreduce

![avatar](/img/hivejoin.jpg)
```sql
SELECT t1.name, t2.orderid
FROM user t1
JOIN order t2
on t1.uid=t2.uid
```
#### 如何优化join
大表*小表，循环去join小表

set hive.auto.current.join=true;

set hive.mapjoin.smalltable.filesize;

例如user(100G)和log(50G)

### Group by
shuffle后相同的key洗牌到同一个reduce中

#### 如何优化group by
由于key分布不均匀导致reduce时都分配到了同一个reduce中，导致这个reduce计算的时间较长等原因，group by过程中会出现数据倾斜

set hive.map.aggr=true;

set hive.groupby.skewindata=true;

第一次MapReduce在shuffle时随机分配到reduce，第二次MapReduce在shuffle时按key分区

## Hive Map数
Map数的计算规则：
* 默认map个数
* 期望大小
* 设置处理的文件大小
* 计算的map个数

Reduce数的计算规则：控制reducer的数量能够有效提高执行效率
set mapred.reduce.tasks;

set hive.exec.reducers.max

## UDF(自定义函数)
类似于java中jar包

## Hive执行计划
```sql
explain select * from table1 where pt='2019-02-01' limit 10;
```

考试要点:技术原理和HiveSQL

## AB Test中的统计学原理
### 为什么要做AB测试
优化转换率，不断迭代设计，随时改善用户体验

技术难点
 * 代表性用户流量均分
 * 指标统计结果置信度量

### 为什么要做AB平台
* 先验性：小部分代表性用户先验证
* 科学性
* 并行性
* 低风险

### A/B Test的方法
传统方式：分渠道上线测试功能，逐步进入到增量用户（渠道之间存在差异会导致数据的偏差）

先进方式：云端控制分发比如Twitter-DDG、太极（样本量一致，样本影响变量唯一，灰度发布、上线方便）

### AB测试的实现流程
分流模型
* 域(domain):划分的一部分流量
* 层(layer):系统参数的一个子集
* 实验(exp):在一个域上，对一个或多个参数修改，改变请求路径的过程

同一个用户两次实验采用不同的编号，样本就在两次实验就分别可以作为实验组和对照组
![avatar](/img/fenliu.jpg)

AB Test流程带来的优势：保证实验的随机性、独立性、样本量，可以采用卡方检验、t检验等排除随机变量、样本量等因素造成的影响

### AB Test中的统计学原理
统计推断原理：通过样本来推断总体

#### 简单随机抽样
总体、个体与样本的概念:

从总体中抽样必须满足两个条件
* 随机性
* 独立性

思考:参加培训的同事是不是随机抽样？不是

#### 抽样分布
样本均值、样本方差的概念

中心极限定理
* 样本的平均值约等于总体的平均值
* 不管总体是什么分布，任意一个总体的样本均值都会围绕在总体的整体均值周围，并且呈`正态分布`

中心极限定理的作用
* 在无法获取总体数据情况下用样本估计总体
* `根据总体的平均值和标准差，判断某个样本是否属于总体`

3sigma原则

假设检验:

小概率事件和显著性水平:小概率时间，是指在一次事件中几乎不可能发生的事件

P值:如果P值`小于`设定的显著性水平，则拒绝原假设

总体比例假设检验:Z检验与区间估计、拒绝域
```R
prop.test(45, 100, p=0.4)
X-squared=0.84375,df=1,p-value=0.3583
```

假设检验样本量小或者样本差异小的情况下进行

#### AB Test的流量到底开多大合适
先验条件:原始版本转化率，最小提升率，显著性水平α，统计功效(1-β)，实验所包含版本数


## 算法入门
### 点击率预估案例
问题：
* 每天入库文章和视频20w+
* 可推荐文章和视频100w+
* 怎么推荐
  * 候选池众多
  * 如何让用户点的更深

#### 数据搜集和处理
* 文章视频

#### 模型选择
模型不能太复杂(过拟合)也不能太简单(欠拟合)

#### 损失函数选择
数据不一样，损失函数不一样

比如点击率(01分布)，成交额(正态分布)

#### 模型训练
训练集和测试集不能重复

#### 模型评价

### AUC
AUC:Area Under Curve，值域[0, 1]

AUC的特性
* 应该大于0.5，模型至少要比随机表现的更好

#### 计算
TP,FP,TN,FN
recall:错的有多离谱
accuracy:预测的有多准

#### 描点
* 设置评判标准(阈值)：阈值达到多少，可以被认定为证，否则为负
* 计数：以上四个指标

#### 算面积

#### python计算AUC
```python
```

概率论，矩阵，最小二乘法，矩阵不满秩如何解决(加入L1,L2正则化)
基础考试范围：Hive,Spark,Hadoop原理，HiveSQL，统计学习方法第一章