
## 1. 关于request、response、session等内置对象的获取：
① 获取request对象：

```
HttpServletRequest request = ((ServletRequestAttributes) RequestContextHolder.getRequestAttributes()).getRequest() ;
```

② 通过方法参数传递

```
	@RequestMapping("member_add")
	public void add(HttpServletRequest request,HttpServletResponse response,HttpSession session){}
```

> 注：如果处理方法使用HttpServletResponse进行响应的话，则处理方法的返回值设置成void.



## 2. 关于日期类型的处理：

```
   	@InitBinder
	public void initBinder(WebDataBinder binder) {
		SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd") ;
		binder.registerCustomEditor(java.util.Date.class, new CustomDateEditor(sdf, true));	
	}
```




## 3. 关于错误页的设置：
① 可以在配置文件上加入异常，即抛出此异常就拦截住，然后跳转到错误页。  <br/>
	示例：上传超出范围的异常。

```
<bean class="org.springframework.web.servlet.handler.SimpleMappingExceptionResolver">
			<property name="exceptionMappings">	<!-- 进行异常处理的映射 -->
				<props>
					<prop key="org.springframework.web.multipart.MaxUploadSizeExceededException">
						plugins/errors
					</prop>
					<prop key="org.springframework.web.util.NestedServletException">
						plugins/errors
					</prop>
				</props>		
			</property>	
		</bean>
```



② 可以在web.xml文件上配置错误页，以状态码为代表  <br/>
示例:500则跳到xx页  <br/>


```
		<error-page>
			<error-code>500</error-code>
			<location>/error.action</location>
		</error-page>
```



③ 使用@ExceptionHandler注解

