

﻿#   Spring学习笔记1

---

# Spring IoC容器

### ApplicationContext容器    

- src目录下的beans.xml：配置JavaBean
- **FileSystemXmlApplicationContext**: `ApplicationContext context= new FileSystemXmlApplicationContext("src/beans.xml");`src为根目录
- **ClassPathXmlApplicationContext**:`ApplicationContext context = new ClassPathXmlApplicationContext("Beans.xml");`classPath为根目录
- **WebXmlApplicationContext**
- 以上三个都会加载并初始化beans.xml中配置的所有JavaBean。
- 多个配置文件时，逗号分隔，或者传入数组
- 元数据有三种配置方式：XML文件,Java类,注解

### Bean的定义

![1558521938019](C:\Users\victor\AppData\Roaming\Typora\typora-user-images\1558521938019.png)

### 	Bean的作用域

![1558505398816](C:\Users\victor\AppData\Roaming\Typora\typora-user-images\1558505398816.png)  

 sigleton作用域:  Bean的默认作用域，在创建容器时就会同时创建bean对象。

 prototype作用域:每次获取Bean时会创建一个新的、不同的对象，创建容器时并没有创建bean对象



### 	Bean的生命周期

​			Bean的定义——Bean的初始化——Bean的使用——Bean的销毁 

​			在每个Bean中声明 **init-method** 和/或  **destroy-method** 指定初始化和销毁时需要调用的方法

​			或者在beans中配置**default-init-method**和**default-destory-method**

​			init在Bean被实例化完成后调用，destroy在Bean的生命周期结束前调用

​			只有调用了**registerShutdownHook()**后才会触发

### 	 Bean的后置处理器

​			需要bean实现BeanPostProcessor接口

​			BeanPostProcessor 可以在bean对象实例化前和后对其进行操作   

​			在beans.xml中配置   <bean class="类" />

### 	  Bean的继承

- ​				指定父类，子类的值会覆盖父类的值

- ​				方式一：创建一个JavaBean类，在beans.xml中定义它，子类指定它为父类

- ​				方式二：不需要创建JavaBean类（因此也不能被实例化，指定abstract=true,否则创建容器的时候自动实例化这个Bean会报错），直接在beans.xml中定义它，子类指定它为父类；


### Bean实例化的三种方法

#### 反射方法

最普通的在XML中进行的配置

#### 工厂方法

##### 静态工厂方法

- factory-method:允许我们调用一个指定的静态工厂方法，从而代替构造方法来创建一个类的实例(即指定静态工厂方法名)

- class：不再是Bean实例的实现类,而是Bean实例的静态工厂类

##### 工厂方法

- factory-bean:指定工厂方法所在的工厂类实例(即工厂方法bean的id，用法与ref类似)
- factory-method:还是指定工厂方法名

#### FactoryBean接口

- getObject()方法实现构造逻辑
- getObjectType()方法表明返回类型
- isSingleton()方法表明是否为单例模式
- 适用于实现逻辑很复杂的情况



## Spring依赖注入

​	所谓的依赖注入，其实是当一个bean实例引用到了另外一个bean实例时spring容器帮助我们创建依赖bean实例并注入（传递）到另一个bean中

### 基于构造函数的DI

- 当容器调用带有一组参数的类构造函数时，基于构造函数的 DI 就完成了，其中每个参数代表一个对其他类的依赖。

- 多个类依赖时`，<constructor-arg ref=""/>`的先后顺序，决定了传入构造函数的顺序

- `<constructor-arg type="" value=""/>`和  `<constructor-arg index="" value=""/>`可以直接传递值

- 在能根据参数类型找到合适匹配时，顺序不重要

- index主要是来解决多个构造函数的参数一样，但是参数的顺序不一样的情况

- 最好不要使用循环依赖

- 没有在xml中注入的话，会注入失败,程序启动不了

  

### 基于setter的DI

- 当容器调用一个无参的构造函数或一个无参的静态 factory 方法来初始化你的 bean 后，通过容器在你的 bean 上调用设值函数，基于设值函数的 DI 就完成了。
- <property name="" ref=""/>来实现注入对象引用，<property name="" value=""/>来注入基本类型的值(注意它们的异同)
- p-namespace以更加简洁的方式，p:name=""普通值，p:name-ref=""表明是对另一个类的引用
- 没有在xml中注入的话，默认注入null，程序正常启动

