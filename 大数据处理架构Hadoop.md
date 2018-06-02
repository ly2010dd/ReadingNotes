# 大数据处理架构Hadoop
> **作者：liy**

## Hadoop发展简史
- 由Apache Lucene的创始人Doug Cutting开发的文本搜索库
- 起源于2002年Apache Nutch项目---一个开源的网络搜索引擎（Lucene的一部分）
- 2004年，Nutch仿GFS开发自己的分布式文件系统NDFS，HDFS的前身
- 2004年，google发表MapReduce论文
- 2005年，Nutch开源实现了google的MapReduce
- 2006年2月，Nutch中的NDFS和MapReduce独立出来，成Lucene的一个子项目，称Hadoop
- 2008年1月，Hadoop成为Apache顶级项目
- 2008年4月，Hadoop打破世界纪录，最快排1TB的系统，910个节点用209秒
- 2009年5月，缩短至62秒，名声大振，成为大数据处理标准

## Hadoop的特性
- 高可靠性
- 高效性
- 高可扩展性
- 高容错性
- 成本低
- 运行在Linux平台上
- 支持多种编程语言

## Hadoop版本演变
- Hadoop一代
1. 三大版本：0.20.x, 0.21.x, 0.22.x
2. 0.20.x--> 1.0.x-->稳定版
3. 0.21.x, 0.22.x增加了NameNode HA
- Hadoop二代
1. 两个版本：0.23.x, 2.x
2. 它们完全不同于Hadoop 1.0，是一套全新的架构，均包含HDFS Federation和YARN两个系统
3. 相比于0.23.x，2.x增加了NameNode HA和Wire-compatibility两个重大特性
- Hadoop1.0 和 Hadoop2.0区别：
1. HDFS: NN Federation、HA
2. MAPREDUCE(cluster resource managemant & data processing) -> YARN(cluster resource managemant) + MAPREDUCE(data processing)

## Hadoop项目结构
![Hadoop结构图](imgs/Hadoop2.2.png)

1. HDFS：分布式文件系统 Common：最基础服务
2. YARN：资源管理和调度器，内存、CUP等资源
3. MapReduce：分布式并行编程模型，离线计算，批处理，不是实时计算，基于磁盘的
4. Tez：运行在YARN之上的下一代Hadoop查询处理框架，对很多MapReduce作业优化以后构建有向无环图
5. Spark：类似于Hadoop MapReduce的通用并行框架，基于内存计算，快
6. Hive：Hadoop上的数据仓库，用于企业决策分析的，支持SQL语句，会转换成一堆MapReduce作业
7. Pig：一个基于Hadoop的大规模数据分析平台，提供类似SQL的查询语言Pig Latin
8. Ooize：Hadoop上的工作流管理系统
9. Hbase：Hadoop上的非关系型的分布式数据库，实时用的
10. Zookeeper：提供分布式协调一致性服务
11. Ambari：Hadoop快速部署工具，支持Apache Hadoop集群的供应、管理和监控
12. Storm：流计算框架
13. Flume：高可用的，高可靠的，分布式的海量日志采集、聚合和传输的系统
14. Sqoop：用于在Hadoop与传统数据库之间进行数据传递，与关系数据库导入导出
15. Kafka：一种高吞吐量的分布式发布订阅消息系统，可以处理消费者规模的网站中的所有动作流数据

## Hadoop集群中有哪些节点类型
HDFS
- NameNode：负责协调集群中的数据存储，应用先来访问这个节点再去找datanaode
- DataNode：存储被拆分的数据块
- SecondaryNameNode：帮助NameNode收集文件系统运行的状态信息，NameNode的冷备份

MapReduce
- JobTracker：协调数据计算任务
- TaskTracker：负责执行由JobTracker指派的任务

## 节点机器选型

- 在集群中，大部分的机器设备是作为Datanode和TaskTracker工作的

Datanode/TaskTracker | 
---|
4个磁盘驱动器（单盘1-2T），支持JBOD(Just a Bunch Of Disks，磁盘簇)|
2个4核CPU,至少2-2.5GHz |
16-24GB内存 |
千兆以太网 |


- NameNode提供整个HDFS文件系统的NameSpace(命名空间)管理、块管理等所有服务，因此需要更多的RAM，与集群中的数据块数量相对应，并且需要优化RAM的内存通道带宽，采用双通道或三通道以上内存。

NameNode | 
---|
8-12个磁盘驱动器（单盘1-2T）|
2个4核/8核CPU |
16-72GB内存 |
千兆/万兆以太网 |

- SecondaryNameNode在小型集群中可以和NameNode共用一台机器，较大的群集可以采用与NameNode相同的硬件
