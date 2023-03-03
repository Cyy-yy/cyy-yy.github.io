---
title: 数据仓库入门
date: 2023-03-01
categories: [数据仓库]
tags: [hive, data warehouse]

author: <author_id>

math: true
mermaid: true
pin: true
---



## 1. 数仓基础

### 1.1 概念

数仓（Data Warehouse, DW）是一个用于存储、分析、报告的数据系统，其目的是构建面向分析的集成化数据环境，分析结果为企业提供决策支持。

### 1.2 与数据库的区别

- 数仓不是大型的数据库
- 数据库是面向事务设计的，数仓是面向主题设计的
- 数据库一般存储业务数据，数仓一般存储历史数据
- 数据库是为捕获数据而设计，数仓是为分析数据而设计的

### 1.3 数仓分层

经典三层架构：

1. 操作型数据层——ODS

   原始数据层，存放未经处理的原始数据

2. 数据仓库层——DW

   对ODS层的数据进行加工与整合，建立一致性维度，构建可复用的面向分析和统计的明细事实表

3. 数据应用层——ADS

   面向最终用户，提供给产品和数据分析使用的数据

## 2. Hive

### 2.1 概念

Hive是一款建立在Hadoop之上的开源数仓系统，可以将存储在Hadoop文件中的结构化、半结构化数据映射为一张数据库表，Hive核心是将HQL（Hive查询语句）转化为MapReduce程序，然后将程序提交到Hadoop集群执行

### 2.2 Hive组件

用户接口、元数据存储（表和文件的映射关系）、Drive驱动器

### 2.3 元数据

#### 2.3.1 Metadata

元数据，即表和文件之间的映射关系，一般用RDBMS存储

#### 2.3.2 Metastore

- **概念**

  Metastore是Hive中用于元数据管理的服务，客户端连接Metastore，再由Metastore连接MySQL数据库来存取元数据

- **配置方式**

  |                             | 内嵌模式 | 本地模式 | 远程模式 |
  | --------------------------- | -------- | -------- | -------- |
  | **Metastore单独配置、启动** | 否       | 否       | 否       |
  | **Metastore存储介质**       | Derby    | MySQL    | MySQL    |


### 2.4 语法

#### 2.4.1 建表

- **默认存储路径**

  Hive表默认存储路径是由`${HIVE_HOME}$/conf/hive-site.xml`配置文件中的`hive.metastore.warehouse.dir`属性指定，默认值是`/user/hive/warehouse`

  建表时可用`location`关键字自定义存储路径

- **建表语句**

  ```sql
  create table tb_name (
  	id int comment "ID",
      name string comment "usr_name",
      item_price map<string, int> comment "复合类型数据"
  ) comment "test table"
  row format delimited
  fields terminated by "\t"
  collection items terminated by "-"
  map keys terminated by ":";
  ```

- **上传文件**

  先建表，再将文件上传到对应的hdfs路径下，使用命令`hadoop fs -put [file_name] [file_path]`

#### 2.4.2 内部表和外部表

- **概念**

  **内部表**：也称为**托管表**（Mangaged Table），Hive拥有该表的结构和文件，管理该表包括元数据和数据在内的生命周期，当内部表被删除时，其元数据和对应文件都将被删除

  **外部表**：不是Hive拥有或管理的，只管理元数据的生命周期，删除外部表只会删除元数据而不会删除文件

- **创建语句**

  ```hive
  create external table tb_name; --创建外部表，无external关键字时默认创建内部表
  ```

- **查看表类型**

  ```hive
  describe formatted tb_name;
  ```

#### 2.4.3 分区表

- **建表**

  ```hive
  create table tb_name(
  	id int comment "ID",
      item string comment "item name",
      country string comment "country"
  ) partitioned by (p_name string)
  row format delimited
  fields terminated by "\t";
  ```

- **加载分区数据**

  **静态加载**

  ```hive
  load data local inpath [local_path] into table tb_name partition(p_name=...);
  ```

  *分区后每个分区会单独成一个文件夹，每个文件夹下单独存放分区对应数据文件*

  **动态加载**

  ```hive
  insert into table tb_name partition(p_name) --p_name为分区名
  select tmp.*, tmp.p_feat from tb_name tmp; --p_feat为分区字段
  ```


#### 2.4.4 分桶表

- **概念**

  将分区文件再作切分，即为分桶表

  开启分桶功能`set hive.enforce.bucketing = true`