## 三种注入方式的比较

![preview](https://pic3.zhimg.com/v2-18515e584368359cfd00f2899740c77a_r.jpg)



### 注入集合

​	四种集合类型注入

![1558528737322](C:\Users\victor\AppData\Roaming\Typora\typora-user-images\1558528737322.png)

list:

	    <property name="">
	     <list>
	        <value></value>
	        <value></value>
	        <value></value>
	        <ref bean=""/>
	        <ref bean=""/>
	         '''
	     </list>
	  </property>
map:

      <property name="">
         <map>
            <entry key="" value=""/>
            <entry key="" value=""/>
            <entry key="" value=""/>
            <entry key="" value-ref=""/>
            <entry key="" value-ref=""/>
         </map>
      </property>
注入NULL值：   <property name=" "><null/></property>

## Spring自动装配

Spring 容器可以在不使用`<constructor-arg>`和`<property>` 元素的情况下**自动装配**相互协作的 bean 之间的关系，这有助于减少编写一个大的基于 Spring 的应用程序的 XML 配置的数量。

自动装配只能用于对bean的装配。

#### 自动装配模式

![1558575927228](C:\Users\victor\AppData\Roaming\Typora\typora-user-images\1558575927228.png)

#### 自动装配的局限性

![1558576032111](C:\Users\victor\AppData\Roaming\Typora\typora-user-images\1558576032111.png)

### 自动装配By_Name

- 即bean的id名和参数的变量名相同
- 在需要自动装配的bean中设置byName

### 自动装配by_type

- 在需要自动装配的bean中设置byType
- 即bean的类型和参数的类型名相同

### 自动装配contructor

类似于by_type，但应用于构造器

## Spirng基于注解的配置

配置`<context:annotation-config/>`

@Service,@Component,@Repository，@Controller注解中：Bean的名称派生自简单的类名但首字母小写

![1558580133110](C:\Users\victor\AppData\Roaming\Typora\typora-user-images\1558580133110.png)

### @Required

受影响的属性，值必须通过xml注入

### @Autowired

默认为byType自动连接，当byType找到多个实现类时会用byName连接

***Setter 中的 @Autowired*:**被视为byType自动连接

***属性中的 @Autowired*:**可视为不需要setter方法的自动连接

***构造函数中的 @Autowired：***对构造函数自动连接

***@Autowired 的（required=false）选项:***不强制要求通过xml注入

### @Resource

非spring的注解

作用相当于@autowired，但是默认为byName自动连接

### @Qualifier("id")

在beans.xml中同一个bean有多种配置时，通过id来指定选择哪种配置(byName)

## Spring基于Java的配置

选择ApplicationContext实现类中的AnnotationConfigApplicationContext

任然默认为单例模式

#### @Configuration

表示这个注解类可以使用 Spring IoC 容器作为 bean 定义的来源

#### @Bean

这个方法将返回一个对象，该对象应该等同于xml中配置的bean中标签,@Bean注解的方法名称等同于xml中的bean的id

### @import

@import 注解允许从另一个配置类中加载 @Bean 定义,这样实例化时，可以不用指定被import的类

### @scope

@scope注解设置bean的范围

## Spring中的事件处理

bean——实现泛型接口 *ApplicationListener<>*——参数类型和作用是下面四个Event——在xml文件中配置这些bean

![1559137396823](C:\Users\victor\AppData\Roaming\Typora\typora-user-images\1559137396823.png)

由于 Spring 的事件处理是单线程的，所以如果一个事件被发布，直至并且除非所有的接收者得到的该消息，该进程被阻塞并且流程将不会继续。因此，如果事件处理被使用，在设计应用程序时应注意。

## classpath和classpath*

- classpath是指打包后WEB-INF/classes目录，src目录下的文件会打包后放在classes目录下
- classpath：只会到你的classpath路径中查找文件
- classpath：不仅包含classes路径，还到jar文件中进行查找
- classpath*的使用：当项目中有多个classpath路径，并同时加载多个classpath路径下（此种情况多数不会遇到）的文件，*就发挥了作用，如果不加*，则表示仅仅加载第一个classpath路径

## war和war_exploded

war包用于发布的时候

war_exploded包用于开发环境，支持热部署，修改jsp后能直接看到效果

# Spring中的AOP

