
ServerInfo进行一些公共的信息存储：

```
package cn.hello.commons;
public class ServerInfo {
	public static final int PORT = 8080 ; // 服务端运行端口号
	public static final String HOSTNAME = "localhost" ; // 服务器地址
}
```



EchoServer服务器端程序：


```
package cn.hello.hellonetty.server;
import cn.hello.commons.ServerInfo;
import cn.hello.hellonetty.server.handle.EchoServerHandler;
import io.netty.bootstrap.ServerBootstrap;
import io.netty.channel.*;
import io.netty.channel.nio.NioEventLoopGroup;
import io.netty.channel.socket.SocketChannel;
import io.netty.channel.socket.nio.NioServerSocketChannel;
import io.netty.handler.codec.string.StringDecoder;
import io.netty.handler.codec.string.StringEncoder;
import io.netty.util.CharsetUtil;
public class EchoServer {
	public void run() throws Exception {	// 程序的运行方法，异常全部抛出
		// 1、在Netty里面服务器端的程序需要准备出两个线程池
		// 1-1、第一个线程池为接收用户请求连接的线程池；
		EventLoopGroup boosGroup = new NioEventLoopGroup() ; 
		// 1-2、第二个线程池为进行数据请求处理的线程池
		EventLoopGroup workGroup = new NioEventLoopGroup() ;
		try {
			// 2、所有的服务端的程序需要通过ServerBootstrap类进行启动
			ServerBootstrap serverBootstrap = new ServerBootstrap() ;
			// 3、为此服务器端配置有线程池对象（配置连接线程池、工作线程池）
			serverBootstrap.group(boosGroup, workGroup) ;
			// 4、指明当前服务器的运行形式，基于NIO的ServerSocket实现
			serverBootstrap.channel(NioServerSocketChannel.class) ;
			// 5、进行Netty数据处理的过滤器配置（责任链设计模式）

			serverBootstrap.childHandler(new ChannelInitializer<SocketChannel>() {
				@Override
				protected void initChannel(SocketChannel ch) throws Exception {
					ch.pipeline().addLast(new StringEncoder(CharsetUtil.UTF_8));
					ch.pipeline().addLast(new StringDecoder(CharsetUtil.UTF_8));
					ch.pipeline().addLast(new EchoServerHandler()) ; // 自定义程序处理逻辑
				} 
			}) ;
			// 6、由于当前的服务器主要实现的是一个TCP的回应处理程序，那么在这样的情况下就必须进行一些TCP属性配置
			serverBootstrap.option(ChannelOption.SO_BACKLOG, 64) ; // 当处理线程全满时的最大等待队列长度
			// 7、绑定服务器端口并且进行服务的启动
			ChannelFuture future = serverBootstrap.bind(ServerInfo.PORT).sync() ;	// 异步线程处理
			future.channel().closeFuture().sync() ; // 处理完成之后进行关闭
		} catch (Exception e) {
			boosGroup.shutdownGracefully() ;	// 关闭主线程池
			workGroup.shutdownGracefully() ;	// 关闭子线程池
		}
	}
}
```
EchoServerHandler 服务器端自定义的ChannelHandleAdapter子类：


