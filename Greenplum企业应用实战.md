> 何勇 陈晓峰 著

> 基础篇
> - 第1章 Greenplum 简介
> - 第2章 Greenplum 快速入门
> - 第3章 Greenplum 实战
---

# 第1章 Greenplum 简介

## 1.1 Greenplum起源和发展历程

1. 利用大规模集群系统的可伸缩性、容错性优势，实现高效的数据管理功能
2. 经典案例：Teradata、Greenplum、Hadoop Hive、Oracle Exadata、IBM Netteza
3. Greenplum提供：企业级数据仓库（EDW）、企业级数据云（EDC）、商务智能（BI）
4. Milestone
    - 2003：成立
    - 2008：在中国运营
    - 2010：被EMC收购
    - 2011-2012：Greenplum社区版发布、Greenplum Chorus发布并开源
    - 2014：Greenplum4.3发布

## 1.2 OLTP与OLAP

1. OLTP：（On-Line Transaction Processing 联机事务处理）
    - 面向前台应用
    - 重吞吐
    - 重高并发
    - 也称为生产系统
    - 事件驱动
    - 如：电商网站交易系统
2. OLTP特点：
    - 数据在系统中产生
    - 基于交易的处理系统
    - 每次交易牵涉的数据量很小
    - 对响应时间要求高
    - 用户人数庞大，大多是操作人员
    - 对DB的各种操作基于索引进行
3. OLAP：（On-Line Analytical Processing 联机分析处理）
    - 重计算
    - 重对大数据集进行统计分析
    - 是基于数据仓库的 分析处理过程
    - 是数据仓库的 用户接口部分
    - 跨部门的
    - 面向主题的
4. OLAP特点：
    - 本身不产生数据
    - 数据来源于生产系统的操作数据（Operational Data）
    - 基于查询的分析系统
    - 复杂查询 常用 多表联结、全表扫描等
    - 牵涉的数据量十分庞大
    - 响应时间 与 具体查询 有很大关系
    - 用户数量 相对少，用户是业务人员、管理人员
    - 由于业务问题不固定，对DB操作不能完全基于索引进行

## 1.3 PostgreSQL与Greenplum的关系

### 1.3.1 PostgreSQL

1. PostgreSQL
    - 非常先进的 对象-关系型 数据库管理系（ORDBMS）
    - 起源于伯克利BSD
2. PostgreSQL特性见P4

### 1.3.2 Greenplum

1. Greenplum
    - 与Oracle、DB2、PostgreSQL一样的 面向对象的关系型数据库
    - 本质上是一个 关系型数据库集群
    - 由 数个 独立的数据库服务 组合成的 逻辑数据库
    - Oracle RAC是Shared-Everything架构
    - Greenplum采用Shared-Nothing架构
        - 整个集群由 多个 数据节点（Segment Host）和 控制节点（Master Host）组成
        - 其中 每个 数据节点上 可以运行多个数据库
        - Shared-Nothing是一种分布式架构
        - 每个节点相对独立
        - 每个节点上所有资源（CPU、内存、磁盘）都是独立的
        - 每个节点都只有全部数据的一部分
        - 每个节点也只能使用本节点的资源
    - 高效的处理I/O数据吞吐和并发计算过程
        1. 要存的数据进入数据库
        2. 数据分布处理
        3. 表中的数据平均分布到每个节点上
        4. 为每个表指定一个分发列（distribute column）
        5. 根据hash分布数据
        - 这样可以充分发挥每个节点的I/O处理能力
        - 这一过程中 控制节点（Master Host）将不再承担计算任务，只负责必要的逻辑控制、客户交互
        - 所有节点服务器组成一个强大的计算平台，实现海量并行计算
    - 面向数据仓库应用的关系型数据库
    - 基于PostgreSQL开发的

## 1.4 Greenplum特性及应用场景

### 1.4.1 Greenplum特性

- 支持海量数据存储和处理
- 高性价比
    - 可以搭建在各种 硬件平台上
    - 在TB数据量上的投资是其他数据仓库或hadoop的1/5
