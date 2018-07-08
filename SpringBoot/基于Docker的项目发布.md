1、进入docker服务的主机
```
vim /lib/systemd/system/docker.service
```

修改配置文件：
```
ExecStart=/usr/bin/dockerd -H fd:// -H tcp://0.0.0.0:2375
```

> “0.0.0.0”:表示绑定任意的主机IP


2、重新加载Docker进行中的配置

```
systemctl daemon-reload
```

3、重新启动Docker服务进行

```
service docker restart
```


4、另外找一台主机测试一下：curl 192.168.133.155:2375/version

```
{"Version":"1.13.1","ApiVersion":"1.26","MinAPIVersion":"1.12","GitCommit":"092cba3","GoVersion":"go1.6.2","Os":"linux","Arch":"amd64","KernelVersion":"4.4.0-124-generic","BuildTime":"2017-11-02T20:40:23.484070968+00:00"}
```
> 看到如上所示的内容，说明docker对外的接口已经打开了。


5、在本地windows系统上修改maven配置文件(setting.xml)

```
    <server>
      <id>docker-hub</id>
      <username>dockerhub用户名</username>
      <password>密码</password>
      <configuration>
        <email>邮箱</email>
      </configuration>
   </server>
```

6、在项目的pom.xml文件中添加如下内容

```
    <build>
		<finalName>micro-boot</finalName>
		<plugins>
			<plugin>					<!-- 该插件的主要功能是进行项目的打包发布处理 -->
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
				<configuration>			<!-- 设置程序执行的主类 -->
					<mainClass>cn.origin.StartBootMain</mainClass>
				</configuration>
				<executions>
					<execution>
						<goals>
							<goal>repackage</goal>
						</goals>
					</execution>
				</executions>
			</plugin>
			
			<plugin>
				<groupId>com.spotify</groupId>
				<artifactId>docker-maven-plugin</artifactId>
				<version>1.0.0</version> 
				
				<configuration>
					<serverId>docker-hub</serverId> <!--此处id要与maven配置文件的id对应-->
					<dockerHost>http://192.168.133.155:2375</dockerHost>
					<imageName>origin/micro-boot-8080</imageName>	<!-- 镜像名称 -->
					<imageTags>								<!-- 镜像标签 -->
                        	<imageTag>dev</imageTag>				<!--可以指定多个标签-->
                        	<imageTag>latest</imageTag>			<!--可以指定多个标签-->
                    	</imageTags>
					<baseImage>java</baseImage>				<!-- 基础镜像 -->
					<forceTags>true</forceTags>				<!--覆盖已存在的镜像-->
					<entryPoint>
						["java", "-jar", "/${project.build.finalName}.jar"]
					</entryPoint>							<!-- 镜像启动命令 -->
					<resources>
						<resource>							<!-- 输出资源 -->
							<targetPath>/</targetPath>
							<directory>${project.build.directory}</directory>
							<include>${project.build.finalName}.jar</include>
						</resource>
					</resources>
				</configuration>
			</plugin>
			
		</plugins>
	</build>
```

 7、进行程序的打包编译，并将打包后的镜像推送到dockerhub之中；

```
clean package docker:build -DpushImage
```


8、在docker主机上查看一下镜像

```
docker images
```


9、执行镜像处理

```
docker run -p 80:8080 -itd origin/micro-boot-8080:dev
```

> 此时可以通过htto://192.168.133.155进行访问，则说明服务部署成功
