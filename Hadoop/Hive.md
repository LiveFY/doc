## Hive分析工具

Hive最核心的本质在于可以直接避免掉MapReduce程序为数据分析人员所带来的困扰。
在Hive里面一共支持有四类数据模型：内部表、外部表、分区表、桶表。

### 1、内部表

**内部表的核心本质在于，将HDFS上的数据直接保存在Hive的内部上进行相关的处理。**

内部表的最大特点是将所有要分析的文件进行了一个重复的拷贝处理操作，必须拷贝到指定的HDFS目录下，但是这样的方式并不可能被实际所使用。

内部表更大的方便之处是在于可以进行本地文件的加载处理，但是如果要是现在HDFS上本身已经存储了，那么建议使用外部表实现。

创建内部表语法：

```
CREATE TABLE 表名称(
	 字段 类型,
	 字段 类型
	)[comments '注释内容']
	ROW FORMAT DELIMITED FIELDS TERMINATED BY '分隔符';
```

所谓的内部表的加载就是将文件拷贝到HDFS上去。

### 2、外部表

**所谓的外部表就是相当于做了一个数据的连接而已。**

创建外部表语法：

```
CREATE EXTERNAL TABLE 表名称(
 字段 类型,
 字段 类型
)[comments '注释']
[ROW FORMAT DELIMITED FIELDS TERMINATED BY '分隔符' LOCATION 'HDFS路径'];
```



### 3、分区表

**为了方便进行数据的更快速的统计，可以采用分区表**

创建分区表语法：

```
CREATE TABLE 表名称(
 字段 类型,
 字段 类型
)[comments '注释']
PARTITIONED BY (分区的字段 字段类型) ROW FORMAT DELIMITED FIELDS TERMINATED BY '分隔符';
```


分区给用户的数据带来更大的好处是可以针对于不同的时间断段，或者自己定义的规则来进行指定的文件的保存，这样在进行分析的时候就可以方便许多。

### 4、桶表

**所谓的桶表指的是根据一种算法（一般都是HASH算法）将不同的数据保存在不同的桶空间之内。**

创建桶表语法：

```
CREATE TABLE 表名称(
 字段 名称,
 字段 名称
)[comments '注释'] 
CLUSTERED BY (字段名称) INTO 分桶规则 BUCKETS ;
```
