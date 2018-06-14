**一、服务提供方**
 
1. Dubbo配置文件中增加Dubbo注解扫描
 

```
<!-- 开启dubbo注解支持 -->
<!-- 扫描注解包路径，多个包用逗号分隔，不填pacakge表示扫描当前ApplicationContext中所有的类 -->
<dubbo:annotation package="cn.mydubbo" />
```

 
2.Service实现类上添加Dubbo Service注解
 

```
import com.alibaba.dubbo.config.annotation.Service;

@Service
public class DubboServiceImpl implements DubboService {
}
```

 
 
**二、服务消费方**
 
1. Dubbo配置文件中增加Dubbo注解扫描（同服务提供方）
 

```
<!-- 开启dubbo注解支持 -->
<!-- 扫描注解包路径，多个包用逗号分隔，不填pacakge表示扫描当前ApplicationContext中所有的类 -->
<dubbo:annotation package="cn.mydubbo" />
```

 
2.Spring MVC配置中引入dubbo配置，解决dubbo注解不兼容问题（很关键，不然控制器中引入服务会报空指针）
 

```
<!-- 引入dubbo配置，解决dubbo注解不兼容问题 -->
<import resource="classpath:spring-dubbo.xml"/>
```

或者在springmvc配置中继续配置dubbo注解
```
<dubbo:annotation package="cn.mydubbo"/>
```


 
3.控制器中引入Dubbo服务后，就可以使用了
 

```
@Reference
private DubboService dubboService;
```
