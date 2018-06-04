在Redis数据库里面提供有一种发布与订阅模式，这种操作更像是JMS中的主题订阅消息。<br/>


1.[Redis客户端-A]开启订阅模式：

```
SUBSCRIBE hello-channel
```
![运行结果](http://ww4.sinaimg.cn/large/87c01ec7gy1frzeoq8jthj20cn032dfp.jpg)
<br/>此时将开启一个通道，而后利用此通道可以实现信息的沟通。



2.[Redis客户端-B]利用指定的通道开启发布者模式，进行内容的传输。

```
publish hello-channel helloworld
```

![运行结果](http://ww3.sinaimg.cn/large/87c01ec7gy1frzerttuesj20fx01bgle.jpg)

[Redis客户端-A]的结果 <br/>
![image](http://ww3.sinaimg.cn/large/87c01ec7gy1frzesse5gsj20dd04vmx2.jpg)

> 这种操作形式和消息组件的应用是非常相似的，在某些时候不希望使用消息组件的时候可以使用Redis来替代。
