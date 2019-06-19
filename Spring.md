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

### 自动装配

#### @Autowired

- 用在构造器和Setter方法上会自动装配参数上所需要的依赖
- 用在属性上会自动装配，可以不用写Setter方法了
- 默认为Required为true，没有匹配的bean会抛出异常；找到多个满足依赖的bean也会抛异常

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