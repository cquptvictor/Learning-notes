# Spring

## IOC

### spring容器的实现

- BeanFactory：bean工厂，是最简单的容器，提供基本的DI支持
- ApplicationContext：应用上下文,基于BeanFactory构建，并提供应用框架级别的服务，通常使用这个

### spring多种应用上下文

- AnnotationConfigApplicationContext：从一个或多个Java的配置类汇总加载Spring应用上下文
- AnnotationConfigWebApplicationContext：从一个或多个基于Java的配置类中加载Spring Web应用上下文
- ClassPathXmlApplicationContext：从类路径下的一个或多个XML配置文件中加载上下文定义，相对classpath路径
- FileSystemXmlApplicationContext：从文件系统下的一个或多个XML配置文件中加载上下文，相对项目根目录
- XmlWebApplicationContext：从Web应用下的一个或多个XML配置文件中加载上下文定义
- 传入多个通过数组或者逗号分隔

### 向IOC中注册bean

- 基于XML：`<bean id = "" class=""/>`
- 基于注解:@Conponent以及它的三个版型:@Controller,@Service,@Dao

# 装配Bean

## 自动化装配bean

spring从两个角度来实现自动化装配

- 组件扫描：Spring会自动发现应用上下文中锁创建的bean
- 自动装配：Spring自动满足bean之间的依赖

### 组件扫描

- 基于XML：`<context:component-scan base-package=""/>`
- 基于注解：@ConponentScan默认会扫描与配置类相同的包，以及子包
- bean的默认id为类名的第一个字母小写

### 过滤扫描的内容

- \<context:component-scan base-package=""/\>的子标签\<context:exclude-filter/\>和\<context:include-filter\>指定需要扫描的bean
- 可以通过注解、类名和正则来过滤

### 自动装配

#### @Autowired

- 用在构造器和Setter方法上会自动装配参数上所需要的依赖
- 用在属性上会自动装配，可以不用写Setter方法了
- 默认为Required为true，没有匹配的bean会抛出异常；找到多个满足依赖的bean也会抛异常
- 默认为ByType自动连接

## 基于Java代码的装配

对于第三方的代码只能通过Java装配或XML装配了，通常会把JvaConfig放在单独的包中

配置灵活，只会受到Java语言本身的限制

### @Configuration

- 表明标注的类是一个配置类

### @Bean

- 表明该方法会返回一个注册在Spring应用上下文中的bean，方法体中是产生bean的逻辑实现
- bean的id可以注解中设置，也可以默认为方法名（不会首字母变为小写了）

## 通过XML代码装配Bean

### 注册一个bean

- `<bean id = "" class = ""/>`默认id为全类名+#0,数字用来计数相同类型的其他Bean

### 构造器初始化

- `<constructor-arg ref = />` ,`<constructor-arg value = />`

### Setter注入

- 通常来说强依赖使用构造器注入，可选依赖使用属性注入
- `<property name = value = />`

## 自动扫描和@Bean以及XML

- 两者都是向Spring上下文中注册bean
- 前者只是单纯的注册bean，后者还有注册的这个bean的实现逻辑
- 如果是第三方类，我们就只能通过@Bean来实现或者是XML来实现

## 混合配置

### JavaConfig互相混合

- `@Import({})`

### JavaConfig中导入XML

- `@ImportResource()`

### XML中导入JavaConfig

- `<bean class=/>`和普通的bean注册没有什么不同

### XML互相混合

- `<import resource=/>`

## 高级装配

### profile切换环境

- Spring只会创建profile激活的bean

#### Profile配置

- XML配置:在beans标签中嵌套beans

  ```
  <beans profile= >
  
  </beans>
  ```

- 注解配置：`@Profile`

#### Profile激活

- spring.profiles.active：配置激活哪些profile，优先级最高
- spring.profiles.default：配置默认激活的profile
- 多种方式设置这两个属性：例如在WEB.xml文件中设置

