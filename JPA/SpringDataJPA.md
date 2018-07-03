## 核心接口：<br/>
	**Repository**：仅仅只是一个标识接口，没有任何方法，方便Spring自动扫描识别<br/>
	**CrudRepository**：继承Repository，实现一组CRUD相关的方法 <br/>
	**PagingAndSortingRepositor**y：继承CrudRepository，实现了一组分页排序相关的方法  <br/>
	**JpaRepository**：继承PagingAndSortingRepository，实现一组JPA规范相关的方法。 <br/>

### (1)CrudRepository接口
在CrudRepository子接口里面定义有如下的一些方法：(11个)
```
1	public	<S extends T> S save(S entity);	追加单个的实体对象
2	public	<S extends T> Iterable<S> saveAll(Iterable<S> entities);实现数据批量增加，需要传递List、Set集合
3	public 	Optional<T> findById(ID id);根据ID进行查询
4	public	boolean existsById(ID id);	判断当前的ID是否存在
5	public	Iterable<T> findAll();	返回全部的查询结果
6	public	Iterable<T> findAllById(Iterable<ID> ids);	得到部分的查询结果
7	public	long count();	取得全部数据量
8	public	void deleteById(ID id);	根据id删除
9	public	void delete(T entity);	直接根据实体对象删除
10	public	void deleteAll(Iterable<? extends T> entities);	删除一堆实体
11	public	void deleteAll(); 删除全部
```

> 注意：在进行删除的时候会先进行查询，会影响效率，所以建议自己写sql实现。


### (2)PagingAndSortingRepository接口
在PagingAndSortingRepository子接口之中定义的方法：

```
1	public	Iterable<T> findAll(Sort sort);	需要设置一个排序的类
2	public	Page<T> findAll(Pageable pageable);需要将所有的内容设置到Pageable类
```

### (3)JpaRepositor接口
在JpaRepository子接口之中定义的方法：

```
    查询全部数据：public List<T> findAll();
    查询全部数据：public List<T> findAll(Sort var1);
    查询部分数据：public List<T> findAll(Iterable<ID> var1);
    批量数据更新：public <S extends T> List<S> save(Iterable<S> var1);
    强制保存到数据库之中：public void flush();
    数据保存并刷新：public <S extends T> S saveAndFlush(S var1);
    数据批量删除：public void deleteInBatch(Iterable<T> var1);
    数据全部删除：public void deleteAllInBatch();
    通过id获取数据：public T getOne(ID var1);
    查询所有对象：public <S extends T> List<S> findAll(Example<S> var1);
    查询所有对象：public <S extends T> List<S> findAll(Example<S> var1, Sort var2);
```

> 注意事项：<br/>
· 几个查询及批量保存方法，和CrudRepository接口相比，返回的是List，使用起来更方便。  <br/>
· 增加了 InBatch 删除， 实际执行时，后台生成一条sql语句，效率更高些。相比较而言，CrudRepository 接口的删除方法，都是一条一条删除的，即便是 deleteAll 也是一条一条删除的。<br/>
· 增加了 getOne() 方法，切记，该方法返回的是对象引用，当查询的对象不存在时，它的值不是Null。<br/>


### (4)JpaSpecificationExecutor接口

该接口提供了对JPA Criteria查询（动态查询）的支持。注意，这个接口很特殊，不属于Repository体系，而Spring data JPA不会自动扫描识别，所以会报找不到对应的Bean，我们只需要继承任意一个继承了Repository的子接口或直接继承Repository接口，Spring data JPA就会自动扫描识别，进行统一的管理。

