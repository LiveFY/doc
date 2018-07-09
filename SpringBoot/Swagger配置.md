1、引入依赖包：

```
        <dependency>
			<groupId>io.springfox</groupId>
			<artifactId>springfox-swagger2</artifactId>
			<version>2.7.0</version>
		</dependency>
		<dependency>
			<groupId>io.springfox</groupId>
			<artifactId>springfox-swagger-ui</artifactId>
			<version>2.7.0</version>
		</dependency>
```


2、定义一个Swagger的配置类：

```
@Configuration
@EnableSwagger2
public class Swagger2Config {
	@Bean
	public Docket getDocket() { // 此类主要是整个的Swagger配置项
		return new Docket(DocumentationType.SWAGGER_2)
				.apiInfo(this.getApiInfo()).select()
				.apis(RequestHandlerSelectors
						.basePackage("cn.hello.boot.controller"))
				.paths(PathSelectors.any()).build(); // 设置文档的显示类型
	}
	private ApiInfo getApiInfo() {
		return new ApiInfoBuilder().title("Swagger构建项目说明信息")
				.description("更多详情内容")
				.termsOfServiceUrl("http://xxxx.com").contact("小小哥")
				.license("小小小").version("1.0").build();
	}
}
```




3、修改控制层注解

```
@RestController
public class MemberController {
	@ApiOperation(value = "实现人员信息的添加处理", notes = "添加用户数据的")
	@ApiImplicitParams({
			@ApiImplicitParam(name = "member", value = "用户描述的详细实体信息", required = true, dataType = "MemberClass")})
	@RequestMapping(value = "/member/add", method = RequestMethod.POST)
	public Object add(@RequestBody Member member) { // 表示当前的配置可以直接将参数变为VO对象
		System.err.println("【MemberController.add()接收对象】" + member);
		return true;
	}
	@ApiOperation(value = "获取指定编号的人员信息", notes = "只需要设置mid的信息就可以获取Member的完整内容")
	@ApiImplicitParams({
			@ApiImplicitParam(name = "mid", value = "用户编号", required = true, dataType = "String")})
	@RequestMapping(value = "/member/get/{mid}", method = RequestMethod.GET)
	public Member get(@PathVariable("mid") long mid) {
		Member vo = new Member();
		vo.setMid(mid);
		vo.setName("hello - " + mid);
		vo.setBirthday(new Date());
		vo.setSalary(99999.99);
		vo.setAge(16);
		return vo;
	}
}
```

4、启动项目配置，打开浏览器：http://localhost/swagger-ui.html
![image](http://wx4.sinaimg.cn/mw690/0060lm7Tly1ft40v8t0rpj30vr0b0aab.jpg)