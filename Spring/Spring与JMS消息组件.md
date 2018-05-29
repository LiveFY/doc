
JMS(Java Message Service、Java消息服务)，指的是可以进行消息的发送与接收处理，在一般进行数据发送（不需要回执）的情况下使用到，但是JMS是一个古老的技术标准（它不是协议实现，而是格式上的实现），因为JMS的性能实际上并不高，最高的是AMQP协议的消息系统（RabbitMQ之类）。

# JMS消息组件的作用
多线程中一个经典的案例：生产者与消费者模型，其基本的核心流程在于：生产者负责生产数据，而消费者每当生产者生产完成数据之后要进行数据的取出，消息组件的本质也是这种生产者与消费者，不过追加了队列的概念，所以消息组件往往被称为消息队列中间件。

如果生产者很多，正常情况下消费者应该是一个数据的处理程序，数据的处理程序肯定不可能很多，如果继续采用一般的生产者和消费者模型就会出现队列塞满的情况，造成数据的发送出现阻塞问题。在很多情况下为了解决这样的问题，就希望可以提供有一个专门的分布式队列，而这种分布式的队列就是消息队列，而JavaEE官方标准就利用JMS作为这种消息队列的实现标准。

利用消息组件实现的业务调研，可以不像传统的WEB开发那样进行业务的处理信息回馈，它可以直接把内容发送过去即可，至于是否收到，并不需要关注。

对于JMS消息而言往往有两种类型：
    · 队列消息：所有的生产者发送数据，会有消费者根据数据的到达时间进行先进先出的消费处理；
    · 主题消息：所有的消费者共同接收到同一个主题内容。


# 配置AcitveMQ组件
  本次使用的是Ubuntu系统，ActiveMQ组件版本为：apache-activemq-5.15.4。
1. 利用FTP工具上传到Linux系统之中（保存路径/usr/local），此次使用的是Ubuntu系统进行操作。

2. 解压缩

```
tar xzvf /srv/ftp/apache-activemq-5.15.4-bin.tar.gz -C /usr/local
```

3. 更名

```
mv apache-activemq-5.15.4/ activemq
```

4. AcitveMQ之中内置了一个Jetty服务器，利用这个Web服务器可以直接打开后台的管理控制台，我们可以给自己设置一个使用账户，修改“/usr/local/activemq/conf/jetty-realm.properties”文件。

```
vim  /usr/local/activemq/conf/jetty-realm.properties
```
创建一个属于自己的账户：格式：username: password [,rolename ...] （用户名：密码，角色）

```
hello:hello,admin
```

5. 同时打开activemq的核心配置文件

```
vim /usr/local/activemq/conf/activemq.xml
```
追加如下内容：

```
<plugins>
		<simpleAuthenticationPlugin>
			<users>
				<authenticationUser username="hello" password="hello" groups="admin"/>
			</users>
		</simpleAuthenticationPlugin>
	</plugins>
```

如下所示：

```
<broker xmlns="http://activemq.apache.org/schema/core" brokerName="localhost" dataDirectory="${activemq.data}">
        <plugins>
                <simpleAuthenticationPlugin>
                        <users>
                                <authenticationUser username="hello" password="hello" groups="admin"/>
                        </users>
                </simpleAuthenticationPlugin>
        </plugins>
```

6.启动ActiveMQ

```
/usr/local/activemq/bin/activemq start
```

7.在Window系统中利用linux的ip通过浏览器进行访问，地址：

```
http://192.168.133.132:8161/
```

之后利用之前配置的用户名和密码进行登录：hello/hello，之后建立两个消息的名称：
>   · Queue队列，名称为：“hello.queue.msg”  <br/>
>   · Topic主题，名称为：“hello.topic.msg”



# 建立Queue消息发送者
    
1.本次使用的Maven分模块的项目构建的形式。
>     hellospring为父项目
>     hellospring-activemq-send为消息发送者 
>     hellospring-activemq-client为消息消费者

[hellospring]其中的pom.xml文件

```
<project xmlns="http://maven.apache.org/POM/4.0.0"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>

	<groupId>cn.hello</groupId>
	<artifactId>hellospring</artifactId>
	<version>0.0.1</version>
	<packaging>pom</packaging>

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
	</properties>
	<dependencyManagement>
		<dependencies>
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
		<module>hellopring-activemq-send</module>
	</modules>
</project>
```

2.[hellospring-acitvemq-send]中的pom文件

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
	<artifactId>hellospring-activemq-send</artifactId>
	<name>hellospring-activemq-send</name>
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
			<groupId>org.apache.activemq</groupId>
			<artifactId>activemq-all</artifactId>
		</dependency>
		<dependency>
			<groupId>javax.jms</groupId>
			<artifactId>jms</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-jms</artifactId>
		</dependency>

	</dependencies>