```
package cn.hello.hellonetty.server.handle;
import io.netty.buffer.ByteBuf;
import io.netty.buffer.Unpooled;
import io.netty.channel.ChannelHandlerAdapter;
import io.netty.channel.ChannelHandlerContext;
import io.netty.util.CharsetUtil;

public class EchoServerHandler extends ChannelHandlerAdapter {
	//注册-链接激活-信息读取
	/**
	 * 当客户端连接到服务器端之后，需要有一个连接激活的方法，此方法可以直接向客户端发送消息
	 */
	@Override
	public void channelActive(ChannelHandlerContext ctx) throws Exception {
		System.err.println("〖服务器端-生命周期〗通道连接激活。");
		// 1、对于发送的消息可以发送中文或者是一些标记（ok标记）
		// byte data [] = "【服务器端】连接通道已经建立成功，可以开始进行服务器通信处理".getBytes() ;
		// 2、Nio的处理本质是需要进行缓冲区的处理
		// ByteBuf message = Unpooled.buffer(data.length) ; // 创建一个缓冲区
		// 3、将数据写入到缓冲区之中
		// message.writeBytes(data) ; // 写入字节数据
		// 4、进行信息的发送，发送完成后刷新缓冲区
		// ctx.writeAndFlush(message) ; // 消息发送
	}

	@Override
	public void channelRegistered(ChannelHandlerContext ctx) throws Exception {
		System.err.println("〖服务器端-生命周期〗通道注册。");
	}
	@Override
	public void channelUnregistered(ChannelHandlerContext ctx) throws Exception {
		System.err.println("〖服务器端-生命周期〗通道注销。");
	}
	@Override
	public void channelInactive(ChannelHandlerContext ctx) throws Exception {
		System.err.println("〖服务器端-生命周期〗通道关闭。");
	}
	/**
	 * 当接收到消息之后会自动调用此方法对消息内容进行处理；
	 * @param msg 接收到的消息，这个消息可能是各种类型的数据，本程序中使用的是ByteBuf
	 */
	@Override
	public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
//		ByteBuf in = (ByteBuf) msg ; // 1、接收消息内容
//		String inputStr = in.toString(CharsetUtil.UTF_8) ; // 2、得到用户发送的数据
		String inputStr = (String) msg;
		String echoContent = "【ECHO】" + inputStr ; // 3、回应的消息内容
		if ("exit".equalsIgnoreCase(inputStr)) {	// 表示发送结束
			echoContent = "quit" ; // 结束的字符串信息
		} else if (inputStr.startsWith("userid")) {	// 该操作为验证数据信息
			echoContent = "【服务器端】欢迎“" + inputStr.split(":")[1] + "”登录访问，连接通道已经建立成功，可以开始进行服务器通信处理";
		}
		ByteBuf echoBuf = Unpooled.buffer(echoContent.length()) ;
		echoBuf.writeBytes(echoContent.getBytes()) ;
		ctx.writeAndFlush(echoBuf) ;
	}
	@Override
	public void channelReadComplete(ChannelHandlerContext ctx) throws Exception {
		System.err.println("〖服务器端-生命周期〗信息读取完毕。");
	}
	@Override
	public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause) throws Exception {
		System.err.println("〖服务器端-生命周期〗服务器出现异常。");
		// ctx.close() ;
	}
}
```

服务器端启动类EchoServerStart：

```
package cn.hello.hellonetty.main;
import cn.hello.hellonetty.server.EchoServer;
public class EchoServerStart {
	public static void main(String[] args) throws Exception {
		System.out.println("************* 服务器正常启动 *************");
		new EchoServer().run(); 
	}
}
```



EchoClient客户端程序：

```
package cn.hello.hellonetty.client;
import cn.hello.commons.ServerInfo;
import cn.hello.hellonetty.client.handler.EchoClientHandler;
import io.netty.bootstrap.Bootstrap;
import io.netty.channel.ChannelFuture;
import io.netty.channel.ChannelInitializer;
import io.netty.channel.EventLoopGroup;
import io.netty.channel.nio.NioEventLoopGroup;
import io.netty.channel.socket.SocketChannel;
import io.netty.channel.socket.nio.NioSocketChannel;
import io.netty.handler.codec.string.StringDecoder;
import io.netty.handler.codec.string.StringEncoder;
import io.netty.util.CharsetUtil;
public class EchoClient {
	public void run() throws Exception  {	// 启动客户端程序
		// 1、创建一个进行数据交互的处理线程池
		EventLoopGroup group = new NioEventLoopGroup() ;
		try {
			Bootstrap clientBootstrap = new Bootstrap() ; // 创建客户端处理
			clientBootstrap.group(group) ; // 设置连接池
			clientBootstrap.channel(NioSocketChannel.class) ; // 设置通道类型
			clientBootstrap.handler(new ChannelInitializer<SocketChannel>() {
				@Override
				protected void initChannel(SocketChannel ch) throws Exception {
					ch.pipeline().addLast(new StringEncoder(CharsetUtil.UTF_8));
					ch.pipeline().addLast(new StringDecoder(CharsetUtil.UTF_8));
					ch.pipeline().addLast(new EchoClientHandler()) ; // 自定义程序处理逻辑
				} 
			}) ;
			// 连接远程服务器端
			ChannelFuture future = clientBootstrap.connect(ServerInfo.HOSTNAME, ServerInfo.PORT).sync() ;
			future.channel().closeFuture().sync() ; // 等待关闭，Handler里面关闭处理 
		} catch (Exception e) {
			group.shutdownGracefully() ; // 关闭线程池
		}
	}
}
```


