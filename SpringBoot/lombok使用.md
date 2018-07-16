## lombok使用技巧

**1、添加pom依赖**

```
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
</dependency>
```


**2、安装插件**


打开idea的plugins处搜索lomok插件,进行安装即可。


**3、常用注解**

@Data：在类上添加注解，生成get、set、toString之类的方法

@Get：只生成get

@Set：只生成set

@ToString：生成toString

@Slf4j：之后直接使用log.info("xxxxx");调用即可

@NonNull：不为空的判断

```
public NonNullExample(@NonNull Dept vo) {
	    this.dname = vo.getDname();
}
```


等价于


```
public NonNullExample(@NonNull Dept vo) {
    if (vo == null) {
      throw new NullPointerException("vo");
    }
    this.dname = vo.getDname();
 }
```


@SneakyThrows:进行异常的抛出，等价于之前在方法上加上throw抛出
> @SneakyThrows(Exception.class)


