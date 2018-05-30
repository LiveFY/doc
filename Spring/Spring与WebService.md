# WebService简介
WebService是历史上出现的第二代分布式技术的实现标准，而现在最为流行的是第三代标准：Restful。WebService最初开发极为复杂，尽管现在已经简化了其开发的模式，但是WebService处理的性能并不是很高。
WebService（Web服务），实际上是RPC（远程过程调用）技术的一项实现手段。可以将公共的操作功能或者业务功能以分布式的形式进行定义是最方便的解决方案。
历史上出现过的分布式技术：CORBA(公共对象请求代理架构)、RMI(远程方法调用)、RMI-IOP协议、WebService技术、Dubbo、HSF、GRPC、Restful。


# WebService开发
分布式项目开发最重要的环节就是远程接口，那么就证明远程服务器端需要使用这个接口，而客户端也需要同样使用到这个接口。
> 项目结构: <br/>
hellospring  父项目 <br />
hellospring-ws-api  远程接口模块 <br/>
hellospring-ws-provider  接口的实现类模块 <br/>
hellospring-ws-consumer  接口的调用类模块


## 创建公共接口项目
1.[hellospring]创建hellospring-ws-api模块。

2.[hellospring-ws-api]创建一个远程接口。

```
package cn.hello.hellospring.service;
import javax.jws.WebService;
@WebService 
public interface IMessageService {
	public String echo(String msg) ;
}
```

3.[hellospring]修改pom.xml文件。

```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<groupId>cn.hello</groupId>
	<artifactId>hellospring</artifactId>
	<version>0.0.1</version>
	<packaging>pom</packaging
	<name>hellospring</name>
	<url>http://maven.apache.org</url>
	<properties>
		<!-- 以下为Maven环境定义属性 -->
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
		<jdk.version>1.8</jdk.version>
		<maven-compiler-plugin.version>3.7.0</maven-compiler-plugin.version>
		<!-- 以下为依赖包版本定义属性 -->
		<junit.version>4.12</junit.version>
		<spring.version>5.0.3.RELEASE</spring.version>
		<servlet.version>4.0.1</servlet.version>
		<jsp.version>2.2</jsp.version>
		<jstl.version>1.2</jstl.version>
		<annotations-api.version>9.0.8</annotations-api.version>
		<quartz.version>2.3.0</quartz.version>
		<activemq.version>5.15.4</activemq.version>
		<jms.version>1.1</jms.version>
		<hellospring.version>0.0.1</hellospring.version>
	</properties>
	<dependencyManagement>
		<dependencies>
			<dependency>
				<groupId>cn.hello</groupId>
				<artifactId>hellospring-ws-api</artifactId>
				<version>${hellospring.version}</version>
			</dependency>
			<dependency>
				<groupId>org.apache.activemq</groupId>
				<artifactId>activemq-all</artifactId>
				<version>${activemq.version}</version>
			</dependency>
			<dependency>
				<groupId>javax.jms</groupId>
				<artifactId>jms</artifactId>
				<version>${jms.version}</version>
			</dependency>
			<dependency>
				<groupId>org.springframework</groupId>
				<artifactId>spring-jms</artifactId>
				<version>${spring.version}</version>
			</dependency>
			<dependency>
				<groupId>org.springframework</groupId>
				<artifactId>spring-aop</artifactId>
				<version>${spring.version}</version>
			</dependency>
			<dependency>
				<groupId>org.springframework</groupId>
				<artifactId>spring-aspects</artifactId>
				<version>${spring.version}</version>
			</dependency>
			<dependency>
				<groupId>junit</groupId>
				<artifactId>junit</artifactId>
				<version>${junit.version}</version>
				<scope>test</scope>
			</dependency>
			<dependency>
				<groupId>org.springframework</groupId>
				<artifactId>spring-context</artifactId>
				<version>${spring.version}</version>
			</dependency>
			<dependency>
				<groupId>org.springframework</groupId>
				<artifactId>spring-core</artifactId>
				<version>${spring.version}</version>
			</dependency>
			<dependency>
				<groupId>org.springframework</groupId>
				<artifactId>spring-beans</artifactId>
				<version>${spring.version}</version>
			</dependency>
			<dependency>
				<groupId>org.springframework</groupId>
				<artifactId>spring-context-support</artifactId>
				<version>${spring.version}</version>
			</dependency>
			<dependency>
				<groupId>org.springframework</groupId>
				<artifactId>spring-test</artifactId>
				<version>${spring.version}</version>
				<scope>test</scope>
			</dependency>
			<dependency>
				<groupId>javax.servlet.jsp</groupId>
				<artifactId>jsp-api</artifactId>
				<version>${jsp.version}</version>
			</dependency>
			<dependency>
				<groupId>javax.servlet</groupId>
				<artifactId>javax.servlet-api</artifactId>
				<version>${servlet.version}</version>
			</dependency>
			<dependency>
				<groupId>javax.servlet</groupId>
				<artifactId>jstl</artifactId>
				<version>${jstl.version}</version>
			</dependency>
			<dependency>
				<groupId>org.apache.tomcat</groupId>
				<artifactId>tomcat-annotations-api</artifactId>
				<version>${annotations-api.version}</version>
			</dependency>
			<dependency>
				<groupId>org.quartz-scheduler</groupId>
				<artifactId>quartz</artifactId>
				<version>${quartz.version}</version>
			</dependency>
			<dependency>
				<groupId>org.springframework</groupId>
				<artifactId>spring-tx</artifactId>
				<version>${spring.version}</version>
			</dependency>
		</dependencies>
	</dependencyManagement>
	<build>
		<plugins>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-compiler-plugin</artifactId>
				<version>${maven-compiler-plugin.version}</version>
				<configuration>
					<source>${jdk.version}</source><!-- 源代码使用的开发版本 -->
					<target>${jdk.version}</target><!-- 需要生成的目标class文件的编译版本 -->
					<encode>${project.build.sourceEncoding}</encode>
				</configuration>
			</plugin>
		</plugins>
	</build>
	<modules>
		<module>hellospring-base</module>
		<module>hellospring-ws-api</module>
		<module>hellospring-ws-provider</module>
    <module>hellospring-ws-consumer</module>
  </modules>
</project>
```
4.[hellospring-ws-api]中的pom.xml文件