- 支持 Just In Time BI
    - Greenplum通过准实时、实时的数据加载方式，实现数据仓库的实时更新
    - 进而实现动态数据仓库（ADW）
    - 因此对业务数据进行BI实时分析
- 系统易用性
    - 完全兼容PostgreSQL
- 支持线性扩展
    - 采用MPP并行处理架构
    - MPP架构中增加节点就可以 线性提高 系统存储容量 和 处理能力
    - 扩展节点时操作简单
    - 短时间内就可完成数据重分布
- 较好的并发支持和高可用支持
    - 硬件级的raid技术
    - 数据库层的mirror机制保护
    - 单个节点的错误不影响整个系统的使用
    - 主节点有master/stand by机制
- 支持MapReduce
- 数据库内部压缩
    - 压缩可以减少对磁盘的访问

### 1.4.2 Greenplum应用场景

- 不需要高端的硬件支持 仍可支撑 大规模的 高性能数据仓库 和 商业智能查询
- 传统数据库
    - 侧重交易处理
    - 关注多用户的双向操作
    - 在保障及时性的要求下
    - 系统通过 内存 来处理数据的分配、读写等操作
    - 存在IO瓶颈
- 分析型数据库
    - 以 实时多维分析技术 作为基础对数据进行 多角度 模拟归纳 得出数据中的 信息和知识
    - 关系型数据库
    - 查询速度快
    - 数据装载速度快
    - 批量DML处理快
    - 性能随硬件的添加线性增长
    - 扩展性好

# 快速入门
## 2.1 软件安装及数据初始化
### 2.1.1 Greenplum架构

1. 架构图

```
Client---LAN
          |                   |---Segment
          |                   |
         Master---GB_Switch---|---Segment
                              |
                              |---Segment
                              |
                              |---Segment
```

- Master
    - 建立 管理 与Client的会话
    - 解析SQL 生成分布式的执行计划
    - 分发执行计划 到 每个Segment上
    - 收集 Segment的执行结果
    - Master不存储业务数据，只存储数据字典
    - Master主机可以一主一备，分布在两台机器上
    - 为提高性能，Master最好单独占用一台机器
- Segment
    - 存取 业务数据
    - 执行Master分发的SQL语句
    - 对Master来说每个Segment都是对等的，负责对应数据的存储和计算
    - 每台机器上可以配置一到多个Segment
    - 由于每个Segment都是对等的，建议采用相同的机器配置
    - Segment分primary和mirror，交错存放在子节点上

2. Master与Segment关系图
```
Master Host---Greenplum Master

Segment Host1---|---Segment1(primary)
                |---Segment2(mirror)
Segment Host2---|---Segment2(primary)
                |---Segmentn(mirror)
    .
    .
    .
Segment Hostn---|---Segmentn(primary)
                |---Segment1(mirror)
```

- Master和Segment都是一个单独的PostgreSQL数据库 都有单独一套元数据字典
- Master叫主节点
- Segment叫数据节点
- Master与Segment的通信通过InterConnect千兆或万兆网卡组成的内部连接
- 同一台Segment机器上可以放多个Segment，不同的Segment会被赋予不同的端口
- Segment之间也不断地交互
- 为实现高可用 每个Segment都有对应的 备节点（Mirror Segment）分别存在不同的机器上

### 2.1.2 环境搭建 P11
- 对于数据库，性能上磁盘IO容易成为瓶颈
- 配置/etc/hosts时，习惯将Master机器叫做mdw，将Segment机器叫做sdw

### 2.1.3 Greenplum 安装 P13
- 安装数据库软件
- 配置hostlist
- 使用gpssh-exkeys打通所有服务器
- 将软件分发给每台机器上
- 配置~/.bash_profile
- 初始化Greenplum的配置文件
- 初始化数据库

### 2.1.4 创建数据库
### 2.1.5 数据库启动与关闭

