## Spring注解开发

### 第一章 注解的基本概念

#### 1.什么是注解编程？

```java
	指的是在类或者方法上加入特定的注解（@XXX)，完成特定功能的开发。
	@Component
	public class XXX{}
```

#### 2.为什么要讲解注解编程

```markdown
1. 注解开发方便
	代码简洁，开发速度大大提高
2. Spring开发的潮流
	Spring 2.x引入注解 Spring 3.x完善注解 SpringBoot普及 推广注解编程。
```

#### 3.注解的作用

- 替换XML这种配置形式，简化配置

![](https://cdn.jsdelivr.net/gh/gwbiubiu/images/20210711085332.png)

- 替换接口，实现调用双方的契约性

```markdown
1. 通过注解的方式，在功能的调用者和提供者之间达成约定，进而进行功能的调用。因为注解更为方便灵活，所以在现实的开发中，更推荐使用注解的形式完成
```

![](https://cdn.jsdelivr.net/gh/gwbiubiu/images/20210711090531.png)

#### 4.Spirng注解的发展历程

```markdown
1. Spirng2.x开始支持注解编程 @Component @Service @Scope
	目的：提供这些注解只是为了在某些情况下简化XML的配置，作为XML的有益补充
2. Spring3.x @Configuration @Bean
	目的：彻底替换XML，基于纯注解编程
3. Spirng4.x SpringBoot
	提倡使用注解进行常见开发
```

#### 5.Spring注解开发的一个问题

```markdown
Spring基于注解进行配置后，还能否解耦合那？

在Spring框架应用注解时，如果对注解配置的内容不满意，可以通过Spring配置文件进行覆盖。
```

### 第二章 Spring基础注解（Spring 2.x）

```markdown
1. 这个阶段的注解，仅仅是简化XML的配置，并不能完全替代XML
```

- 搭建开发环境

```xml
<context:component-scan base-package="com.gewei"/>

作用：让Spring框架在设置包及其子包扫描对应的注解，使其生效。
```

### 对象创建相关注解

#### 2.1@Component

```markdown
作用：替换Spring配置文件中的Bean标签

注意：id属性 component注解提供了默认的设置方式，即单词首字母小写
	 class属性，通过反射获得class内容
```

![](https://cdn.jsdelivr.net/gh/gwbiubiu/images/20210711093946.png)

测试：

引入Junit测试单元

```xml
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.12</version>
    <scope>test</scope>
</dependency>
```

```java
@Test
public void test1(){
    ApplicationContext ctx = new ClassPathXmlApplicationContext("/SpringContext.xml");
    User user = (User) ctx.getBean("user");
    System.out.println("user+"+user);

}
```

##### 2.1.1 Component注解使用细节

- 如何显示的指定工厂创建的id值

```java
@Component("u")
```

- Spring配置文件覆盖注解配置内容

```xml
applicationContext.xml

<bean id = "u" class = "com.gewei.bean.Usesr" />
# id的值和class的值要和注解中的设置保持一致，这样才能达到覆盖的功能，如果id值不一致，那么会创建出一个新的对象，并不能实现覆盖的功能。
```

##### 2.1.2 Component衍生注解

```markdown
@Repository
@Service
@Controller
	注意：本质上这些衍生注解就是@Component 作用，细节以及用法完全一致
	目的：更加准确的表达一个类型的作用。
	
Spring整合Mybatis开发过程中，不适用@Reposity和Component 
```

#### 2.2 @Scope

```xml
作用：控制简单对象的创建次数
	注意：不添加@Scope Spring提供的默认值为singleton
<bean id=" " class = " " scope = "single|prototype"/>
```

```java
@Component
@Scope("singleton")
public class Customer {

}
```

```java
@Test
public void test2() {
    ApplicationContext ctx = new ClassPathXmlApplicationContext("/SpringContext.xml");
    Customer user1 = (Customer) ctx.getBean("customer");
    Customer user2 = (Customer) ctx.getBean("customer");
    System.out.println("customer+" + user1);
    System.out.println("customer+" + user2);

}
```

结果：

```
customer+com.gewei.scope.Customer@78a773fd
customer+com.gewei.scope.Customer@78a773fd
```

#### 2.3 @Lazy

```
作用：延迟创建单实例对象

<bean id = " " class = " " lazy = "false"/>	
```

```java
@Component
public class Account {
    public Account(){
        System.out.println("Account.account");
    }
}
```

```java
@Test
public void test3(){
    ApplicationContext ctx = new ClassPathXmlApplicationContext("/SpringContext.xml");
}
```

输出

```
Account.account
```

默认情况下，Spring工厂会直接创建需要加载的对象，如果我们想延迟创建单实例对象，这边可以直接使用@Lazy注解

在使用到需要对象时，才回去创建。

```java
@Test
public void test3(){
    ApplicationContext ctx = new ClassPathXmlApplicationContext("/SpringContext.xml");
    Account account = (Account) ctx.getBean("account");
}
```

### 生命周期相关注解

```markdown
1. 初始化相关方法 @PostConstruct
	InitalizingBean
	<bean init-method = "" />
2. 销毁方法 @PreDestroy
	DisposableBean
	<bean destory-method = ""/>
```

 @PostConstruct和@PreDestroy都在javax.annotation包下，这是java的一个扩展包。

```xml
<dependency>
    <groupId>javax.annotation</groupId>
    <artifactId>javax.annotation-api</artifactId>
    <version>1.3.2</version>
</dependency>
```

```java
@Component
public class Product {

    @PostConstruct
    public void myInit(){
        System.out.println("Product.myInit");
    }
    @PreDestroy
    public void myDestory(){
        System.out.println("Product.Destory");
    }
}
```

```java
@Test
public void test4(){
    ClassPathXmlApplicationContext ctx = new ClassPathXmlApplicationContext("/SpringContext.xml");
    ctx.close();
}
```

```
Product.myInit
Product.Destory
```

```markdown
	注意: 以上的两个注解并不是Spring提供的，而是JSR（JAVAEE 规范）520
    再一次验证了通过注解实现接口的契约性
```

### 注入 相关注解

- 用户自定义类型

#### @Autowire

传统的配置文件开发

![](https://cdn.jsdelivr.net/gh/gwbiubiu/images/20210711114059.png)

基于注解开发

![](https://cdn.jsdelivr.net/gh/gwbiubiu/images/20210711114359.png)

```java
@Repository
public class UserDaoImpl implements UserDao{
    @Override
    public void save() {
        System.out.println("UserDao.Impl");
    }
}
```

```java
@Service
public class UserServiceImpl implements UserService{

    private UserDao userDao;

    public UserDao getUserDao() {
        return userDao;
    }
    @Autowired
    public void setUserDao(UserDao userDao) {
        this.userDao = userDao;
    }

    @Override
    public void register() {
        userDao.save();
    }
}
```

```java
@Test
public void test5() {
    ClassPathXmlApplicationContext ctx = new ClassPathXmlApplicationContext("/SpringContext.xml");
    UserService userServiceImpl = (UserService) ctx.getBean("userServiceImpl");
    userServiceImpl.register();
}
```

```markdown
@AutoWired细节
	1.AutoWired注解基于类型进行注入
	基于类型注入：注入对象的类型，必须与目标成员变量类型相同或者是其子类（实现类）
	2.AutoWired 配合Qualifier注解基于名称进行注入  {了解即可}
			注入对象的id值必须与Qualifier注解中设置的名字相同
@Autowired
@Qualifier("userDaoImpl")
public void setUserDao(UserDao userDao) {
    this.userDao = userDao;
}
	3.@AutoWried注解放置位置
	a) 放置在对应成员变量的set方法上
	b) 直接把这个注解放置在成员变量上，Spring通过反射直接对成员变量进行注入（没有调用set方法）【推荐】
	
	4. JavaEE规范中类似功能的注解
	JSR 250 @Resource(name = "userDaoImpl") 基于名字进行注入
	@Autowired
	@Qualifier("userDaoImpl")
	注意：在使用Resource这个注解时，名字没有配对成功，那么会继续按照类型进行注入
	JSR330 @Inject作用@Autowired完全一致，基于类型进行注入
	<dependency>
            <groupId>javax.inject</groupId>
            <artifactId>javax.inject</artifactId>
            <version>1</version>
        </dependency>
```

#### @Value

![](https://cdn.jsdelivr.net/gh/gwbiubiu/images/20210711150211.png)

创建出init.properties文件，放在resource目录下

```properties
id = 24
name = gewei
```

在配置文件SpringContext.xml文件下配置：

```xml
<context:property-placeholder location="classpath:init.properties"/>
```

```java
@Value("${id}")
private Integer id;
@Value("${name}")
private String name;
```

##### @propertySource

```markdown
作用：替换了spring中 <context:property-placeholder location="classpath:init.properties"/>标签
```

```java
@Component
@PropertySource("classpath:/init.properties")
public class Category {

}
```

@Value使用细节

```
@Value不可以应用到静态成员变量上
	如果应用到静态成员变量上，那么会造成注入失败
	
@Value+properties这种方式，不可以注入集合类型
	这边Spring提供了YAML或YML（SpringBoot）进行集合的注入
```

### 注解扫描

```xml
<context:component-scan base-package="com.gewei"/>

当前包，及其子包
```

#### 1.排除方式

```xml
<context:component-scan base-package="com.gewei">
    <context:exclude-filter type="" expression=""/>
</context:component-scan>

spring的type提供了5种排除方式，分别为
    assignable：排除特定类型不进行扫描
	<context:component-scan base-package="com.gewei">
        <context:exclude-filter type="assignable" expression="com.gewei.bean.User"/>
    </context:component-scan>
    anntation：排除特定的注解不进行扫描
	<context:component-scan base-package="com.gewei">
        <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Service"/>
    </context:component-scan>
    aspectj
        <context:exclude-filter type="aspectj" expression="com.gewei.bean..*"/>
		包切入点：com.gewei.bean..*
		类切入点：*..User
    regex：正则表达式
    custom：自定义排除策略，一般在框架的底层开发中会用到。
```

#### 2.包含方式

```xml
<context:component-scan base-package="com.gewei" use-default-filters="false">
    <context:include-filter type="" expression=""/>
</context:component-scan>

1.use-default-filters = "false"
	让Spring默认的扫描方式失效
2. <context:include-filter type="" expression=""/>
	作用：指定扫描哪些注解
```

### 对于注解开发的思考

- 配置互通

```xml
Spring注解配置与配置文件的配置互通

@Repository
public class UserDAOImpl{

}

public class UserServiceImpl{
	private UserDAO userDAO;
	set;
	get;
}

<bean id = "userService" class =  "com.gewei.UserServiceImpl"
	<property name = "userDAO" ref = "userDAOImpl"
</bean>
```

- 什么情况下使用注解，什么情况下使用配置文件

```markdown
1. 在程序员开发的类型上，可以加入对应注解，进行对象的创建
2. 应用于其他非程序员开发的类型时，还是需要<bean>进行配置的
```

Spring高级注解（Spring 3.x)

#### 1.配置Bean

```java
1.Spring在3.x提供的一个注解，用于替换XML配置文件。

@Configuration
public class Appconfig{

}
```

1.配置bean在应用的过程中替换了XML具体什么内容那？

![](https://cdn.jsdelivr.net/gh/gwbiubiu/images/20210711214041.png)

2. AnnotationConfigApplicationContext

![](https://cdn.jsdelivr.net/gh/gwbiubiu/images/20210711214329.png)

- 配置Bean开发的细节分析

  - 基于注解开发使用日志

  ```
  不能继承log4j，更倾向于使用更新的技术
  logback
  ```

  引入相关jar包

  ```xml
  <dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-api</artifactId>
    <version>1.7.25</version>
  </dependency>
  
  <dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>jcl-over-slf4j</artifactId>
    <version>1.7.25</version>
  </dependency>
  
  <dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.2.3</version>
  </dependency>
  
  <dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-core</artifactId>
    <version>1.2.3</version>
  </dependency>
  
  <dependency>
    <groupId>org.logback-extensions</groupId>
    <artifactId>logback-ext-spring</artifactId>
    <version>0.1.4</version>
  </dependency>
  ```

  引入 logback 配置文件：logback.xml

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <configuration>
      <!-- 控制台输出 -->
      <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
          <encoder>
              <!--格式化输出：%d表示日期，%thread表示线程名，%-5level：级别从左显示5个字符宽度%msg：日志消息，%n是换行符-->
              <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n</pattern>
          </encoder>
      </appender>
      
  
      <root level="DEBUG">
          <appender-ref ref="STDOUT" />
      </root>
  
  </configuration>
  ```

  @Configuration注解的本质

  ```java
  本质：@Configuration也是@Component的衍生注解
  
  同样的也可以使用<context:component-scan进行扫描
  ```

##### @Bean

```markdown
@Bean注解在bean中进行使用，等同于XML配置文件中的<bean 标签
```

- @Bean注解的基本使用

对象的创建

![](https://cdn.jsdelivr.net/gh/gwbiubiu/images/20210711220346.png)

```java
	1. 简单对象
        直接通过new方式创建出的对象
    2.复杂对象
        不能通过new方式创建出来对象
        Connection SqlSessionFactory
        
```

创建复杂对象：

```
public Connection conn(){
	Connection conn = null;
	try{
		class.forName("com.mysql.jdbc.Driver");
		conn = DriverManager.getConnection("jdbc:mysql://locolhost:3306/suns?useSSL=false,"root","123456");
	}catch(ClassNotFoundException e){
		e.printStackTrace();
	}catch(SQLException e){
		e.printStackTrace();
	}
	return conn;
}
```

创建复杂对象在Spring中通常使用实现FactoryBean的方式

```java
public class ConnectionFactoryBean implements FactoryBean<Connection> {
    @Override
    public Connection getObject() throws Exception {
        Class.forName("com.mysql.jdbc.Driver");
        Connection conn = DriverManager.getConnection("jdbc:mysql://locolhost:3306/suns?useSSL=false","root","root");
        return null;
    }

    @Override
    public Class<?> getObjectType() {
        return Connection.class;
    }

    @Override
    public boolean isSingleton() {
        return false;
    }
}
```

这里我们在配置类中可以这样去实现。

```java
@Bean
public Connection conn(){
    ConnectionFactoryBean factoryBean = new ConnectionFactoryBean();
    Connection conn = null;
    try {
        conn = factoryBean.getObject();
    } catch (Exception e) {
        e.printStackTrace();
    }
    return conn;
}
```

自定义id值

```markdown
- 注意：@Bean注释的方法名字就是bean的id值
	如果我们想自定义id值，只需要在@Bean("u")中命名即可。
```

控制对象创建次数

```java
@Bean
@Scope(singleton|prototype) 默认值是singleton
```

#### 2.@Bean注解的注入

- 自建类型的注入

```java
@Configuration
public class AppConfig {
    @Bean
    public UserDao userDao(){
        return new UserDaoImpl();
    }

    @Bean
    public UserService userService(UserDao userDao){
        UserServiceImpl userService = new UserServiceImpl();
        userService.setUserDao(userDao);
        return userService;
    }

}

@Test
public void test3() {
    ApplicationContext ctx = new AnnotationConfigApplicationContext(AppConfig.class);
    UserService userService = (UserService) ctx.getBean("userService");
    userService.register();
}
```

这里还有个简化写法

```
@Bean
    public UserService userService(){
        UserServiceImpl userService = new UserServiceImpl();
        userService.setUserDao(userDao());
        return userService;
    }

```

JDK类型的注入

![](https://cdn.jsdelivr.net/gh/gwbiubiu/images/20210712091029.png)

JDK类型的注入细节分析

```markdown
- 使用配置Bean的方式直接从代码中对Bean进行配置，这会带来耦合啊，那怎么去解耦合那
	
	通过配置文件的方式
```

首先还是先创建init.properties

```java
@Configuration
@PropertySource("classpath:/init.properties")
public class AppConfig {
}
```

然后再这个配置类的内部，对init.properties文件进行读取

```java
public class AppConfig {
    
    @Value("${id}")
    private Integer id;
    
    @Value("${name}")
    private String name;
    
    @Bean
    public Customer customer(){
        Customer customer = new Customer();
        customer.setId(id);
        customer.setName(name);
        return customer;
    }
}
```

#### 3.@ComponentScan

```markdown
@ComponentScan注解在配置Bean中使用，相当于XML文件中的<context:component-scan>标签

	目的：进行相关注解的扫描（@Component @Value @Autowired）
```

```java
@Configuration
@PropertySource("classpath:/init.properties")
//对com.gewei.scan包下带有@Component注解的类进行扫描
@ComponentScan(basePackages = "com.gewei.scan")
public class AppConfig {
}
```

- 排除和包含相关

排除

![](https://cdn.jsdelivr.net/gh/gwbiubiu/images/20210712093845.png)

包含

![](https://cdn.jsdelivr.net/gh/gwbiubiu/images/20210712094328.png)

#### 4.Spring工厂创建对象的多种配置方式

1. 多种配置方式的应用场景

![](https://cdn.jsdelivr.net/gh/gwbiubiu/images/20210712095214.png)

2.配置优先级

```markdown
@Component及其衍生注解< @Bean < 配置文件bean标签

	#优先级高的配置，可以覆盖优先级低的配置
```

这边有个问题：在基于注解的开发环境中，怎么讲Spring的配置文件进行导入那

```markdown
这边要学习一个新的注解
@ImportResource("applicationContext.xml")

	#通过这个注解我们可以在applicationContext.xml这个配置文件中进行配置，同时只需要在代码中只导入注解类文件。
```

利用这个方式，我们也可以解决配置Bean中耦合的问题，只需要覆盖即可

![](https://cdn.jsdelivr.net/gh/gwbiubiu/images/20210712105416.png)

```markdown
# 这边考虑一个问题，虽然在配置Bean中并没有修改类中的代码，但是在这个Bean中加入了新的注解@ImportResource，这算不算耦合那？
	解决这个问题我们可以通过，新创建一个配置Bean，然后对其添加@ImportResource注解，然后在加载配置文件的过程中，加载进去
	ApplicationContext ctx = new AnnotationConfigApplicationContext(AppConfig1.class， AppConfig2.class);
```

```java
@Configuration
@ImportResource("SpringContext.xml")
public class AppConfig2 {
}
```

#### 5.整合多个配置信息

如果我们只在一个文件中配置Bean，那么这个文件中可能有上千行代码，不利于维护，所以我们对其进行拆分。

```
# 为什么会有多个配置信息
	拆分多个配置bean的开发，是一种模块化开发的形式，也体现了面向对象中各司其职的设计思想。
```

多配置Bean的整合方式

- 多个配置Bean的整合
- 配置Bean和@Component相关注解的整合
- 配置Bean与Spring.xml配置文件的整合

整合多种配置需要关注哪些要点

- 如何使多配置信息整合成一个整体
- 如何实现跨配置的注入

##### 5.1 多个配置Bean的整合

- 如何使多配置信息整合成一个整体
- 如何实现跨配置的注入

![](https://cdn.jsdelivr.net/gh/gwbiubiu/images/20210712112853.png)

此外，还可以通过@Import的方式直接在AppConfig上添加注解

```java
@Configuration
@Import(AppConfig2.class)
public class AppConfig1 {
    @Bean
    public UserDao userDao(){
        return new UserDaoImpl();
    }
}
```

```java
@Test
public void test4() {
    ApplicationContext ctx = new AnnotationConfigApplicationContext(AppConfig1.class);
    UserDaoImpl userDao = (UserDaoImpl) ctx.getBean("userDao");
    UserServiceImpl userService = (UserServiceImpl) ctx.getBean("userService");
    System.out.println(userDao);
    System.out.println(userService);
}
```

这个@Import注解和XML文件中<import resource = " ">标签一样的

最后一种方式是在工厂创建时，指定多个配置Bean的Class对象

```java
ApplicationContext ctx = new AnnotationConfigApplicationContext(AppConfig1.class，AppConfig2.class);
```

跨配置进行注入

![](https://cdn.jsdelivr.net/gh/gwbiubiu/images/20210712114931.png)

```markdown
# 在应用配置Bean的过程中，不管使用哪种方式进行配置信息的汇总，其操作方式都是通过成员变量加@AutoWired注解完成的
```

##### 5.2 配置Bean与@Component相关注解的整合

![](https://cdn.jsdelivr.net/gh/gwbiubiu/images/image-20210712115310566.png)

注入：

直接使用@AutoWired进行注入

![](https://cdn.jsdelivr.net/gh/gwbiubiu/images/20210712115451.png)

##### 5.3 配置Bean与配置文件整合

![](https://cdn.jsdelivr.net/gh/gwbiubiu/images/20210712115742.png)

```markdown
# 注意在配置Bean注入的过程中，使用@AutoWried这个方式可能会出现一个红色的下波浪线警告，这是因为idea这个工具联想不到，属于正常情况。
```

#### 6.配置Bean的底层实现

![](https://cdn.jsdelivr.net/gh/gwbiubiu/images/20210712143854.png)

![](https://cdn.jsdelivr.net/gh/gwbiubiu/images/20210712144045.png)

```markdown
# Spring在配置Bean中加入@Configuration注解后，底层会通过Cglib的方式，来进行相关的配置、处理。
```

#### 7.四维一体的开发思想

1. 什么是四维一体

![](https://cdn.jsdelivr.net/gh/gwbiubiu/images/20210712145854.png)

![](https://cdn.jsdelivr.net/gh/gwbiubiu/images/20210712145435.png)

![](https://cdn.jsdelivr.net/gh/gwbiubiu/images/20210712145709.png)

#### 8.纯注解AOP编程

```
1. 搭建环境
2. 注解扫描
```

开发步骤

```java
1. 原始对象
2. 创建切面类（额外功能、切入点、组装）
    @Aspect
    public class MyAspect {

        @Around("execution(* login(..))")
        public Object around(ProceedingJoinPoint joinPoint) throws Throwable{
            System.out.println("------aspectj log ----");
            Object ret = joinPoint.proceed();
            return ret;
        }
    }
3.Spring配置文件中
    <aop:aspectj-autoproxy> 和 @EnableAspectjAutoProxy等价（写在配置Bean当中）
        
	@Configuration
	@ComponentScan(basePackages = "com.gewei.aop")
	@EnableAspectJAutoProxy
	public class AppConfig5 {
	}
```

AOP细节分析

```markdown
1. 创建代理的切换 JDK CGlib
<aop:aspectj-autoproxy proxy-target-clsss = true|false>
@EnableAspectjAutoProxy(proxyTargetClass)

2.SpringBoot AOP的开发方式
	@EnableAspectjAutoProxy已经设置好了
只需要：
	1.创建原始对象
	2.创建切面类（额外功能、切入点、组装切面）
# Spring AOP代理的默认实现是JDK，而SpringBoot AOP代理的默认实现是Cglib
```

#### 9.纯注解版Spring+MyBatis整合

![](https://cdn.jsdelivr.net/gh/gwbiubiu/images/20210712180715.png)

![](https://cdn.jsdelivr.net/gh/gwbiubiu/images/20210712180857.png)

![](https://cdn.jsdelivr.net/gh/gwbiubiu/images/20210712181009.png)

配置Bean

![](https://cdn.jsdelivr.net/gh/gwbiubiu/images/20210712181310.png)

MapperLocations编码的通配写法

```
//设置Mapper文件的路径
sqlSessionFactoryBean.setMapperLocations(Resource..);
Resource resource = new ClassPathResource("UserDAOMapper.xml");


<property name = "mapperLocations">
	<list>
		<value> classpath:com.gewei.mapper/*Mapper.xml</value>
	<list>
</property>
```

在配置Bean中可以这样书写

![](https://cdn.jsdelivr.net/gh/gwbiubiu/images/20210712182659.png)

解决配置Bean的耦合问题

![](https://cdn.jsdelivr.net/gh/gwbiubiu/images/20210712183045.png)

![](https://cdn.jsdelivr.net/gh/gwbiubiu/images/20210712183112.png)

然后在配置Bean中将MybatisProperties注入。

纯注解版事务编程

![](https://cdn.jsdelivr.net/gh/gwbiubiu/images/20210712184131.png)

#### 10.Spring框架中YML的使用

```markdown
# YML(YAML)是一种新形式的配置文件，比XML更简单，比properties更强大
```

properties配置文件存在问题

```markdown
1. properties表达过于繁琐，无法表达数据的内在联系。
2. properties无法表达对象集合类型 
```

![](https://cdn.jsdelivr.net/gh/gwbiubiu/images/20210712185041.png)

YML语法

![](https://cdn.jsdelivr.net/gh/gwbiubiu/images/20210712185308.png)

Spring与YML文件整合

```
# 首先Spring是不支持YML文件的
首先是YamlPropertiesFactoryBean这个创建复杂对象的工厂将YML文件创建成properties文件，然后交给PropertiesSourcePlaceholderConfifurer进行处理。
```

![](https://cdn.jsdelivr.net/gh/gwbiubiu/images/20210712185902.png)

环境搭建

```xml
<dependency>
    <groupId>org.yaml</groupId>
    <artifactId>snakeyaml</artifactId>
    <version>1.28</version>
</dependency>
```

编码

```java
1.准备yml配置文件
2.配置Bean中操作，完成YAML读取，与PropertiesSourcePlaceholderConfifurer的创建

@Configuration
@ComponentScan(basePackages = "com.gewei.yml")
public class YmlAutoConfiguration {


    @Bean
    public PropertySourcesPlaceholderConfigurer configurer(){
        YamlPropertiesFactoryBean yamlPropertiesFactoryBean = new YamlPropertiesFactoryBean();
        yamlPropertiesFactoryBean.setResources(new ClassPathResource("init.yml"));
        Properties properties = yamlPropertiesFactoryBean.getObject();
        PropertySourcesPlaceholderConfigurer configurer = new PropertySourcesPlaceholderConfigurer();
        configurer.setProperties(properties);
        return configurer;
    }
}
3.类中加入@Value注解 
    
@Component
public class Account {

    @Value("${name}")
    private String name;
    @Value("${password}")
    private String password;
}
```

解决集合的问题

```markdown
age =
    -11
    -12
    -13
Spring的YamlPropertiesFactoryBean还是解决不了这个问题
   # 利用Spring的EL表达式进行结局
age = 11,12,13
    
    @Value(#{${age}.split(',')})
    private List<String> list;
    
对象类型的YAML配置时，过于繁琐
	#springBoot 提供的@ConfigrationProperties更好的解决了这个问题
```

