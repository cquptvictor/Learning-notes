# SpringMVC



## 配置文件

### web.xml

配置拦截器，拦截不同的url，并交给不同的dispatcher处理

### xx-dispatcher.xml

- xx为dispatcher的名字
- 默认为WEB-INF/xx-dispatcher.xml
- 可以在web.xml中修改默认路径
- 配置视图解析器
- 非注解方式，在此文件中配置url与不同的HandlerMapping
- 注解方式，在此文件中指定扫描哪些包的注解，并注册到上下文中
- <mvc:annotation-driven>会默认加载很多东西，例如注解映射器和注解适配器，json转换解析器

可以使用基于Java的方式来配置

## 两个上下文

- DispatcherServlet加载包含Web组件的bean，如控制器，视图解析器，以及处理器映射
- ContextLoaderListener加载应用中的其他bean，通常是驱动应用后端的中间层和数据层组件

## 注解

### @Controller

表示被注解的类是一个Controller

### @RequestMapping

配置url和请求的方法

类url+方法url

### @ControllerAdvice

集中代码，便于跨控制器共享

### @ExceptionHandler

处理发生在定义该方法所在类中的异常

### @ResponseBody

返回的内容直接写在响应正文中，相当于print出来

### @ModelAttribute(value="")

- 用在方法上可以将方法的返回值设置在域对象中,value指定了域对象的key值
- 用在参数列表的pojo类型前，将pojo放入域对象中，value指定了域对象的key值

## 返回格式

### ModelAndView

指定跳转的页面

设置jsp能获取的key-value

生命周期request等级

### Map

可以直接返回Map，jsp中可以获得map的值

生命周期request等级

### 

### String

根据视图解析器，指定返回到哪一个页面

## 参数处理

### 查询参数

- 在方法的参数列表中：`@RequestParam(value = "参数的id" , defaultValue="" ) T value`
- 不使用@RequestParam则要求形参和参数名相同
- 数据类型会自动从String转换为需要的

### 路径变量

- 设置路径变量：`@RequestMapping(value = "/home/{id}")`
- 在方法的参数列表中获取：`@PathVariable("id") int id`
- 如果占位符的名称与方法参数名相同，@PathVariable中不需要value属性

### 获取表单数据

- `public ModelAndView register(@ModelAttribute User user, ModelAndView modelAndView){`
- 在参数列表中声明一个对象，对象的属性会由表单的中同名参数来填充，@ModelAttribute可有可无
- 在对象的属性上加上校验注解，来限制输入

## 参数绑定

### 默认绑定

1. HttpServletRequest对象
2. HttpServletResponse对象
3. HttpSession对象
4. Model/ModelMap对象

在参数绑定过程中，如果遇到上面类型就直接进行绑定。也就是说，我们可以在controller的方法的形参中直接定义上面这些类型的参数，springmvc会自动绑定。

### 简单类型的绑定

形参和参数名相同，或者使用@RequestParam注解

### POJO类型的绑定

#### 简单POJO

- 参数名和POJO对象的属性名相同，仅针对基本类型
- 如果有一个属性是DATE类型，就需要程序员自己定义转换器了，实现convert接口

#### POJO中嵌套POJO

前端form表单名字定义为`嵌套pojo.attribute`

### 集合类型的绑定



## 控制器



## 异常处理

### 局部异常处理

*@ExceptionHandler*：处理所标记的方法所在的类中的异常，前提是抛出的异常和方法参数的异常相同或者是它的子类。

### 全局异常处理

#### 基于Java的配置

*@ContollerAdvice*：标记这是一个全局异常处理的类

*@ExceptionHandler*:根据不同的异常找到不同的异常处理方法

要配置`<mvc:annotation-driven/>`全局配置才会有效

#### 基于XML的配置



## 重定向与forward

`return "redirect:url"` 

`return "forward:url"`

相当于Servlet中的redirect与forward的区别

## 数据回显

- spring自动把pojo数据加入到request域中
- @ModelAttribute将方法的返回值传回页面

## 实战

### 文件上传

通过Commons FileUpload，在dipatcher中配置多部分解析器；**临时文件夹在Tomcat中**，不在你的项目中

通过Servlet3.1多部分请求流程，在web.xml中配置相关限制



### 乱码解决

#### 在web.xml中添加字符过滤器，解决返回值和请求值有中文

```
<filter>
    <filter-name>encodingFilter</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
        <param-name>encoding</param-name>
        <param-value>UTF-8</param-value>
    </init-param>
</filter>
<filter-mapping>
    <filter-name>encodingFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

#### 在dispatcher中配置只能解决@Responsebody的中文乱码

```
<mvc:annotation-driven>
<mvc:message-converters register-defaults="true">
    <bean class="org.springframework.http.converter.StringHttpMessageConverter">
        <property name="supportedMediaTypes" value="text/html;charset=UTF-8"></property>
    </bean>
</mvc:message-converters>
</mvc:annotation-driven>
```

### 拦截器

- preHandle:在进入handler之前调用
- postHandle：在返回ModelView前调用
- afterCompletion：在handler执行完毕后调用
- 在dispatcher.xml中依照声明顺序调用拦截器,先执行preHandle的拦截器会后执行postHandle和afterCompletion
- 第一个拦截器通过，第二个拦截器被拒绝，会执行第一个的preHandle和postHandle

```
<mvc:interceptors>
    <!--多个拦截器,顺序执行 -->
    <mvc:interceptor>
        <mvc:mapping path="/**" /><!-- 表示拦截所有的url，包括子url路径 -->
        <bean class="cn.itcast.ssm.interceptor.HandlerInterceptor1"></bean>
    </mvc:interceptor>
    <mvc:interceptor>
        <mvc:mapping path="/**" />
        <bean class="cn.itcast.ssm.interceptor.HandlerInterceptor2"></bean>
    </mvc:interceptor>
    </mvc:interceptors>
```