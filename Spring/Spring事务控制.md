	事务的主要作用三保证业务操作的完整性，在一个业务处理之中所有的更新操作要么一起成功，要么一起失败。
	
## 传统JDBC事物处理控制

对于事务的控制实际上数据库本身是有事务控制支持的，同时程序开发之中也提供有数据库的事务支持，而所有的事务都在Connection接口之中进行了定义，有如下的几个方法：setAutoCommit()、commit()、roolback()，而在事务控制里面有一个ACID原则。
	ACID：数据库事务正确执行的基本要素的缩写。
>	• 原子性（Atomicity）：整个事务中的所有操作，要么全部完成，要么全部不完成，不可能停滞在中间某个环节。事务在执行过程中发生错误，会被回滚（Rollback）到事务开始前的状态，就像这个事务从来没有执行过一样。<br/>
	• 一致性（Consistency）：一个事务可以封装状态改变（除非它是一个只读的）。事务必须始终保持系统处于一致的状态，不管在任何给定的时间并发事务有多少。<br/>
	• 隔离性（Isolation）：隔离状态执行事务，使它们好像是系统在给定时间内执行的唯一操作。如果两个事务，运行在相同的时间内，执行相同的功能，事务的隔离性将确保每一事务在系统中任务只有该事务在使用系统。<br/>
	• 持久性（Durability）：在事务完成以后，该事务对数据库所做的更改边持久的保存在数据库之中，并不会被回滚。

但是在整体的操作之中一定要清楚一个核心概念：事务的确是可以保证操作的完整性，但是事务iude完整性是依靠性能的损失来实现（SQL数据库最大的亮点：支持事务，但也是它最大的败笔，因为事务的存在导致传统关系型数据库性能下降），正是由于有了此类的限制，所以才会导致NOSQL数据的大力发展（没有事务支持）。

## Spring事务处理架构

使用JDBC进行数据库的开发是在Java开发之中唯一的途径，也就是不管你未来如何改变，那么对于这一操作是永远不可能改变的，所以所有的事物控制的本质就是对JDBC事务的更加高效的管理与控制，Spring开发框架本身具备的最大特点是可以进行有效的服务整合，这其中就包含了各种开源的ORMapping组件（Hibernate、MyBatis、JPA等），每一个持久层开源组件都可能对JDBC的事物进行重新的包装，所以在Spring设计的时候充分的考虑到了这类问题，使得事务的控制必须进行更加合理的抽象与管理。在Spring之中关于事务的标准定义使用了**org.springframework.transaction. PlatformTransactionManager**

1.观察PlanformTransactionManager接口中的方法。

```
public interface PlatformTransactionManager {
	public TransactionStatus getTransaction(@Nullable TransactionDefinition definition) throws TransactionException;
	public void commit(TransactionStatus status) throws TransactionException;
	public void rollback(TransactionStatus status) throws TransactionException;
}
```
	在这个接口中针对于JDBC提供的各个事务的处理实际上是看不见的，即：通过Spring进行了有效的封装。
 
## 事务传播属性
事务的传播属性是由Spring自行提供的事务操作扩展，主要是为了更好的解决数据层与业务层之间的事物处理操作（大部分情况下事务处理都应该放在业务层中完成），对于事务的传播属性，可以直接参考**org.springframework.transaction. TransactionDefinition**接口定义即可。
<br/>在此接口里面定义有如下的几个常量:

No. | 传播属性 | 描述
---|--- | ---
1 | PROPAGATION_REQUIRED |表示一定会存在有一个事务，如果现在没有事务则一定会创建新事务
2 | PROPAGATION_SUPPORTS |如果存在有事务则支持当前事务，如果没有事务则采用非事务模式运行
3 | PROPAGATION_MANDATORY |如果已经存在事务则支持当前事务，如果没有存在的事务抛出异常
4 | PROPAGATION_REQUIRES_NEW |总是开启一个新的事务，如果已经存在有事务，则会将原事务挂起
5 | PROPAGATION_NOT_SUPPORTED |总是以非实物的方式执行，并且挂起已经存在的事务
6 | PROPAGATION_NEVER |总是以非事务的方式执行，如果存在有事务则抛出异常
7 | PROPAGATION_NESTED |如果存在有一个活动的事务，则运行在一个嵌套的事务里面，如果没有事务存在则按照PROPAGATION_REQUIRED属性来处理

