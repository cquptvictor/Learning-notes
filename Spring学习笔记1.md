#   Spring学习笔记1

---

## Spring IoC容器

### ApplicationContext容器    

- src目录下的beans.xml：配置JavaBean
- **FileSystemXmlApplicationContext**: `ApplicationContext context= new FileSystemXmlApplicationContext("src/beans.xml");`src为根目录

- **ClassPathXmlApplicationContext**:`ApplicationContext context = new ClassPathXmlApplicationContext("Beans.xml");`classPath为根目录

- **WebXmlApplicationContext**
- 以上三个都会加载并初始化beans.xml中配置的所有JavaBean。
- 多个配置文件时，逗号分隔，或者传入数组

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

​			只有调用了**registerShutdownHook()**后才会触发

### 	 Bean的后置处理器

​			BeanPostProcessor 可以在bean对象实例化前和后对其进行操作   

​			在beans.xml中配置   <bean class="类" />

### 	  Bean的继承

​				指定父类，子类的值会覆盖父类的值

​				方式一：创建一个JavaBean类，在beans.xml中定义它，子类指定它为父类

​				方式二：不需要创建JavaBean类（因此也不能被实例化，指定abstract=true,否则创建容器的时候自动实例化这个Bean会报错），直接在beans.xml中定义它，子类指定它为父类；

## Spring依赖注入

所谓的依赖注入，其实是当一个bean实例引用到了另外一个bean实例时spring容器帮助我们创建依赖bean实例并注入（传递）到另一个bean中

### 基于构造函数的DI

- 当容器调用带有一组参数的类构造函数时，基于构造函数的 DI 就完成了，其中每个参数代表一个对其他类的依赖。
- 多个类依赖时，<constructor-arg ref=""/>的先后顺序，决定了传入构造函数的顺序
-  `<constructor-arg type="" value=""/>`和  `<constructor-arg index="" value=""/>`可以直接传递值
- index主要是来解决多个构造函数的参数一样，但是参数的顺序不一样的情况

### 基于setter的DI

- 当容器调用一个无参的构造函数或一个无参的静态 factory 方法来初始化你的 bean 后，通过容器在你的 bean 上调用设值函数，基于设值函数的 DI 就完成了。
- <property name="" ref=""/>来实现注入对象引用，<property name="" value=""/>来注入基本类型的值(注意它们的异同)
- p-namespace以更加简洁的方式，p:name=""普通值，p:name-ref=""表明是对另一个类的引用

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

