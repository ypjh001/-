# Spring IoC 概念及实战

# 说在前面

- **本章相关代码及笔记地址**：飞机票🚀
- **🌍**Github：**[**🚀Java超神之路：【🍔Java全生态技术学习笔记，一起超神吧🍔】](https://github.com/EayonLee/JavaGod)
- **🪐**CSDN：**[**🚀Java超神之路：【🍔Java全生态技术学习笔记，一起超神吧🍔】](https://blog.csdn.net/qq_20492277/article/details/114269863)

# 目录
- [Spring IoC](#spring-ioc)
- [说在前面](#说在前面)
- [目录](#目录)
- [一. 什么是Spring？](#一-什么是spring)
  - [1.1 什么是Spring？（面试题）](#11-什么是spring面试题)
  - [1.2 Spring优点（面试题）](#12-spring优点面试题)
- [二. 入门案例](#二-入门案例)
  - [2.1 下载Spring 5](#21-下载spring-5)
  - [2.2 创建Java工程](#22-创建java工程)
  - [2.3 导入Spring核jar包](#23-导入spring核jar包)
  - [2.4 创建User类](#24-创建user类)
  - [2.5 创建XML配置文件去实例化User Bean](#25-创建xml配置文件去实例化user-bean)
  - [2.6 测试](#26-测试)
- [三. Spring-IoC控制反转 相关概念](#三-spring-ioc控制反转-相关概念)
  - [3.1 什么是IoC（面试）](#31-什么是ioc面试)
  - [3.2 IoC底层原理（面试）](#32-ioc底层原理面试)
  - [3.3 Spring提供IoC容器实现的两种方式（Spring的两个核心接口）（面试）](#33-spring提供ioc容器实现的两种方式spring的两个核心接口面试)
  - [3.4 ApplicationContext接口主要的实现类（面试）](#34-applicationcontext接口主要的实现类面试)
  - [3.5 Spring中Bean的作用域（面试）](#35-spring中bean的作用域面试)
- [四. IoC Bean管理 - 基于XML方式](#四-ioc-bean管理---基于xml方式)
  - [4.1 什么是Bean管理？](#41-什么是bean管理)
  - [4.2 创建Bean](#42-创建bean)
  - [4.3 属性注入](#43-属性注入)
    - [4.3.1 setter方式](#431-setter方式)
    - [4.3.2 构造函数方式](#432-构造函数方式)
    - [4.3.3 属性注入 - 注入外部Bean](#433-属性注入---注入外部bean)
    - [4.3.4 属性注入 - 注入内部Bean](#434-属性注入---注入内部bean)
    - [4.3.5 属性注入 - 集合属性](#435-属性注入---集合属性)
  - [4.4 FactoryBean（工厂Bean）](#44-factorybean工厂bean)
    - [4.4.1 第一步：创建工厂Bean](#441-第一步创建工厂bean)
    - [4.4.2 第二步：在XML中定义Bean](#442-第二步在xml中定义bean)
    - [4.4.3 第三步：测试](#443-第三步测试)
  - [4.5 Bean的作用域（单例/多例）](#45-bean的作用域单例多例)
    - [4.5.1 验证Spring默认情况下的Bean是单例的](#451-验证spring默认情况下的bean是单例的)
    - [4.5.1 修改Spring Bean默认单例为多例](#451-修改spring-bean默认单例为多例)
  - [4.6 自动装配（自动注入）](#46-自动装配自动注入)
    - [4.6.1 手动装配](#461-手动装配)
    - [4.6.2 自动装配](#462-自动装配)
- [五. IoC Bean管理 - 基于注解方式](#五-ioc-bean管理---基于注解方式)
  - [5.1 Spring中创建Bean的注解](#51-spring中创建bean的注解)
  - [5.2 基于注解实现Bean创建](#52-基于注解实现bean创建)
    - [5.2.1 引入依赖](#521-引入依赖)
    - [5.2.2 开启组件扫描](#522-开启组件扫描)
    - [5.2.3 创建类 添加注解](#523-创建类-添加注解)
    - [5.2.4 测试](#524-测试)
  - [5.3 基于注解实现属性自动注入](#53-基于注解实现属性自动注入)
    - [5.3.1 @AutoWired](#531-autowired)
    - [5.3.2 @Qualifie](#532-qualifie)
    - [5.3.3 @Resource](#533-resource)
    - [5.3.4 @Value](#534-value)
- [六. 通过配置类完全替代XML文件](#六-通过配置类完全替代xml文件)
  - [6.1 创建配置类](#61-创建配置类)
  - [6.2 测试](#62-测试)


# 一. 什么是Spring？

## 1.1 什么是Spring？（面试题）

Spring是一个设计层框架，有两个核心：

**IOC控制反转**，目的是降低耦合度，它是基于工厂设计模式，所谓控制反转就是将自己手动new对象的操作交给Spring容器来完成。和控制反转配套使用的还有一个DI依赖注入。我们可以进行构造函数注入、属性注入（getter/setter）等。最常用的还是属性注入。可以注入各种类型：Map、List、Properties。注入可以通过ByType和ByName分别按照类型和名字进行自动注入。Spring中的Bean支持单例和原型两种方式，默认是单例的。可以通过Scope="seingleton"，Scope="prototype"来配置。所谓单例：即至始至终在JVM中都只有一个该类的实例。所谓原型：也叫多例，就是每次都会创建一个新的对象。

**AOP面向切面编程**，不修改源代码进行方法增强，AOP是OOP（面向对象编程）的延续，主要用于日志记录、性能统计、安全控制、事务处理等方面。它是基于代理设计模式，而代理设计模式又分为静态代理和动态代理，静态代理比较简单就是一个接口，分别由一个真实实现和一个代理实现，而动态代理分为基于接口的JDK动态代理和基于类的cglib的动态代理，咱们正常都是面向接口开发，所以AOP使用的是基于接口的JDK动态代理。

**注：POJO和JavaBean的区别**

- POJO 和JavaBean是我们常见的两个关键字，一般容易混淆，POJO全称是Plain Ordinary Java Object / Pure Old Java Object，中文可以翻译成：普通Java类，具有一部分getter/setter方法的那种类就可以称作POJO，但是JavaBean则比 POJO复杂很多， Java Bean 是可复用的组件，对 Java Bean 并没有严格的规范，理论上讲，任何一个 Java 类都可以是一个 Bean 。但通常情况下，由于 Java Bean 是被容器所创建（如 Tomcat) 的，所以 Java Bean 应具有一个无参的构造器。

- 通常 Java Bean 还要实现 Serializable 接口用于实现 Bean 的持久性。 Java Bean 是不能被跨进程访问的。JavaBean是一种组件技术，就好像你做了一个扳子，而这个扳子会在很多地方被拿去用，这个扳子也提供多种功能(你可以拿这个扳子扳、锤、撬等等)，而这个扳子就是一个组件。一般在web应用程序中建立一个数据库的映射对象时，我们只能称它为POJO。POJO(Plain Old Java Object)这个名字用来强调它是一个普通java对象，而不是一个特殊的对象，其主要用来指代那些没有遵从特定的Java对象模型、约定或框架（如EJB）的Java对象。理想地讲，一个POJO是一个不受任何限制的Java对象（除了Java语言规范）。

## 1.2 Spring优点（面试题）

- **IOC可以方便解耦，简化开发**：原来我们需要手动new对象，现在我们只需要将创建对象交给IOC并且管理对象之间的关系从而降低耦合。
- **Aop编程支持**：不改变源代码进行方法（功能）增强
- **方便程序测试**：Spring对Junit的支持可以通过注解方便的测试Spring程序
- **方便和其他框架进行整合**：方便整合MyBatis、SpringMVC、Hibernate、Quartz等等。
- **方便进行事务操作**：通过Spring声明式事务灵活的进行事务控制，提高开发效率
- **降低 API开发难度**：Spring对一些难用的Java API进行了简易封装，如JDBC、JavaMail



# 二. 入门案例

## 2.1 下载Spring 5

下载地址：https://repo.spring.io/release/org/springframework/spring/

![image-20210819105019708](https://cdn.jsdelivr.net/gh/EayonLee/IMG-Cloud@master/data/image-20210819105019708.png)

下载完成后，解压如下：

![image-20210819104957397](https://cdn.jsdelivr.net/gh/EayonLee/IMG-Cloud@master/data/image-20210819104957397.png)

## 2.2 创建Java工程

![image-20210819105100148](https://cdn.jsdelivr.net/gh/EayonLee/IMG-Cloud@master/data/image-20210819105100148.png)

## 2.3 导入Spring核jar包

由于现在只做入门案例，所以只导入Spring的基础核心jar包即可。

那我们通过上面Spring架构图中可得知 Spring的核心为4个

![image-20210819105148588](https://cdn.jsdelivr.net/gh/EayonLee/IMG-Cloud@master/data/image-20210819105148588.png)

那么我们就可以从libs文件夹中取出如上几个Jar包等待导入进工程

![image-20210819105213141](https://cdn.jsdelivr.net/gh/EayonLee/IMG-Cloud@master/data/image-20210819105213141.png)

commons-logging-1.1.1.jar（日志）需要单独下载：http://commons.apache.org/proper/commons-logging/download_logging.cgi

**创建存放jar包的lib文件夹，并将4个jar包添加**

![image-20210819105235247](https://cdn.jsdelivr.net/gh/EayonLee/IMG-Cloud@master/data/image-20210819105235247.png)

**将jar包导入项目**

![image-20210819105253684](https://cdn.jsdelivr.net/gh/EayonLee/IMG-Cloud@master/data/image-20210819105253684.png)

![image-20210819105310891](https://cdn.jsdelivr.net/gh/EayonLee/IMG-Cloud@master/data/image-20210819105310891.png)

![image-20210819105322813](https://cdn.jsdelivr.net/gh/EayonLee/IMG-Cloud@master/data/image-20210819105322813.png)

## 2.4 创建User类

![image-20210819105709786](https://cdn.jsdelivr.net/gh/EayonLee/IMG-Cloud@master/data/image-20210819105709786.png)

## 2.5 创建XML配置文件去实例化User Bean

![image-20210819105536125](https://cdn.jsdelivr.net/gh/EayonLee/IMG-Cloud@master/data/image-20210819105536125.png)

## 2.6 测试

![image-20210819105558880](https://cdn.jsdelivr.net/gh/EayonLee/IMG-Cloud@master/data/image-20210819105558880.png)



# 三. Spring-IoC控制反转 相关概念

## 3.1 什么是IoC（面试）

**IOC控制反转**，目的是降低耦合度，它是基于工厂设计模式，所谓控制反转就是将自己手动new对象的操作交给Spring容器来完成。和控制反转配套使用的还有一个DI依赖注入。我们可以进行构造函数注入、属性注入等。最常用的还是属性注入。可以注入各种类型：Map、List、Properties。注入可以通过ByType和ByName分别按照类型和名字进行自动注入。Spring中的Bean支持单例和原型两种方式，默认是单例的。可以通过Scope="seingleton"，Scope="prototype"来配置。所谓单例：即至始至终在JVM中都只有一个该类的实例。所谓原型：也叫多例，就是每次都会创建一个新的对象。

## 3.2 IoC底层原理（面试）

**IOC底层主要实用技术**：xml解析、工厂设计模式、反射

![image-20210819105846881](https://cdn.jsdelivr.net/gh/EayonLee/IMG-Cloud@master/data/image-20210819105846881.png)

**上图解释**：

比如我们想要在UserServlet中使用UserService中的方法，我们需要在UserServlet中去new UserService，但是这样耦合度太高了。我们如果使用IoC来托管Bean的话就是如下情况

我们在xml文件中去通过``<bean id="user" class="com.eayon.service.UserService"> ``标签配置需要实例化的对象，IoC的底层其实就是通过工厂类（工厂设计模式），去解析xml文件中配置需要实例化对些的bean标签（如使用dom4j解析xml），从而获取到bean标签中的class属性值，即是需要实例化对象的Class全路径，然后就可以通过Class.forName()获取到他的字节码文件，然后通过newInstance()方法通过反射创建UserService对象并返回。

这样UserServlet类中的每个方法就不需要一个个去new对象了，直接通过工厂去获取就可以。就算UserService对象的路径变了，我们也只需要修改xml配置文件中该类的class属性即可。

## 3.3 Spring提供IoC容器实现的两种方式（Spring的两个核心接口）（面试）

**IOC 思想基于 IOC 容器完成，IOC 容器底层就是对象工厂**

（1）**BeanFactory接口**：IOC容器基本实现，是Spring内部用接口，不提供给开发人员进行使用

​		**特点**：加载配置文件时候不会创建对象，在使用getBean()获取对象时才会创建对象。

![image-20210819105958728](https://cdn.jsdelivr.net/gh/EayonLee/IMG-Cloud@master/data/image-20210819105958728.png)

（2）**ApplicationContext接口**：BeanFactory接口的子接口，提供更多更强大的功能，提供给开发人员使用

​		**特点**：加载配置文件时候就会把在配置文件对象进行创建

![image-20210819110020048](https://cdn.jsdelivr.net/gh/EayonLee/IMG-Cloud@master/data/image-20210819110020048.png)

## 3.4 ApplicationContext接口主要的实现类（面试）

- **FileSystemXmlApplicationContext**：从XML文件中去加载bean，没有盘符的是项目工作路径,即项目的根目录;有盘符表示的是文件绝对路径.
- **ClassPathXmlApplicationContext**：从XML文件中去加载bean，默认就是指项目的classpath路径下面。如果要使用绝对路径,需要加上file:前缀表示这是绝对路径;

**备注：通过Ctrl + H 可以查看ApplicationContext的结构**

![image-20210819110056043](https://cdn.jsdelivr.net/gh/EayonLee/IMG-Cloud@master/data/image-20210819110056043.png)

## 3.5 Spring中Bean的作用域（面试）

- **singleton**：单例，容器中只有一个实例**（默认）**
- **prototype**：多例，每个请求都会创建一个实例
- **request**：它是为每个请求创建一个实例，请求完后bean会失效进行垃圾回收
- **session**：它是确保每个session中都有一个bean实例，当session过期后bean会失效

**singletion和prototype的区别**：

除了单例和多例的区别外还有实例化bean时机的区别，singleton则在加载xml文件时就去实例化，而prototype则是在getBean()的时候实例化



# 四. IoC Bean管理 - 基于XML方式

## 4.1 什么是Bean管理？

**Bean管理分为**：

- 创建对象
- 属性注入（创建对象的时候，为类中属性设置属性值）

**Bean管理的操作有两种方式**

- 基于xml配置文件方式实现创建对象和属性注入
- 基于注解方式实现创建对象和属性注入

## 4.2 创建Bean

![image-20210819110300141](https://cdn.jsdelivr.net/gh/EayonLee/IMG-Cloud@master/data/image-20210819110300141.png)

在xml文件中使用Bean标签，在标签中添加对应的属性，就可以实现对象的创建

**Bean标签中的属性**：

- **id**：该实例化对象的标识
- **class**：实例化对象的类全路径
- **name**：和id属性作用相同，区别在于name可以加特殊符号，id不可以（不常用）

**特点**：

- 使用xml的bean标签创建对象默认使用该对象的无参构造去创建，所以如果该对象没有生成无参构造会实例化失败

## 4.3 属性注入

### 4.3.1 setter方式

**Book实体**

![image-20210819110542025](https://cdn.jsdelivr.net/gh/EayonLee/IMG-Cloud@master/data/image-20210819110542025.png)

**bean.xml配置文件**

**注意**：想要使用setter注入的话该实体一定要有set方法

![image-20210819110355959](https://cdn.jsdelivr.net/gh/EayonLee/IMG-Cloud@master/data/image-20210819110355959.png)

**测试**

![image-20210819110442380](https://cdn.jsdelivr.net/gh/EayonLee/IMG-Cloud@master/data/image-20210819110442380.png)

### 4.3.2 构造函数方式

**order订单实体**

![image-20210819110631420](https://cdn.jsdelivr.net/gh/EayonLee/IMG-Cloud@master/data/image-20210819110631420.png)

**bean.xml配置文件**

**注意**：想要使用狗咱函数注入的话该实体一定要有有参构造

![image-20210819110645122](https://cdn.jsdelivr.net/gh/EayonLee/IMG-Cloud@master/data/image-20210819110645122.png)

**测试**

![image-20210819110659431](https://cdn.jsdelivr.net/gh/EayonLee/IMG-Cloud@master/data/image-20210819110659431.png)

### 4.3.3 属性注入 - 注入外部Bean

比如现在有个实体叫 School，这个实体中有个属性是Book，那么我们该如何去使用xml方式去实例化并属性注入School呢？

**School实体**

![image-20210819110725131](https://cdn.jsdelivr.net/gh/EayonLee/IMG-Cloud@master/data/image-20210819110725131.png)

**bean.xml配置文件**

**注意**：这里我是用setter方法注入，使用构造函数的话也是一样的

![image-20210819110739068](https://cdn.jsdelivr.net/gh/EayonLee/IMG-Cloud@master/data/image-20210819110739068.png)

**测试**

![image-20210819110757867](https://cdn.jsdelivr.net/gh/EayonLee/IMG-Cloud@master/data/image-20210819110757867.png)

### 4.3.4 属性注入 - 注入内部Bean

**Book实体（和上面相同）**

![image-20210819110837664](https://cdn.jsdelivr.net/gh/EayonLee/IMG-Cloud@master/data/image-20210819110837664.png)

**School实体（和上面相同）**

![image-20210819110850572](https://cdn.jsdelivr.net/gh/EayonLee/IMG-Cloud@master/data/image-20210819110850572.png)

**bean.xml配置文件**

**注意**：这里我是用setter方法注入，使用构造函数的话也是一样的

![image-20210819110903901](https://cdn.jsdelivr.net/gh/EayonLee/IMG-Cloud@master/data/image-20210819110903901.png)

**测试**

![image-20210819110919779](https://cdn.jsdelivr.net/gh/EayonLee/IMG-Cloud@master/data/image-20210819110919779.png)

### 4.3.5 属性注入 - 集合属性

**部门实体（包含了List、数据、Map）**

![image-20210819110954059](https://cdn.jsdelivr.net/gh/EayonLee/IMG-Cloud@master/data/image-20210819110954059.png)

**bean.xml配置文件**

**注意**：这里我是用setter方法注入，使用构造函数的话也是一样的

![image-20210819111009972](https://cdn.jsdelivr.net/gh/EayonLee/IMG-Cloud@master/data/image-20210819111009972.png)

**测试**

![image-20210819111026438](https://cdn.jsdelivr.net/gh/EayonLee/IMG-Cloud@master/data/image-20210819111026438.png)

## 4.4 FactoryBean（工厂Bean）

**区别**：

​	**普通bean**：上面的所有我们在xml中配置的bean返回值类型就是当前bean的类型

​	**工厂bean**：还有一种bean就是工厂bean，他在xml中配置的bean的返回值类型就可以不是当前bean类型

### 4.4.1 第一步：创建工厂Bean

该类一定要实现FactoryBean接口

```java
//需要在FactoryBean的泛型中定义需要返回的bean对象
public class MyBean implements FactoryBean<User> {

    /**
     * 定义返回bean
     */
    @Override
    public User getObject() throws Exception {
        User user = new User();
        user.setName("张三");
        return user;
    }
    
    @Override
    public Class<?> getObjectType() {
        return null;
    }
}
```

### 4.4.2 第二步：在XML中定义Bean

```java
<!--工厂bean 返回bean类型非当前定义类型-->
<bean id="myBean" class="com.eayon.entity.MyBean"></bean>
```

### 4.4.3 第三步：测试

![image-20210819111220706](https://cdn.jsdelivr.net/gh/EayonLee/IMG-Cloud@master/data/image-20210819111220706.png)

## 4.5 Bean的作用域（单例/多例）

在Spring中的bean是分为单例和多例的，那么我们同样可以在创建Bean的时候去进行控制

**在不做任何配置的情况下Spring创建出来的Bean是单例的。**

### 4.5.1 验证Spring默认情况下的Bean是单例的

如果输出的User对象地址是同一个则说明是单例

**xml文件中定义user bean**

![image-20210819111324681](https://cdn.jsdelivr.net/gh/EayonLee/IMG-Cloud@master/data/image-20210819111324681.png)

**测试**

![image-20210819111341594](https://cdn.jsdelivr.net/gh/EayonLee/IMG-Cloud@master/data/image-20210819111341594.png)

### 4.5.1 修改Spring Bean默认单例为多例

**xml文件中定义user bean**

我们可以通过``Scope = "singleton"`` 或者`` Scope = "prototype"`` 修改bean为单例（singleton）或者多例（prototype）

并且如果设置成singleton则在加载xml文件时就去实例化，而prototype则是在getBean()的时候实例化

![image-20210819111436998](https://cdn.jsdelivr.net/gh/EayonLee/IMG-Cloud@master/data/image-20210819111436998.png)

**测试**

![image-20210819111451671](https://cdn.jsdelivr.net/gh/EayonLee/IMG-Cloud@master/data/image-20210819111451671.png)

## 4.6 自动装配（自动注入）

### 4.6.1 手动装配

我们上面所有的操作都是手动装配，这里再演示一次 让大家好理解

**实体**

![image-20210819111526118](https://cdn.jsdelivr.net/gh/EayonLee/IMG-Cloud@master/data/image-20210819111526118.png)

![image-20210819111534163](https://cdn.jsdelivr.net/gh/EayonLee/IMG-Cloud@master/data/image-20210819111534163.png)

**xml文件**

其实就是和之前的操作是一样的，手动装配外部bean

![image-20210819111552636](https://cdn.jsdelivr.net/gh/EayonLee/IMG-Cloud@master/data/image-20210819111552636.png)

**测试**

![image-20210819111605807](https://cdn.jsdelivr.net/gh/EayonLee/IMG-Cloud@master/data/image-20210819111605807.png)

### 4.6.2 自动装配

**实体相同 不做演示**

**XML文件**

![image-20210819111629232](https://cdn.jsdelivr.net/gh/EayonLee/IMG-Cloud@master/data/image-20210819111629232.png)

**测试**

![image-20210819111642752](https://cdn.jsdelivr.net/gh/EayonLee/IMG-Cloud@master/data/image-20210819111642752.png)



# 五. IoC Bean管理 - 基于注解方式

## 5.1 Spring中创建Bean的注解

- @Controller：常用于Web层
- @Service：常用于Service层
- @Repository：常用于持久层
- @Component：通用

其实四个注解功能都是一样的，都可以创建Bean实例，只不过是一种规范而已。

## 5.2 基于注解实现Bean创建

### 5.2.1 引入依赖

![image-20210819111745348](https://cdn.jsdelivr.net/gh/EayonLee/IMG-Cloud@master/data/image-20210819111745348.png)

### 5.2.2 开启组件扫描

在xml文件中添加开启组件扫描，去扫描我们加了注解的类

**备注：xml文件头必须为如下格式**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                            http://www.springframework.org/schema/context  http://www.springframework.org/schema/context/spring-context.xsd">

    <!--开启组件扫描   扫描com.eayon包下所有带有注解的类 然后去实例化-->
    <context:component-scan base-package="com.eayon"></context:component-scan>

</beans>
```

### 5.2.3 创建类 添加注解

![image-20210819111830901](https://cdn.jsdelivr.net/gh/EayonLee/IMG-Cloud@master/data/image-20210819111830901.png)

### 5.2.4 测试

![image-20210819111844856](https://cdn.jsdelivr.net/gh/EayonLee/IMG-Cloud@master/data/image-20210819111844856.png)

## 5.3 基于注解实现属性自动注入

- @AutoWired：默认是按照ByType进行注入，如果当前Type类型个数大于1的话则按照ByName进行注入。如果声明按照ByName进行注入需要和@Qualifier注解一起进行使用。
- @Qualifier：根据ByName属性名称进行自动注入，必须要和@AutoWired结和使用
- @Resource：默认按照ByName进行注入，如果注入失败，则按照ByType进行注入
- @Value：注入普通类型属性，如String...

### 5.3.1 @AutoWired

默认是按照ByType进行注入，如果当前Type类型个数大于1的话则按照ByName进行注入。如果声明按照ByName进行注入需要和@Qualifier注解一起进行使用。

**UserService**

![image-20210819111932337](https://cdn.jsdelivr.net/gh/EayonLee/IMG-Cloud@master/data/image-20210819111932337.png)

**UserDao**

![image-20210819111944850](https://cdn.jsdelivr.net/gh/EayonLee/IMG-Cloud@master/data/image-20210819111944850.png)

**测试UserService是否通过注解自动注入了UserDao**

![image-20210819111958089](https://cdn.jsdelivr.net/gh/EayonLee/IMG-Cloud@master/data/image-20210819111958089.png)

### 5.3.2 @Qualifie

根据ByName属性名称进行自动注入，必须要和@AutoWired结和使用

**UserService**

![image-20210819112021658](https://cdn.jsdelivr.net/gh/EayonLee/IMG-Cloud@master/data/image-20210819112021658.png)

**UserDao**

![image-20210819112045944](https://cdn.jsdelivr.net/gh/EayonLee/IMG-Cloud@master/data/image-20210819112045944.png)

**测试UserService是否通过注解自动注入了UserDao**

![image-20210819112058729](https://cdn.jsdelivr.net/gh/EayonLee/IMG-Cloud@master/data/image-20210819112058729.png)

### 5.3.3 @Resource

默认按照ByName进行注入，如果注入失败，则按照ByType进行注入

**UserService**

![image-20210819112122037](https://cdn.jsdelivr.net/gh/EayonLee/IMG-Cloud@master/data/image-20210819112122037.png)

**UserDao**

![image-20210819112135830](https://cdn.jsdelivr.net/gh/EayonLee/IMG-Cloud@master/data/image-20210819112135830.png)

**测试UserService是否通过注解自动注入了UserDao**

![image-20210819112150153](https://cdn.jsdelivr.net/gh/EayonLee/IMG-Cloud@master/data/image-20210819112150153.png)

### 5.3.4 @Value

**UserService**

![image-20210819112237369](https://cdn.jsdelivr.net/gh/EayonLee/IMG-Cloud@master/data/image-20210819112237369.png)

**测试**

![image-20210819112254263](https://cdn.jsdelivr.net/gh/EayonLee/IMG-Cloud@master/data/image-20210819112254263.png)



# 六. 通过配置类完全替代XML文件

如上我们通过注解可以完全替代在XML中编写bean标签去实例化bean的操作和属性注入的操作，

但是我们还是需要在XML中编写标签去扫描我们增加注解的类才能去实例化，其实我们可以通过配置类去解决。

## 6.1 创建配置类

![image-20210819112335230](https://cdn.jsdelivr.net/gh/EayonLee/IMG-Cloud@master/data/image-20210819112335230.png)

这个配置类就等同于如下XML配置

![image-20210819112348630](https://cdn.jsdelivr.net/gh/EayonLee/IMG-Cloud@master/data/image-20210819112348630.png)

## 6.2 测试

测试和之前有所不同，因为我们不需要再去加载XML文件了，而是去加载我们的配置类

![image-20210819112407552](https://cdn.jsdelivr.net/gh/EayonLee/IMG-Cloud@master/data/image-20210819112407552.png)





