不管事务如何改变，本质上的事务还是以JDBC的传统事务操作为主，那么既然以JDBC操作事务为准，下面就针对于传播属性的操作形式进行说明。
> 1.	TransactionDefinition.PROPAGATION_REQUIRED：在项目开发之中此类的传播属性使用是最多的，它的本质在于，如果说没有开始事务则开启一个新的事务，如果已经开启了事务，则使用已有事务进行处理。<br/>
> 2.	TransactionDefinition. PROPAGATION_SUPPORTS：如果业务层上分配了事务，则数据层继续使用该事务进行操作，如果没有分配事务，那么数据层采用非事务的方式运行。<br/>
> 3.	TransactionDefinition. PROPAGATION_MANDATORY：如果现在有事务则使用事务处理，如果没有事务则直接抛出异常（非法的事务状态异常）。<br/>
> 4.	TransactionDefinition. PROPAGATION_REQUIRES_NEW：表示永远都要求建立一个新的事务。<br/>
> 5.	TransactionDefinition. PROPAGATION_NOT_SUPPORTED：永远采用非事务的方式来进行执行。<br/>
> 6.	TransactionDefinition. PROPAGATION_NEVER：以非事务的方式运行，如果已经存在有事务则抛出异常。<br/>
> 7.	TransactionDefinition. PROPAGATION_NESTED：如果事务存在，则会在内部开启一个新的事务作为嵌套事务的存在，则效果与REQUIES相同。<br/>

在大部分情况下对于事务的控制往往都会在业务层中进行，而且数据层应该与业务层享受同样的数据库连接，所以往往要使用的就是REQUIRES传播属性。

## 事务隔离级别

在进行数据库事务处理操作过程之中，为了解决并发状态下的数据操作的正确性，就可以使用数据库的隔离级别（如果不是必须的情况下对于数据库的隔离级别不要操作，应该交由数据库自己来完成），隔离级别的主要作用就是为了解决数据库的脏读、幻读、不可重复读的问题。
> 1.脏读：脏读就是指当一个事务正在访问数据，并且对数据进行了修改，而这种修改还没有提交到数据库中，这时另外一个事务也访问这个数据，然后使用了这个数据。<br/>
    · 在一个人事系统里面，A人事修改了一个雇员的工资，为其增长了20%，但是在进行事务提交的过程里面，由经理读取出了这个原有的数据信息，那么这个时候该雇员的信息就属于脏数据。<br/>
> 2.幻读：指当事务不是独立执行时发生的一种现象，例如第一个事务对一个表中的数据进行了修改，这种修改涉及到表中的全部数据行。同时第二个事务也修改这个表中的数据，这种修改是向表中插入了一行新数据。那么以后就会繁盛操作第一条事务的用户发现表中还有没有修改的数据行，就好像发生了幻觉一样。这种情况是发生在表之间的，可以采用表级锁解决。<br/>
> 3.不可重复读：在一个事务内，多次读同一数据。在这个事务还没有结束时，另外一个事务也访问同一个数据。那么在第一个事务中的两次读数据之间，由于第二个事务的修改，那么第一个事务两次督导的数据可能是不一样的，这样就发生了在一个事务内两次督导的数据是不一样的，因此称为不可重复读。这种情况下是发生在数据行之间的，可以采用行锁的方式解决。


对于数据库访问的隔离级别也是在TranactionDefinition接口里面定义的如下几种模式：

No. | 隔离级别 | 描述
---|--- | ---
 1 | ISOLATION_DEFAULT | 使用数据库默认的事务隔离级别（推荐做法） 
 2 | ISOLATION_READ_UNCOMMITTED | 最低的事务隔离级别，它允许其他事务可以看到这个事务未提交的数据，这种隔离级别会产生脏读、幻读、不可重复读问题 
 3 | ISOLATION_READ_COMMITTED | 保证一个事务更改后的数据提交后才可以被另外一个事务所读取到，另外一个事务无法读取到未提交的数据，这种操作可以避免脏读问题出现，但有可能出现不可重复读或幻读 
 4 | ISOLATION_REPEATABLE_READ | 防止脏读、不可重复读，但是有可能会出现幻读，保证一个事务不能读取另外一个事务未提交的数据，同时还可以避免脏读、不可重复读
 5 | ISOLATION_REPEATABLE | 可靠的隔离级别，事务处理的时候被顺序执行，可防止脏读、幻读、不可重复读。
 
 > **关于默认隔离级别：**<br/>
 Mysql 默认:可重复读（ISOLATION_REPEATABLE_READ） <br/>
 Oracle 默认:读已提交（ISOLATION_READ_COMMITTED）


