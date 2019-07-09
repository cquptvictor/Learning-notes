# SpringMVC

## web.xml配置文件

```
<!--配置前端处理器-->
<servlet> 

<servlet-name>dispatcher</servlet-name>    
<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>   

<!--init参数来设置dispatcher的位置-->
<init-param>        
<param-name>contextConfigLocation</param-name>        
<param-value>WEB-INF/dispatcher.xml</param-value>    
</init-param>   

<!--表示在应用加载时就加载这个servlet-->
<load-on-startup>1</load-on-startup>
</servlet>

<servlet-mapping>
<servlet-name>dispatcher</servlet-name>    

<!--拦截所有的url-->
<url-pattern>/</url-pattern>
</servlet-mapping>
```

- 默认的dispatcher为`/WEB-INF/<servlet-name>-dispatcher.xml`

## @RequestMapping注解

- 可以修饰方法，也可以修饰类
- URL=类修饰的URL+方法修饰的URL

### 属性

- value：配置url
- method：配置请求方法
- params：配置必须有哪些参数，或者参数必须要等于某些值
- headers：配置请求头的信息
- 用户的请求方法必须要满足@RequestMapping中的内容，否则404

### 通配符

- ?：匹配文件名中的一个字符
- *：匹配文件名中的任意字符
- **：匹配多层路径

### @PathVariable

- `{name}`:设定占位符
- 把URL中占位符映射到方法的参数中

## HiddenHttpMethodFilter过滤器

- 在web.xml中配置
- 可以把POST请求转化为PUT和DELETE
- 在请求表单中添加一个隐藏域，`<input type = hidden name = "_method" value = "PUT"`

### Rest风格

- 增删改查对应POST、DELETE、PUT、GET

## @RequestParam

- 获得请求时的参数
- value：参数的name
- required：参数是否是必须，默认为true；若为false，没有这个参数就会赋值为Null，用包装类型可以避免报错
- defaultValue：配置默认值

## @RequestHeader

- 类似于@RequestParam，用于获得请求头信息

## @CookieValue

- 类似于@RequestParam，用于获取Cookie的值

## POJO映射