```
<?xml version="1.0"?>
<project
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd"
	xmlns="http://maven.apache.org/POM/4.0.0"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
	<modelVersion>4.0.0</modelVersion>
	<parent>
		<groupId>cn.hello</groupId>
		<artifactId>hellospring</artifactId>
		<version>0.0.1</version>
	</parent>
	<artifactId>hellospring-ws-api</artifactId>
	<name>hellospring-ws-api</name>
	<url>http://maven.apache.org</url>
	<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
	</properties>
	<dependencies>
		<dependency>
			<groupId>junit</groupId>
			<artifactId>junit</artifactId>
			<scope>test</scope>
		</dependency>
	</dependencies>
</project>
```

## 创建WebService服务提供者
WebService的服务提供者的主要功能是进行远程接口的具体实现，定义的是接口实现子类。
1.[hellospring]创建新的项目:hellospring-ws-provider。

2.[hellospring-ws-provider]修改其中pom.xml文件。

```
<?xml version="1.0"?>
<project
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd"
	xmlns="http://maven.apache.org/POM/4.0.0"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
	<modelVersion>4.0.0</modelVersion>
	<parent>
		<groupId>cn.hello</groupId>
		<artifactId>hellospring</artifactId>
		<version>0.0.1</version>
	</parent>
	<artifactId>hellospring-ws-provider</artifactId>
	<name>hellospring-ws-provider</name>
	<url>http://maven.apache.org</url>
	<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
	</properties>
	<dependencies>
		<dependency>
			<groupId>cn.hello</groupId>
			<artifactId>hellospring-ws-api</artifactId>
		</dependency>
		<dependency>
			<groupId>junit</groupId>
			<artifactId>junit</artifactId>
			<scope>test</scope>
		</dependency>
	</dependencies>
</project>
```

3.[hellospring-ws-provider]定义IMessageService接口实现子类

```
package cn.hello.hellospring.service.impl;
import javax.jws.WebService;
import cn.hello.hellospring.service.IMessageService;
@WebService(		// 进行WebService服务注册
		serviceName = "messageService", 	// 服务名称
		endpointInterface = "cn.hello.hellospring.service.IMessageService")	// 服务对应的远程接口
public class MessageServiceImpl implements IMessageService {

	@Override 
	public String echo(String msg) {
		if (msg == null) {
			throw new RuntimeException("空的消息，无法进行处理！") ;
		}
		return "【ECHO】msg = " + msg;
	}

}
```
4.[hellospring-ws-provider]编写一个main程序进行接口发布测试。