## 编程式事务控制
清楚了整个的事务架构组成之后可以通过具体的代码来进行编程式的事务控制处理操作，如果要想进行事务的控制则一定要在你的项目职责引入spring-tx依赖库。
1.[hellospring]修改pom.xml配置文件，引入依赖库。

```
<?xml version="1.0" encoding="UTF-8"?>
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
		<spring.version>5.0.6.RELEASE</spring.version>
		<servlet.version>4.0.1</servlet.version>
		<jsp.version>2.2</jsp.version>
		<jstl.version>1.2</jstl.version>
		<annotations-api.version>9.0.8</annotations-api.version>
		<quartz.version>2.3.0</quartz.version>
		<hellospring.version>0.0.1</hellospring.version>
		<mysql.version>5.0.8</mysql.version>
		<c3p0.version>0.9.5.2</c3p0.version>
	</properties>
	<dependencyManagement>
		<dependencies>
			<dependency>
				<groupId>com.mchange</groupId>
				<artifactId>c3p0</artifactId>
				<version>${c3p0.version}</version>
			</dependency>

			<dependency>
				<groupId>mysql</groupId>
				<artifactId>mysql-connector-java</artifactId>
				<version>${mysql.version}</version>
			</dependency>
			<dependency>
				<groupId>org.springframework</groupId>
				<artifactId>spring-jdbc</artifactId>
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
	</modules>
</project>
```
2.[hellospring-base]修改pom.xml配置文件。

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
	<artifactId>hellospring-base</artifactId>
	<name>mldnspring-base</name>
	<url>http://maven.apache.org</url>
	<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
	</properties>
	<dependencies>
		<dependency>
			<groupId>com.mchange</groupId>
			<artifactId>c3p0</artifactId>
		</dependency>
		<dependency>
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-jdbc</artifactId>
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
			<groupId>org.springframework</groupId>
			<artifactId>spring-tx</artifactId>
		</dependency>
		<dependency>
			<groupId>org.apache.tomcat</groupId>
			<artifactId>tomcat-annotations-api</artifactId>
		</dependency>
	</dependencies>
</project>
```

3.[hellospring-base]修改spring-base.xml配置文件，引入数据源的事务管理子类

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:p="http://www.springframework.org/schema/p"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:task="http://www.springframework.org/schema/task"
	xmlns:aop="http://www.springframework.org/schema/aop"
	xsi:schemaLocation="http://www.springframework.org/schema/task http://www.springframework.org/schema/task/spring-task-4.3.xsd
		http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.3.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd
		http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.3.xsd">  
	<context:component-scan base-package="cn.hello.hellospring"/>	<!-- 定义扫描包 -->
	<context:property-placeholder location="classpath:config/*.properties"/>
	<!-- 定义事务处理子类，该事务处理主要是针对于数据源完成的控制 -->
	<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
		<property name="dataSource" ref="dataSource"/>	<!-- 引入已经存在的DataSource -->
	</bean> 

	<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
		<property name="driverClass" value="${datasource.driverclass}"/>
		<property name="jdbcUrl" value="${datasource.jdbcurl}"/>
		<property name="user" value="${datasource.user}"/>
		<property name="password" value="${datasource.password}"/>
		<property name="maxPoolSize" value="${datasource.maxpoolsize}"/>
		<property name="initialPoolSize" value="${datasource.initialpoolsize}"/>
		<property name="minPoolSize" value="${datasource.minpoolsize}"/>
		<property name="maxIdleTime" value="${datasource.maxidletime}"/>
	</bean>
	<!-- JdbcTemplate是Spring提供的JDBC操作模版，利用此模版可以轻松实现CRUD处理 -->
	<bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
		<property name="dataSource" ref="dataSource"/>	<!-- 配置要使用的数据源 -->
	</bean>
</beans>
```
> 注：本次采用的JdbcTemplate进行的测试
相关的配置文件：

