本次使用的系统的是Ubuntu，由于在系统之中要使用RabbitMQ,需要Erlang的支持。

1.将erlang的源代码“otp-src-19.2.tar.gz”上传到系统之中。

2.将源代码进行解压缩

```
tar xzvf /srv/ftp/otp_src_19.2.tar.gz -C /usr/local/src/
```

3.编译erlang之前需要配置一个组件包

```
apt-get -y install libncurses5-dev
```

4.进入源代码所在目录

```
cd /usr/local/src/otp_src_19.2/
```

5.进行安装配置

```
./configure --prefix=/usr/local/erlang
```

6.进行编译与安装


```
make && make install
```

7.将erlang的环境配置到环境之中 <br/>
· 打开配置文件
```
vim /etc/profile
```


· 在其中追加：

```
export ERLANG_HOME=/usr/local/erlang
export JAVA_HOME=/usr/local/jdk
export MYSQL_HOME=/usr/local/mysql
export PATH=$PATH:$JAVA_HOME/bin:$MYSQL_HOME/bin:$ERLANG_HOME/bin:
```

· 使配置文件立即生效：

```
source /etc/profile
```

8.打开erlang的交互式界面

```
erl
```

9.编写如下代码

```
io:format("helloworld").
```
> 可以得到数据的显示说明环境成功安装。

10.退出交互环境


```
halt().
```


