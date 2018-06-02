# 《MySQL技术内幕InnoDB存储引擎》笔记
> Author: liy

---


# 第四章 表

## 4.1 索引组织表
- 索引组织表：根据主键顺序组织存放的表

- 没显示定义主键，InnoDB选主键方式：
1. 选非空的唯一索引
2. 自动创建一个6B的指针
3. 有多个非空唯一索引时选第一个作为主键

- _rowid可以显示表的主键，只能查看单列为主键的情况

## 4.2 InnoDB逻辑存储结构

### 4.2.1 表空间
- 默认InnoDB存储引擎有一个共享表空间ibdata1
- innodb_file_per_table参数启用后，每张表内数据单独放到一个表空间内。且存的是数据、索引、插入缓冲Bitmap页
- 其他数据，如回滚信息、插入缓冲索引页、系统事务信息、二次写缓冲等还是放在共享表ibdata1中

### 4.2.2 段
- 表空间由段组成，常见段有数据段、索引段、回滚段等
- 数据即索引，索引即数据
- 数据段即为B+树的叶子结点
- 索引段即为B+树的非叶子节点
- 回滚段较为特殊
- 对段的管理由引擎自身完成

### 4.2.3 区
- 区是由连续的页组成的空间
- 区大小1MB
- 页大小16KB
- 一个区中共64个连续的页
- 压缩页大小可为2K、4K、8K，通过KEY_BlOCK_SIZE设置

### 4.2.4 页
- InnoDB磁盘管理最小单位
- 默认页大小16KB
- 常见的页类型

### 4.2.5 行
- InnoDB存储引擎是面向行的
- infobright存储引擎是按列存放数据的，对于数据仓库下分析很有帮助

## 4.3 InnoDB行记录格式
- InnoDB提供两种格式存放行记录：Compact、Redundant

### 4.3.1 Compact行记录格式
- 设计目标：高效低存储数据
- 一页中放的行数据越多，性能就越高

### 4.3.2 Redundant行记录格式
- 为了兼容之前版本

### 4.3.3 行溢出数据
- InnoDB可以将一条记录的某些数据存储在真正的数据页面之外
- MySQL的VARCHAR类型可以存放65535字节，然而实际测试发现能存放VARCHAR类型的最大长度是65532字节。存入字节太长时MySQL自动将VARCHAR转成了TEXT
- VARCHAR(N)中的N指的是字符长度，而文档说明VARCHAR类型最大支持65535字节，具体存储时需要依赖当时的字符集类型，如latin1可以存65542个，GBK是32767个，UTF8是21845个
- 官方定义的65535长度是所有VARCHAR列的长度总和
- InnoDB数据都存放在页类型为B-tree node中，当行溢出时，数据存放在页类型为Uncompress BLOB页中
- 一个页中至少能放入两行数据，那VARCHAR类型的行数据就不会存放到BLOB页中
- TEXT或BLOB类型数据是存放在数据页还是BLOB页中，看至少保证一个页能存放两条记录

### 4.3.4 Compressed和Dynamic行记录格式
- InnoDB 1.0.x后引入新的文件格式
- 以前的Compact和Redundant格式成为Antelope文件格式
- 新的文件格式称未Barracuda，包含两种新的行记录格式Compressed和Dynamic

### 4.3.5 CHAR的行结构存储
- CHAR(N)中N指的是字符的长度，例如插入‘ab’占2B，插入‘我们’占4B
- UTF8下CHAR(10)最小可存储10字节，最大存储30字节
- 对于未沾满长度的字符还是填充0x20
- 在多字符集下，CHAR和VARCHAR的实际行存储基本没区别

## 4.4 InnoDB数据页结构
没看懂

## 4.5 Named File Formats机制

## 4.6 约束
### 4.6.1 数据完整性
- 关系数据库本身能保证存储数据的完整性，而文件系统需要程序端进行控制
- 数据完成性三种形式
1. 实体完整性：保证表中有一个主键，通过Primary Key、Unique Key、触发器保证
2. 域完整性：保证数据每列的值满足特定的条件，通过合适的数据类型、外键、触发器、DEFAULT约束保证
3. 参照完整性：保证两表之间的关系，通过外键、触发器保证
- InnoDB提供以下几种约束：
1. Primary Key
2. Unique Key
3. Foreign Key
4. Default
5. NOT NULL

### 4.6.2 约束的创建和查找
- 建表时
- ALTER TABLE命令


### 4.6.3 约束和索引的区别
- 当创建了一个唯一索引就创建了一个唯一约束
- 约束更是一个逻辑概念，用来保证数据完整性
- 索引是一个数据结构，既有逻辑概念，还代表着物理存储的方式

### 4.6.4 对错误数据的约束
-MySQL允许非法的或不正确的数据插入或更新

### 4.6.5 ENUM和SET约束
- MySQL不支持传统CHECK约束，但可以通过ENUM和SET类型解决

### 4.6.6 触发器与约束
- 触发器在执行INSERT、DELETE和UPDATE命令之前或之后自动调用SQL命令或存储过程
- CREATE [DEFINER = {user | CURRENT_USER}] TRIGGER trigger_name BEFORE|AFTER INSERT|UPDATE|DELETE ON tbl_name FOR EACH ROW trigger_stmt
- 只有Super权限用户才能执行上边这条命令

### 4.6.7 外键约束
- FOREIGN KEY [index_name] (col) REFERENCES parent(col')
    [ON DELETE|UPDATE reference_option]
    reference_option:RESTRICT| CASCADE| SET NULL| NO ACTION
- 被引用的表为父表，引用的表为子表
- ON DELETE|UPDATE 表示在对父表进行DELETE和UPDATE操作时，对子表所做的操作：
1. CASCADE:父表干啥子表就干啥
2. SET NULL:子表被更新为NULL
3. NO ACTION:抛出错误，不允许这类操作发生
4. RESTRICT:同上
- MySQL是即时检查，因此3==4
- InnoDB外键建立时会自动对该列加一个索引，很好避免外键列上午索引而导致的死锁问题的产生
- 数据导入操作时，外键导致在外键约束的检查上花费大量时间，因为是即时检查，MySQL可以关掉外键检查
- SET foreign_key_checks=0

## 4.7 视图
### 4.7.1 视图的作用
- 一个命名的虚表，没有实际的物理存储
- 虽然是虚拟表，但可更新视图

### 4.7.2 物化视图
- Oracle支持
- 根据基表实际存在的实表
- 物化视图的数据存储在非易失的存储设备上
- MySQL本身不支持物化视图，但可以通过一些机制实现


## 4.8 分区表
### 4.8.1 分区概述
