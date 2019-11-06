## Lesson2   Hadoop 两大框架

#### Learning Process
分布式存储框架-> Map Reduce-> Hbase

#### Ⅰ 分布式存储框架|HDFS(Google File System)
```
# 分布式
1. 单个文件存储
原有文件通过offset放入block中，通过index访问，删除文件一般是删除index，恢复时也是恢复index
2. 分布式文件存储
将file分成多份，在不同机器构建其index放入该台机器的server/machine，index有顺序，按顺序才能构成文件

#Trait| 高吞吐量（速度不一定快）+ 流式数据访问（不等到全部接收再处理，而是数据批处理）

# HDFS相关概念
1. Metadata：元数据。包括名称空间、文件到文件块的映射、文件块到Datanode的映射
2. NameNode：一般一个集群只有一个，管理文件系统的命名空间，维护文件系统文件树及所有文件和目录的元数据，即MetaData。
3. Secondary NameNode：NameNode故障的备用节点，NameNode用久之后edit logs变很大，重启时间久，将更新fsimage文件夹的任务分给SecondaryNameNode
4. DataNode：HDFS中保存数据的节点，根据需要存储并检索数据块，受客户端或NameNode调度，定期向NameNode报告其存储的数据块列表
5. 块：HDFS中存储单位，默认64MB
```

##### TOPIC_0：HDFS体系结构
```
1. 主从结构| NameNode为Master，其他DATANode为Slave，文件以数据块形式存储在DataNode中(数据块ID和数据块内容, 未必全部使用，有空白的数据块空间)
2. SecondaryNameNode一般存在另一台物理机，NameNode发生故障时进行数据恢复
3. DataNode定期向NameNode发送心跳信号提示正常工作
4. DataNode和NameNode可交互：NameNode对文件块创建、删除、复制等进行指挥和调度，DataNode仅在接受NameNode命令后开始任务
5. Client进行读写操作之前，需先从NameNode获得文件存储的元数据信息
```
<img src="https://img-blog.csdn.net/20160415202744965" width = "50%" height = "90"/>

##### TOPIC_1：HDFS工作原理
```

```
##### Application_1：GFS实现
<img src="https://github.com/WIN0624/image/blob/master/%E5%88%86%E5%B8%83%E5%BC%8F%E6%96%87%E4%BB%B6%E5%AD%98%E5%82%A8.png?raw=true"  width = "50%" height = "170"/>

```
—— Basic Storage Element：Block
A file can be divided to different physic blocks.
—— Master/Slave Structure
(1) Namenode：对应master，管理整个文件系统，分配节点，可以对datanode增删和复制任务
    用户仅将需求提交给Namenode即可，Namenode是唯一连接外界命令的界面且从不同datanode得到数据
    namenode->datanode等后台资源管理=云计算
(2) Datanode: 存储文件的部分和index，每个datanode对应一个slave节点
"""
Master|ResoureManager：进行资源分配
Slave| NodeManager：计算资源
"""
```
##### Application_2：容错性
```
If one datanode is broken？
[心跳检测机制]_后台自动容错
1. datanode will send heartbeat to namenode every 3 second.
2. If datanode does not send heartbeat more than 3 times, namenode will consider that it is broken.
3. namenode will fetch(get) backup data from other good datanodes.
4. Secondary Namenode and Secondary datanode will help to realize data recover. 
```

