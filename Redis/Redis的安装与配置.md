## Redis编译与安装
1.将Redis开发包上传到Linux系统之中，但是需要注意的是，由于给出开发包属于源代码，所以建议解压缩到“/usr/local/src”目录之中。

```
tar xzvf /srv/ftp/redis-4.0.9.tar.gz -C /usr/local/src/
```

2.进入到redis源代码所在的安装目录（/usr/local/src） 

```
cd /usr/local/src/redis-4.0.9/
```

3.进行程序的编译处理

``` make ```


4.编译完成之后进行Redis安装

``` make install ```

5.Redis属于内存缓存数据库，那么如果现在是一台单独的Redis服务器，则应该考虑将所有可用内存都交给Redis来进行支配，所以输入下面命令来执行此操作。

```
echo "vm.overcommit_memory=1" >> /etc/sysctl.conf
```
> 本次的操作指的是进行一次内存的分配策略，在进行设置的时候“vm.overcommit_memory=1”属性有如下是三个取值范围：<br/>
· “0”：表示在进行处理的时候首先要检查是否有足够的内存供应，如果现在没有足够的内存供应，则无法分配，内存申请失败，如果有可用内存则直接进行申请开辟。<br/>
· “1”：表示将所有的内存都交给应用使用，而不关心当前的内存状态如何。<br/>
· “2”：表示允许分配超过所有的物理内存和交换空间的内存的总和。<br/>

6.将以上的配置写入内核参数之中

```
/sbin/sysctl -p
```

7.为了方便使用Redis数据库，建立一个Redis支持的命令工具目录

```
mkdir -p /usr/local/redis/{bin,conf}
```

8.通过源代码目录拷贝出Redis所需要的程序运行文件 <br/>
1）拷贝Redis服务启动程序：

```
cp /usr/local/src/redis-4.0.9/src/redis-server /usr/local/redis/bin/
```

2）拷贝Redis命令行客户端：

```
cp /usr/local/src/redis-4.0.9/src/redis-cli /usr/local/redis/bin/
```
3）拷贝性能测试工具：

```
cp /usr/local/src/redis-4.0.9/src/redis-benchmark /usr/local/redis/bin/
```
4）拷贝配置文件：

```
cp /usr/local/src/redis-4.0.9/redis.conf /usr/local/redis/conf/
```

## Redis数据库配置
如果要想配置Redis数据库，主要的配置文件就是“redis.conf”，所有的配置项一定要在此处完成。<br/>
1.Redis作为一个具备持久化功能的缓存数据库，所以其一定也会有一个用于数据存储的目录， 那么一般在Redis处理的时候会有三类文件需要做保存：Redis运行时的pid、Redis相关处理日志、Redis的数据文件，所以建立一个目录用于存放这些数据。

```
mkdir -p /usr/data/redis/{run,logs,dbcache}
```
2.修改redis.conf配置文件

```
vim /usr/local/redis/conf/redis.conf
配置Redis运行端口：port 6379
配置Redis是否为后台运行：daemonize yes
设置进程保存路径：pidfile /usr/data/redis/run/redis_6379.pid
设置日志保存目录：logfile "/usr/data/redis/logs/redis_6379.log"
Redis支持的数据库个数：databases 16
保存数据文件目录：dir /usr/data/redis/dbcache
```

3.启动Redis服务

```
/usr/local/redis/bin/redis-server /usr/local/redis/conf/redis.conf
```
> 如果要启动Redis-Server就必须明确要使用redis.conf配置文件。

4.Redis启动会占用6379端口，可以查看端口

```
netstart -nptl
或者使用 
ps -ef | grep redis
```

5.启动Redis客户端
方式一：连接本机6379端口

```
/usr/local/redis/bin/redis-cli
```
方式二：远程连接

```
/usr/local/redis/bin/redis-cli -h 127.0.0.1 -p 6379
```

6.设置一个数据

```
set hello java
```


7.取得数据

```
get hello
```


8.在Redis数据库中存在有”redis-benchmark“性能测试工具，可以直接使用这个工具来观察Redis的性能。

```

查看使用帮助：/usr/local/redis/bin/redis-benchmark --help  
模拟200个客户端进行数据处理：/usr/local/redis/bin/redis-benchmark -h 127.0.0.1 -p 6379 -n 10000 -d 30 -c 200
```
> “-h”: 要连接的Redis服务器的地址 <br/>
“-p”:  要连接的Redis的运行端口 <br/>
“-c”：指的是模拟客户端的数量 <br/>
“-d”: 每一次操作的客户端数量 <br/>
“-n”: 每一个用户发出的请求数量

9.关闭Redis <br/>
> · 取得要关闭的Redis服务的进程，而后使用kill直接杀死 <br/>
· 直接使用killall redis-server 干掉所有的Redis服务
