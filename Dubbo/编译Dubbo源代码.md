

1. 从github下载dubbo源代码
   
```
git https://github.com/apache/incubator-dubbo.git
```
或者直接从releases下下载对应的压缩文件包。


2.如果要想进行Dubbo源代码的编译还需要获取一个阿里的父pom.xml文件支持。


3.由于更快的编译，可以进入maven的安装位置调整maven的内存大小。

```
我的位置：E:\Development software\apache-maven-3.3.3\bin\mvn
添加内容：MAVEN_OPTS="$MAVEN_OPTS -Xms6g -Xmx6g"
```

4.解压opensesame-master文件，并在cmd下进入此目录

```
    cd opensesame-master
	mvn clean install package
```

5.解压缩dubbo源代码，并在cmd下进入此目录，修改pom.xml配置文件
    · 修改测试插件（**maven-surefire-plugin**）:
    
要修改的位置：
```
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-surefire-plugin</artifactId>
                <version>2.20.1</version>
                <configuration>
                		<skipTests>true</skipTests>
                    <useSystemClassLoader>true</useSystemClassLoader>
                    <forkMode>once</forkMode>
                    <argLine>${argline}</argLine>
```

    
        |- <configuration>下添加如下内容：
        
```
    <skipTests>true</skipTests>
```

        |- 修改插件版本：

```
<version>2.20.1</version>
```
    
    · 修改编译插件(maven-compiler-plugin)：在<configuration>中修改：
要修改的位置：
```
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.7.0</version>
                <configuration>
                		<executable>E:\Development software\Java\jdk1.8.0_161\bin\javac</executable>
                    <fork>true</fork>
                    <source>${java_source_version}</source>
                    <target>${java_target_version}</target>
                    <encoding>${file_encoding}</encoding>
                </configuration>
            </plugin>
```


```
修改内容为：
<executable>E:\Development software\Java\jdk1.8.0_161\bin\javac</executable>
```

            

6.然后在dubbo源代码目录下，进行编译

```
mvn clean install package -T 4
```

“-T 4”：表示将使用电脑的4核线程同时进行源代码的编译。


7.编译成功之后就可以看到如下内容

![image](http://wx4.sinaimg.cn/mw690/0060lm7Tly1fs9ja1pmv4j30ka098q33.jpg)




之后进入当前目录中就可以获取到
    

· dubbo管理界面的**dubbo-admin-2.5.10.war**包
    
```
G:\dubbo\incubator-dubbo-dubbo-2.5.10\dubbo-admin\target\dubbo-admin-2.5.10.war
```


· 监控**dubbo-monitor-simple-2.5.10-assembly.tar.gz**相关的包
    
    
```
G:\dubbo\incubator-dubbo-dubbo-2.5.10\dubbo-simple\dubbo-monitor-simple\target\dubbo-monitor-simple-2.5.10-assembly.tar.gz
```



