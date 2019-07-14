# Mybatis

## JDBC的缺陷

1. 创建连接存在硬编码
2. 执行statement时存在硬编码
3. 频繁的开闭数据库连接，会影响数据库的性能。

## Configuration

### properties标签

- 引入外部的properties文件，通过${}来取值，通常数据库连接信息的配置就放在.properties文件中

### settings标签

- 主要用于配置的设定

### typeAliase标签

- `<typeAliases>`: 单独配置类的别名，或者@Alias注解配置别名，默认为类名
- 通过package标签批量配置别名，但是要注意子包下相同类名的冲突，默认为首字母小写的类名

### typeHandlers标签

- 配置类型处理器，用于将数据库取出来的值转换为相应的Java类型
- 实现`org.apache.ibatis.type.TypeHandler` 接口，或继承org.apache.ibatis.type.BaseTypeHandler类来完成自己的类型处理器

## 环境配置

- 可以同时设置多个环境，但是需要指定一个默认的环境
- 一个SqlSessionFactory只能对应一种环境

#### transactionManager

JDBC或Managed事务管理器;或实现transactionFactory接口来完成自定义事务管理器

#### dataSource数据源

- type有UNPOOLED,POOLED,JNDI
- 如果要使用自定义的数据源，实现DataSource接口，方法返回自定义的数据源,type是全类名

## mappers标签

- 引入映射文件
- resource、class、url三种注册方式
- `<package name=""/>`:进行批量注册
- class方式注册非注解接口时，接口与对应的XML需在同一个包下

## 映射配置文件

### 增删改标签

- id：在命名空间中唯一的标识符。可以用来调用的SQL语句
- parameterType：传入参数的数据类型，可以不写，自动判断
- resultType：从这条语句中返回的期望类型的类的完全限定名或别名
- resultMap：外部映射的引用
- 返回值为int，返回受影响的行数
- 返回值是Boolean，返回执行的结果

### CRUD

默认需要手动提交

#### 增

```
<insert id="add" parameterType="domain.Student">
    INSERT INTO STUDENT (ID, NAME, SAL) VALUES (#{id},#{name},#{sal});
</insert>
```

#### 查


```
<select id="select" parameterType="int" resultMap="student">
    select * from student where id = #{id};
</select>
```

#### 删

```
<delete id ="delete" parameterType="int">
    delete from student where id = #{id};
</delete>
```

#### 改

```
<update id="update" parameterType="domain.Student">
    update student set id = #{id}, sal = #{sal}, name = #{name};
</update>
```

- 实体的属性不一定要全部应用到参数上，如果id设置为自增的，SQL插入语句去掉id，传入Student为参数也同样能够成功
- 增删改可以用相同的XML标签

#### useGeneratedKeys和keyProperty

- 仅对update和insert语句有效
- useGeneratedKeys： MyBatis 使用 JDBC 的 getGeneratedKeys 方法来取出由数据库内部生成的主键
- keyProperty：唯一标记一个属性，MyBatis 会通过 getGeneratedKeys 的返回值或者通过 insert 语句的 selectKey 子元素设置它的键值
- 合在一起使用可以把自增主键的值赋值给pojo对应自增主键的属性

### 参数

- 通过#{property,javaType=int,jdbcType=NUMERIC}来置入参数
- 通常不需要后两者，除非是HashMap

### 结果映射

- 对于复杂的映射需要使用resultMap，把列名映射到属性上
- id：映射主键，有助于提高整体性能
- result：映射普通属性
- constructor：实例化类时，注入结果到构造方法中，通常用于不可变类
- `collection` ： 一个复杂类型的集合
- `association` ： 一个复杂类型的关联；许多结果将包装成这种类型

### 关联

- 通过association标签实现

#### 嵌套Select查询

- select：指定嵌套的语句ID
- column：数据库中的列名，或者是列的别名，当做参数传给第二个查询
- column="{prop1=col1,prop2=col2}"：针对于复合主键
- 延迟加载来提升性能

#### 嵌套结果

- 在一个resultMap中放入association子标签，子标签内部设置另一个resultMap
- 如果不打算重用子resultMap，可以直接把结果映射作为子元素嵌套在内

### 集合

- 通过collection标签来实现
- 使用方法和association很相似
- ofType：表示集合中内容的类型

## 自动映射

1. 全局设置autoMappingBehavior,默认为Partial，对除了嵌套映射之外的属性进行自动映射，null为关闭自动映射
2. 当自动映射查询结果时，MyBatis 会获取结果中返回的列名并在 Java 类中查找相同名字的属性（忽略大小写）
3. a_cloumn -> aColumn符合驼峰命名法，mapUnderscoreToCamelCase=true,开启驼峰自动映射
4. resultMap进行自定义映射
5. 列名和属性名没有精准匹配，可以在sql语句中使用别名

## ResualtMap的应用

### 返回的pojo对象的某些属性类型是另一个pojo

1.使用级联属性

`<result column = "columnName" property = "innerPojo.innerPojoPropertry">`

 columnName是查询出来的列名，innerPojo是outerPojo的属性名，不是类型

2.使用`<association>`标签

- property是outerPojo中innerPojo的属性名

- javaType指定innerPojo对应的类，可以用别名
- 子标签的配置和resultMap一样

```
<association property="dep" javaType="department">
    <id column="did" property="id"/>
    <result column="department" property="department"/>
    <result column="id" property="uid"/>
</association>
```

3.使用association来进行分步查询