```
[hellospring-base]profiles/dev/database.properties文件
# 定义数据库连接池的驱动程序
datasource.driverclass=org.gjt.mm.mysql.Driver
# 定义数据库连接地址
datasource.jdbcurl=jdbc:mysql://localhost:3306/mldn
# 数据库用户名
datasource.user=root
# 数据库密码
datasource.password=mysqladmin
# 连接池的最大可用连接数
datasource.maxpoolsize=100
# 连接池的初始化连接数
datasource.initialpoolsize=30
# 连接池最小维持的连接数
datasource.minpoolsize=10
# 用户最大等待时间（毫秒）
datasource.maxidletime=2000
```

4.[hellospring-base]编写测试

```
package cn.hello.hellospring.test;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;
import org.springframework.transaction.PlatformTransactionManager;
import org.springframework.transaction.TransactionDefinition;
import org.springframework.transaction.TransactionStatus;
import org.springframework.transaction.support.DefaultTransactionDefinition;
@ContextConfiguration(locations= {"classpath:spring/spring-base.xml"})
@RunWith(SpringJUnit4ClassRunner.class)
public class TestTransaction {
	@Autowired	// 注入事务管理类
	private PlatformTransactionManager transactionManager ;
	@Autowired
	private JdbcTemplate jdbcTemplate ;
	@Test
	public void test() throws Exception {
		// 利用TransactionDefinition来定义事务的相关环境配置属性
		DefaultTransactionDefinition transactionDefinition = new DefaultTransactionDefinition() ;
		// 定义事务的传播属性，所有的操作都一定需要开启事务的支持
		transactionDefinition.setPropagationBehavior(TransactionDefinition.PROPAGATION_REQUIRED);
		// 使用默认的隔离级别（数据库处理）作为事务的隔离级别
		transactionDefinition.setIsolationLevel(TransactionDefinition.ISOLATION_DEFAULT);
		// 利用PlatformTransactionManager和TransactionDefinition可以获取一个事务的状态
		TransactionStatus status = this.transactionManager.getTransaction(transactionDefinition) ;
		try {
			String sql = "DELETE FROM news WHERE nid=?" ;
			long nid = 19 ;
			int len = this.jdbcTemplate.update(sql, nid) ; 
			System.out.println("更新行数：" + len);
			this.transactionManager.commit(status);
		} catch (Exception e) {
			this.transactionManager.rollback(status);
		}
	} 
}
```
整体上给我们的感觉是比较复杂的，如果所有的业务层都在这样的环境下进行处理，那么基本上程序就别写了，总是在
进行上面相关事务代码的复制。

## 注解式事务控制
除了采用这种编程式的事务处理之外，更多的情况可以使用注解的配置完成，注解配置的事务一定要有业务层存在。

1.[hellospring-base]为spring-base.xml配置文件引入tx事务命名空间，而后进行如下事务配置：

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:p="http://www.springframework.org/schema/p"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:task="http://www.springframework.org/schema/task"
	xmlns:aop="http://www.springframework.org/schema/aop"
	xmlns:tx="http://www.springframework.org/schema/tx"
	xsi:schemaLocation="http://www.springframework.org/schema/task http://www.springframework.org/schema/task/spring-task-4.3.xsd
		http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.3.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd
		http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.3.xsd
		http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-4.3.xsd">  
	<context:component-scan base-package="cn.hello.hellospring"/>	<!-- 定义扫描包 -->
	<tx:annotation-driven transaction-manager="transactionManager"/>
	
	<context:property-placeholder location="classpath:config/*.properties"/>
	<!-- 定义事务处理子类，该事务处理主要是针对于数据源完成的控制 -->
	<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
		<property name="dataSource" ref="dataSource"/>	<!-- 引入已经存在的DataSource -->
	</bean> 
	
	<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
		<property name="driverClass" value="${datasource.driverclass}"/>
		<property name="jdbcUrl" value="${datasource.jdbcurl}"/>
		<property name="user" value="${datasource.user}"/>
		<property name="password" value="${datasource.password}"/>
		<property name="maxPoolSize" value="${datasource.maxpoolsize}"/>
		<property name="initialPoolSize" value="${datasource.initialpoolsize}"/>
		<property name="minPoolSize" value="${datasource.minpoolsize}"/>
		<property name="maxIdleTime" value="${datasource.maxidletime}"/>
	</bean>
	<!-- JdbcTemplate是Spring提供的JDBC操作模版，利用此模版可以轻松实现CRUD处理 -->
	<bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
		<property name="dataSource" ref="dataSource"/>	<!-- 配置要使用的数据源 -->
	</bean>