## 2.2 安装Greenplum的常见问题 P22

## 2.3 畅游 Greenplum
### 2.3.1 如何访问 Greenplum
1. psql
2. pgAdmin

### 2.3.2 数据库整体概况
1. Greenplum将PostgreSQL改造成一个分布式数据库
2. Master和Segment都是一个单独的PostgreSQL数据库
3. Master本身不存储数据
4. 数据都保存在Segment上
5. 分布数据包括 哈希分布 和 随机分布

### 2.3.3 基本语法介绍

1. \h
```
\h  #查看Greenplum支持语法
\h command #获取具体命令语法，eg：\h create view
```

2. 执行psql直接进testdb库
```
export PGDATABASE=testdb
psql
```

3. CREATE TABLE 与其他数据库的不同
    - 需指定分布键
    - 如需用某个字段分区，通过partition by将表建成分区表
    - like操作 创建 与like表结构一致的表，功能类似create table t1 as select * from t2 limit 0
    - inherits实现表的继承

4. Greenplum两种数据分布策略
    - Hash分布
        - 指定一个或多个分布键，计算hash值，路由到对应的Segment上
        - 语法Distributed by(...)
        - 默认第一个字段作为分布键
    - 随机分布
        - 也叫平均分布
        - 无论什么内容都平均分布
        - 执行关联SQL操作时需要将数据重分布性能差
        - 语法DISTRIBUTED RANDOMLY

5. Select 与其他数据库的不同
    - 要了解分布式的SQL的执行计划
    - Greenplum数据是切分放在不同的Segment上的，查询的时候，Master数据展现是以Master先接收到的数据顺序，每个Segment的数据到Master的顺序是随机的、不固定的，多以select出的数据顺序也是不固定的，即使表没变化
    - 如果要求两次查询结果一样则必须显示加order by

6. explain
    - explain用于查询一个表的执行计划
    - 在sql优化时常用

7. insert、update、delete在数据切片后的问题
    - insert的时候留意分布键不能为空
    - 否则分布键会变成null
    - 数据将被保存在一个节点上造成分布不均
    - 不能对分布键做update，因这样会造成数据重分布，Greenplum(4.1.1)暂不支持，不知后续版本是否已经支持
    - delete中有select子句且子查询还会涉及数据重分布，这样会报错

8. truncate
    - truncate直接删表的物理文件，然后创建新的数据文件
    - truncate比delete在性能上有非常大提升
    - 如有sql正在操作此表，truncate会被锁住直到锁释放

### 2.3.4 常用数据类型 P35

### 2.3.5 常用函数 P37

### 2.3.6 分析函数 P43

### 2.3.7 分区表 P46

### 2.3.8 外部表 P49

