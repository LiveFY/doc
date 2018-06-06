1、【hellospring】修改pom.xml文件，进行druid的依赖库配置。

```
<druid.version>1.1.10</druid.version>
<dependency>
	<groupId>com.alibaba</groupId>
	<artifactId>druid</artifactId>
	<version>${druid.version}</version>
</dependency>
```

2、【hellospring-ssm】在子模块中进行相关依赖引入。

```
<dependency>
    <groupId>com.alibaba</groupId>
	<artifactId>druid</artifactId>
</dependency>
```

3、【hellospring-ssm】所有的数据库连接都应该在资源文件中定义，修改database.properties配置文件

```
# 定义数据库驱动程序名称
db.druid.driverClassName=org.gjt.mm.mysql.Driver
# 数据库连接地址
db.druid.url=jdbc:mysql://localhost:3306/hello
# 数据库连接用户名
db.druid.username=root
# 数据库连接密码
db.druid.password=mysqladmin
# 数据库最大连接数
db.druid.maxActive=1
# 数据库最小维持连接数
db.druid.minIdle=1
# 数据库初始化连接
db.druid.initialSize=1
# 数据库连接池最大等待时间
db.druid.maxWait=30000
# 配置间隔多久才进行一次检测，检测需要关闭的空闲连接，单位是毫秒
db.druid.timeBetweenEvictionRunsMillis=60000
# 配置一个连接在池中最小生存的时间，单位是毫秒
db.druid.minEvictableIdleTimeMillis=300000
# 检测查询处理
db.druid.validationQuery=SELECT 'x'
# 申请连接的时候检测，如果空闲时间大于timeBetweenEvictionRunsMillis，执行validationQuery检测连接是否有效。
db.druid.testWhileIdle=true
# 申请连接时执行validationQuery检测连接是否有效，做了这个配置会降低性能
db.druid.testOnBorrow=false
# 归还连接时执行validationQuery检测连接是否有效，做了这个配置会降低性能
db.druid.testOnReturn=false
# 是否缓存preparedStatement，也就是PSCache。PSCache对支持游标的数据库性能提升巨大，比如说oracle。在mysql下建议关闭
db.druid.poolPreparedStatements=false
# 要启用PSCache，必须配置大于0，当大于0时，poolPreparedStatements自动触发修改为true。在Druid中，不会存在Oracle下PSCache占用内存过多的问题，可以把这个数值配置大一些，比如说100 
db.druid.maxPoolPreparedStatementPerConnectionSize=20
# 配置监控统计拦截的filters，去掉后监控界面sql无法统计，'wall'用于防火墙
db.druid.filters=stat
```


4、【hellospring-ssm】修改spring-datasource.xml文件

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
	<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource" init-method="init">
		<property name="driverClassName" value="${db.druid.driverClassName}"/>	<!-- 定义数据库驱动程序 -->
		<property name="url" value="${db.druid.url}"/>	<!-- 数据库连接地址 --> 
		<property name="username" value="${db.druid.username}"/>		<!-- 数据库连接用户名 -->
		<property name="password" value="${db.druid.password}"/>	<!-- 数据库连接密码 -->
		<property name="maxActive" value="${db.druid.maxActive}"/>	<!-- 最大连接数 -->
		<property name="minIdle" value="${db.druid.minIdle}"/>	<!-- 最小连接池 -->
		<property name="initialSize" value="${db.druid.initialSize}"/>	<!-- 初始化连接大小 -->
		<property name="maxWait" value="${db.druid.maxWait}"/>	<!-- 最大等待时间 -->
		<property name="timeBetweenEvictionRunsMillis" value="${db.druid.timeBetweenEvictionRunsMillis}" />  <!-- 配置间隔多久才进行一次检测，检测需要关闭的空闲连接，单位是毫秒 --> 
		<property name="minEvictableIdleTimeMillis" value="${db.druid.minEvictableIdleTimeMillis}" /> <!-- 配置一个连接在池中最小生存的时间，单位是毫秒 --> 
		<property name="validationQuery" value="${db.druid.validationQuery}" />  <!-- 验证SQL -->
		<!-- 建议配置为true，不影响性能，并且保证安全性。 -->
		<!-- 申请连接的时候检测，如果空闲时间大于timeBetweenEvictionRunsMillis，执行validationQuery检测连接是否有效。 -->
		<property name="testWhileIdle" value="${db.druid.testWhileIdle}" />
		<property name="testOnBorrow" value="${db.druid.testOnBorrow}" />	<!-- 申请连接时执行validationQuery检测连接是否有效，做了这个配置会降低性能 -->
		<property name="testOnReturn" value="${db.druid.testOnReturn}" /> 	<!-- 归还连接时执行validationQuery检测连接是否有效，做了这个配置会降低性能 -->
		<!-- 是否缓存preparedStatement，也就是PSCache。PSCache对支持游标的数据库性能提升巨大，比如说oracle。在mysql下建议关闭。 -->
		<property name="poolPreparedStatements" value="${db.druid.poolPreparedStatements}" />
		<!-- 要启用PSCache，必须配置大于0，当大于0时，poolPreparedStatements自动触发修改为true。在Druid中，不会存在Oracle下PSCache占用内存过多的问题，可以把这个数值配置大一些，比如说100 -->
		<property name="maxPoolPreparedStatementPerConnectionSize" value="${db.druid.maxPoolPreparedStatementPerConnectionSize}" /> 
		<property name="filters" value="${db.druid.filters}" /> <!-- 配置监控统计拦截的filters，去掉后监控界面sql无法统计，'wall'用于防火墙 -->
	</bean>

</beans>
```



5、【hellospring-ssm】druid本身支持有使用的监控界面，如果要想启动则需要修改web.xml文件进行处理。

```
<filter>
		<filter-name>DruidWebStatFilter</filter-name>
		<filter-class>com.alibaba.druid.support.http.WebStatFilter</filter-class>
		<init-param>
			<param-name>exclusions</param-name>
			<param-value>*.js,*.gif,*.jpg,*.png,*.css,*.ico,/druid/*</param-value>
		</init-param>
	</filter>
	<filter-mapping>
		<filter-name>DruidWebStatFilter</filter-name>
		<url-pattern>/*</url-pattern>
	</filter-mapping>
	<servlet>
		<servlet-name>DruidStatView</servlet-name>
		<servlet-class>com.alibaba.druid.support.http.StatViewServlet</servlet-class>
	</servlet>
	<servlet-mapping>
		<servlet-name>DruidStatView</servlet-name>
		<url-pattern>/druid/*</url-pattern>
	</servlet-mapping>
```


之后就可以在项目中直接采用Druid数据源进行程序的管理，同时可以进行状态监控。监控地址:
> http://localhost/hellospring-ssm/druid