</project>
```

3.[hellospring-activemq-send]创建src/profiles/dev目录，再创建config子目录，其中定义activemq.properties配置文件，其中保存所有的连接信息。

```
# 定义activemq的连接地址
activemq.broker.url=tcp://http://192.168.133.132:61616
# 定义activemq的用户名 
activemq.user.name=hello
# 定义activemq的密码
activemq.user.password=hello
# 要操作的队列名称
activemq.queue.name=hello.queue.msg
```
4.[hellospring-activemq-send]创建srr/resource/spring-base.xml配置文件，追加如下内容。

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:p="http://www.springframework.org/schema/p"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:task="http://www.springframework.org/schema/task"
	xsi:schemaLocation="http://www.springframework.org/schema/task http://www.springframework.org/schema/task/spring-task-4.3.xsd
		http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.3.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd">  
	<context:component-scan base-package="cn.mldn.mldnspring"/>	<!-- 定义扫描包 -->
	<!-- 配置资源项加载完成之后就可以在配置文件里面利用资源的key结合SpEL获取对应的value -->
	<context:property-placeholder location="classpath:config/*.properties"/>	<!-- 加载所有的资源配置项 -->
	<!-- 1、配置一个AcitveMQ的连接工厂定义 -->
	<bean id="targetConnectionFactory" class="org.apache.activemq.ActiveMQConnectionFactory">
		<property name="brokerURL" value="${activemq.broker.url}"/>
		<property name="userName" value="${activemq.user.name}"/>
		<property name="password" value="${activemq.user.password}"/>
	</bean>
	<!-- 2、定义连接工厂类，配置Spring-JMS的相关信息 -->
	<bean id="connectionFactory" class="org.springframework.jms.connection.SingleConnectionFactory">
		<property name="targetConnectionFactory" ref="targetConnectionFactory"/>
	</bean>
	<!-- 3、有了连接的相关信息之后还需要配置发送的目的地 -->
	<bean id="destination" class="org.apache.activemq.command.ActiveMQQueue">
		<constructor-arg value="${activemq.queue.name}"/>
	</bean>
	<!-- 定义JMS的发送的数据的模版类 -->
	<bean id="jmsTemplate" class="org.springframework.jms.core.JmsTemplate">
		<property name="connectionFactory" ref="connectionFactory"/>
	</bean>  
</beans>
```

5.[hellospring-activemq-send]中创建一个消息发送的业务接口：IMessageService。

```
public interface IMessageService {
	public void send(String msg) ;
}
```

6.[hellospring-activemq-send]中注入消息发送的目的地与JmsTemplate对象。

```
import javax.jms.Destination;
import javax.jms.JMSException;
import javax.jms.Message;
import javax.jms.Session;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.jms.core.JmsTemplate;
import org.springframework.jms.core.MessageCreator;
import org.springframework.stereotype.Service;
import cn.hello.hellospring.service.IMessageService;
@Service
public class MessageServiceImpl implements IMessageService {
	@Autowired
	private JmsTemplate jmsTemplate ;
	@Autowired
	private Destination destination ;
	@Override
	public void send(String msg) {
		this.jmsTemplate.send(this.destination,new MessageCreator() {
			@Override
			public Message createMessage(Session session) throws JMSException {
				return session.createTextMessage(msg);
			}
		});
	}

}
```