- Greenplum数据加载明显优势是 支持并发加载
- pgfdist是并发加载工具，数据库中对应的就是 外部表
- Greenplum外部表（gpfdist）的实现 架构图
```
                     |---Segment---
                     |             |
Master---GB_Switch---|---Segment--- --gpfdist---External Table Files
                     |             |
                     |---Segment--- --gpfdist---External Table Files
                     |             |
                     |---Segment---     (ETL Server)
``` 
- 外部表是 一张表的数据是 指向数据库之外的 数据文件
- 可对外部表 执行DML操作
- 外部表支持在Segment上并发地高速从gpfdist导入数据
- 由于直接从Segment上导入 所以效率非常高
```
CREATE [READABLE] EXTERNAL TABLE table_name 
     ( column_name data_type [, ...] | LIKE other_table )
      LOCATION ('file://seghost[:port]/path/file' [, ...])
        | ('gpfdist://filehost[:port]/file_pattern[#transform]'
        | ('gpfdists://filehost[:port]/file_pattern[#transform]'
            [, ...])
        | ('gphdfs://hdfs_host[:port]/path/file')
      FORMAT 'TEXT' 
            [( [HEADER]
               [DELIMITER [AS] 'delimiter' | 'OFF']
               [NULL [AS] 'null string']
               [ESCAPE [AS] 'escape' | 'OFF']
               [NEWLINE [ AS ] 'LF' | 'CR' | 'CRLF']
               [FILL MISSING FIELDS] )]
           | 'CSV'
            [( [HEADER]
               [QUOTE [AS] 'quote'] 
               [DELIMITER [AS] 'delimiter']
               [NULL [AS] 'null string']
               [FORCE NOT NULL column [, ...]]
               [ESCAPE [AS] 'escape']
               [NEWLINE [ AS ] 'LF' | 'CR' | 'CRLF']
               [FILL MISSING FIELDS] )]
           | 'CUSTOM' (Formatter=<formatter specifications>)
     [ ENCODING 'encoding' ]
     [ [LOG ERRORS] SEGMENT REJECT LIMIT count
       [ROWS | PERCENT] ]

```
- 外表需指定gpfdist的IP和端口，要有详细的目录地址，支持通配符
- 可写多个gpfdist地址，但不能超过总的Segment数
- 外表还支持本地文件导入，不过效率低，不建议使用
- 启动gpfdist 及创建外表
    - 在文件服务器（假设10.10.10.10）上起gpfdist服务
    ```
    nohup $GPHOME/bin/gpfdist -d /home/admin -p 8888 > /tmp/gpfdist.log 2>&1 &
    ```
    - 将需要加载的数据文件放在10.10.10.10的/home/admin下
    - 在greenplum中创建外表
    ```
    CREATE EXTERNAL TABLE public.test001_ext (
    id integer,
    name varchar(128)
    )
    LOCATION (
    'gpfdist://10.10.10.10:8888/gpextdata/test001.txt'
    )
    FORMAT 'TEXT' (DELIMITER AS E'|' NULL AS '' ESCAPE AS 'OFF')
    ENCODING 'GB18030' LOG ERRORS INTO public.test001_err SEGMENT REJECT LIMIT 10 ROWS;
    ```
    - 外部表查询 及加载数据
    ```
    select * from public.test001_ext;
    insert into test001 select * from test001_ext;
    ```
    - 如果加载报错，错误会被插入到err表中
    ```
    select * from public.test001_err;
    ```

### 2.3.9 COPY命令
- 实现将文件导入导出
- 要通过master
- 效率没外表高
- 数据量小的情况下COPY更方便
- 命令语法
```
Command:     COPY
Description: copy data between a file and a table
Syntax:
COPY table [(column [, ...])] FROM {'file' | PROGRAM 'command' | STDIN}
     [ [WITH] 
       [BINARY]
       [OIDS]
       [HEADER]
       [DELIMITER [ AS ] 'delimiter']
       [NULL [ AS ] 'null string']
       [ESCAPE [ AS ] 'escape' | 'OFF']
       [NEWLINE [ AS ] 'LF' | 'CR' | 'CRLF']
       [CSV [QUOTE [ AS ] 'quote'] 
            [FORCE NOT NULL column [, ...]]
       [FILL MISSING FIELDS]
     [ [LOG ERRORS ]  
       SEGMENT REJECT LIMIT count [ROWS | PERCENT] ]

COPY {table [(column [, ...])] | (query)} TO {'file' | PROGRAM 'command' | STDOUT}
      [ [WITH] 
        [ON SEGMENT]
        [BINARY]
        [OIDS]
        [HEADER]
        [DELIMITER [ AS ] 'delimiter']
        [NULL [ AS ] 'null string']
        [ESCAPE [ AS ] 'escape' | 'OFF']
        [CSV [QUOTE [ AS ] 'quote'] 
             [FORCE QUOTE column [, ...]] ]
      [IGNORE EXTERNAL PARTITIONS ]
```
- Greenplum4.x引入可写外部表，在导出数据时可以用可写外表，性能好
- Greenplum3.x导出数据只能用COPY实现，在master上汇总导出

# 第3章 Greenplum实战

## 3.1 历史拉链表 P53

