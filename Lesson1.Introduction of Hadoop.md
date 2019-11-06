## Introduction of Hadoop
#### Distribution System
1. 分布式并行环境的原因
（1）Data Storage
数据量增加，而读取数据的速度慢->solution：在100个磁盘同时读取
（2）Data Analysis
计算机分析任务大
2. What is Distribution System
一个集群（多台电脑）协同运作完成任务
3. CAP Theorem（定理）
一致性|有效性|分区容错性
（1）Consistency = High Speed
（2）Availability
（3）Patrition Tolerance：如果有机器出错，其他如何兼容
> 过分强调其中一点，则另两点弱
4. Why not use 关系型数据库
（1）传统关系型数据库
适用于信息快速检索，但不适于大规模数据处理，不适于数据结构复杂结构
（2）分布式数据库
因为电商和社交网络使得信息爆炸，而此前信息不多时主要考虑efficiency
① MySQL BTree：适合大规模数据的增删插入，重建一棵新的树
② HDFS based：Double Buffer Strategy(One write, multi read)
* 垂直扩展
增大一台机的信息量

* Horizontal Extension|水平扩展
多台同时运作，使得处理信息量大
① Mater-Slave Replication| step1&3常用
<img src="https://github.com/WIN0624/image/blob/master/Master-Slave.png?raw=true"  width = "50%" height = "50%"/>
step1. 主从复制：分布式分工处理，Slave只读，Master写，此后主从调换
Step2. 集群：多线程，多个data base对本地存储进行操作
Step3. Sharding|垂直分片：将所有20TB数据库进行分区，以a开头为第一分区....第n分区，则若query，判断此query的区域，仅在其所属分区进行检索
（3）Data Type
① Structured Data Store：key（键） field（字段）
② Unstructured ：以文件或文件夹形式存储

#### Development of Hadoop
1. 来源于爬虫
（1）Apache Lucene（搜索引擎底层框架）
（2）Apache Nutch 2002： 爬虫任务-> GFS(Google File System)
2. Google2004提出
（1）分布式存储 NDFS(Nutch Distribution File System 2004)
（2）分布式计算 Map Reduce
3. Hadoop(Apache Project2006)
（1）Make use of distributed calculation resources
（2）Handle large-scale data set
（3）Improve efficiency of data processing
（4）Keep System more steady.
=>使得可用脚本语言便捷操作，降低学习成本

#### Important Modules of Hadoop
1. Necesary Modules|必备
```
1. Hadoop Common: The common utilities that support the other Hadoop modules.
*2. Hadoop Distributed File System (HDFS): A distributed file system that provides high-throughput(高速读取) access to application data.
3. Hadoop Yarn (2.0 above版本才有): A framework for job scheduling and cluster resource management => 进行任务分配及资源管理
4. Hadoop MapReduce: A YARN-based system for parallel processing of large data sets.
```
2. Other Important Modules
```
1. Arvo: A data serialization(序列化) system.
* 2. Hbase: A scalable, distributed database that supports structured data storage for large tables => 工程
3. Hive|数据仓库: A data warehouse infrastructure that provides data summarization and ad hoc querying => 用于数据分析
4. Mahout|数据挖掘: A Scalable machine learning and data mining library => 和Spark一起做数据分析和挖掘
5. Pig|数据流处理语言: A high-level data-flow language and execution framework for parallel computation => 一个脚本语言，基于数据流进行并行化计算的计算框架
6. Streaming:  Allow different languages to run on hadoop => 运行其它编程语言Python|awk等在Hadoop运行
*7. Spark: A fast and general compute engine for Hadoop data. Spark provides a simple and expressive programming model that supports a wide range of applications, including ETL, machine learning, stream processing, and graph computation.
8. Zookeeper|分布式协作服务:  A high-performance coordination service for distributed applications => 协同服务机制，加强功能，将一些管理机制构成模型，选举机制|共同投票机制
```
> RPC：进行不同节点的通讯
#### Apllication|Intel
用Spark|替换Mahout进行数据挖掘
用awk+streaming替换Pig
<img src="https://github.com/WIN0624/image/blob/master/Intel.jpg?raw=true"  width = "50%" height = "50%"/>

#### Learning Process
分布式存储框架-> Map Reduce-> Hbase