7.[hellospring-activemq-send]编写测试类实现消息的发送

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
	public void testSend() {
		this.messageService.send("hello");
	}
}
```
    
此时并没有消费者出在哪找，所以可以在ActvieMQ的web服务器端可以看到相应的信息。



# 建立Queue消息消费者
项目模块：hellospring-acitvemq-client

1.[hellospring-activemq-send]把之前的send模块拷贝过来，修改配置文件，将其变为clent模块。

2.[hellospring-activemq-client]创建一个监听的程序类，由于有JMS的存在，故可以直接使用MessageListener接口。

```
package cn.hello.hellospring.listener;
import javax.jms.JMSException;
import javax.jms.Message;
import javax.jms.MessageListener;
import javax.jms.TextMessage;
import org.springframework.stereotype.Component;
@Component
public class ConsumerMessageListener implements MessageListener {
	@Override
	public void onMessage(Message message) { 
		if (message instanceof TextMessage) {	// 文本消息
			TextMessage textMsg = (TextMessage) message ;
			try {
				System.out.println(textMsg.getText());
			} catch (JMSException e) {
				e.printStackTrace();
			}
		}
	}

}
```

3.[hellospring-activemq-client]修改spring-base.xml配置文件，进行消费端的配置。

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:p="http://www.springframework.org/schema/p"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:task="http://www.springframework.org/schema/task"
	xsi:schemaLocation="http://www.springframework.org/schema/task http://www.springframework.org/schema/task/spring-task-4.3.xsd
		http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.3.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd">  
	<context:component-scan base-package="cn.mldn.mldnspring"/>	<!-- 定义扫描包 -->
	<!-- 配置资源项加载完成之后就可以在配置文件里面利用资源的key结合SpEL获取对应的value -->
	<context:property-placeholder location="classpath:config/*.properties"/>	<!-- 加载所有的资源配置项 -->
	<!-- 1、配置一个AcitveMQ的连接工厂定义 -->
	<bean id="targetConnectionFactory" class="org.apache.activemq.ActiveMQConnectionFactory">
		<property name="brokerURL" value="${activemq.broker.url}"/>
		<property name="userName" value="${activemq.user.name}"/>
		<property name="password" value="${activemq.user.password}"/>
	</bean>
	<!-- 2、定义连接工厂类，配置Spring-JMS的相关信息 -->
	<bean id="connectionFactory" class="org.springframework.jms.connection.SingleConnectionFactory">
		<property name="targetConnectionFactory" ref="targetConnectionFactory"/>
	</bean>
	<!-- 3、有了连接的相关信息之后还需要配置发送的目的地 -->
	<bean id="destination" class="org.apache.activemq.command.ActiveMQQueue">
		<constructor-arg value="${activemq.queue.name}"/>
	</bean>
	<!-- 4、进行消息接收的监听程序的配置处理 -->
	<bean id="jmsContainer" class="org.springframework.jms.listener.DefaultMessageListenerContainer">
		<property name="connectionFactory" ref="connectionFactory"/>
		<property name="destination" ref="destination"/> 
		<property name="messageListener" ref="consumerMessageListener"/>
	</bean> 
</beans>
```

4.[hellospring-activemq-client]中添加Spring容器启动类

```
package cn.hello.hellospring.main;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
public class ClientMain {
	public static void main(String[] args) {
		ApplicationContext context = new ClassPathXmlApplicationContext("classpath:spring/spring-*.xml") ;
	}
}
```

此时程序运行结果为：
> hello 

# 处理Topic消息
队列消息主要指的就是可以有多个消息的生产者和多个消息的消费者，消费者将共同进行消费的处理，这个时候每一个消费者获取的消息内容一定是不同的，而如果说现在有多个消息的消费端，需要处理同一种消息就需要使用主题消息。
1.[hellospring-activemq-*]修改所有相关模块下的active.properties配置文件，修改为主题消息。

```
# 定义activemq的连接地址
activemq.broker.url=tcp://192.168.133.132:61616
# 定义activemq的用户名 
activemq.user.name=hello
# 定义activemq的密码
activemq.user.password=hello
# 要操作的队列名称
activemq.topic.name=hello.topic.msg
```


2.[hellospring-activemq-*]修改所有相关的模块下的spring-base.xml配置文件，将之前的队列消息类型更换为主题消息类型。

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:p="http://www.springframework.org/schema/p"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:task="http://www.springframework.org/schema/task"
	xsi:schemaLocation="http://www.springframework.org/schema/task http://www.springframework.org/schema/task/spring-task-4.3.xsd
		http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.3.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd">  
	<context:component-scan base-package="cn.mldn.mldnspring"/>	<!-- 定义扫描包 -->
	<!-- 配置资源项加载完成之后就可以在配置文件里面利用资源的key结合SpEL获取对应的value -->
	<context:property-placeholder location="classpath:config/*.properties"/>	<!-- 加载所有的资源配置项 -->
	<!-- 1、配置一个AcitveMQ的连接工厂定义 -->
	<bean id="targetConnectionFactory" class="org.apache.activemq.ActiveMQConnectionFactory">
		<property name="brokerURL" value="${activemq.broker.url}"/>
		<property name="userName" value="${activemq.user.name}"/>
		<property name="password" value="${activemq.user.password}"/>
	</bean>
	<!-- 2、定义连接工厂类，配置Spring-JMS的相关信息 -->
	<bean id="connectionFactory" class="org.springframework.jms.connection.SingleConnectionFactory">
		<property name="targetConnectionFactory" ref="targetConnectionFactory"/>
	</bean>
	<!-- 3、有了连接的相关信息之后还需要配置发送的目的地 -->
	<bean id="destination" class="org.apache.activemq.command.ActiveMQTopic">
		<constructor-arg value="${activemq.topic.name}"/>
	</bean> 
	<!-- 定义JMS的发送的数据的模版类 -->
	<bean id="jmsTemplate" class="org.springframework.jms.core.JmsTemplate">
		<property name="connectionFactory" ref="connectionFactory"/>
	</bean>  
</beans>
```

之后启动若干个消息的消费端进行消息的消费处理。
    
    
> **总结** <br/>
ActiveMQ属于最标准的JMS协议实现，不过由于其出现的时间较早，采用的是公共的TCP协议（在传输层上做的数据结构的封装），所以它的处理性能并不会特别的高。