- 自动映射POJO对象，支持级联属性
- 对于级联属性，表单要是nameclass.attribute`

## Servlet原生API入参

- 直接在参数中写上需要的原生API
- 支持9种原生API

## 处理模型数据

### ModelAndView

- 返回值为ModelAndView
- 包含模型和视图信息
- SpringMVC经过一系列操作，将model放入了request的域对象中

### Map

- 返回值为String
- 设置模型的信息

### @SessionAttribute

- 只能修饰类或者接口
- 在方法中，ModelMap设置值的时候，如果，模型数据的key与@SessionAttributes的value相同，则将模型数据同时添加到Session中
- 可以根据name来放入，也可以同时根据type来放入

## @ModelAttribute

### 用在方法上

- 在同一个Controller类中，@ModelAttribute标记的方法会在每一个目标方法被调用前调用
- 方法返回值是void，接收一个Model对象，然后向其添加任意数量的属性
- 方法返回一个具体类型，将返回值添加进属性
- @ModelAttribute修饰的方法的中，放入到map中的key，应该和接下来的方法中参数的类型名首字母小写相同，才能在接下来的方法中获得

### 用在参数上

- 改名，数据绑定的时候默认的名字是参数的名字，使用@ModelAttribute后对应的名字就应该是@ModelAttribute的值
- 标注在方法参数上的@ModelAttribute说明了该方法参数的值将由model中取得。如果model中找不到，那么该参数会先被实例化，然后被添加到model中。在model中存在以后，请求中所有名称匹配的参数都会填充到该参数中。

### 用在@RequestMapping标注的方法上

- @RequestMapping方法的返回值将会被解释为model的一个属性，而非一个视图名，此时视图名将以视图命名约定来方式来确定。

- 应用场景：要修改一个对象的几个值（有一部分值不变），如果传入一个对象给mybatis，那样会直接修改了所有值；需要在映射表单值之前读取原来的对象，然后新值覆盖掉旧值

## 视图解析流程

- 不管是view，String，ModelAndView都会被转化为ModelAndView交由ViewResolver处理

## 配置URL直接访问页面

- 配置\<mvc:view-controller/\>标签
- 需要同时有\<mvc:annotation-driven\>标签，否则要经过Servlet的访问就会404

## 转发与重定向

- 方法的返回值为String
- `redirect:`：代表重定向
- `forward:`：代表转发

## 自定义类型转换器

1. 实现Converter<>接口，实现convert方法
2. 在dispatcher.xml配置文件中注册相应的自定义转换器bean，或者使用注解
3. 在ConversionServiceFactoryBean容器中注册converters
4. 在mvc:annotation-driven中配置ConversionServiceFactoryBean

### converter与formatter

- formatter只能把String转化为其他类型，是专门针对于Web层的

## \<mvc:annotaion-driven\\>

- 会自动注册RequestMappingHandlerMapping、RequestMappingHandlerAdapter和ExceptionHandlerExceptionResolver
- 支持使用ConversionService实例对表单参数进行类型转换
- 支持使用@DateFormat和@NumberFormat注解完成数据类型的格式化
- 支持使用@Valid注解对JavaBean实例进行JSR303验证
- 支持使用@RequestBody注解

## @InitBinder注解

- 对WebDataBinder对象进行初始化，WebDataBinder是DataBinder的子类用于完成由表单字段到JavaBean属性的绑定 
- @InitBinder注解修饰的方法不能有返回值
- @InitBinder注解修饰的方法通常是WebDataBinder

## @DateFormat和@NumberForamt

- 需要先配置\<mvc:annoitation-driven\>
- 在目标属性上使用这个注解
- 指定pattern

## 数据校验

- JSR303：是Java为Bean数据合法性校验提供的标准，Hibernate Validator是它的实现
- 需要使用validation-api、hibernate-validator和el-api
- 需要校验的pojo和错误绑定中间补鞥呢有其他参数

## HttpMessageConverter\<T\>

![1562584569158](C:\Users\victor\AppData\Roaming\Typora\typora-user-images\1562584569158.png)

- 是Spring3.0后新添加的一个接口，实际工作是由接口的各种实现类来完成
- 负责将请求信息转换为一个对象，或将对象输出为响应信息

### @RequestBody与@ResponseBody

- @RequestBody：用于入参，获取请求体
- @ResponseBody：用在方法上，直接返回json结果

### HttpEntity\<T\>和ResponseEntity\<T\>

- HttpEntity：作为入参的参数类型，获取请求体和请求头
- ResponseEntity：当做返回值，来设置响应头、响应体；可以用来进行文件下载

## 文件上传

- SpringMVC通过MultipartResolver接口来实现文件上传，提供了CommonsMultipartResolver和StanderdServletMultipartResolver的实现类
- 直接在dispatcher.xml中配置相关信息就可以了

## 拦截器

- 需要实现HandlerInterceptor接口
- 在配置文件中配置\<mvc:interceptors\>下的\<bean/\>子标签
- 子标签\<mvc:interceptor\>能够指定拦截某些URL，或者指定不拦截某些URL

### 拦截器的三个方法

- preHandle:在进入handler之前调用，false就停止执行，true继续执行；权限验证，日志，事务
- postHandle：在渲染视图前调用；对请求域中的属性和视图进行修改
- afterCompletion：在handler执行完毕后调用；释放资源
- 多个拦截器，第一个方法正序执行，后两个方法逆序执行
- 第一个方法执行如果遇见false，立即停止，按序执行后两个方法

## 异常处理

- SpringMVC通过HandlerExceptionResolver接口的实现类来对异常进行处理
- 在配置\<mvc:annotation-drivent\>会默认注册三个实现类

### ExceptionHandlerExceptionResolver

- 使用@ExceptionHandler注解标记方法
- 方法的入参类型决定了能处理哪些异常，比如说接收Exception类型，就能处理所有的异常
- 如果想传值给前端，只能使用ModelAndView和Model，不能使用Map
- 优先匹配更相近的处理方法

#### 统一异常处理类

- 把异常处理统一在一个类中
- 这个类需要用@ControllerAdvice标记，类中方法和普通处理一样
- 先在发生异常的类中找@ExceptionHandler方法，找不到才会去全局异常处理类中寻找

### ResponseStatusExceptionResolver

- 使用@ResponseStatus来标记类，或者方法
- 接收一个value=状态码，reason

#### 标识在方法上

- 不管是否有错，都会返回注解中设定的状态码

#### 标识在类上

- 这个类通常是自定义的异常类
- 当我们抛出自定义的异常类的时候页面就会显示设定的状态码和reason

### DefaultHandlerExceptionResolver

- 对一些特殊的异常进行处理

## SimpleMappingExceptionResolver

- 对所有异常进行全局统一处理
- 在XML中配置指定的异常，和需要转到的页面