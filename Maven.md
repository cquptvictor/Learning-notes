

# Maven

## Maven的目录

### bin

maven的运行脚本

### Boot

maven的类加载框架，使用该框架来加载自己的类库

### Conf

该目录的settings文件全局地限制maven的行为，用户目录下的settings文件只限制当前用户范围

### Lib

包含maven运行时所需要的类库

## Maven项目的目录结构

- src:项目的根目录

- -main:项目的主代码
- -test:项目的测试代码
- --java:项目的class文件

## Maven的使用

### mvn clean  compile

清理输出目录target/，并编译项目主代码

### mvn clean test

执行测试用例

### mvn clean package

默认将项目打包为jar；

maven在打包前会执行编译测试等操作；

main方法会被忽略

### mvn clean install

将项目输出的jar安装到本地的Maven仓库

**上述四个命令，依照顺序，每次执行后面的命令时都会重新执行一次前面的命令**

## Maven的配置

- ### groupId，artifactId，version


groupId定义了项目属于哪个组,main中的java包结构；artifactId定义了当前Maven项目在组中唯一的ID，项目的根目录名称

；version指定项目当前的版本

- ### 本地仓库与远程仓库

- ### settings.xml中通过该<mirrors/>设置镜像仓库

- ### settings.xml中通过<localRepository/>修改本地仓库的位置,默认在C盘

## Archetype

### 创建目录

archetype:generate

archetype:generate -DgroupId=? -DartifactId=? -Dversion=? -Dpackage=?

## Maven的生命周期

### clean

pre-clean执行清理前的工作

clean清理上一次构建生成的所有文件

post-clean执行清理后的文件

### default构建项目

compile test package  install

### site生成站点

pre-site生成项目站点前要完成的工作

site生成项目的站点文档

post-site生成项目站点后要完成的工作

site-deploy发布生成的站点到服务器上

## Pom.xml

### 依赖范围

compile(默认)、provided(测试和编译时有效）、runtime（测试和运行时有效），test（在测试时有效）

### 依赖的传递

a依赖b，b依赖c

### 依赖排除

<exclusion/>

### 依赖冲突

短路优先；

路径相同，先声明先优先

### 聚合和继承