- 一种数据模型
- 记录一个事物从开始一直到 当前状态的 所有变化信息
- 可以避免 每天存储所有记录 的 海量存储问题
- 也是处理 缓慢变化数据 的常见方式

## 3.2 日志分析 P65

## 3.3 数据分布
- 充分体现分布式架构优势
- 了解数据如何散落在各个节点上
- 了解数据倾斜 对 数据加载、数据分析、数据导出 影响

### 3.3.1 数据分散情况查看
- 利用generate_series和repeat函数 生成一些测试数据
```
create table test_distribute_1
as (select a as id, 
           round(random()) as flag, 
           repeat('a', 1024) as value 
    from generate_series(1, 5000000)a
    );
```
- 500万数据散落在4个数据节点 
- 查看数据分布情况
```
select gp_segment_id, count(*)
from test_distribute_1
group by 1; 

 gp_segment_id |  count  
---------------+---------
             3 | 1250000
             1 | 1250000
             2 | 1250001
             0 | 1249999
(4 rows)
```

### 3.3.2 数据加载速度影响
1. 数据倾斜状态下 的 数据加载
    - 准备测试数据，将测试数据导出
    ```
    copy test_distribute_1 to '/home/gpadmincloud/test_data/test_distribute.dat' 
    with delimiter '|';
    ```
    - 建立测试表，以flag字段为分布键
    ```
    create table test_distribute_2 as select * from test_distribute_1 limit 0 distributed by(flag);
    ```
    - 执行数据导入
    ```
    time psql -h localhost -d testdb -c "copy test_distribute_2 from stdin with delimiter '|'" < /home/gpadmincloud/test_data/test_distribute.dat
        
    real	2m5.871s
    user	0m2.552s
    sys	0m3.416s
    ```
    - 由于flag取值0、1，因此数据只能分散到两个数据节点上（数据倾斜）
    ```
    select gp_segment_id, count(*)
    from test_distribute_2
    group by 1; 
    
     gp_segment_id |  count  
    ---------------+---------
                 1 | 2498796
                 2 | 2501204
    (2 rows)
    ```
    - 查询数据分布节点id对应的host
    ```
    testdb=# select dbid, content, role, port, hostname from gp_segment_configuration where content in (1,2) order by role;
     dbid | content | role | port  |      hostname       
    ------+---------+------+-------+---------------------
       10 |       2 | m    | 50000 | sdw4-snova-2mtwkcou
        5 |       1 | m    | 50000 | sdw1-snova-2mtwkcou
        7 |       2 | p    | 40000 | sdw3-snova-2mtwkcou
        3 |       1 | p    | 40000 | sdw2-snova-2mtwkcou
    (4 rows)

    testdb=# select * from gp_segment_configuration ;
     dbid | content | role | preferred_role | mode | status | port  |      hostname       |       address       | replication_port 
    ------+---------+------+----------------+------+--------+-------+---------------------+---------------------+------------------
        1 |      -1 | p    | p              | s    | u      |  5432 | mdw-snova-2mtwkcou  | mdw-snova-2mtwkcou  |                 
        2 |       0 | p    | p              | s    | u      | 40000 | sdw1-snova-2mtwkcou | sdw1-snova-2mtwkcou |            41000
        3 |       1 | p    | p              | s    | u      | 40000 | sdw2-snova-2mtwkcou | sdw2-snova-2mtwkcou |            41000
        4 |       0 | m    | m              | s    | u      | 50000 | sdw2-snova-2mtwkcou | sdw2-snova-2mtwkcou |            51000
        5 |       1 | m    | m              | s    | u      | 50000 | sdw1-snova-2mtwkcou | sdw1-snova-2mtwkcou |            51000
        6 |      -1 | m    | m              | s    | u      |  5432 | smdw-snova-2mtwkcou | smdw-snova-2mtwkcou |                 
        7 |       2 | p    | p              | s    | u      | 40000 | sdw3-snova-2mtwkcou | sdw3-snova-2mtwkcou |            41000
       10 |       2 | m    | m              | s    | u      | 50000 | sdw4-snova-2mtwkcou | sdw4-snova-2mtwkcou |            51000
        8 |       3 | p    | p              | s    | u      | 40000 | sdw4-snova-2mtwkcou | sdw4-snova-2mtwkcou |            41000
        9 |       3 | m    | m              | s    | u      | 50000 | sdw3-snova-2mtwkcou | sdw3-snova-2mtwkcou |            51000
    (10 rows)

    ```