#### Ⅱ 分布式计算框架| MapReduce
基于key-value的计算格式
##### TOPIC_0: Definition&Function
```
# Definiton
Map = 映射(What to do, 将数据打散、分片)
Reduce = 化简(How to do, 对数据聚集)

e.g 54张扑克多少张黑桃
Map：将牌尽可能均匀分给多位牌友，每人计算自己手上有多少张
Reduce：将所得各张数相加

# Function
1. Map：将一个函数应用于集合中所有成员，返回一个结果集
2. Reduce：对多个进程(子系统)并行执行，并将多个处理结果集进行分类和归纳(有时分类可能正是map的函数任务)
```
##### TOPIC_1: Map和Reduce任务原理
Input(File) -> Split(Part1 Part2...) -> Map -> Shuffle(Combine,Partition,Sort,Copy,Merge) -> Reduce
>Shuffle一般指尽可能任务分配均匀，防止某子系统任务过重，导致整体耗时久
```
1.Map
（1）Split：分片| 每个片段包括文件名、开始位置、长度、位置等信息。Split的结果是Map任务最小输入单位
（2）Map：每个split执行各自map任务
（3）Shuffle：洗牌| 结点间进行数据交换(包含本地化混合、分区、排序、复制、融合=拥有同样key值的键值对，按key组成value list)
2. Reduce：执行任务输出结果
```
##### TOPIC_2：MapReduce架构
```
0. 组成：4个独立结点node
（1） Client：提交需要进行的MapReduce作业，称之为Job(将会被分成若干Task)
（2） JobTracker：负责调度| 初始化作业、分配作业并与TaskTracker通信并协调作业
（3） TaskTracker：负责任务执行| 将分配来的数据片段执行MapReduce任务，并保持与JobTracker通信
（4） HDFS：用来在其他结点间共享文件| Client需要执行的作业资源将复制到HDFS
1. 主从结构| JobTracker为Master，TaskTracker为Slave
```
##### 
<img src="https://github.com/WIN0624/image/blob/master/MR.png?raw=true"  width = "80%" height = "80%"/>

```
# 运用JobTracker+TaskTracker
Map Stages:
将大数据切分呈不同小块，将各小块分发到不同mappers
计算后存储到本地硬盘，通过TaskTracker传到Reduce Tasks
Reduce Stages：
整合所有mappers的结果，总结各结果得到最终计算总结果

TaskTracker和Reduce之间是Shuffle
Hadoop调优：解决数据倾斜（大量key分配到同一个）和内存溢出的问题，都是分配不均的问题
```
##### Exmaple| 词频统计
<img src="https://github.com/WIN0624/image/blob/master/%E5%9B%BE%E7%89%871.png?raw=true"  width = "80%" height = "80%"/>

```
将map的输出作为reduce的输入
reduce对各个map的计算结果，依据每一个唯一标识的key对value进行聚合
```


##### Streaming部署到MapReduce中
<img src="https://github.com/WIN0624/image/blob/master/Streaming%E8%AE%A1%E7%AE%97%E6%B5%81%E7%A8%8B.png?raw=true"  width = "60%" height = "60%"/>

```
step1. Map分发数据
print 提取四列重新编号为1、2、3、4列
awk -F'\t' '{print $1"\t"$4"\t"$2"\t"$11}'
step2. 结果聚合

step3. 本地结果测试
管道将数据导入map并运行| 再将分发计算结果导入reduce聚合
hadoop dfs -cat /dataset/zx...|sh map_awk.sh
```
sh run_hadoop_awk.sh<br>
公共数据集：/dataSet
```
hadoop_home=/usr/hadoop/hadoop-2.7.3/bin/       主节点在此
INPUT=/dataSet
HDFS=/stud2019/bd61
OUTPUT1=/awk_hadoop_a/

hadoop dfs -rm -r ${HDFS}${OUTPUT}  # 保证输出路径没有文件，没有覆盖提醒

$hadoop_home/hadoop jar /streaming目录        # 运行java，jar，在后i按这个目录中
# 注意下面所有代码以/结尾后换行，后面不可有空格
-input #INPUT/zx...   -output $    
# 以下四行重要
-file map.sh
-file red.sh
-mapper sh map.sh
-reducer sh red.sh
# 以下目前照抄照搬
-jobconf 进行hadoop调优

得到jobID和一个可以跟踪其工作进程的网址
reducer达到100%后运行完成，产生文件报告|Filw System counter\Job counters\Map-Reduce Framework <读取bytes和combine records>\shuffle errors\output directory输出路径
```
<img src="https://github.com/WIN0624/image/blob/master/Streaming%E8%AE%A1%E7%AE%97%E6%B5%81%E7%A8%8B.png?raw=true"  width = "60%" height = "60%"/>

#### 远程监督|分析当前代码运行情况
```
1. 远程登陆windows
remote desktop connection
114.67.37.170 
账号：student30
密码：123456
在局域网中登陆跟踪进程的网站，提高安全性
2. google浏览器
访问master8088/cluster| 后端监控界面
错误信息在Diagonnostics可见
```