1. 先按照员工id查询员工信息
2. 根据查询员工信息中的d_id的值去部门表查出部门信息
3. 部门设置到员工中z
   	   

```
<association property="dep" select="selectDep" column="id"/>
```

- property:查询出来的结果封装到哪个属性

- select:指定分步查询的语句

- column：第一次查询出来的列作为参数传入分步查询

- 传多个参数的时候封装成map,`column = "{key = value}"`

### collection标签定义集合封装规则

```
<collection property="userAll" ofType="user"  fetchType="lazy">
    <id column="id" property="id"/>
    <result column="mail" property="mail"/>
    <result column="password" property="password"/>
</collection>
```

- property:查询出来的结果封装到哪一个属性
- ofType：集合中的元素的类型
- 外层的属性需要是一样的，内层属性不同，才能把内层封装为一个List，外层一样的话会报selectOne错误
- 可以通过select 和 cloumn属性来进行分步查询

### collection和association总结

- association用于一对一的情况
- 三种使用方式：分步查询，嵌套resultMap（复用版与非复用版）
- collection适用于一对多的情况
- 三种使用方式：分步查询，嵌套resultMap（复用版与非复用版）

## Mybatis接口式编程

- 定义一个接口；mappings文件的namespace是接口的全类名，方法的id是接口的方法名
- 这样传入的参数具有类型检查，而不是Object
- IDE可以进行代码补全
- 基本类型使用包装类，来避免空指针错误

## #{}占位符

- 单个参数时括号中随便写一个名字，但不能为空
- 多个参数会被封装成一个Map，key为param1——paramN，可以通过@Param来指定key
- 如果多个参数中有pojo，也有基本数据类型,paramN.Property来获得pojo中的值
- 如果参数为List，Set，数组，也会被封装成Map，通过collection[i],array[i],list[i]来获得值，也可以通过@Param来指定key
- 在参数是Map时，括号中为Map的key值
- 在参数是pojo时，括号中为pojo的属性名

## #{}和${}的区别与联系

- #{}和${}的使用方法没有什么区别
- #{}采用JDBC的预编译形式
- ${}是字符串拼接，并且会转义危险的输入值
- ${}通常用于对表名，字段名，排序字段等不能预编译的字段进行占位处理

## Mybatis缓存机制

### 一级缓存

- 默认开启的缓存，SqlSession级别的缓存
- 与数据库同一次会话期间查询到的数据放在本地缓存中，以后如果需要相同的数据，就会直接从缓存中拿
- 从缓存中获取的是对象的引用，如果修改了对象，使用相同的SQL从缓存中获得的是修改后的对象，并不是真实对象

#### 一级缓存失效情况

- 不同的sqlSession
- sqlSession相同，查询条件不同
- sqlSession相同，两次查询之间执行了增删改操作
- sqlSession相同，主动清除了缓存

### 二级缓存

- 需要手动配置和开启，基于namspace级别的缓存，通过缓存接口来实现 

#### 工作机制

- 一个会话查询一条数据，该数据会被保存在当前会话的以及缓存中
- 如果会话关闭，一级缓存中的数据会被存储到二级缓存中

### 使用步骤

1. 在全局配置文件中，开启二级缓存：`<setting name="cacheEnabled" value="true"/>`
2. 在mapper文件中使用二级缓存：`<cache/>`
3. pojo对象需要设置为可序列化的

### cache标签的属性

```
<cache
  eviction="FIFO"
  flushInterval="60000"
  size="512"
  readOnly="true"/>
```

#### eviction（清除策略）

- `LRU` – 最近最少使用：移除最长时间不被使用的对象。默认使用
- `FIFO` – 先进先出：按对象进入缓存的顺序来移除它们。
- `SOFT` – 软引用：基于垃圾回收器状态和软引用规则移除对象。
- `WEAK` – 弱引用：更积极地基于垃圾收集器状态和弱引用规则移除对象。

#### flushInterval（刷新间隔）

- 设置为任意的正整数，设置的值应该是一个以毫秒为单位的合理时间量。
- 默认情况是不设置，也就是没有刷新间隔，缓存仅仅会在调用语句时刷新。

#### size（引用数目）

- 默认值是 1024。
- 属性可以被设置为任意正整数，要注意欲缓存对象的大小和运行环境中可用的内存资源。

#### readOnly（只读）

- 默认为false
- true：返回的是缓存对象的引用，速度快，用于缓存对象不需要改变的时候
- false：返回的是缓存对象的拷贝，速度相对较慢，更加安全

#### type

- 指定第三方cache

### 与缓存有关的标签

- 全局配置文件中cacheEnabled：false会关闭二级缓存，但不影响一级缓存
- 每个增删改标签中useCache：false会不使用二级缓存，但不影响一级缓存
- flushCache：执行了sql语句后会清除一级和二级缓存，对于select默认为false，对于增删改默认为true
- SqlSession.clearCache()：只会清除一级缓存
- localCacheScope：默认为Session作用域，即基于sqlSession的一级缓存；设置为Statement表示不使用一级缓存
- `cache-ref`：对其它Cache配置的引用

## Mybatis与Spring的整合

- 需要mybatis-spring包
- 在Spring配置文件中通过\<mybatis-spring:scan  base-package = \>来进行扫描mybatis的接口

## 作用域和生命周期

- SqlSessionFactoryBuilder：一旦创建了SqlSessionFactory就不需要它了，最好是局部方法变量
- SqlSessionFactory：单例作用域
- SqlSession：非线程安全，请求作用域或方法作用域
- 映射器实例：方法作用域，最大作用域是和SqlSession相同