2. 数据分布均匀状态下 的 数据加载
    - 建立测试表，以id字段为分布键
    ```
    create table test_distribute_3 as select * from test_distribute_1 limit 0 distributed by(id);
    ```
    - 执行数据导入
    ```
    time psql -h localhost -d testdb -c "copy test_distribute_3 from stdin with delimiter '|'" < /home/gpadmincloud/test_data/test_distribute.dat
    
    real	1m37.148s
    user	0m2.460s
    sys	0m2.776s
    ```
    - 由于id取值顺序分布，因此数据可以均匀分散至所有数据节点
    ```
    select gp_segment_id, count(*)
    from test_distribute_3
    group by 1; 
    
     gp_segment_id |  count  
    ---------------+---------
                 1 | 1249999
                 3 | 1250000
                 2 | 1250001
                 0 | 1250000
    (4 rows)
    ```
    - 因此，数据分布均匀的情况下，可以利用更多机器进行工作，性能高
    
### 3.3.3 数据查询速度影响
1. 数据倾斜状态下的数据查询

```
select gp_segment_id, count(*), max(length(value)) from test_distribute_2 group by 1;
 
 gp_segment_id |  count  | max  
---------------+---------+------
             1 | 2498796 | 1024
             2 | 2501204 | 1024
(2 rows)

Time: 11689.211 ms
```

2. 数据分布均匀状态下的数据查询

```
select gp_segment_id, count(*), max(length(value)) from test_distribute_3 group by 1;
 
 gp_segment_id |  count  | max  
---------------+---------+------
             2 | 1250001 | 1024
             3 | 1250000 | 1024
             0 | 1250000 | 1024
             1 | 1249999 | 1024
(4 rows)

Time: 6180.228 ms
```

- 由于数据分布在所有节点上，所有服务器都有磁盘消耗，从而大大提升数据查询性能

## 3.4 数据压缩
### 3.4.1 对数据加载速度影响

1. 创建一个未压缩普通表

```
create table test_compress_1 as select * from test_distribute_1 distributed by(flag);

Time: 46831.182 ms
```

2. 创建一个压缩表

```
create table test_compress_2 with(appendonly=true,compresslevel=5) as select * from test_distribute_1 distributed by(flag);

Time: 31004.558 ms
```
### 3.4.2 对数据查询速度影响

1. 查询未压缩表

```
select gp_segment_id, count(*), max(length(value)) from test_compress_1 group by 1;
 gp_segment_id |  count  | max  
---------------+---------+------
             1 | 2498796 | 1024
             2 | 2501204 | 1024
(2 rows)

Time: 21115.277 ms

```

2. 查询已压缩表

```
 gp_segment_id |  count  | max  
---------------+---------+------
             2 | 2501204 | 1024
             1 | 2498796 | 1024
(2 rows)

Time: 12818.013 ms
```

## 3.5 索引
- Greenplum支持B-tree、bitmap、函数索引等
- 未建索引的表查询
```
create table test_index_1 as select * from test_distribute_1;
```
- 执行查询，用1099ms
```
select id, flag from test_index_1 where id=100;
 id  | flag 
-----+------
 100 |    0
(1 row)

Time: 1099.389 ms
```
- 在id上创建索引
```
create index test_index_1_idx on test_index_1(id);
```
- 再次执行查询，只用27ms
```
select id, flag from test_index_1 where id=100;
 id  | flag 
-----+------
 100 |    0
(1 row)

Time: 27.650 ms
```