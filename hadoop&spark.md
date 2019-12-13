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