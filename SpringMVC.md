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

- 只能修饰类
- ModelMap设置值的时候，将模型数据也加入Session中
- 可以根据name来放入，也可以同时根据type来放入

## @ModelAttribute

- @ModelAttribute标记的方法会在每一个目标方法被调用前调用
- 应用场景：要修改一个对象的几个值（有一部分值不变），如果传入一个对象给mybatis，那样会直接修改了所有值；需要在映射表单值之前读取原来的对象，然后新值覆盖掉旧值

