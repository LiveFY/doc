
### 三个核心配置文件：

```
● core-site.xml
● hdfs-site.xml
● yarn-site.xml
```

### 五个核心进程：
NameNode、SecondaryNameNode、DataNode、ResourceManager、NodeManager。

### start-all.sh文件
(**/usr/local/hadoop/sbin/start-all.sh**)
直接启动DFS与YARN相关进程，命令相关的进程分析：
```
● DFS相关进程（存储）：NameNode、SecondaryNameNode、DataNode；
    ┠ 配置文件：hdfs-site.xml；
● YARN相关进程（数据分析）：ResourceManager、NodeManager；
    ┠ 配置文件：yarn-site.xml；
```

### 手工分开命令启动：
（**/usr/local/hadoop/sbin/hadoop-daemon.sh**）
> 启动namenode进程：hadoop-daemon.sh start namenode <br/>
> 停止namenode进程：hadoop-daemon.sh stop namenode

> 启动SecondaryNameNode进程：hadoop-daemon.sh start secondarynamenode <br/>
> 停止SecondaryNameNode进程：hadoop-daemon.sh stop secondarynamenode

> 启动DataNode进程：hadoop-daemon.sh start datanode  <br/>
> 停止DataNode进程：hadoop-daemon.sh stop datanode

> 启动ResourceManager进程：yarn-daemon.sh start resourcemanager  <br/>
> 停止ResourceManager进程：yarn-daemon.sh stop resourcemanager

> 启动NodeManager进程：yarn-daemon.sh start nodemanager  <br/>
> 停止NodeManager进程：yarn-daemon.sh stop nodemanager

### 自动化脚本启动：
(**/usr/local/hadoop/sbin/***)

脚本分为两类：**dfs自动化脚本**、**yarn自动化脚本**
    
自动化启动dfs：start-dfs.sh <br/>
    此脚本执行之后会启动三个进程：NameNode、DataNode、SecondaryNameNode；

自动化启动yarn：start-yarn.sh  <br/>
    此脚本执行之后会启动两个进程：ResourceManager、NodeManager；

自动化关闭dfs：stop-dfs.sh

自动化关闭yarn：stop-yarn.sh



## 分布式文件系统
分布式文件系统在物理结构上是由计算机集群中的多个节点构成的，这些节点分为两列，一类叫“主节点（Master Node）”或者也被称为“名称节点（Name Node）”,另一类叫“从节点（Slave Node）”或者也被称为“数据节点（DataNode）”。

所有的HDFS数据都会被拥有DataNode进程的节点所保存，而NameNode只是做一个“导航”的作用。如果没有NameNode将无法操作DataNode节点。

> **原则:** NameNode的数据保存在内存里面，需要大内存的电脑，而DataNode需要的存储，要使用大容量的硬盘。

**1、NameNode进程分析**

当用户上传一个待分析的文件（理论上任何的文件都可以上传，不分类型，但是基本上在Hadoop上所保存的数据都是需要进行统计分析的数据），这些数据的信息被NameNode所接收，而后这些具体的基本信息都会保存在有一个元数据内存空间（MetaData），而后真正的数据一定要保存在DataNode之中，那么至于说数据保存在哪个DataNode节点上，由NameNode来进行记录。<br/>

但是每一个DataNode是根据数据块来存储的（Hadoop的默认的数据库为128M），如果你现在存储了一个1k的文件，对于DataNode也占有128M的磁盘空间，如果你存储了300M的文件，那么这个文件会自动拆分为3份(Hadoop之中数据备份默认三份)，保存在不同的数据块里面。 <br/>

SecondaryNameNode属于一个NameNode的经理级别，负责一些琐碎的事务，就是负责为NameNode进行数据更新的，因为NameNode有可能不是最新的数据，所以就需要有一个辅助的操作去帮助NameNode进行更新。

**（1）NameNode组成**

NameNode存储目录包含有：edits、fsimage(filesystemimage)、fstime三个文件

```
● 编辑日志（edits）:描述的是整个的文件的所有相关记录，例如：xx时候，有一个文件上传、大小、创建时间......
    ┠ 但是随着运行时间的加长，那么日志的文件的内容也会越来越多，所以在NameNode之中针对于日志是需要进行新日志切换的，保证一个日志的内容不会特别的长。
● 元数据（MetaData）：记录了所有的当前保留下来的文件相关信息，与edits中的文件的内容相比，元数据的内容是滞后的。
● fsimage（文件系统快照）：是元数据的持久化保存，fsimage要与元数据是同步的，但是滞后于edits，fsimage是保存在磁盘上的元空间数据，在节点宕机滞后会提供元数据的持久化保存。
● fstime：保存最后一次的检查点（CheckPoint）信息；
    |- fs.checkpoin.size：规定edits文件的最大值，一旦超过了这个值（默认大小为64M）将强制执行checkpoin，不管其是否达到了最大时间间隔。
    
```

>  **注：**<br/>
用户如果需要执行HDFS的文件操作，那么所有的操作会记录在edits日志文件之中;<br/>
而用户如果需要进行文件的相关获取，那么就需要通过元数据的模式来完成，但是考虑到有可能出现宕机的情况，所以内存中的元数据也会在磁盘中进行持久化保存（fsimage）。<br/>
而后为了保证元数据的内容是最新的，此时就需要SecondartNameNode来对元数据进行更新处理。<br/>

> **SecondaryNameNode工作流程：**  <br/>
SecondaryNameNode通过NameNode切换到一个新的edits文件； <br/>
SecondaryNameNode通过http访问从NameNode获得fsimage与edits； <br/>
SecondaryNameNode将fsimage载入内存，然后开始合并edits； <br/>
SecondaryNameNode将新的fsimage发送回NameNode；<br/>
NameNode用新的fsimage替换掉旧的fsimage. 

**(2)HDFS Shell命令**

① 列出根目录下的所有的文件信息；

```
hadoop fs -ls /
```

② 在HDFS上创建目录：

```
hadoop fs -mkdir -p /cn/input
```

③ 将文件上传到指定的目录之中：

```
hadoop fs -put /usr/local/info.txt /cn/input
```

④ 复制HDFS的文件

```
hadoop fs -cp /cn/input/info.txt /cn
```

⑤ 删除文件：

```
hadoop fs -rm /cn/info.txt
```

⑥ 移动文件：

```
hadoop fs -mv /cn/input/info.txt /cn/input/nihao.txt
```

⑦ 删除文件目录：

```
hadoop fs -rmr /cn
hadoop fs -rmr -r /cn
```

⑧ 查看帮助信息：

```
hadoop fs -help
```