```
package cn.hello.hellospring.main;
import javax.xml.ws.Endpoint;
import cn.hello.hellospring.service.IMessageService;
import cn.hello.hellospring.service.impl.MessageServiceImpl;
public class StartServiceMain {
	public static void main(String[] args) {
		IMessageService service = new MessageServiceImpl();
		Endpoint.publish("http://192.168.59.1:8888/message", service) ;
	}
}
```
5.WebService服务运行之后会生成一个访问地址：“http://192.168.59.1:8888/message？wsdl”
访问之后就会出现如下所示：
![WebService](http://ww1.sinaimg.cn/large/87c01ec7gy1frted202dpj211w096wez.jpg)
此时证明项目成功运行。


## 创建WebService客户端
这次我们使用Java官方标准的WebService实现，虽然已经被定为标准了，但是对于客户端的消费处理依然复杂，需要生产许多的伪代码。<br/>
在JDK的工具提供有**wsimport**的命令可以生产伪代码。
在windows下随便建立一个目录，可以测试一下此命令的使用。
![伪代码](http://ww1.sinaimg.cn/large/87c01ec7gy1frtfsr3n12j20qh07hdfu.jpg)

1.[hellospring]创建一个hellospring-ws-consumer项目模块

2.[hellospring-ws-consumer]创建一个WebService客户端程序。<br/>
![伪代码](http://ww1.sinaimg.cn/large/87c01ec7gy1frtfxmznjrj20eo0az0ta.jpg)

3.[hellospring-ws-consumer]输入之前的wsdl访问地址：http://192.168.59.1:8888/message？wsdl
![](http://ww3.sinaimg.cn/large/87c01ec7gy1frtg1nmr3mj20es0dwq45.jpg)

4.[hellospring-ws-consumer]选择代码生成的保存目录。

5.[hellospring-ws-consumer]此时生产很多的伪代码，利用这些伪代码可以实现远程的web服务调用。

6.[hellospring-ws-conusmer]编写程序利用伪代码进行程序调用。

```
package cn.hello.hellospring.main;
import cn.hello.hellospring.service.IMessageService;
import cn.hello.hellospring.service.impl.MessageServiceLocator;
public class ConsumerService {
	public static void main(String[] args) throws Exception {
		// 通过远程获取接口对象，但是获取之后的调用就好像该接口在本地存在一样
		IMessageService messageService = new MessageServiceLocator().getMessageServiceImplPort();
		System.out.println(messageService.echo("hello world"));
	}
}
```

7.[hellospring-ws-consumer]此时在console窗体下有可能会有警告信息，如果不想看到此信息，则可以修改pom.xml文件，配置两个依赖库。

```
<?xml version="1.0"?>
<project
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd"
	xmlns="http://maven.apache.org/POM/4.0.0"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
	<modelVersion>4.0.0</modelVersion>
	<parent>
		<groupId>cn.hello</groupId>
		<artifactId>hellospring</artifactId>
		<version>0.0.1</version>
	</parent>
	<artifactId>hellospring-ws-consumer</artifactId>
	<name>mldnspring-ws-consumer</name>
	<url>http://maven.apache.org</url>
	<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
	</properties>
	<dependencies>
	<dependency>
		<groupId>javax.mail</groupId>
		<artifactId>mail</artifactId>
		<version>1.4.7</version>
	</dependency>
	<dependency>
		<groupId>javax.activation</groupId>
		<artifactId>activation</artifactId>
		<version>1.1.1</version>
	</dependency>

		<dependency>
			<groupId>junit</groupId>
			<artifactId>junit</artifactId>
			<scope>test</scope>
		</dependency>
	</dependencies>
</project>
```

此时就完成了一个JavaEE支持的WebService的实现。


# Spring整合WebService

我们从上面的JavaEE标准的支持上，可以看出在客户端要想进行服务的调用，都需要生成一个伪代码才可以进行WebService服务访问。感觉这样的功能很鸡肋。不过还好，Spring为我们提供了支持，可以避免伪代码的出现。
WebService技术：Apache Axis、XFire、CXF。本次使用CXF与Spring进行整合。

1.[hellospring]修改pom.xml文件，引入cxf相关依赖。

```
<cxf.version>3.2.4</cxf.version>
```

```
<dependency>
	<groupId>org.apache.cxf</groupId>
	<artifactId>cxf-rt-frontend-jaxws</artifactId>
	<version>${cxf.version}</version>
</dependency>
<dependency>
	<groupId>org.apache.cxf</groupId>
	<artifactId>cxf-rt-transports-http</artifactId>
	<version>${cxf.version}</version>
	</dependency>
<dependency>
	<groupId>org.apache.cxf</groupId>
	<artifactId>cxf-rt-transports-http-jetty</artifactId>
	<version>${cxf.version}</version>
</dependency>
```

2.[hellospring-ws-provider]修改pom.xml文件，引入以上依赖库，同时引入Spring的依赖库。

```
<?xml version="1.0"?>
<project
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd"
	xmlns="http://maven.apache.org/POM/4.0.0"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
	<modelVersion>4.0.0</modelVersion>
	<parent>
		<groupId>cn.hello</groupId>
		<artifactId>hellospring</artifactId>
		<version>0.0.1</version>
	</parent>
	<artifactId>hellospring-ws-provider</artifactId>
	<name>hellospring-ws-provider</name>
	<url>http://maven.apache.org</url>
	<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
	</properties>
	<dependencies>
		<dependency>
			<groupId>org.apache.cxf</groupId>
			<artifactId>cxf-rt-frontend-jaxws</artifactId>
		</dependency>
		<dependency>
			<groupId>org.apache.cxf</groupId>
			<artifactId>cxf-rt-transports-http</artifactId>
		</dependency>
		<dependency>
			<groupId>org.apache.cxf</groupId>
			<artifactId>cxf-rt-transports-http-jetty</artifactId>
		</dependency>
		<dependency>
			<groupId>cn.hello</groupId>
			<artifactId>hellospring-ws-api</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-aop</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-aspects</artifactId>
		</dependency>
		<dependency>
			<groupId>junit</groupId>
			<artifactId>junit</artifactId>
			<scope>test</scope>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-context</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-core</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-beans</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-context-support</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-test</artifactId>
			<scope>test</scope>
		</dependency>
		<dependency>
			<groupId>javax.servlet.jsp</groupId>
			<artifactId>jsp-api</artifactId>
		</dependency>
		<dependency>
			<groupId>javax.servlet</groupId>
			<artifactId>javax.servlet-api</artifactId>
		</dependency>
		<dependency>
			<groupId>javax.servlet</groupId>
			<artifactId>jstl</artifactId>
		</dependency>
		<dependency>
			<groupId>org.quartz-scheduler</groupId>
			<artifactId>quartz</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-tx</artifactId>
		</dependency>
		<dependency>
			<groupId>org.apache.tomcat</groupId>
			<artifactId>tomcat-annotations-api</artifactId>
		</dependency>
		<dependency>
			<groupId>junit</groupId>
			<artifactId>junit</artifactId>
			<scope>test</scope>
		</dependency>
	</dependencies>
</project>
```

3.[hellospring-ws-provider]修改spring-base.xml文件，将远程接口交给Spring管理。

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:jaxws="http://cxf.apache.org/jaxws"
	xsi:schemaLocation="
		http://cxf.apache.org/jaxws 
		http://cxf.apache.org/schemas/jaxws.xsd
		http://www.springframework.org/schema/beans 
		http://www.springframework.org/schema/beans/spring-beans-4.3.xsd
		http://www.springframework.org/schema/context 
		http://www.springframework.org/schema/context/spring-context-4.3.xsd">  
	<context:component-scan base-package="cn.hello.hellospring"/>	<!-- 定义扫描包 -->
	<jaxws:endpoint implementor="cn.hello.hellospring.service.impl.MessageServiceImpl" 
		id="messageService" address="http://192.168.59.1:8888/message"/>
</beans>
```

4.[hellospring-ws-provider]此时主方法之中只是进行一个容器的启动。

```
package cn.hello.hellospring.main;
import javax.xml.ws.Endpoint;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import cn.hello.hellospring.service.IMessageService;
import cn.hello.hellospring.service.impl.MessageServiceImpl;
public class StartServiceMain {
	public static void main(String[] args) {
		new ClassPathXmlApplicationContext("spring/spring-*.xml") ;
	}
}
```

再次通过该地址进行访问：http://192.168.59.1:8888/message?wsdl，此时成功访问。
![](http://ww4.sinaimg.cn/large/87c01ec7gy1frtgtfod1xj20zo0ardga.jpg)


5.[hellospring-ws-consumer]之前的客户端需要生成一系列的伪代码进行处理，而Spring可以避免这些伪代码的生成，修改pom.xml文件追加依赖库。

```
<?xml version="1.0"?>
<project
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd"
	xmlns="http://maven.apache.org/POM/4.0.0"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
	<modelVersion>4.0.0</modelVersion>
	<parent>
		<groupId>cn.hello</groupId>
		<artifactId>hellospring</artifactId>
		<version>0.0.1</version>
	</parent>
	<artifactId>hellospring-ws-consumer</artifactId>
	<name>hellospring-ws-consumer</name>
	<url>http://maven.apache.org</url>
	<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
	</properties>
	<dependencies>
		<dependency>
			<groupId>javax.mail</groupId>
			<artifactId>mail</artifactId>
			<version>1.4.7</version>
		</dependency>
		<dependency>
			<groupId>javax.activation</groupId>
			<artifactId>activation</artifactId>
			<version>1.1.1</version>
		</dependency>
		<dependency>
			<groupId>org.apache.cxf</groupId>
			<artifactId>cxf-rt-frontend-jaxws</artifactId>
		</dependency>
		<dependency>
			<groupId>org.apache.cxf</groupId>
			<artifactId>cxf-rt-transports-http</artifactId>
		</dependency>
		<dependency>
			<groupId>org.apache.cxf</groupId>
			<artifactId>cxf-rt-transports-http-jetty</artifactId>
		</dependency>
		<dependency>
			<groupId>cn.hello</groupId>
			<artifactId>hellospring-ws-api</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-aop</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-aspects</artifactId>
		</dependency>
		<dependency>
			<groupId>junit</groupId>
			<artifactId>junit</artifactId>
			<scope>test</scope>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-context</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-core</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-beans</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-context-support</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-test</artifactId>
			<scope>test</scope>
		</dependency>
		<dependency>
			<groupId>javax.servlet.jsp</groupId>
			<artifactId>jsp-api</artifactId>
		</dependency>
		<dependency>
			<groupId>javax.servlet</groupId>
			<artifactId>javax.servlet-api</artifactId>
		</dependency>
		<dependency>
			<groupId>javax.servlet</groupId>
			<artifactId>jstl</artifactId>
		</dependency>
		<dependency>
			<groupId>org.quartz-scheduler</groupId>
			<artifactId>quartz</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-tx</artifactId>
		</dependency>
		<dependency>
			<groupId>org.apache.tomcat</groupId>
			<artifactId>tomcat-annotations-api</artifactId>
		</dependency>
		<dependency>
			<groupId>junit</groupId>
			<artifactId>junit</artifactId>
			<scope>test</scope>
		</dependency>
	</dependencies>
</project>
```

6.[hellospring-ws-consumer]在spring-base.xml文件里面定义相关的客户端配置。

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.3.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd">  
	<context:component-scan base-package="cn.hello.hellospring"/>	<!-- 定义扫描包 -->
	<!-- 2、根据工厂类创建一个临时的本地接口对象，该接口对象与远程的真实接口对象对应 -->
	<bean id="messageClient" class="cn.hello.hellospring.service.IMessageService" factory-bean="clientFactory" factory-method="create"/>
	<!-- 1、尽管使用了spring进行管理，但是整体的项目之中依然需要有一个代理程序 -->
	<bean id="clientFactory" class="org.apache.cxf.jaxws.JaxWsProxyFactoryBean">
		<!-- 定义出需要被代理的业务接口对象信息 -->
		<property name="serviceClass" value="cn.hello.hellospring.service.IMessageService"/>
		<property name="address" value="http://192.168.59.1:8888/message"/>
	</bean>
</beans>
```

7.[hellospring-ws-consumer]编写测试类实现远程调用。

```
package cn.hello.hellospring.test;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;
import cn.hello.hellospring.service.IMessageService;
@ContextConfiguration(locations= {"classpath:spring/spring-base.xml"})
@RunWith(SpringJUnit4ClassRunner.class)
public class TestMessageService {
	@Autowired
	private IMessageService messageService ;
	@Test
	public void testAdd() {
		System.out.println(this.messageService.echo("hello world"));
	}
}
```
此时虽然调用的是远程业务，但是却和调用本地方法一样。


> **总结**： <br/>
我们从JavaEE官方提供的WebService实现上可以看出，整体的开发非常的啰嗦，虽然现在和Spring整合进行处理之后，避免了客户端调用时伪代码的生成，但是这种形式的技术依然不是一个好的实现方式。之后我会写关于Dubbo和SpringCloud分布式开发技术实现方式。