### 条件化Bean

- 只有在给定条件的计算结果为true时，才会创建这个bean

### 处理自动装配的歧义性

- byType自动连接可能有多个候选项

#### 设置首选的bean

- XML配置：bean标签中设置primary属性
- 注解配置：@Primary

#### 限定自动装配的bean

- Qualifer配置ID
- Qualifer指定装配的ID

### Bean的作用域

- 单例（Singleton）：在整个应用中，只创建一个bean实例
- 原型（Prototype）：每次注入或者通过Spring应用上下文获取的时候，都会创建一个新的bean实例、
- 会话（Session）：在Web应用中，为每个会话创建一个bean实例
- 请求（Request）：在Web应用中，为每个请求创建一个Bean实例

#### 会话和请求作用域的代理

- 一个单例bean需要注入请求或会话bean，但是这时请求或会话的作用域bean还不存在，就无法注入
- 通过注入一个代理，当要调用方法时才会去调用真正的bean的方法
- 分为针对于接口的代理和类的代理

### SpEL

- 使用Bean的ID来引用bean
- 调用方法和访问对象的属性
- 对值进行算术、关系和逻辑运算
- 正则表达式匹配
- 集合操作

#### T()运算符

- `#{T(System).currentTimeMillis()}`：在运行时，动态调用cuurentTimeMills()方法，获取当前时间
- T()将Sysytem视为Java中对应的类型
- 可以通过T()操作符来调用一些静态方法和静态常量

# AOP

- 通过AOP功能，可以把之前粉丝啊在应用各处的行为放入可重用的模块中，程序员需要显示地声明在何处如何应用该行为

## 术语

- 横切关注点：散布于应用中多处的功能被称为横切关注点，这些关注点从概念上是与业务逻辑相分离的
- 切面：横切关注点被模块化为特殊的类，这些类被称为切面；切面=切点+通知
- 通知：切面的工作被称为通知
- 连接点：在应用执行过程中能够插入切面的点
- 切点：切点的定义会匹配通知所有要织入的一个或多个连接点
- 引入：允许我们向现有的类添加新方法或属性
- 织入：把切面应用到目标对象并创建新的代理对象的过程

## 通知

- 前置通知：在目标方法被调用前通知
- 后置通知：在目标方法完成之后调用通知，此时不会关心方法的输出
- 返回通知：在目标方法**成功执行**后调用通知，可以在参数中接收目标方法的返回值
- 异常通知：在目标方法抛出异常后调用通知，可以在参数中接收特定类型的异常，如果接收的异常类型和抛出的不一致，则不能调用异常通知
- 环绕通知：通知包裹了被通知的方法，在被通知的方法调用之前和之后执行自定义的行为，通过`ProceedingJoinPoint对象的proceed()方法`把控制权交还给被通知的方法

## 通知中的参数

- `args(parameters)`:切点中定义的参数与切点方法中的参数名称是一样的
- 被通知方法的参数和通知中的**参数类型匹配，顺序一致**时才会调用通知

## 切点复用

- @Pointcut注解能够在@AspectJ切面内定义可重用的切点
- @Pointcut用在一个空的方法上（可以写，但没必要）
- 在切点上直接调用被@Pointcut修饰的方法就可以复用切点

## 被代理通知引入新功能

- 该功能可以在不修改原有的类的基础上，给被代理的通知添加新的方法

```
@DeclareParents(value = "com.cqupt.AOP.ArithmeticCalculatorImp",
                defaultImpl=LoggingExtraMethod.class)
public extraMethod loggingExtraMethod;//这必须是一个接口类型

```

- @DeclareParents注解所标注的静态属性指明了要引入的接口
- defaultImpl指定了接口的默认实现类
- value指定了哪种类型的bean要引入该接口
- @DeclareParents注解修饰的是一个属性，一个类型为接口的属性

## 切面的优先级

- 通过@Order设置优先级
- 数值越小（可以是负数），优先级越大

