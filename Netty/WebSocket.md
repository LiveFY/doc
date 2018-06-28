Tomcat本身支持有WebSocket处理能力。本次直接利用Tomcat进行WebSocket的开发。

1、导入开发包

```
            <dependency>
				<groupId>javax.websocket</groupId>
				<artifactId>javax.websocket-api</artifactId>
				<version>${websocket.version}</version>
				<scope>provided</scope>
			</dependency>
```


2、服务端程序

```
import java.io.IOException;
import javax.websocket.OnClose;
import javax.websocket.OnMessage;
import javax.websocket.OnOpen;
import javax.websocket.Session;
import javax.websocket.server.ServerEndpoint;
@ServerEndpoint("/message") // 此为WebSocket程序的访问路径
public class EchoMessageServer {
	@OnOpen
	public void openMethod() {	// 方法名称可以自己任意定义
		System.out.println("********* 【EchoMessageServer】 打开连接 ************");
	}
	@OnMessage
	public void messageHandle(String message, Session session) { // 消息处理
		System.out.println("*********** 【EchoMessageServer】接收到发送的消息：" + message);
		try {
			session.getBasicRemote().sendText("ECHO : " + message);
		} catch (IOException e) {
			e.printStackTrace();
		} 
	}
	@OnClose 
	public void closeMethod() {
		System.out.println("********* 【EchoMessageServer】 关闭连接 ************");
	}
}
```


3、前端页面

```
<html>
<head>
	<title>WebSocket通讯</title>
	<meta charset="UTF-8"/>
	<script type="text/javascript">
		url = "ws://localhost/message" ; // 通讯地址
		window.onload = function() {	// 进行加载时间处理
			webSocket = new WebSocket(url) ; // 创建WebSocket对象
			webSocket.onopen = function() {
				document.getElementById("contentDiv").innerHTML += "<p>服务器连接成功，请进行消息的处理。</p>" ;
			}
			webSocket.onclose = function() {
				document.getElementById("contentDiv").innerHTML += "<p>已经和服务器断开连接。</p>" ;
			}
			document.getElementById("sendBut").addEventListener("click",function(){
				info = document.getElementById("msg").value ;
				webSocket.send(info) ; // 发送请求
				webSocket.onmessage = function(obj) {	// 进行消息回应
					document.getElementById("contentDiv").innerHTML += "<p>" + obj.data + "</p>" ;
					document.getElementById("msg").value = "" ;
				}
			},false) ;
		}
	</script>
</head>
<body>
<div id="contentDiv" style="height :300px; overflow:scroll ; background : lightblue">

</div>
<div id="inputDiv">
	请输入信息：<input type="text" name="msg" id="msg">
	<button type="button" id="sendBut">发送</button>
</div>
</body>
</html>
```

访问地址：http://localhost/mldnnetty-tomcat/message.htm


![image](http://wx3.sinaimg.cn/mw690/0060lm7Tly1fsrc6btuzdj30ka097q2u.jpg)