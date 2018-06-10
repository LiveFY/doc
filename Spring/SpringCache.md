
**系统不同层级对应的缓存技术** ：<br/>


![缓存技术](http://wx3.sinaimg.cn/mw690/0060lm7Tly1fs605n4ro3j30to09w7c2.jpg)

# Spring缓存实现
	在整个Spring框架之中，所有的操作都是基于配置实现的，而对于SpringCache也同样需要，SpringCache配置主要是通过配置文件中的cache命名空间来完成的。
	
1.【hellomybatis-ssm】在spring下创建spring-cache.xml配置文件。

2.【hellomybatis-ssm】修改spring-cache.xml配置文件，追加缓存预处理。

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:p="http://www.springframework.org/schema/p"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:task="http://www.springframework.org/schema/task"
	xmlns:aop="http://www.springframework.org/schema/aop"
	xmlns:tx="http://www.springframework.org/schema/tx"
	xmlns:cache="http://www.springframework.org/schema/cache"
	xsi:schemaLocation="http://www.springframework.org/schema/task http://www.springframework.org/schema/task/spring-task-4.3.xsd
		http://www.springframework.org/schema/cache http://www.springframework.org/schema/cache/spring-cache-4.3.xsd
		http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.3.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd
		http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.3.xsd
		http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-4.3.xsd">  
	<cache:annotation-driven cache-manager="cacheManager"/>
	<bean id="cacheManager" class="org.springframework.cache.support.SimpleCacheManager">
		<property name="caches">
			<set>
				<bean id="emp" 
					class="org.springframework.cache.concurrent.ConcurrentMapCacheFactoryBean"/>
			</set>
		</property>
	</bean>
</beans>
```



3.【hellomybatis-ssm】修改IempService接口。

```
public interface IEmpService {
	@Cacheable(cacheNames = "emp")
	public Emp preEdit(long id) ;
}
```


4.【hellomybatis-ssm】编写测试类


```
@ContextConfiguration(locations= {"classpath:spring-test.xml"})
@RunWith(SpringJUnit4ClassRunner.class)
public class TestEmpCache {
	@Autowired
	private IEmpService empService ;
	@Test
	public void testPreEdit() {
		Emp voA = this.empService.preEdit(111L) ;
		System.out.println(voA);
		System.out.println("********************************");
		Emp voB = this.empService.preEdit(111L) ;
		System.out.println(voB);
	}
}
```
>  测试结果：
```
DEBUG [cn.hello.ssm.dao.IEmpDAO.findById] - ==>  Preparing: SELECT empno,name,job,salary,photo FROM emp where empno=? ; 
DEBUG [cn.hello.ssm.dao.IEmpDAO.findById] - ==> Parameters: 111(Long)
TRACE [cn.hello.ssm.dao.IEmpDAO.findById] - <==    Columns: empno, name, job, salary, photo
TRACE [cn.hello.ssm.dao.IEmpDAO.findById] - <==        Row: 111, 2的风格大哥大哥大, 分3435345611145454333, 1111, c0f3ee4a-c696-4030-8d64-d0952fe5549f.jpeg
DEBUG [cn.hello.ssm.dao.IEmpDAO.findById] - <==      Total: 1
Emp [empno=111, name=2的风格大哥大哥大, job=分3435345611145454333, salary=1111.0, photo=c0f3ee4a-c696-4030-8d64-d0952fe5549f.jpeg]
********************************
Emp [empno=111, name=2的风格大哥大哥大, job=分3435345611145454333, salary=1111.0, photo=c0f3ee4a-c696-4030-8d64-d0952fe5549f.jpeg]
```



##  Cacheable注解
@Cacheable注解是整个缓存的实现依据所在，而且在进行数据查询的时候，有可能你设置到业务层之中的查询字段非常的多，例如：现在可能需要根据雇员编号和名称进行数据的查询，那么在这样的情况下就可以自己来指派一个缓冲的key。因为缓冲中的所有内容都是根据key=value的形式获取的。
	
1.【hellomybatis-ssm】增加一个新的方法，根据编号和名字查询内容。

● 在IempDAO接口里面扩充新的查询方法：


```
public interface IEmpDAO extends IBaseDAO<Long, Emp> {
	public Emp findByIdAndName(Map<String,Object> map) ;
}
```

●	在Emp.xml配置文件里面进行内容定义：

```
<select id="findByIdAndName" resultType="Emp" parameterType="map">
		<include refid="selectBase"/>
		where empno=#{empno} AND name=#{name} ;
	</select>
```

●	追加IEmpService接口中的方法：


```
public interface IEmpService {
	@Cacheable(cacheNames = "emp", key = "#eid")
	public Emp getEmp(long eid, String ena);
}
```


```
@Service
public class EmpServiceImpl extends AbstractService implements IEmpService {
	@Autowired
	private IEmpDAO empDAO ;
	@Override
	public Emp getEmp(long eid, String ena) {
		Map<String,Object> map = new HashMap<String,Object>() ;
		map.put("empno", eid) ;
		map.put("name", ena) ;
		return this.empDAO.findByIdAndName(map);
	}
}
```


•   编写测试程序：


```
@ContextConfiguration(locations= {"classpath:spring-test.xml"})
@RunWith(SpringJUnit4ClassRunner.class)
public class TestEmpCache {
	@Autowired
	private IEmpService empService ;
	@Test
	public void testGet() {
		Emp voA = this.empService.getEmp(111L,"小小") ;	// id作为缓存
		System.out.println(voA);
		System.out.println("********************************");
		Emp voB = this.empService.getEmp(111L,"小小") ;	// 根据id查询
		System.out.println(voB);
		System.out.println("********************************");
		Emp voC = this.empService.preEdit(111L) ;	// 根据id查询
		System.out.println(voC);
	}
}
```

> 测试结果：

```
DEBUG [cn.hello.ssm.dao.IEmpDAO.findByIdAndName] - ==>  Preparing: SELECT empno,name,job,salary,photo FROM emp where empno=? AND name=? ; 
DEBUG [cn.hello.ssm.dao.IEmpDAO.findByIdAndName] - ==> Parameters: 111(Long), 小小(String)
TRACE [cn.hello.ssm.dao.IEmpDAO.findByIdAndName] - <==    Columns: empno, name, job, salary, photo
TRACE [cn.hello.ssm.dao.IEmpDAO.findByIdAndName] - <==        Row: 111, 小小, 分3435345611145454333, 1111, c0f3ee4a-c696-4030-8d64-d0952fe5549f.jpeg
DEBUG [cn.hello.ssm.dao.IEmpDAO.findByIdAndName] - <==      Total: 1
Emp [empno=111, name=小小, job=分3435345611145454333, salary=1111.0, photo=c0f3ee4a-c696-4030-8d64-d0952fe5549f.jpeg]
********************************
Emp [empno=111, name=小小, job=分3435345611145454333, salary=1111.0, photo=c0f3ee4a-c696-4030-8d64-d0952fe5549f.jpeg]
********************************
Emp [empno=111, name=小小, job=分3435345611145454333, salary=1111.0, photo=c0f3ee4a-c696-4030-8d64-d0952fe5549f.jpeg]
```

> 注意：如果现在传递的是一个VO类，则采用的缓存key格式为“#vo对象名称.属性”。


2.【hellomybatis-ssm】在进行缓存处理的时候有可能出现多个线程并发访问的处理，那么这样的操作就必须思考一下缓存数据的同步问题，因为有可能造成多个相同对象被同时缓存问题。

```
@Cacheable(cacheNames = "emp", key = "#eid" ,sync = true)
public Emp getEmp(long eid, String ena);
```


3.【hellomybatis-ssm】在进行缓存配置的时候也可以设置一些过滤条件：

```
@Cacheable(cacheNames = "emp", key = "#eid" ,sync = true,condition = "#eid<200")
public Emp getEmp(long eid, String ena);
```

4.【hellomybatis-ssm】在进行数据查询的时候对于返回的数据也有可能出现不缓存的情况（例如：返回结果为null）

```
@Cacheable(cacheNames = "emp", key = "#eid" ,sync = true,condition = "#eid<200",unless="#resule==null")
public Emp getEmp(long eid, String ena);
```

> 此时当返回结果为null的时候将不进行数据的缓存处理。


## CachePut注解（缓存更新）
在进行数据缓存的时候一般情况下都会使用只读缓存，也就是说缓存中的内容可能和真实的数据有差异，但是在SpringCache里面充分的考虑到了这种需求，所以对于缓存数据的更新也是有所支持的。

**范例：解决数据的更新问题。**


```
public interface IEmpService {
	@CachePut(cacheNames = "emp",key="#vo.empno",unless="#resule==null")
	public Emp edit2(Emp vo);
}
```


```
@Service
public class EmpServiceImpl extends AbstractService implements IEmpService {
	@Autowired
	private IEmpDAO empDAO ;
	@Override
	public Emp edit2(Emp vo) {
		if (this.empDAO.doEdit(vo)) {
			return vo;  //此处仅仅只是作为演示之用
		}
		return null;
	}
}
```


```
<update id="doEdit" parameterType="Emp">
   UPDATE emp 
   <set>
      <if test="name != null">
         name=#{name} ,
      </if>
      <if test="job != null">
         job=#{job} ,
      </if>
      <if test="salary != null">
         salary=#{salary},
      </if>
      <if test="photo != null">
         photo=#{photo},
      </if>
   </set>
   <where>
      empno=#{empno} 
   </where>
</update>
```


•   编写测试程序：

```
@ContextConfiguration(locations= {"classpath:spring-test.xml"})
@RunWith(SpringJUnit4ClassRunner.class)
public class TestEmpCache {
	@Autowired
	private IEmpService empService ;
	@Test
	public void testEmpUpdate() {
		Emp voA = this.empService.preEdit(111L) ;	// 数据查询得到缓存
		System.out.println("*** 1、【数据查询】" + voA);
		Emp newEmp = new Emp() ;
		newEmp.setEmpno(111L);
		newEmp.setName("大大");
		newEmp.setJob("打工的");
		newEmp.setSalary(9000.00);
		System.out.println("*** 2、【数据更新】" + this.empService.edit2(newEmp));
		Emp voB = this.empService.preEdit(111L) ;
		System.out.println("*** 3、【数据查询】" + voB);
	}
}
```


> 测试结果：

```
DEBUG [cn.hello.ssm.dao.IEmpDAO.findById] - ==>  Preparing: SELECT empno,name,job,salary,photo FROM emp where empno=? ; 
DEBUG [cn.hello.ssm.dao.IEmpDAO.findById] - ==> Parameters: 111(Long)
TRACE [cn.hello.ssm.dao.IEmpDAO.findById] - <==    Columns: empno, name, job, salary, photo
TRACE [cn.hello.ssm.dao.IEmpDAO.findById] - <==        Row: 111, 小小, 分3435345611145454333, 1111, c0f3ee4a-c696-4030-8d64-d0952fe5549f.jpeg
DEBUG [cn.hello.ssm.dao.IEmpDAO.findById] - <==      Total: 1
*** 1、【数据查询】Emp [empno=111, name=小小, job=分3435345611145454333, salary=1111.0, photo=c0f3ee4a-c696-4030-8d64-d0952fe5549f.jpeg]
DEBUG [cn.hello.ssm.dao.IEmpDAO.doEdit] - ==>  Preparing: UPDATE emp SET name=? , job=? , salary=? WHERE empno=? 
DEBUG [cn.hello.ssm.dao.IEmpDAO.doEdit] - ==> Parameters: 大大(String), 打工的(String), 9000.0(Double), 111(Long)
DEBUG [cn.hello.ssm.dao.IEmpDAO.doEdit] - <==    Updates: 1
*** 2、【数据更新】Emp [empno=111, name=大大, job=打工的, salary=9000.0, photo=null]
*** 3、【数据查询】Emp [empno=111, name=小小, job=分3435345611145454333, salary=1111.0, photo=c0f3ee4a-c696-4030-8d64-d0952fe5549f.jpeg]
```

	
此时可能还无法看出发生了什么，那么通过下面的方式就可以发现真相了。只需将原先的@Cacheput注解改变为@Cacheable注解。

```
@Cacheable(cacheNames="emp",key="#vo.empno")
public Emp edit2(Emp vo);
```


> 测试结果：
```
DEBUG [cn.hello.ssm.dao.IEmpDAO.findById] - ==>  Preparing: SELECT empno,name,job,salary,photo FROM emp where empno=? ; 
DEBUG [cn.hello.ssm.dao.IEmpDAO.findById] - ==> Parameters: 111(Long)
TRACE [cn.hello.ssm.dao.IEmpDAO.findById] - <==    Columns: empno, name, job, salary, photo
TRACE [cn.hello.ssm.dao.IEmpDAO.findById] - <==        Row: 111, 大大, 打工的, 9000, c0f3ee4a-c696-4030-8d64-d0952fe5549f.jpeg
DEBUG [cn.hello.ssm.dao.IEmpDAO.findById] - <==      Total: 1
*** 1、【数据查询】Emp [empno=111, name=大大, job=打工的, salary=9000.0, photo=c0f3ee4a-c696-4030-8d64-d0952fe5549f.jpeg]
*** 2、【数据更新】Emp [empno=111, name=大大, job=打工的, salary=9000.0, photo=c0f3ee4a-c696-4030-8d64-d0952fe5549f.jpeg]
*** 3、【数据查询】Emp [empno=111, name=大大, job=打工的, salary=9000.0, photo=c0f3ee4a-c696-4030-8d64-d0952fe5549f.jpeg]
```

	通过上面的日志可以发现，如果只是使用@Cacheable注解的话，那么出来的结果还是原先缓存下来的内容。



## @CacheEvict注解（缓存清除）

	当数据表中的数据已经被删除之后很明显也希望缓存中的数据也被删除，但是在默认的情况下这样的操作是不会出现的。
1.【hellomybatis-ssm】修改业务方法，实现删除处理。


```
public interface IEmpService {
	@CacheEvict(cacheNames="emp",key="#ids[0]")
	public boolean delete(Set<Long> ids) ;
}
```

2.【hellomybatis-ssm】进行测试：


```
@ContextConfiguration(locations= {"classpath:spring-test.xml"})
@RunWith(SpringJUnit4ClassRunner.class)
public class TestEmpCache {
	@Autowired
	private IEmpService empService ;
	@Test
	public void testEmpDelete() {
		long eid = 11111L ;
		Emp voA = this.empService.preEdit(eid) ;	// 数据查询得到缓存
		System.out.println("*** 1、【数据查询】" + voA);
		Set<Long> all = new HashSet<Long>() ;
		all.add(eid) ;
		System.out.println("*** 2、【数据更新】" + this.empService.delete(all));
		Emp voB = this.empService.preEdit(eid) ;
		System.out.println("*** 3、【数据查询】" + voB);
	}
}
```
> 测试结果：

```
DEBUG [cn.hello.ssm.dao.IEmpDAO.findById] - ==>  Preparing: SELECT empno,name,job,salary,photo FROM emp where empno=? ; 
DEBUG [cn.hello.ssm.dao.IEmpDAO.findById] - ==> Parameters: 11111(Long)
TRACE [cn.hello.ssm.dao.IEmpDAO.findById] - <==    Columns: empno, name, job, salary, photo
TRACE [cn.hello.ssm.dao.IEmpDAO.findById] - <==        Row: 11111, 111, 111, 1111, d34a1fc7-b854-4a49-86e5-7c3c1826446c.png
DEBUG [cn.hello.ssm.dao.IEmpDAO.findById] - <==      Total: 1
*** 1、【数据查询】Emp [empno=11111, name=111, job=111, salary=1111.0, photo=d34a1fc7-b854-4a49-86e5-7c3c1826446c.png]
DEBUG [cn.hello.ssm.dao.IEmpDAO.doRemove] - ==>  Preparing: DELETE FROM emp WHERE empno IN ( ? ) 
DEBUG [cn.hello.ssm.dao.IEmpDAO.doRemove] - ==> Parameters: 11111(Long)
DEBUG [cn.hello.ssm.dao.IEmpDAO.doRemove] - <==    Updates: 1
*** 2、【数据更新】true
DEBUG [cn.hello.ssm.dao.IEmpDAO.findById] - ==>  Preparing: SELECT empno,name,job,salary,photo FROM emp where empno=? ; 
DEBUG [cn.hello.ssm.dao.IEmpDAO.findById] - ==> Parameters: 11111(Long)
DEBUG [cn.hello.ssm.dao.IEmpDAO.findById] - <==      Total: 0
*** 3、【数据查询】null
```

改变@Cacheable注解，则可以看到如下内容：


```
@Cacheable(cacheNames="emp")
public boolean delete(Set<Long> ids) ;
```

> 测试结果：
```
INFO [com.alibaba.druid.pool.DruidDataSource] - {dataSource-1} inited
DEBUG [cn.hello.ssm.dao.IEmpDAO.findById] - ==>  Preparing: SELECT empno,name,job,salary,photo FROM emp where empno=? ; 
DEBUG [cn.hello.ssm.dao.IEmpDAO.findById] - ==> Parameters: 11111(Long)
TRACE [cn.hello.ssm.dao.IEmpDAO.findById] - <==    Columns: empno, name, job, salary, photo
TRACE [cn.hello.ssm.dao.IEmpDAO.findById] - <==        Row: 11111, 111, 111, 1111, d34a1fc7-b854-4a49-86e5-7c3c1826446c.png
DEBUG [cn.hello.ssm.dao.IEmpDAO.findById] - <==      Total: 1
*** 1、【数据查询】Emp [empno=11111, name=111, job=111, salary=1111.0, photo=d34a1fc7-b854-4a49-86e5-7c3c1826446c.png]
DEBUG [cn.hello.ssm.dao.IEmpDAO.doRemove] - ==>  Preparing: DELETE FROM emp WHERE empno IN ( ? ) 
DEBUG [cn.hello.ssm.dao.IEmpDAO.doRemove] - ==> Parameters: 11111(Long)
DEBUG [cn.hello.ssm.dao.IEmpDAO.doRemove] - <==    Updates: 1
*** 2、【数据更新】true
*** 3、【数据查询】Emp [empno=11111, name=111, job=111, salary=1111.0, photo=d34a1fc7-b854-4a49-86e5-7c3c1826446c.png]
```


## 多级缓存策略
	之前的注解@Cacheable、@CacheEvict、@CachePut注解均是在方法上的注解形式。
	

```
@CacheEvict(cacheNames="emp",key="#ids[0]")
@Cacheable(cacheNames = "emp", key = "#eid" ,sync = true,condition = "#eid<200",unless="#resule==null")
@CachePut(cacheNames = "emp",key="#vo.empno",unless="#result==null")
```

	如果在同一个类之中需要缓存的方法注解属性都相似，则需要一个个地重复添加。因为Spring同样也提供了类级别的注解形式@CacheConfig来解决这个问题。
	

```
@CacheConfig(cacheNames="emp")
public interface IEmpService {
	@CachePut(key="#vo.empno",unless="#result==null")
	public Emp edit2(Emp vo);
	@Cacheable(key = "#eid" ,sync = true,condition = "#eid<200",unless="#resule==null")
	public Emp getEmp(long eid, String ena);
	@Cacheable
	public Emp preEdit(long id) ;
	@CacheEvict(key="#ids[0]")
	public boolean delete(Set<Long> ids) ;
}
```


## 多级缓存策略
	之前的注解均是单个的在方法上出现，如果想在某一个缓存里面设置有多个缓存的key，则就可以利用复合缓存的形式来完成。
	
1.【hellomybatis-ssm】观察复合缓存的定义


```
@CacheConfig(cacheNames="emp")
public interface IEmpService {
	@Caching(put= {
			@CachePut(key="#vo.empno",unless="#result==null"),
			@CachePut(key="#vo.name",unless="#result==null")
	})
	public Emp edit2(Emp vo);
	@Caching(cacheable= {
			@Cacheable(key="#eid",unless="#result==null"),
			@Cacheable(key="#ena",unless="#result=null")
	})
	public Emp getEmp(long eid, String ena);
}
```


2.【hellomybatis-ssm】编写测试类


```
@ContextConfiguration(locations= {"classpath:spring-test.xml"})
@RunWith(SpringJUnit4ClassRunner.class)
public class TestEmpCache {
	@Autowired
	private IEmpService empService ;
	@Test
	public void testGetEdit2() {
		long eid = 111L ;
		String name = "大大" ;
		Emp voA = this.empService.getEmp(eid,name) ;	
		System.out.println(voA);
		System.out.println("********************************");
		Emp newEmp = new Emp() ;
		newEmp.setEmpno(111L);
		newEmp.setName("大大大");
		System.out.println("*************** " + this.empService.edit2(newEmp));
		Emp voB = this.empService.getEmp(eid,"大小") ; ;	
		System.out.println("********************************");
		System.out.println(voB);
	}
}
```

> 测试结果:

```
DEBUG [cn.hello.ssm.dao.IEmpDAO.findByIdAndName] - ==>  Preparing: SELECT empno,name,job,salary,photo FROM emp where empno=? AND name=? ; 
DEBUG [cn.hello.ssm.dao.IEmpDAO.findByIdAndName] - ==> Parameters: 111(Long), 大大(String)
TRACE [cn.hello.ssm.dao.IEmpDAO.findByIdAndName] - <==    Columns: empno, name, job, salary, photo
TRACE [cn.hello.ssm.dao.IEmpDAO.findByIdAndName] - <==        Row: 111, 大大, 打工的, 9000, c0f3ee4a-c696-4030-8d64-d0952fe5549f.jpeg
DEBUG [cn.hello.ssm.dao.IEmpDAO.findByIdAndName] - <==      Total: 1
Emp [empno=111, name=大大, job=打工的, salary=9000.0, photo=c0f3ee4a-c696-4030-8d64-d0952fe5549f.jpeg]
********************************
DEBUG [cn.hello.ssm.dao.IEmpDAO.doEdit] - ==>  Preparing: UPDATE emp SET name=? WHERE empno=? 
DEBUG [cn.hello.ssm.dao.IEmpDAO.doEdit] - ==> Parameters: 大大大(String), 111(Long)
DEBUG [cn.hello.ssm.dao.IEmpDAO.doEdit] - <==    Updates: 1
*************** Emp [empno=111, name=大大大, job=null, salary=null, photo=null]
********************************
Emp [empno=111, name=大大大, job=null, salary=null, photo=null]
```


3.【hellomybatis-ssm】虽然复合缓存可以实现更多内容的缓存（更多的缓存意味着速度快和空间占有率高），但是这样的配置过于复杂了，所以在SpringCache之中考虑到这种配置结构的繁琐性，可以让用户采用自定义Annoation的形式处理复合缓存。


```
@Caching(cacheable= {
		@Cacheable(key = "#eid", unless="#result == null") ,
		@Cacheable(key = "#ena", unless="#result == null") ,
})
@Target({ElementType.METHOD, ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Inherited
@Documented 
public @interface EmpGetCache {
}
```




```
@EmpGetCache
public Emp getEmp(long eid, String ena);
```


## 整合EHCache缓存组件
之前的缓存都是利用了ConcurrentHashMap子类实现的处理操作，但是从另外一个角度来看，对于缓存的实现应该利用更加专业性的缓存组件。在开发之中，单机缓存的代表就是EHCache组件。

1.【hellomybatis】修改pom.xml文件，追加ehcache依赖。


```
<ehcache.version>2.10.5</ehcache.version>
<dependency>
   <groupId>net.sf.ehcache</groupId>
   <artifactId>ehcache</artifactId>
   <version>${ehcache.version}</version>
</dependency>
```


2.【hellomybatis-ssm】修改pom.xml配置文件


```
<dependency>
   <groupId>net.sf.ehcache</groupId>
   <artifactId>ehcache</artifactId>
</dependency>
```


3.【hellomybatis-ssm】如果要想使用ehcache组件需要配置一个缓存支持，在resource目录下创建ehcache配置目录，里面提供有ehcache.xml配置文件


```
<?xml version="1.1" encoding="UTF-8"?>
<ehcache name="cache">
   <diskStore path="java.io.tmpdir" />   ->定义一个临时的目录
   <defaultCache                             ->进行默认的缓存配置
      maxElementsInMemory="2000"          ->最多允许缓存的元素个数
      eternal="true"                        ->允许自动失效 
      timeToIdleSeconds="120"             ->设置缓存的失效时间
      timeToLiveSeconds="120"             ->设置缓存的存活时间
      overflowToDisk="true" />            ->如果出现不足则将缓存内容写入到磁盘
   <cache                                    ->配置具体的缓存项
      name="emp"                            ->缓存名称
      maxElementsInMemory="2000"         ->缓存个数
      eternal="true"                        ->允许失效
      timeToIdleSeconds="300"             ->设置缓存的失效时间
      timeToLiveSeconds="300"             ->设置缓存的存活时间
      overflowToDisk="true">              ->如果出现不足则将缓存内容写入到磁盘
   </cache>
</ehcache>
```


4.【hellomybatis-ssm】修改spring-cache.xml的定义。


```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
   xmlns:p="http://www.springframework.org/schema/p"
   xmlns:context="http://www.springframework.org/schema/context"
   xmlns:task="http://www.springframework.org/schema/task"
   xmlns:aop="http://www.springframework.org/schema/aop"
   xmlns:tx="http://www.springframework.org/schema/tx"
   xmlns:cache="http://www.springframework.org/schema/cache"
   xsi:schemaLocation="http://www.springframework.org/schema/task http://www.springframework.org/schema/task/spring-task-4.3.xsd
      http://www.springframework.org/schema/cache http://www.springframework.org/schema/cache/spring-cache-4.3.xsd
      http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.3.xsd
      http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd
      http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.3.xsd
      http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-4.3.xsd">  
   <cache:annotation-driven cache-manager="cacheManager"/>
   <bean id="cacheManagerFactoryBean" class="org.springframework.cache.ehcache.EhCacheManagerFactoryBean">
      <property name="configLocation" value="classpath:ehcache/ehcache.xml"/>
   </bean>
   <bean id="cacheManager" class="org.springframework.cache.ehcache.EhCacheCacheManager">
      <property name="cacheManager" ref="cacheManagerFactoryBean"/>
   </bean>
</beans>
```


## 整合Redis分布式缓存
在开发之中，分布式一定是主流话题，也就是说所有的程序都必须考虑集群问题，包括Web集群使用Nginx，而业务集群可以利用RPC技术拆分，一旦使用了集群架构，那么对于缓存的配置就必须利用分布式缓存来解决，故本次采用Redis来实现分布式缓存。

1.【hellomybatis-ssm】建立config目录将redis.properties配置文件加入到此目录之中。

```
redis.host=192.168.188.131
redis.port=6379
redis.auth=hellojava
redis.timeout=2000
redis.pool.maxTotal=200
redis.pool.maxIdle=30
redis.pool.maxWaitMillis=2000
redis.pool.testOnBorrow=true
```


2.【hellomybatis-ssm】修改spring-base.xml配置文件，引入资源项。


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
   <context:component-scan base-package="cn.hello.ssm.service,cn.hello.ssm.dao"/>
   <context:property-placeholder location="classpath:config/*.properties"/>
</beans>
```


3.【hellomybatis-ssm】追加spring-redis.xml配置文件，进行redis的相关配置。


```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
   xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
   <!-- 3、配置RedisTemplate操作模版，利用此模版可以方便的进行Redis的数据操作 -->
   <bean id="redisTemplate" class="org.springframework.data.redis.core.RedisTemplate">
      <property name="connectionFactory" ref="jedisConnectionFactory"/>
      <property name="keySerializer">    <!-- 进行key的序列化管理定义 -->
         <bean class="org.springframework.data.redis.serializer.StringRedisSerializer"/>
      </property>
      <property name="valueSerializer">  <!-- 可以保存各种对象 -->
         <bean class="org.springframework.data.redis.serializer.JdkSerializationRedisSerializer"/>
      </property>
      <property name="hashKeySerializer">
         <bean class="org.springframework.data.redis.serializer.StringRedisSerializer"/>
      </property>
      <property name="hashValueSerializer">
         <bean class="org.springframework.data.redis.serializer.JdkSerializationRedisSerializer"/>
      </property>
   </bean>
   
   <!-- 1、进行Jedis连接池的相关属性定义 -->
   <bean id="jedisPoolConfig" class="redis.clients.jedis.JedisPoolConfig">
      <property name="maxTotal" value="${redis.pool.maxTotal}"/>
      <property name="maxIdle" value="${redis.pool.maxIdle}"/>
      <property name="testOnBorrow" value="${redis.pool.testOnBorrow}"/>
      <property name="maxWaitMillis" value="${redis.pool.maxWaitMillis}"/>
   </bean>
   <!-- 2、根据连接池的配置进行连接工厂的定义，此时使用Spring提供的连接工厂类 -->
   <bean id="jedisConnectionFactory" class="org.springframework.data.redis.connection.jedis.JedisConnectionFactory">
      <property name="poolConfig" ref="jedisPoolConfig"/>
      <property name="hostName" value="${redis.host}"/>
      <property name="port" value="${redis.port}"/>
      <property name="password" value="${redis.auth}"/>
      <property name="timeout" value="${redis.timeout}"/>
   </bean> 
</beans>
```


4.【hellomybatis-ssm】实现SpringCache接口手工进行Redis的处理实现


```
package cn.hello.util.cache;
import java.util.concurrent.Callable; 
import org.springframework.cache.Cache;
import org.springframework.cache.support.SimpleValueWrapper;
import org.springframework.data.redis.core.RedisTemplate;
public class RedisCache implements Cache {
   private RedisTemplate<String,Object> redisTemplate ; 
   private String name ; // 缓存区的名字
   /**
    * 返回缓存的名字
    * @return
    */
   @Override
   public String getName() {
      return this.name;
   } 

   @Override
   public Object getNativeCache() {
      return this.redisTemplate;
   }

   /**
    * 返回缓存的值
    * @param key
    * @return
    */
   @Override
   public ValueWrapper get(Object key) {
      Object object = this.redisTemplate.opsForValue().get(String.valueOf(key)) ;
      if (object != null) {  // 数据查找到了
         return new SimpleValueWrapper(object) ;
      }
      return null;
   }

   /**
    * 返回缓存的值
    * @param key
    * @param type
    * @param <T>
    * @return
    */
   @Override
   public <T> T get(Object key, Class<T> type) {
      T object = (T) this.redisTemplate.opsForValue().get(String.valueOf(key)) ;
      return object;
   }

   /**
    * 返回缓存的值
    * @param key
    * @param valueLoader
    * @param <T>
    * @return
    */
   @Override
   public <T> T get(Object key, Callable<T> valueLoader) {
      return null;
   }

   /**
    * 向缓存中加入数据
    * @param key
    * @param value
    */
   @Override
   public void put(Object key, Object value) {
      this.redisTemplate.opsForValue().set(String.valueOf(key), value);
   }

   /**
    * 将指定的数据放入指定的key之中
    * @param key
    * @param value
    * @return
    */
   @Override
   public ValueWrapper putIfAbsent(Object key, Object value) {
      return null;
   }

   /**
    * 清空单个缓存
    * @param key
    */
   @Override
   public void evict(Object key) {
      this.redisTemplate.delete(String.valueOf(key)) ;
   }

   /**
    * 清空所有缓存
    */
   @Override
   public void clear() {
      this.redisTemplate.getConnectionFactory().getConnection().flushDb(); 
   }

   /**
    * 设置redis的操作模板
    * @param redisTemplate
    */
   public void setRedisTemplate(RedisTemplate<String, Object> redisTemplate) {
      this.redisTemplate = redisTemplate;
   }
   
   public void setName(String name) {
      this.name = name;
   }
}
```


5.【hellomybatis-ssm】修改spring-cache.xml文件，使用Redis缓存策略替换掉ConcurrentHashMap缓存策略。


```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
   xmlns:p="http://www.springframework.org/schema/p"
   xmlns:context="http://www.springframework.org/schema/context"
   xmlns:task="http://www.springframework.org/schema/task"
   xmlns:aop="http://www.springframework.org/schema/aop"
   xmlns:tx="http://www.springframework.org/schema/tx"
   xmlns:cache="http://www.springframework.org/schema/cache"
   xsi:schemaLocation="http://www.springframework.org/schema/task http://www.springframework.org/schema/task/spring-task-4.3.xsd
      http://www.springframework.org/schema/cache http://www.springframework.org/schema/cache/spring-cache-4.3.xsd
      http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.3.xsd
      http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd
      http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.3.xsd
      http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-4.3.xsd">  
   <cache:annotation-driven cache-manager="cacheManager"/>
   <bean id="cacheManager" class="org.springframework.cache.support.SimpleCacheManager">
      <property name="caches">
         <set>
            <bean class="cn.hello.util.cache.RedisCache">
               <property name="name" value="emp"/>
               <property name="redisTemplate" ref="redisTemplate"/>
            </bean>
         </set>
      </property>
   </bean>
</beans>
```


> 总的来说，SpringCache给缓存技术提供了一个标准，带来方便的同时依旧也有不足。