```
		public class TestController{
		@RequestMapping("show")
		public Object show(Emp emp) {
			if(2>1){
				throw RuntimeException("error");
			}
			return emp ;  
		} 
		
		@ExceptionHandler(RuntimeException.class)
		public String errorException(RuntimeException e){
			//return "forward:/error";  //跳转到error.action之中  //forward跳转的话算是同一条请求，不知道直接跳会如何
			return "error";
		}


		public  String  error(){
			return "error"; //跳转到error.jsp页面
		}
		
	}
```

		
> 解释：show.action处抛出一个运行时异常，然后@ExceptionHandler绑定运行时异常的配置，然后跳转到相应的界面。<br/>
注：目前发现@ExceptionHandler方法只能对同一类中的方法进行响应。待测试。无法对其他类产生影响。但是这种情况在加入@ControllerAdvice注解之后就可以对其他的类的异常进行处理了。 <br/>
	可参考:
	[如下内容](https://blog.csdn.net/liujia120103/article/details/75126124)	


## 4. 关于提示信息的设置：
	可以专门提供一个vo类来做信息的提示。
	信息提示此处一般只用于数据的更新处提示。（增加、删除、修改）


## 5. 关于注解
**@Controller**：此处表示这是一个控制器。和在配置文件上添加bean一样。同样以下四个注解可以产生相同的效果。

```
    	●【数据层】仓库配置类：@Repository（org.springframework.stereotype.Repository）
	●【业务层】业务配置类：@Service（org.springframework.stereotype.Service）
	●【工具组件】工具类配置：@Component（org.springframework.stereotype.Component）
	●【控制层】控制层配置：@Controller（org.springframework.stereotype.Controller）
```


**@RequestMapping**:其中value:请求URL,method:请求参数,params:请求参数,headers:报文头,produces:指定返回的内容类型


```
   	 ● value 表示需要匹配的url的格式

	● method 表示所需处理请求的http 协议(如get,post,put,delete等)
		@RequestMapping(value = "/member/add",method = RequestMethod.POST)

	● params 格式为”paramname=paramvalue” 或 “paramname!=paramvalue”。 
		表示参数必须等于某值，或者不等于才进入此映射方法。不填写的时候表明不限制。所以当请求/testParams.do?param1=value1&param2=value2 的时候能够正确访问到该testParams 方法：
		    @RequestMapping (value= "testParams" , params={ "param1=value1" , "param2" , "!param3" })
		    public String testParams() {
		       System. out .println( "test Params..........." );
		       return "testParams" ;
		    }

	● headers	用来限定对应的reqeust请求的headers中必须包括的内容，例如headers={"Connection=keep-alive"}, 表示请求头中的connection的值必须为keep-alive。当请求/testHeaders.do 的时候只有当请求头包含Accept 信息，且请求的host 为localhost 的时候才能正确的访问到testHeaders 方法：
			@RequestMapping (value= "testHeaders" , headers={ "host=localhost" , "Accept" })
		    public String testHeaders() {
		       return "headers" ;
		    }

	● produces 指定返回的内容类型，仅当request请求头中的(Accept)类型中包含该指定类型才返回
		@RequestMapping (value= "testProduces" , produces="text/plain;charset=utf-8")
		@RequestMapping (value= "testProduces" , produces="application/json;charset=utf-8")
		@RequestMapping (value= "testProduces" , produces="application/xml;charset=utf-8")
```


**@DeleteMapping**  <=====>@RequestMapping(value = "/member/delete",method = RequestMethod.DELETE)  <br/>

**@GetMapping **<=====>@RequestMapping(value = "/member/get",method = RequestMethod.GET)  <br/>


**@PostMapping** <=====>@RequestMapping(value = "/member/add",method = RequestMethod.POST) <br/>

**@PutMapping** <=====>@RequestMapping(value = "/member/update",method = RequestMethod.PUT)

> 一般情况下，我们会默认把:  <br/>
	POST  ===  创建; <br/>
	GET   ===  获取;  <br/>
	PUT或PATCH === 更新;  <br/>
	DELETE  === 删除


**@PathVariable**:将url中的占位符参数绑定到控制器处理方法的入参中，最好在@PathVariable中显式指定绑定的参数名，以避免因不同编译方式造成参数绑定失败。

```
        @RequestMapping(value="/{userId}")
	public void detail(@PathVariable("userId") String userId){
	    userService.getUserById(userId);
	}
```


**@RequestParam**：指定对应的请求参数<br/>
    ● value:参数名  前端传入进来的参数的名字  <br/>
    ● required：是否必须，默认为true，表示请求中包含对应的参数名，如果不存在则抛出异常 <br/>
    ● defaultValue:默认参数名，在使用该参数时自动将required设为false。一般情况下不推荐使用。


```
        @RequestMapping ( "requestParam" )
	public String testRequestParam( @RequestParam(value="name",required=false) String name, @RequestParam ("age") int age) {
		return "requestParam" ;
	}
```


**@ResponseBody**：配置jackson相关的依赖后会以json方式返回给前端

```
        @RequestMapping("show")
	@ResponseBody
	public Object show(Emp emp) {
		return emp ;  
	}
```
 

**@RequestBody**:用来将指定的客户端发送过来的请求参数的数据格式转换成Java实体

```
        @RequestMapping(value = "/xxxxx.do")
	public void create(@RequestBody() String host){
	    System.out.println("-----------" + host);
	}
```


**@ResponseStatus**:返回一个指定的http response状态码。

```
    @ResponseStatus(reason="no reason",value=HttpStatus.BAD_REQUEST)
	//@ResponseStatus(reason="no reason",value=HttpStatus.NOT_FOUND)
	@RequestMapping("/responsestatus")
	public void responseStatusTest(){  	
	}
```


> 注：如果出现没任何没有映射的异常，响应都会带有500响应码。


**@CookieValue**:可以把Requestheader中关于cookie的值绑定到方法的参数上。

```
        @RequestMapping("/handle")  
 	public String handle(@CookieValue("JSESSIONID") String cookie)  {  
 	}
```

	

**@RequesetHeader**:可以将报文属性值绑定到处理方法的入参中。

```
        @RequsetMapping("/handle")
	public String handle(@RequestHeader("Accept-Enocoding")String encoding,@RequestHeader("Keepe-Alive")long keepAlive){}
```

	
> 注：@RequestHeader与@RequestParam有一样的参数。



**@RestController** 等价于  @Controller+@ResponseBody

**@ExceptionHandler**

```
    	 @RequestMapping("/exception")
	 public void ExceptionTest() throws Exception{
	    throw new Exception("i don't know");
	 }    
	 @ExceptionHandler
	 public String handleException(Exception e,HttpServletRequest request){  
	    System.out.println(e.getMessage());  
	    return "helloworld";  
	 }
```


**RestTemplate**类：可以使用该类调用web服务器端的服务。



## 6.关于Model、ModelAndView、Map、String

**ModelAndView**:可以放模型数据和逻辑视图名

**String**:存放逻辑视图名

**Model**:存放模型数据。Model是一个接口。

> 注：在使用时可以Model和String二者可以结合，Model来存放数据，返回String(即逻辑视图)  Model的使用要从方法参数上传递下来，不要直接用new 创建。因为它是一个接口，无法直接实例化。

```
    	@RequesMapping("member_list")
	public String list(Model model){
		model.addAttribute("xxxx",xxxx);
		return "member_list";  /
	}

	@RequestMapping("member_list")
	public ModelAndView list(){
		ModelAndView mav = new ModelAndView("member_list");
		mav.addObject("xxx",xxx);
		return mav;
	}
```



**@SessionAttributes**:在多个请求之间共用某个模型属性数据。将处理方法对应的模型数据属性透明地保存在HttpSession中。

**ModelAndView常用方法**：

```
    	public ModelAndView()：无参构造，不跳转（Ajax请求）
	设置视图名：
		public ModelAndView(String viewName)：设置跳转路径，需要写完整路径
		public void setViewName(String viewName)：设置跳转路径
	设置模型数据：
		public ModelAndView addObject(String attributeName, Object attributeValue)设置传递的属性内容，相当于：request.setAttribute()
		public ModelAndView addAllObjects(Map<String,?> modelMap)：自动处理Map集合的数据
```

**Model常用方法：**


```
设置单个模型数据：
	public Model addAttribute(String attributeName, Object attributeValue);

	public Model addAttribute(Object attributeValue);

设置集合模型数据：
	public Model addAllAttributes(Collection<?> attributeValues);

	public Model addAllAttributes(Map<String, ?> attributes);


	public Model mergeAttributes(Map<String, ?> attributes);
	
	public boolean containsAttribute(String attributeName);

	public Map<String, Object> asMap();
```


**@ModelAndView**：将方法入参对象添加到模型中。

① 方法入参时使用：

```
		@RequestMapping("member_list")
		public String get(@ModelAttribute("user") User user){
			this.userService.get(user.getUserId());
			return "member_list";
		}
```

② 方法定义时使用：

```
        @ModelAttribute  //此处表示把user对象放入到模型中  等价于  mav.addObject("user",user);
		public User getUser(){
			User user = new User();
			user.setUserId("111");
			return user;
		}
		@RequsetMapping("member_list")
		public String get(@ModeAttribute("user")User user){
			user.setUserName("test");
			return "member_list";
		}
```




```
@Controller**：用于标识是处理器类；
@RequestMapping：请求到处理器功能方法的映射规则；
@RequestParam：请求参数到处理器功能处理方法的方法参数上的绑定；
@ModelAttribute：请求参数到命令对象的绑定；
@SessionAttributes：用于声明session 级别存储的属性，放置在处理器类上，通常列出模型属性（如
@ModelAttribute）对应的名称，则这些属性会透明的保存到session 中；
@InitBinder：自定义数据绑定注册支持，用于将请求参数转换到命令对象属性的对应类型；

@CookieValue：cookie 数据到处理器功能处理方法的方法参数上的绑定；
@RequestHeader：请求头（header）数据到处理器功能处理方法的方法参数上的绑定；
@RequestBody：请求的body体的绑定（通过HttpMessageConverter 进行类型转换）；
@ResponseBody：处理器功能处理方法的返回值作为响应体（通过HttpMessageConverter进行类型转换）；
@ResponseStatus：定义处理器功能处理方法/异常处理器返回的状态码和原因；
@ExceptionHandler：注解式声明异常处理器；
@PathVariable：请求URI 中的模板变量部分到处理器功能处理方法的方法参数上的绑定，从而支持RESTful 架构风
格的URI；
```



## 7.关于redirect和forward
**redirect**:属于客户端跳转

**forward**：属于服务器端跳转

> 注：如果字符串带有“forward:”或“redirect:”前缀，则SpringMVC会对它们进行特殊处理：将“forward:”和“redirect:”当成提示符，其后的字符作为URL处理。redirect会让浏览器重新发起一个请求，而forward请求与当前请求同属一个请求。