```
public interface UserRepository extends JpaRepository<User, Integer>,JpaSpecificationExecutor<User>{}
```
详细内容可以参考：[该博客内容](https://blog.csdn.net/liuchuanhong1/article/details/52042477)


## 查询方法映射关键词：

```
· And	                 findByLastnameAndFirstname	           … where x.lastname = ?1 and x.firstname = ?2
· Or	                 findByLastnameOrFirstname	           … where x.lastname = ?1 or x.firstname = ?2
· Is,Equals              findByFirstname,findByFirstnameIs,findByFirstnameEquals	… where x.firstname = ?1
· Between	             findByStartDateBetween	               … where x.startDate between ?1 and ?2
· LessThan	             findByAgeLessThan	                   … where x.age < ?1
· LessThanEqual			 findByAgeLessThanEqual				   … where x.age <= ?1
· GreaterThan			 findByAgeGreaterThan                  … where x.age > ?1
· GreaterThanEqual	     findByAgeGreaterThanEqual	           … where x.age >= ?1
· After	                 findByStartDateAfter	               … where x.startDate > ?1
· Before	             findByStartDateBefore	               … where x.startDate < ?1
· IsNull	             findByAgeIsNull	                   … where x.age is null
· IsNotNull,NotNull	     findByAge(Is)NotNull	               … where x.age not null
· Like	                 findByFirstnameLike	               … where x.firstname like ?1
· NotLike	             findByFirstnameNotLike	               … where x.firstname not like ?1
· StartingWith	         findByFirstnameStartingWith		   … where x.firstname like ?1(parameter bound with appended %)
· EndingWith	         findByFirstnameEndingWith	           … where x.firstname like ?1(parameter bound with prepended %)
· Containing	 		 findByFirstnameContaining	           … where x.firstname like ?1(parameter bound wrapped in %)
· OrderBy				 findByAgeOrderByLastnameDesc		   … where x.age = ?1 order by x.lastname desc
· Not					 findByLastnameNot					   … where x.lastname <> ?1
· In					 findByAgeIn(Collection<Age> ages)	   … where x.age in ?1
· NotIn					 findByAgeNotIn(Collection<Age> ages)  … where x.age not in ?1
· True					 findByActiveTrue()					   … where x.active = true
· False					 findByActiveFalse()				   … where x.active = false
· IgnoreCase			 findByFirstnameIgnoreCase			   … where UPPER(x.firstame) = UPPER(?1)
```

几个范例：
```
· 根据部门编号和名称查询数据：public Dept findByDeptnoAndDname(Long deptno,String dname);
· 根据部门编号查询：public Dept findByDetno(Long deptno);
· 根据人数范围查询部门信息：public List<Dept> findByNumBetween(Integer lo,Integer hi);
· 查找部门名称不为空的数据：public List<Dept> findByDnameNotNull();
· 模糊查询：public List<Dept> findByDnameContaining(String name);
	为用户追加自动匹配边界的作用：
		|- StartingWith（开头追加“%”） public List<Dept> findByDnameStartingWith(String name);
		|- EndWith（结尾追加“%”） public List<Dept> findByDnameEndWith(String name);
		|- Containing(前后都追加“%”)
· 根据部门名称查询并通过部门编号排序：public List<Dept> findByDnameContainingOrderByDeptnoDesc(String keyWord) ;
· 查找指定范围内的数据：public List<Dept> findByDeptnoIn(Set<Long> ids);
```


## 关于自定义SQL的问题：
### 基于JPQL
方式一：使用JPQL  Spel语法

```
    @Query("SELECT d FROM Dept AS d WHERE d.deptno=?#{[0]}")	// 使用Spel语法来进行参数的获得
	public Dept findById(Long id) ;	// 如果要查询一定要使用Query接口
```


方式二：使用JPQL  使用标记

```
    @Query("DELETE FROM Dept AS d WHERE d.deptno=:dno")
	public int doRemove(@Param("dno") Long deptno) ; 

	@Query("SELECT d FROM Dept AS d WHERE d.deptno IN :ids")
	public List<Dept> findByIds(@Param(value="ids") Set<Long> ids) ;
	
	@Query("SELECT d FROM Dept AS d WHERE d.deptno=:#{#mydept.deptno} AND dname=:#{#mydept.dname}")
	public Dept findByIdAndDname(@Param(value="mydept") Dept dept) ;

	@Modifying(clearAutomatically=true)	// 追加有缓存的清除与更新
	@Query("UPDATE Dept AS d SET d.dname=:#{#mydept.dname},d.num=:#{#mydept.num} WHERE d.deptno=:#{#mydept.deptno}")
	public int doEdit(@Param(value="mydept") Dept po) ;
```


方式三：使用JPQL 使用？

```
    @Modifying	// 属于更新操作
	@Query("UPDATE Dept AS d SET d.dname=?2 WHERE d.deptno=?1")
	public int doEditDname(Long deptno,String dname) ;
```



### 基于原生SQL

```
    @Query(value="select * from t_user where user_name=?1",nativeQuery=true)
	public User findByUserName(String userName);
```



```
    @Query("UPDATE Dept SET dept.dname=?2 WHERE dept.deptno=?1")
	public int doEditDname(Long deptno,String dname) ;
```