</beans>
```
> <tx:annotation-driven transaction-manager="transactionManager"/>

2.[hellospring-base]修改NewServiceImpl业务接口子类定义，直接在业务方法上采用注解的形式进行事务控制。

```
package cn.hello.hellospring.service.impl;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Isolation;
import org.springframework.transaction.annotation.Propagation;
import org.springframework.transaction.annotation.Transactional;
import cn.mldn.mldnspring.dao.INewsDAO;
import cn.mldn.mldnspring.service.INewsService;
@Service
public class NewsServiceImpl implements INewsService {
	@Autowired
	private INewsDAO newsDAO ;
	@Transactional(propagation=Propagation.REQUIRED,isolation=Isolation.DEFAULT)
	@Override
	public boolean remove(long nid) {
		return this.newsDAO.doRemove(nid) ;
	}
}
```

INewService业务接口定义

```
package cn.hello.hellospring.service;
import org.springframework.transaction.annotation.Isolation;
import org.springframework.transaction.annotation.Propagation;
import org.springframework.transaction.annotation.Transactional;
public interface INewsService {
	public boolean remove(long nid) ;
}
```
INewDAO数据层接口定义：

```
package cn.hello.hellospring.dao;
public interface INewsDAO {
	public boolean doRemove(Long nid) ;
}
```


NewDAOImpl数据层接口子类定义：

```
package cn.hello.hellospring.dao.impl;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.stereotype.Service;
import cn.mldn.mldnspring.dao.INewsDAO;
@Repository
public class NewsDAOImpl implements INewsDAO {
	@Autowired
	private JdbcTemplate jdbcTemplate ;
	@Override
	public boolean doRemove(Long nid) {
		String sql = "DELETE FROM news WHERE nid=?" ;
		return this.jdbcTemplate.update(sql, nid) > 0 ; 
	}
}
```

News类：

```
package cn.hello.hellospring.vo;
import java.io.Serializable;
import java.util.Date;
@SuppressWarnings("serial")
public class News implements Serializable {
	private Long nid ;
	private String title; 
	private String note ; 
	private Date pubdate ; 	
	private Double price ;
	private Integer readcount ;
	public Long getNid() {
		return nid;
	}
	public void setNid(Long nid) {
		this.nid = nid;
	}
	public String getTitle() {
		return title;
	}
	public void setTitle(String title) {
		this.title = title;
	}
	public String getNote() {
		return note;
	}
	public void setNote(String note) {
		this.note = note;
	}
	public Date getPubdate() {
		return pubdate;
	}
	public void setPubdate(Date pubdate) {
		this.pubdate = pubdate;
	}
	public Double getPrice() {
		return price;
	}
	public void setPrice(Double price) {
		this.price = price;
	}
	public Integer getReadcount() {
		return readcount;
	}
	public void setReadcount(Integer readcount) {
		this.readcount = readcount;
	}
	
}
```


3.[hellospring-base]测试类上不再需要编写事务控制了。

```
package cn.hello.hellospring.test;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;
import cn.mldn.mldnspring.service.INewsService;
@ContextConfiguration(locations= {"classpath:spring/spring-base.xml"})
@RunWith(SpringJUnit4ClassRunner.class)
public class TestTransaction {
	@Autowired
	private INewsService newsService ;
	@Test
	public void test() throws Exception {
		System.out.println(this.newsService.remove(1));
	}  
}
```

## 声明式事务控制
不管是使用的是编程式的事务还是注解式的事务，都会发现在每一个业务方法的声明或者实现处都需要编写大量的内容，这样的操作流程是非常繁琐的，所以最简单的形式是以方法名称作为事务的切入点来进行控制，那么这就有了Spring中最为重要的声明式事务的概念，只需要做一个简单的配置，而后在需要的地方引入此配置就可以针对于特点的方法名称进行事务控制。
1.[hellospring-base]按照传统的方式定义业务接口和子类

```
package cn.hello.hellospring.service.impl;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import cn.mldn.mldnspring.dao.INewsDAO;
import cn.mldn.mldnspring.service.INewsService;
@Service
public class NewsServiceImpl implements INewsService {
	@Autowired
	private INewsDAO newsDAO ;
	@Override
	public boolean remove(long nid) {
		return this.newsDAO.doRemove(nid) ;
	}
}
```
    业务层的方法命名一般以addXXX、editXXX、removeXXX等等进行命名。
    
2.[hellospring-base]修改spring-base.xml配置文件，追加事务定义

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:p="http://www.springframework.org/schema/p"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:task="http://www.springframework.org/schema/task"
	xmlns:aop="http://www.springframework.org/schema/aop"
	xmlns:tx="http://www.springframework.org/schema/tx"
	xsi:schemaLocation="http://www.springframework.org/schema/task http://www.springframework.org/schema/task/spring-task-4.3.xsd
		http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.3.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd
		http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.3.xsd
		http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-4.3.xsd">  
	<context:component-scan base-package="cn.hello.hellospring"/>	<!-- 定义扫描包 -->
	<tx:annotation-driven transaction-manager="transactionManager"/>
	<tx:advice id="txAdvice" transaction-manager="transactionManager">
		<tx:attributes>	<!-- 定义事务的相关的配置属性 -->
			<tx:method name="add*" propagation="REQUIRED"/>		
			<tx:method name="edit*" propagation="REQUIRED"/>		
			<tx:method name="remove*" propagation="REQUIRED"/>		
			<tx:method name="list*" propagation="REQUIRED" read-only="true"/>		
			<tx:method name="get*" propagation="REQUIRED" read-only="true"/>		
		</tx:attributes>
	</tx:advice>
	<!-- 随后需要为项目配置有一个切面，而切面就需要通过AOP技术指派 -->
	<aop:config>
		<aop:pointcut expression="execution(public * cn.hello..service..*.*(..))" id="servicePointcut"/>
		<aop:advisor advice-ref="txAdvice" pointcut-ref="servicePointcut"/>
	</aop:config>
	<context:property-placeholder location="classpath:config/*.properties"/>
	<!-- 定义事务处理子类，该事务处理主要是针对于数据源完成的控制 -->
	<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
		<property name="dataSource" ref="dataSource"/>	<!-- 引入已经存在的DataSource -->
	</bean> 
	
	<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
		<property name="driverClass" value="${datasource.driverclass}"/>
		<property name="jdbcUrl" value="${datasource.jdbcurl}"/>
		<property name="user" value="${datasource.user}"/>
		<property name="password" value="${datasource.password}"/>
		<property name="maxPoolSize" value="${datasource.maxpoolsize}"/>
		<property name="initialPoolSize" value="${datasource.initialpoolsize}"/>
		<property name="minPoolSize" value="${datasource.minpoolsize}"/>
		<property name="maxIdleTime" value="${datasource.maxidletime}"/>
	</bean>
	<!-- JdbcTemplate是Spring提供的JDBC操作模版，利用此模版可以轻松实现CRUD处理 -->
	<bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
		<property name="dataSource" ref="dataSource"/>	<!-- 配置要使用的数据源 -->
	</bean>
</beans>
```

增加如下内容：
```
<tx:advice id="txAdvice" transaction-manager="transactionManager">
		<tx:attributes>	<!-- 定义事务的相关的配置属性 -->
			<tx:method name="add*" propagation="REQUIRED"/>		
			<tx:method name="edit*" propagation="REQUIRED"/>		
			<tx:method name="remove*" propagation="REQUIRED"/>		
			<tx:method name="list*" propagation="REQUIRED" read-only="true"/>		
			<tx:method name="get*" propagation="REQUIRED" read-only="true"/>		
		</tx:attributes>
	</tx:advice>
	<!-- 随后需要为项目配置有一个切面，而切面就需要通过AOP技术指派 -->
	<aop:config>
		<aop:pointcut expression="execution(public * cn.hello..service..*.*(..))" id="servicePointcut"/>
		<aop:advisor advice-ref="txAdvice" pointcut-ref="servicePointcut"/>
	</aop:config>
```

此时结合AOP切面的思想，事务的控制已经不再需要明确的程序开发和注解了，直接采用了配置文件的模式进行声明式的定义，整体的事务控制会变得容易。