客户端的ChannelHandlerAdapter子类：

```
package cn.hello.hellonetty.client.handler;

import cn.hello.util.InputUtil;
import io.netty.buffer.ByteBuf;
import io.netty.buffer.Unpooled;
import io.netty.channel.ChannelFuture;
import io.netty.channel.ChannelFutureListener;
import io.netty.channel.ChannelHandlerAdapter;
import io.netty.channel.ChannelHandlerContext;
import io.netty.util.CharsetUtil;

public class EchoClientHandler extends ChannelHandlerAdapter {
	@Override
	public void channelActive(ChannelHandlerContext ctx) throws Exception {// 客户端的连接激活
		String data = "userid:hello" ; // 当前建立连接时用户的身份信息
		ctx.writeAndFlush(data);
//		ByteBuf buf = Unpooled.buffer(data.length()) ;
//		buf.writeBytes(data.getBytes()) ;
//		ctx.writeAndFlush(buf) ;
	}

	@Override
	public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
//		ByteBuf buf = (ByteBuf) msg ; // 接收远程发回的数据信息
//		String content = buf.toString(CharsetUtil.UTF_8) ; // 接收数据
		String content = (String)msg;
		if ("quit".equalsIgnoreCase(content)) {	// 服务器端要结束处理
			System.out.println("##### 本次操作结束，已退出 #####");
			ctx.close() ; // 关闭连接通道，和服务器端端口
		} else {	// 需要进行进一步的处理操作
			System.out.println("｛客户端｝" + content); // 服务器端的回应信息
			String inputStr = InputUtil.getString("请输入要发送的信息：") ;
			ByteBuf newBuf = Unpooled.buffer(inputStr.length()) ;
			newBuf.writeBytes(inputStr.getBytes()) ; // 写入数据
			ChannelFuture future = ctx.writeAndFlush(newBuf) ; // 发送数据
			future.addListener(new ChannelFutureListener() {	// 进行监听的回调处理
				@Override
				public void operationComplete(ChannelFuture future) throws Exception {
					if (future.isSuccess()) {	// 操作成功
						System.out.println("********** 客户端回信息发送成功。");
					}
				}}) ;

		}
	}
}
```

客户端启动主类：


```
package cn.hello.hellonetty.main;
import cn.hello.hellonetty.client.EchoClient;
public class EchoClientMain {
	public static void main(String[] args) throws Exception {
		System.out.println("*******************客户端正常启动***********");
		new EchoClient().run() ;	// 客户端运行
	}
}
```


键盘输入工具类：

```
package cn.hello.util;
import java.io.BufferedReader;
import java.io.InputStreamReader;
public class InputUtil {
	private static final BufferedReader KEYBOARD_INPUT  = new BufferedReader(new InputStreamReader(System.in)) ;
	private InputUtil() {}
	/**
	 * 获取一个键盘输入的字符串数据，如果数据为空则重新输入
	 * @param prompt 提示文字
	 * @return 输入内容
	 */
	public static String getString(String prompt) {
		String content = null ;
		boolean flag = true ;
		while (flag) {
			System.out.print(prompt);
			try {
				content = KEYBOARD_INPUT.readLine() ;
				if (content == null || "".equals(content)) {
					System.out.println("输入的内容为空，请重新输入！");
				} else {
					flag = false ; // 结束循环
				}
			} catch (Exception e) {
				System.out.println("输入数据错误，请重新输入！");
			}
		}
		return content ;
	}
}
```

 

