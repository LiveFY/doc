> 踩坑小记：erlang的环境为19.x,对应的rabbitmq只能只能使用到3.6.x,如果想使用最新版本的rabbimq，则需要下载新版的erlang开发包。

1. 将组件“rabbitmq-server-generic-unix-3.6.6.tar.xz”直接上传到linux系统之中。

2. 解压缩文件  <br/>

· 对“xz”文件解压缩：

```
xz -d /srv/ftp/rabbitmq-server-generic-unix-3.6.6.tar.xz
```

· 对“tar”文件解压缩：

```
tar xvf /srv/ftp/rabbitmq-server-generic-unix-3.6.6.tar -C /usr/local/
```

3. 对解压缩的文件进行更名

```
mv /usr/local/rabbitmq_server-3.6.6/ /usr/local/rabbitmq
```

4. 启动rabbimq服务


```
/usr/local/rabbitmq/sbin/rabbitmq-server start > /dev/null 2>&1 &
```
> 此为后台启动，如果想直接启动，则直接输入/usr/local/rabbitmq/sbin/rabbitmq-server start 即可。


5. 为rabbimq配置一个新的账户：

```
/usr/local/rabbitmq/sbin/rabbitmqctl add_user hello java
```

> 账号/密码： hello/java

6. 为hello用户设置为管理员权限


```
/usr/local/rabbitmq/sbin/rabbitmqctl set_user_tags hello administrator
```

7. rabbimq本身提供有管理控制台，直接启动控制台进程即可


```
/usr/local/rabbitmq/sbin/rabbitmq-plugins enable rabbitmq_management
```

8. 在window上通过浏览器进行访问：http://192.168.133.132:15672

![image](http://wx1.sinaimg.cn/mw690/0060lm7Tly1fsgs4zg3oyj30dv05pmx1.jpg)