- **语法**

  ```hive
  create table tb_name(
  	id int comment "ID",
      item string comment "item name",
      country string comment "country"
  )
  clustered by(country)
  sorted by(item desc) --每个桶内按item倒序排序
  into num buckets;
  ```

- **数据加载**

  1. 创建普通表`t_ordinary`

  2. 上传数据文件

  3. 创建分桶表`t_bucket`

  4. 插入查询语句

     ```hive
     insert into t_bucket (select * from t_ordinary);
     ```

- **与分区表区别**

  1. 分区后产生的是分区目录，而分桶后直接产生多个文件
  2. 创建语句不同，分区表采用`partitioned by`子句创建，分区字段为虚拟字段，需要指定字段类型；而分桶表采用`clustered by`子句创建，分桶字段为真实字段，需要指定分桶数量
  3. 分区避免全表扫描，分桶在抽样和JOIN时能够提高MR程序效率

#### 2.4.5 事务表

- **开启事务配置**

  ```hive
  set hive.support.concurrency = true;
  set hive.enforce.bucketing = true;
  set hive.exec.dynamic.partition.mode = nonstrict;
  set hive.txn.manager = org.apache.hadoop.hive.ql.lockmgr.DbTxnManager;
  set hive.compactor.initiator.on = true;
  set hive.compactor.worker.threads = 1;
  ```

- **创建事务表**

  ```hive
  create table trans_student(
  	id int,
      name string,
      age int
  )
  clustered by(id) into 2 buckets
  stored as orc
  TBLPROPERTIES("transactional"="true");
  ```

#### 2.4.6 视图

**创建视图**

```hive
create view view_name as (select * from tb_name);
```

**物化视图**

```hive
create materialized view view_name as (select * from tb_name);
```

#### 2.4.7 创建数据库

```hive
create database if not exists test_db
comment "this is a test database"
with dbproperties ("createBy"="cyy");
```

#### 2.4.8 查看表元数据

```hive
describe formatted tb_name;
```

#### 2.4.9 修改字段

```hive
alter table tb_name change feat_old feat_new [feat_type: int or string etc.];
```

#### 2.4.10 分区相关操作

**新增分区**

```hive
alter table tb_name add partition (p_name=p_val) location [file_path];
```

**重命名分区**

```hive
alter table tb_name partition p_name_old rename to partition p_name_new;
```

**分区修复**

- 直接使用`hadoop fs`命令在表文件夹下创建分区文件夹并上传数据，此时Hive查询不到该表数据，因为Metastore未记录

  ```hive
  msck add partitions;
  ```

- 直接使用`hadoop fs`命令删除分区文件夹，此时Hive仍然能查询到分区表，因为在Metastore中仍然没有删除

  ```hive
  msck drop partitions;
  ```

#### 2.4.11 加载数据

```hive
load data [local] inpath file_path [overwrite] into tb_name [partition(p_name=p_val, ...)] 
```

- 指定`local`则表示从Hiveserver2所在的服务器读取文件，不指定则一般从HDFS上读取

#### 2.4.12 多重插入

```hive
from tb_1
insert overwrite table tb_2
select feat_1
insert overwrite table tb_3
select feat_2
```

#### 2.4.13 动态分区插入

```hive
set hive.exec.dynamic.partition = true;
set hive.exec.dynamic.partition.mode = nonstrict;
```

*参照分区表插入方法*

#### 2.4.14 导出数据

```hive
insert overwrite directory [local] file_path (select * from tb_name)
[row format delimited fields terminated by "\t"]
[stored as orc];
```

#### 2.4.15 查询语法树

```hive
select [all | distinct] select_expr, ...
from tb_name
[where condition]
[group by col_list]
[order by col_list]
[cluster by col_list |
  [distribute by col_list][sort by col_list]
]
[limit cnt_rows];
```

- `order by` 全局排序，只有一个reducer
- `distribute by` 根据指定字段分组，`sort by`为分组后组内排序
- `cluster by` 既有分组又有排序，`distribute + sort by = cluster by`

#### 2.4.16 表联结

联结方式

```hive
[inner] join
left | right [outer] join
full [outer] join
left semi join  --相当于inner join，但是只返回左表字段
cross join
```

### 2.5 Hive客户端配置

#### 2.5.1 服务启动

```bash
$HIVE_HOME/bin/beeline
! connect jdbc:hive2://node:10000
```

