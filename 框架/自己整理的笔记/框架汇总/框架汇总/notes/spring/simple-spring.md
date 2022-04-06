# simple-spring

A simple IOC container refer to Spring.

## quick-start

- Spring 部分配置特性
  - [id 和 name](https://github.com/hzcforever/simple-spring#id-和-name)
  - [配置是否允许 Bean 覆盖和循环依赖](https://github.com/hzcforever/simple-spring#配置是否允许-Bean-覆盖和循环依赖)
  - [profile](https://github.com/hzcforever/simple-spring#profile)
  - [工厂模式生成 Bean](https://github.com/hzcforever/simple-spring#工厂模式生成-Bean)
  - [FactoryBean](https://github.com/hzcforever/simple-spring#FactoryBean)
  - [BeanWrapper](https://github.com/hzcforever/simple-spring#BeanWrapper)
  - [初始化 Bean 的回调](https://github.com/hzcforever/simple-spring#初始化-Bean-的回调)
  - [销毁 Bean 的回调](https://github.com/hzcforever/simple-spring#销毁-Bean-的回调)
  - [Bean 的继承](https://github.com/hzcforever/simple-spring#Bean-的继承)
  - [lookup-method](https://github.com/hzcforever/simple-spring#lookup-method)
  - [replaced-method](https://github.com/hzcforever/simple-spring#replaced-method)
  - [init-method](https://github.com/hzcforever/simple-spring#init-method)
  - [BeanPostProcessor](https://github.com/hzcforever/simple-spring#BeanPostProcessor)
- version 1.0
  - [简单的 IOC](https://github.com/hzcforever/simple-spring#简单的-IOC)
  - [简单的 AOP](https://github.com/hzcforever/simple-spring#简单的-AOP)
- version 2.0
  - [simple-spring 2.0 的功能](https://github.com/hzcforever/simple-spring#simple-spring-20-的功能)
  - IOC 的实现
    - [BeanFactory 的流程](https://github.com/hzcforever/simple-spring#BeanFactory-的流程)
    - [BeanDefinition 的介绍](https://github.com/hzcforever/simple-spring#BeanDefinition-的介绍)
    - [xml 的解析](https://github.com/hzcforever/simple-spring#xml-的解析)
    - [BeanPostProcessor 的注册](https://github.com/hzcforever/simple-spring#BeanPostProcessor-的注册)
    - [getBean 的解析流程](https://github.com/hzcforever/simple-spring#getBean-的解析流程)
  - AOP 的实现
    - [AOP 原理](https://github.com/hzcforever/simple-spring#AOP-原理)
    - [基于 JDK 的动态代理](https://github.com/hzcforever/simple-spring#基于-JDK-的动态代理)
    - [基于 CGLIB 的动态代理](https://github.com/hzcforever/simple-spring#基于-CGLIB-的动态代理)
  - [IOC 与 AOP 的协作](https://github.com/hzcforever/simple-spring#IOC-与-AOP-的协作)
  - [一点思考](https://github.com/hzcforever/simple-spring#一点思考)

## Spring 部分配置特性

### id 和 name

每个 Bean 在 Spring 容器中都有一个唯一的名字（beanName）和 0 个或多个别名（aliases）。

我们从 Spring 容器中获取 Bean 的时候，可以根据 beanName，也可以通过别名。

```
beanFactory.getBean("beanName or alias");
```

在配置 的过程中，我们可以配置 id 和 name，看几个例子就知道是怎么回事了。

```
<bean id="admin" name="m1, m2, m3" class="com.test.Admin" />
```

以上配置的结果是：beanName 为 admin，别名有 3 个，分别为 m1、m2、m3。

```
<bean name="m1, m2, m3" class="com.test.Admin" />
```

以上配置的结果是：beanName 为 m1，别名有 2 个，分别为 m2、m3。

```
<bean class="com.test.Admin" />
```

以上配置的结果是：beanName 为 com.test.Admin#0，别名为： com.test.Admin

```
<bean id="admin" class="com.test.Admin" />
```

以上配置的结果是：beanName 为 admin，没有别名。

### 配置是否允许 Bean 覆盖和循环依赖

我们说过，默认情况下，allowBeanDefinitionOverriding 属性为 null。如果在同一配置文件中 Bean id 或 name 重复了，会抛出异常，但是如果不是同一配置文件中，会发生覆盖。

可是有些时候我们希望在系统启动的过程中就严格杜绝发生 Bean 覆盖，因为万一出现这种情况，会增加我们排查问题的成本。

循环依赖说的是 A 依赖 B，而 B 又依赖 A；或者是 A 依赖 B，B 依赖 C，而 C 却依赖 A。默认 allowCircularReferences 也是 null。

它们两个属性是一起出现的，必然可以在同一个地方一起进行配置。

添加这两个属性的作者 Juergen Hoeller 在这个 jira 的讨论中说明了怎么配置这两个属性。

```
public class NoBeanOverridingContextLoader extends ContextLoader {

    @Override
    protected void customizeContext(ServletContext servletContext, ConfigurableWebApplicationContext applicationContext) {
        super.customizeContext(servletContext, applicationContext);
	    AbstractRefreshableApplicationContext arac = (AbstractRefreshableApplicationContext) applicationContext;
	    arac.setAllowBeanDefinitionOverriding(false);
    }
}

public class MyContextLoaderListener extends org.springframework.web.context.ContextLoaderListener {

    @Override
    protected ContextLoader createContextLoader() {
        return new NoBeanOverridingContextLoader();
    }

}

<listener>
    <listener-class>com.javadoop.MyContextLoaderListener</listener-class>  
</listener>
```

如果以上方式不能满足你的需求，请参考这个链接：[解决spring中不同配置文件中存在name或者id相同的bean可能引起的问题](https://blog.csdn.net/zgmzyr/article/details/39380477)

### profile

我们可以把不同环境的配置分别配置到单独的文件中，举个例子：

```
<beans profile="development"
    xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:jdbc="http://www.springframework.org/schema/jdbc"
    xsi:schemaLocation="...">

    <jdbc:embedded-database id="dataSource">
	    <jdbc:script location="classpath:com/bank/config/sql/schema.sql"/>
	    <jdbc:script location="classpath:com/bank/config/sql/test-data.sql"/>
    </jdbc:embedded-database>
</beans>

<beans profile="production"
    xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:jee="http://www.springframework.org/schema/jee"
    xsi:schemaLocation="...">

    <jee:jndi-lookup id="dataSource" jndi-name="java:comp/env/jdbc/datasource"/>
</beans>
```

应该不必做过多解释了吧，看每个文件第一行的 profile=""。

当然，我们也可以在一个配置文件中使用：

```
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:jdbc="http://www.springframework.org/schema/jdbc"
    xmlns:jee="http://www.springframework.org/schema/jee"
    xsi:schemaLocation="...">

    <beans profile="development">
	    <jdbc:embedded-database id="dataSource">
		    <jdbc:script location="classpath:com/bank/config/sql/schema.sql"/>
		    <jdbc:script location="classpath:com/bank/config/sql/test-data.sql"/>
	    </jdbc:embedded-database>
    </beans>

    <beans profile="production">
	    <jee:jndi-lookup id="dataSource" jndi-name="java:comp/env/jdbc/datasource"/>
    </beans>

</beans>
```

理解起来也很简单吧。

接下来的问题是，怎么使用特定的 profile 呢？Spring 在启动的过程中，会去寻找 “spring.profiles.active” 的属性值，根据这个属性值来的。那怎么配置这个值呢？

Spring 会在这几个地方寻找 spring.profiles.active 的属性值：操作系统环境变量、JVM 系统变量、web.xml 中定义的参数、JNDI。

最简单的方式莫过于在程序启动的时候指定：`-Dspring.profiles.active="profile1,profile2"`

profile 可以激活多个

当然，我们也可以通过代码的形式从 Environment 中设置 profile：

```
AnnotationConfigApplicationContext ctx = new AnnotationConfigApplicationContext();
ctx.getEnvironment().setActiveProfiles("development");
ctx.register(SomeConfig.class, StandaloneDataConfig.class, JndiDataConfig.class);
ctx.refresh(); // 重启
```

如果是 Spring Boot 的话更简单，我们一般会创建 application.properties、application-dev.properties、application-prod.properties 等文件，其中 application.properties 配置各个环境通用的配置，application-{profile}.properties 中配置特定环境的配置，然后在启动的时候指定 profile：

```
java -Dspring.profiles.active=prod -jar JavaDoop.jar
```

如果是单元测试中使用的话，在测试类中使用 @ActiveProfiles 指定，这里就不展开了。

### 工厂模式生成 Bean

请注意 factory-bean 和 FactoryBean 的区别。这节说的是前者，指的是静态工厂或实例工厂，而后者是 Spring 中的特殊接口，代表一类特殊的 Bean，下面一节会介绍 FactoryBean。

设计模式里，工厂方法模式分静态工厂和实例工厂，我们分别看看 Spring 中怎么配置这两个，来个代码示例就什么都清楚了。

静态工厂：

```
<bean id="clientService" class="examples.ClientService" factory-method="createInstance"/>

public class ClientService {
    private static ClientService clientService = new ClientService();
    private ClientService() {}

    // 静态方法
    public static ClientService createInstance() {
	    return clientService;
    }
}
```

实例工厂：

```
<bean id="serviceLocator" class="examples.DefaultServiceLocator">
	<!-- inject any dependencies required by this locator bean -->
</bean>

<bean id="clientService" factory-bean="serviceLocator" factory-method="createClientServiceInstance"/>

<bean id="accountService" factory-bean="serviceLocator" factory-method="createAccountServiceInstance"/>

public class DefaultServiceLocator {
    private static ClientService clientService = new ClientServiceImpl();

    private static AccountService accountService = new AccountServiceImpl();

    public ClientService createClientServiceInstance() {
	    return clientService;
    }

    public AccountService createAccountServiceInstance() {
	    return accountService;
    }
}
```

### FactoryBean

FactoryBean 以 Bean 结尾，表示它是一类 Bean，不同于普通 Bean 的是：它是实现了 FactoryBean 接口的 Bean，当在 IOC 容器中的 Bean 实现了 FactoryBean 接口后，通过 getBean(String BeanName) 方法从 BeanFactory 中获取到的 Bean 对象并不是 FactoryBean 的实现类对象，实际上是通过 FactoryBean 的 getObject() 返回的对象。如果要获取 FactoryBean 对象，需要在 beanName 前面加一个 & 符号来获取。

```
<bean id="school" class="com.test.School">
    <property name="schoolName" value="hit"/>
    <property name="address" value="harbin"/>
</bean>
<bean id="factoryBean" class="com.test.FactoryBeanTest">
    <property name="type" value="school"/>
</bean>
```

下面通过一个类实现 FactoryBean 接口，在配置文件中将该类的 type 属性设置为 student，会在 getObject() 方法中返回 Student 对象。

```
public class FactoryBeanTest implements FactoryBean {

    private String type;

    public String getType() {
        return type;
    }

    public void setType(String type) {
        this.type = type;
    }

    public Object getObject() throws Exception {
	    if ("student".equals(type)) {
            return new Student();
	    } else {
            return new School();
	    }
    }

    public Class<?> getObjectType() {
        return School.class;
    }

    public boolean isSingleton() {
        return true;
    }
}
```

通过测试可以验证之前的想法。

```
School school = (School) applicationContext.getBean("factoryBean");
FactoryBeanTest factoryBean = (FactoryBeanTest) applicationContext.getBean("&factoryBean");
System.out.println(school.getClass().getName());
System.out.println(factoryBean.getClass().getName());
```

测试结果：

```
com.test.School 
com.test.FactoryBeanTest
```

所以从 IOC 容器获取实现了 FactoryBean 的实现类时，返回的是实现类中的 getObject 方法返回的对象，要想获取 FactoryBean 的实现类，得在 getBean 中的 BeanName 前加上 & ，即 getBean(String &BeanName)。

### BeanWrapper

BeanWrapper 接口，作为 spring 内部的一个核心接口，正如其名，它是 bean 的包裹类，即在内部中将会保存该 bean 的实例，提供其它一些扩展功能。同时 BeanWrapper 接口还继承了 PropertyAccessor、propertyEditorRegistry、TypeConverter、ConfigurablePropertyAccessor 接口，所以它还提供了访问 bean 的属性值、属性编辑器注册和类型转换等功能。

```
School school = new School();
BeanWrapper beanWrapper = PropertyAccessorFactory.forBeanPropertyAccess(school);
PropertyValue schoolName = new PropertyValue("schoolName", "HIT");
PropertyValue address = new PropertyValue("address", "Harbin");
beanWrapper.setPropertyValue(schoolName);
beanWrapper.setPropertyValue(address);
System.out.println(beanWrapper.getWrappedInstance());
```

上面的代码已经很清楚地演示了如何使用 BeanWrapper 设置和获取 bean 的属性。

### 初始化 Bean 的回调

有以下四种方案：

```
<bean id="exampleInitBean" class="examples.ExampleBean" init-method="init"/>

public class AnotherExampleBean implements InitializingBean {

    public void afterPropertiesSet() {
    // do some initialization work
    }
}

@Bean(initMethod = "init")
public Foo foo() {
    return new Foo();
}

@PostConstruct
public void init() {

}
```

### 销毁 Bean 的回调

```
<bean id="exampleInitBean" class="examples.ExampleBean" destroy-method="cleanup"/>

public class AnotherExampleBean implements DisposableBean {

    public void destroy() {
    // do some destruction work (like releasing pooled connections)
    }
}

@Bean(destroyMethod = "cleanup")
public Bar bar() {
    return new Bar();
}

@PreDestroy
public void cleanup() {

}
```

### Bean 的继承

在初始化 Bean 的地方：`RootBeanDefinition bd = getMergedLocalBeanDefinition(beanName);`

这里涉及到的就是``中的 parent 属性，我们来看看 Spring 中是用这个来干什么的。

首先，我们要明白，这里的继承和 java 语法中的继承没有任何关系，不过思路是相通的。child bean 会继承 parent bean 的所有配置，也可以覆盖一些配置，当然也可以新增额外的配置。

Spring 中提供了继承自 AbstractBeanDefinition 的 ChildBeanDefinition 来表示 child bean。

看如下一个例子:

```
<bean id="inheritedTestBean" abstract="true" class="org.springframework.beans.TestBean">
    <property name="name" value="parent"/>
    <property name="age" value="1"/>
</bean>

<bean id="inheritsWithDifferentClass" class="org.springframework.beans.DerivedTestBean" parent="inheritedTestBean" init-method="initialize">
    <property name="name" value="override"/>
</bean>
```

parent bean 设置了 abstract="true" 所以它不会被实例化，child bean 继承了 parent bean 的两个属性，但是对 name 属性进行了覆写。

child bean 会继承 scope、构造器参数值、属性值、init-method、destroy-method 等等。

当然，我不是说 parent bean 中的 abstract = true 在这里是必须的，只是说如果加上了以后 Spring 在实例化 singleton beans 的时候会忽略这个 bean。

比如下面这个极端 parent bean，它没有指定 class，所以毫无疑问，这个 bean 的作用就是用来充当模板用的 parent bean，此处就必须加上 abstract = true。

```
<bean id="inheritedTestBeanWithoutClass" abstract="true">
    <property name="name" value="parent"/>
    <property name="age" value="1"/>
</bean>
```

### lookup-method

如果一个单例模式的 bean A 需要引用另一个非单例模式的 bean B，有两种方法：

1. 让 bean A 通过实现 ApplicationContextAware 接口来感知 applicationContext，从而能在运行时通过 applicationContext.getBean(String beanName) 的方法来获取最新的 bean B。但这时就与 Spring 代码耦合，违背了控制反转原则，即 bean 完全由 Spring 容器管理，我们只用使用 bean 就可以了
2. 通过 `` 标签实现

看以下一个场景，NewsProvider 是一个单例类，News 是非单例类，NewsProvider 每次提供最新的 news，现在分别通过以上两种方法来实现该需求。

**通过实现 ApplicationContextAware 接口：**

```
public class NewsProvider implements ApplicationContextAware {

    private News news;

    private ApplicationContext applicationContext;

    public News getNews() {
	    return applicationContext.getBean("news", News.class);
    }

    public void setNews(News news) {
	    this.news = news;
    }

    public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
	    this.applicationContext = applicationContext;
    }
}
```

让NewsProvider类实现ApplicationContextAware接口（实现 BeanFactoryAware 接口也可以），每次调用 NewsProvider 的 getNews 方法时，都会从 ApplicationContext 中获取一个新的 News 实例。

对应的配置文件为：

```
<bean id="news" class="com.test.News" scope="prototype"/>
<bean id="newsProvider" class="com.test.NewsProvider">
    <property name="news" ref="news"/>
</bean>
```

**通过 `` 标签实现：**

```
class LookupProvider {
    private News news;

    public News getNews() {
	    return news;
    }

    public void setNews(News news) {
	    this.news = news;
    }
}
```

此时无需实现任何接口，只用在配置文件中进行如下设置即可：

```
<bean id="lookupProvider" class="com.test.LookupProvider">
    <lookup-method name="getNews" bean="news"/>
</bean>
```

显然我们没有用到 Spring 的任何类和接口 ，实现了与 Spring 代码的耦合。

其中最为核心的部分就是 lookup-method 的配置，Spring 应用了 CGLIB 动态代理类库，在初始化容器时对 中的 bean 做了特殊处理，Spring 会对 bean 指定的 class 做动态代理， 通过 name 中指定的方法，返回 bean 的实例对象。

### replaced-method

主要作用是替换方法体及其返回值。需要改变的方法，实现 MethodReplacer 接口并重写 reimplement 方法来动态地改变方法。内部实现为 CGLIB 方法，重新生成子类，重写配置方法和返回对象，达到动态改变的效果。

直接看例子，bean 配置文件：

```
<bean id="admin" class="com.test.Admin">
    <property name="name" value="hzc"/>
    <property name="age" value="23"/>
    <replaced-method name="introduce" replacer="replacedAdmin"/>
</bean>

<bean id="replacedAdmin" class="com.test.ReplacedAdmin"/>
```

Admin 代码：

```
class Admin {

    private String name;
    private int age;
    public Admin() {

    }

    public Admin(String id) {
	    this.id = id;
    }

    public String getName() {
	    return name;
    }

    public void setName(String name) {
	    this.name = name;
    }

    public int getAge() {
	    return age;
    }

    public void setAge(int age) {
	    this.age = age;
    }

    public void introduce() {
	    System.out.println("hello, my name is " + name +
		", and I'am " + age + " years old");
    }
}
```

ReplacedAdmin 代码：

```
class ReplacedAdmin implements MethodReplacer {

    public Object reimplement(Object o, Method method, Object[] objects) throws Throwable {
	    System.out.println("已经被替换!");
	    return null;
    }
}
```

测试代码：

```
public void test() throws Exception {
    String location = "bean.xml";
    ApplicationContext applicationContext = new ClassPathXmlApplicationContext(location);
    Admin admin = (Admin) applicationContext.getBean("admin");
    admin.introduce(); // 结果为 “已经被替换”，成功地替换了原来 introduce() 的内容
}
```

### init-method

Spring 实现 bean 的初始化方法有两种方式：

1. 实现 InitializingBean 接口，并实现其中的 afterPropertiesSet() 方法，这种方式需要实现接口，从而使得 bean 的代码与 Spring 耦合在一起
2. 通过 Spring 提供的 init-method 功能来执行一个 bean 的自定义初始化方法

对于实现 InitializingBean 接口的这一种方法来说，在 xml 配置文件中不需要对 bean 进行特殊的设置，Spring 在配置文件中完成该 bean 的全部赋值后，会检查该 bean 是否实现了 InitializingBean 接口，如果实现了就直接调用 bean 的 afterPropertiesSet 方法

对于第二种方式，bean 的类不需要实现任何接口，只需在 `` 中加入 init-method="xxx" 即可。其中 xxx 必须是一个无参方法，否则会抛出异常，该方法将会在 bean 初始化完成后被调用。

InitializingBean 和 init-method 可以一起使用，Spring 会先处理 InitializingBean 再处理 init-method。init-method 是通过反射执行的，而 afterPropertiesSet 是直接执行的，所以 afterPropertiesSet 的执行效率比 init-method 高，不过 init-method 消除了 bean 对 Spring 依赖，推荐使用 init-method。

如果一个 bean 被定义为非单例的，那么 afterPropertiesSet 和 init-method 在 bean 的每一个实例被创建时都会执行。单例 bean 的 afterPropertiesSet 和 init-method 只在 bean 第一次实例时执行。一般情况下 afterPropertiesSet 和 init-method 都应用在单例 bean 上。

### BeanPostProcessor

应该说 BeanPostProcessor 概念在 Spring 中也是比较重要的。我们看下接口定义：

```
public interface BeanPostProcessor {

    Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException;

    Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException;
}
```

看这个接口中的两个方法名字我们大体上可以猜测 bean 在初始化之前会执行 postProcessBeforeInitialization 这个方法，初始化完成之后会执行 postProcessAfterInitialization 这个方法。但是，这么理解是非常片面的。

首先，我们要明白，除了我们自己定义的 BeanPostProcessor 实现外，Spring 容器在启动时自动给我们也加了几个。如在获取 BeanFactory 的 obtainFactory() 方法结束后的 prepareBeanFactory(factory)，大家仔细看会发现，Spring 往容器中添加了这两个 BeanPostProcessor：ApplicationContextAwareProcessor、ApplicationListenerDetector。

我们回到这个接口本身，请看第一个方法，这个方法接受的第一个参数是 bean 实例，第二个参数是 bean 的名字，重点在返回值将会作为新的 bean 实例，所以，没事的话这里不能随便返回个 null。那意味着什么呢？我们很容易想到的就是，我们这里可以对一些我们想要修饰的 bean 实例做一些事情。但是对于 Spring 框架来说，它会决定是不是要在这个方法中返回 bean 实例的代理，这样就有更大的想象空间了。

最后，我们说说如果我们自己定义一个 bean 实现 BeanPostProcessor 的话，它的执行时机是什么时候？

如果仔细看了代码分析的话，其实很容易知道了，在 bean 实例化完成、属性注入完成之后，会执行回调方法，具体请参见类 AbstractAutowireCapableBeanFactory#initBean 方法。

首先会回调几个实现了 Aware 接口的 bean，然后就开始回调 BeanPostProcessor 的 postProcessBeforeInitialization 方法，之后是回调 init-method，然后再回调 BeanPostProcessor 的 postProcessAfterInitialization 方法。

## version 1.0

### 简单的 IOC

最简单的 IOC 容器只需以下几步即可实现：

1. 加载 xml 配置文件，遍历其中的标签
2. 获取标签中的 id 和 class 属性，加载 class 属性对应的类，并创建 bean
3. 遍历标签中的标签，获取属性值，并将属性值填充到 bean 中
4. 将 bean 注册到 bean 容器中

先对 version 1.0 代码结构做一个简要介绍：

- SimpleIOC：IOC 的实现类，实现了上面所说的几个步骤
- SimpleIOCTest：IOC 的测试类
- Car：IOC 测试使用的 bean
- Wheel：测试使用的 bean
- ioc.xml：bean 配置文件

### 简单的 AOP

AOP 是基于代理模式的，在介绍 AOP 的具体实现之前，先引入 Spring AOP 中的一些概念：

**连接点(Joint Point)**：表示在程序中明确定义的点，一般指的是方法调用。

**切点(Pointcut)**：表示一组 Joint Point，这些 Joint Point 通过匹配规则集中在一起，它定义了相应的 Advice 将要发生的地方。

**通知(Advice)**：定义了在 Pointcut 里面定义的程序点具体要做的操作，Spring 中对应5种不同类型的通知：

1. 前置通知（Before）：在目标方法执行前，执行通知
2. 后置通知（After）：在目标方法执行后，执行通知，此时不关系目标方法返回的结果是什么
3. 返回通知（After-returning）：在目标方法执行后，执行通知
4. 异常通知（After-throwing）：在目标方法抛出异常后执行通知
5. 环绕通知（Around）: 目标方法被通知包裹，通知在目标方法执行前和执行后都被会调用

**切面(Aspect)**：包含了一些 Pointcut 以及对应的 Advice，通知和切点共同定义了切面是什么，在何时，何处执行切面逻辑。

**织入(Weaving)**：将 Aspect 和其它对象连接起来，并创建 Advice 的过程。

**目标对象(Target)**：织入 Advice 的目标对象。

下面以一个简单的例子来说明以上概念之间的关系：

> 让我们来假设一下, 从前有一个叫爪哇的小县城, 在一个月黑风高的晚上, 这个县城中发生了命案. 作案的凶手十分狡猾, 现场没有留下什么有价值的线索. 不过万幸的是, 刚从隔壁回来的老王恰好在这时候无意中发现了凶手行凶的过程, 但是由于天色已晚, 加上凶手蒙着面, 老王并没有看清凶手的面目, 只知道凶手是个男性, 身高约七尺五寸. 爪哇县的县令根据老王的描述, 对守门的士兵下命令说: 凡是发现有身高七尺五寸的男性, 都要抓过来审问. 士兵当然不敢违背县令的命令, 只好把进出城的所有符合条件的人都抓了起来。

- Joint point——爪哇的小县城里的百姓: 因为根据定义, Joint point 是所有可能被织入 Advice 的候选的点, 在 Spring AOP中, 则可以认为所有方法执行点都是 Joint point。而在我们上面的例子中, 命案发生在小县城中, 按理说在此县城中的所有人都有可能是嫌疑人
- Pointcut——身高约七尺五寸的男性: 我们知道, 所有的方法(joint point) 都可以织入 Advice, 但是我们并不希望在所有方法上都织入 Advice, 而 Pointcut 的作用就是提供一组规则来匹配joinpoint, 给满足规则的 joinpoint 添加 Advice。同理, 对于县令来说, 他再昏庸, 也知道不能把县城中的所有百姓都抓起来审问, 而是根据凶手是个男性, 身高约七尺五寸, 把符合条件的人抓起来. 在这里“凶手是个男性, 身高约七尺五寸”就是一个修饰谓语, 它限定了凶手的范围, 满足此修饰规则的百姓都是嫌疑人, 都需要抓起来审问
- Advice——抓过来审问, Advice 是一个动作, 即一段 Java 代码, 这段代码作用于 pointcut 所限定的那些 Joint point 上的。同理, 对比到我们的例子中, 抓过来审问 这个动作就是对作用于那些满足“男性身高约七尺五寸”的爪哇的小县城里的百姓
- Aspect——pointcut 与 Advice 的组合, 因此在这里我们就可以类比: “根据老王的线索, 凡是发现有身高七尺五寸的男性, 都要抓过来审问” 这一整个动作可以被认为是一个 Aspect

说完概念，接下来我们来说说简单 AOP 实现的步骤。这里 AOP 是基于 JDK 动态代理实现的，只需3步即可完成：

1. 定义一个包含切面逻辑的对象，这里假设叫 logMethodInvocation
2. 定义一个 Advice 对象（实现了 InvocationHandler 接口），并将上面的 logMethodInvocation 和目标对象传入
3. 将上面的 Adivce 对象和目标对象传给 JDK 动态代理方法，为目标对象生成代理

上面步骤比较简单，不过在实现过程中，要引入一些辅助接口才能实现。接下来介绍一下简单 AOP 的代码结构：

- MethodInvocation 接口——实现类包含了切面逻辑，如 logMethodInvocation
- Advice 接口——继承了 InvocationHandler 接口
- BeforeAdvice 类——实现了 Advice 接口，是一个前置通知
- SimpleAOP 类——生成代理类
- SimpleAOPTest——SimpleAOP 的测试类
- HelloService 接口——目标对象接口
- HelloServiceImpl——目标对象

## version 2.0

### simple-spring 2.0 的功能

在 version 1.0 的版本中，我实现了最简单的 IOC 和 AOP 容器，功能很单一，而且 IOC 和 AOP 两个模块没有整合到一起，只能独立运行。IOC 在加载 bean 的过程中，AOP 不能对 bean 织入相关的通知。在 2.0 的版本中，主要实现以下功能：

1. 根据 xml 配置文件加载相关的 bean
2. 对 BeanPostProcessor 类型的 bean 提供支持
3. 对 BeanFactoryAware 类型的 bean 提供支持
4. 实现基于 JDK 动态代理的 AOP
5. 整合 IOC 和 AOP 两个模块，使得可以协同工作

### IOC 的实现

#### BeanFactory 的流程

1. BeanFactory 加载 Bean 的配置文件，将读到的配置属性信息封装成 BeanDefinition 对象
2. 将封装好的 BeanDefinition 对象注册到 BeanDefinition 容器中
3. 注册 BeanPostProcessor 相关实现类到 BeanPostProcessor 容器中
4. BeanFactory 进入就绪状态
5. 外部调用 BeanFactory 的 getBean(String name) 方法，BeanFactory 着手实例化相应的 bean
6. 重复步骤 3 和 4，直至 BeanFactory 被销毁

上面就是 BeanFactory 的生命流程，即 IOC 容器的生命流程。

#### BeanDefinition 的介绍

在详细介绍 IOC 容器的工作原理前，这里先介绍实现 IOC 所用到的一些辅助类，包括 BeanDefinition、BeanReference、PropertyValues 和 PropertyValue。这些类与接下来 xml 配置文件的解析紧密相关。按照顺序，先从 BeanDefinition 开始介绍。

BeanDefinition 从字面意思上翻译成中文就是“ Bean 的定义”。从翻译结果中就可以猜出这个类的用途，即根据 Bean 配置信息生成相应的 Bean 对象。举个例子，如果把 Bean 比作是电脑，那么 BeanDefinition 就是这台电脑的配置清单。我们从外观上无法看出这台电脑里面都有哪些配置，也看不出电脑的性能咋样。但是通过配置清单，我们就可了解这台电脑的详细配置。我们可以知道这台电脑是不是用了A厂的 CPU、B 厂的固态硬盘等。透过配置清单，我们也就可大致评估出这台电脑的性能。

[![根据 bean 的配置生成 BeanDefinition](https://camo.githubusercontent.com/fd627e154984df031e67da76b0806383c1f9d4e20e378f03796370cbb3290acf/68747470733a2f2f696d6167652d7374617469632e7365676d656e746661756c742e636f6d2f3336322f3432342f333632343234313736342d35613635393930386137313033)](https://camo.githubusercontent.com/fd627e154984df031e67da76b0806383c1f9d4e20e378f03796370cbb3290acf/68747470733a2f2f696d6167652d7374617469632e7365676d656e746661756c742e636f6d2f3336322f3432342f333632343234313736342d35613635393930386137313033)

接下来我们来说说上图中的 ref 对应的 BeanReference 对象。BeanReference 对象保存的是 bean 配置中 ref 属性对应的值，在后续 BeanFactory 实例化 bean 时，会根据 BeanReference 保存的值去实例化 bean 所依赖的其他 bean。

接下来说说 PropertyValues 和 PropertyValue 这两个长的比较像的类，首先是PropertyValue。PropertyValue 中有两个字段 name 和 value，用于记录 bean 配置中的 标签的属性值。然后是PropertyValues，PropertyValues 从字面意思上来看，是 PropertyValue 复数形式，在功能上等同于 List。那么为什么 Spring 不直接使用 List，而自己定义一个新类呢？答案是要获得一定的控制权，看下面的代码：

```
public class PropertyValues {

	private final List<PropertyValue> propertyValueList = new ArrayList<PropertyValue>();

	public void addPropertyValue(PropertyValue pv) {
	// 在这里可以对参数值 pv 做一些处理，如果直接使用 List，就不行了
		this.propertyValueList.add(pv);
	}

	public List<PropertyValue> getPropertyValues() {
		return this.propertyValueList;
	}
}
```

#### xml 的解析

BeanFactory 初始化时，会根据传入的 xml 配置文件路径加载并解析配置文件。但是加载和解析 xml 配置文件这种脏活累活，BeanFactory 可不太愿意干，它只想管理容器中的 bean。于是 BeanFactory 将加载和解析配置文件的任务委托给专职人员 BeanDefinitionReader 的实现类 XmlBeanDefinitionReader 去做。那么 XmlBeanDefinitionReader 具体是怎么做的呢？XmlBeanDefinitionReader 做了如下几件事情：

1. 将 xml 配置文件加载到内存中
2. 获取根标签 下所有的 标签
3. 遍历获取到的 标签列表，并从标签中读取 id，class 属性
4. 创建 BeanDefinition 对象，并将刚刚读取到的 id，class 属性值保存到对象中
5. 遍历 标签下的 标签，从中读取属性值，并保持在 BeanDefinition 对象中
6. 将 <id, BeanDefinition> 键值对缓存在 Map 中，留作后用
7. 重复3、4、5、6步，直至解析结束

上面的解析步骤并不复杂，实现起来也不难，在之前 simple-spring 1.0 的版本中我就已经实现了。

#### BeanPostProcessor 的注册

BeanPostProcessor 接口是 Spring 对外拓展的接口之一，其主要用途提供一个机会，让开发人员能够插手 bean 的实例化过程。通过实现这个接口，我们就可在 bean 实例化时，对bean 进行一些处理。比如，我们所熟悉的 AOP 就是在这里将切面逻辑织入相关 bean 中的。正是因为有了 BeanPostProcessor 接口作为桥梁，才使得 AOP 可以和 IOC 容器产生联系。

接下来说说 BeanFactory 是怎么注册 BeanPostProcessor 相关实现类的。

XmlBeanDefinitionReader 在完成解析工作后，BeanFactory 会将它解析得到的 <id, BeanDefinition> 键值对注册到自己的 beanDefinitionMap 中。BeanFactory 注册好 BeanDefinition 后，就立即开始注册 BeanPostProcessor 相关实现类。这个过程比较简单：

1. 根据 BeanDefinition 记录的信息，寻找所有实现了 BeanPostProcessor 接口的类
2. 实例化 BeanPostProcessor 接口的实现类
3. 将实例化好的对象放入 List 中
4. 重复2、3步，直至所有的实现类完成注册

#### getBean 的解析流程

在完成了 xml 的解析、BeanDefinition 的注册以及 BeanPostProcessor 的注册过程后。BeanFactory 初始化的工作算是结束了，此时 BeanFactory 处于就绪状态，等待外部程序的调用。

外部程序一般都是通过调用 BeanFactory 的 getBean(String name) 方法来获取容器中的 bean。BeanFactory 具有延迟实例化 bean 的特性，也就是等外部程序需要的时候，才实例化相关的 bean。这样做的好处是比较显而易见的，第一是提高了 BeanFactory 的初始化速度，第二是节省了内存资源。下面我们就来详细说说 bean 的实例化过程：

[![bean的实例化流程](https://camo.githubusercontent.com/dc25ae262058dc857dab734ccda07a85df407c2945aa4814045db10984fb5fcb/68747470733a2f2f696d6167652d7374617469632e7365676d656e746661756c742e636f6d2f3431372f3638332f343137363833323336322d35393936393963613261363864)](https://camo.githubusercontent.com/dc25ae262058dc857dab734ccda07a85df407c2945aa4814045db10984fb5fcb/68747470733a2f2f696d6167652d7374617469632e7365676d656e746661756c742e636f6d2f3431372f3638332f343137363833323336322d35393936393963613261363864)

上图是一个完整的 Spring bean 实例化过程图。在我的仿写项目中，没有做的这么复杂，简化了 bean 实例化的过程，如下：

1. 实例化 bean 对象，类似于 new XXObject()
2. 将配置文件中对应的属性填充到刚创建的 bean 对象中
3. 检查 Aware 相关接口，并设置相关依赖，simple-spring 目前仅对 BeanFactoryAware 接口实现类提供支持
4. BeanPostProcessor 前置处理，即 postProcessBeforeInitialization(Object bean, String beanName)
5. BeanPostProcessor 后置处理，即 postProcessAfterInitialization(Object bean, String beanName)
6. 此时 bean 对象处于就绪状态，可以使用

### AOP 的实现

#### AOP 原理

AOP 是基于动态代理模式实现的，具体实现上可以基于 JDK 动态代理或者 Cglib 动态代理。其中 JDK 动态代理只能代理实现了接口的对象，而 Cglib 动态代理则无此限制。所以在为没有实现接口的对象生成代理时，只能使用 Cglib。在 simple-spring 中，暂时只实现了基于 JDK 动态代理的代理对象生成器。

关于 AOP 原理这里就不多说了，下面说说 simple-spring 中 AOP 的实现步骤：

1. AOP 逻辑介入 BeanFactory 实例化 bean 的过程
2. 根据 Pointcut 定义的匹配规则，判断当前正在实例化的 bean 是否符合规则
3. 如果符合，代理生成器将切面逻辑 Advice 织入 bean 相关方法中，并为目标 bean 生成代理对象
4. 将生成的 bean 的代理对象返回给 BeanFactory 容器，到此，AOP 逻辑执行结束

对于上面的四步流程，如果熟悉 Spring AOP 应该很容易能理解。

#### 基于 JDK 的动态代理

基于 JDK 的动态代理主要是通过 JDK 提供的代理类 Proxy 为目标对象创建代理。JDK 动态代理只能为实现了接口的目标类生成代理对象。

```
public static Object newProxyInstance(ClassLoader classLoader, Class<?>[] interfaces, InvocationHandler h)
```

以上是 Proxy 创建代理的方法声明，classLoader 为类加载器，interfaces 是目标类实现的接口列表，InvocationHandler 是一个接口类型，里面定义了一个 invoke 方法，用于封装代理的逻辑。

Talk is cheap,show me the code.

目标类定义：

```
public interface Waiter {
    void server();
}

public class ManWaiter implements Waiter {
    public void server() {
        System.out.println("man servering...");
    }
}
```

代理创建者定义：

```
public class ProxyFactory {

    private Object targetObject;
    private BeforeAdvice beforeAdvice;
    private AfterAdvice afterAdvice;

    public Object getTargetObject() {
        return targetObject;
    }

    public void setTargetObject(Object targetObject) {
        this.targetObject = targetObject;
    }

    public BeforeAdvice getBeforeAdvice() {
        return beforeAdvice;
    }

    public void setBeforeAdvice(BeforeAdvice beforeAdvice) {
        this.beforeAdvice = beforeAdvice;
    }

    public AfterAdvice getAfterAdvice() {
        return afterAdvice;
    }

    public void setAfterAdvice(AfterAdvice afterAdvice) {
        this.afterAdvice = afterAdvice;
    }

    public Object createProxy() {
        ClassLoader classLoader = this.getClass().getClassLoader();
        // 获取当前类型所实现的所有接口类型
        Class[] interfaces = targetObject.getClass().getInterfaces();

        InvocationHandler invocationHandler = new InvocationHandler() {
            public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                if (beforeAdvice != null) {
                    beforeAdvice.before();
                }
                Object result = method.invoke(targetObject, args);
                afterAdvice.after();
                return result;
            }
        };
        Object proxyObject = Proxy.newProxyInstance(classLoader, interfaces, invocationHandler);
        return proxyObject;
    }
}
```

代码测试：

```
public class JdkProxyCreatorTest {

    @Test
    public void getProxy() {

        ProxyFactory proxyFactory = new ProxyFactory();
        proxyFactory.setTargetObject(new ManWaiter()); // 设置目标对象
        proxyFactory.setBeforeAdvice(new BeforeAdvice() {
            public void before() {
                System.out.println("hi, you're welcome!");
            }
        });
        proxyFactory.setAfterAdvice(new AfterAdvice() {
            public void after() {
                System.out.println("Goodbye!");
            }
        });
        Waiter waiter = (Waiter) proxyFactory.createProxy();
        waiter.server();
    }
}
```

结果为：

```
hi, you're welcome!
man servering...
Goodbye!
```

从代码运行结果我们可以看出，代理逻辑正常执行了。总结一下以上动态代理流程：

1. 创建代理工厂
2. 给工厂设置目标对象、前置增强和后置增强
3. 调用 createProxy() 得到代理对象
4. 执行代理对象方法时，先执行前置增强，其次执行目标方法，最后执行后置增强

#### 基于 CGLIB 的动态代理

当目标类未实现任何接口时，就无法使用基于 JDK 的动态代理了。那么此类的目标对象生成代理时就应该使用 CGLIB 了。在 CGLIB 中，代理逻辑是封装在 MethodInterceptor 实现类中的，代理对象通过 Enhancer 类的 create 方法进行创建。

目标类：

```
public class Tank59 {
    public void run() {
	    System.out.println("极速前行中");
    }
    public void shoot() {
	    System.out.println("轰轰轰...");
    }
}
```

CGLIB 代理创建类：

```
public class CglibProxyCreator implements ProxyCreator {

    private Object target;

    private MethodInterceptor  methodInterceptor;

    public CglibProxyCreator(Object target, MethodInterceptor methodInterceptor) {
	    assert (target != null && methodInterceptor != null);
	    this.target = target;
	    this.methodInterceptor = methodInterceptor;
    }

    public Object getProxy() {
	    Enhancer enhancer = new Enhancer();
	    // 设置代理类的父类
	    enhancer.setSuperclass(target.getClass());
	    // 设置代理逻辑
	    enhancer.setCallback(methodInterceptor);
	    // 创建代理对象
	    return enhancer.create();
    }
}
```

方法拦截器：

```
public class TankRemanufacture implements MethodInterceptor {

    public Object intercept(Object o, Method method, Object[] objects, MethodProxy methodProxy) throws Throwable {
	    if (method.getName().equals("run")) {
	        System.out.println("正在重造59 tank");
	        System.out.println("重造成功");
	        System.out.println("已起飞");
	        methodProxy.invokeSuper(o, objects);
	        System.out.println("已击落敌机，正在返航");
	        return null;
	    }
	    return methodProxy.invokeSuper(o, objects);
    }
}
```

代码测试：

```
public class CglibProxyCreatorTest {

    @Test
    public void getProxy() {
	    ProxyCreator proxyCreator = new CglibProxyCreator(new Tank59(), new TankRemanufacture());
	    Tank59 tank59 = (Tank59) proxyCreator.getProxy();

	    System.out.println("proxy class = " + tank59.getClass());
	    tank59.run();
	    System.out.println("射击测试:");
	    tank59.shoot();
    }
}
```

结果为：

```
proxy class = class com.test.CGLIB.Tank59$$EnhancerByCGLIB$$1e296a91
正在重造59 tank
重造成功
已起飞
极速前行中
已击落敌机，正在返航
射击测试:
轰轰轰...
```

**两种代理方式总结：**

1. JDK 代理不需要第三方库的支持，只需要 JDK 环境即可进行代理，即实现 InvocationHandler，使用 Proxy.newProxyInstance 产生代理对象，并且被代理的对象必须实现接口
2. CGLIB 必须依赖 CGLIB 的类库，将代理对象类的 class 文件加载进来，通过修改其字节码生成子类来处理
3. 如果目标对象实现了接口，默认情况下会采用 JDK 的动态代理实现 AOP，也可强制使用 CGLIB；如果目标对象没有实现接口，必须使用 CGLIB 库
4. 可以通过添加CGLIB库(aspectjrt-xxx.jar、aspectjweaver-xxx.jar、cglib-nodep-xxx.jar)或在 Spring 配置文件中加入 `` 强制使用 CGLIB 实现 AOP
5. JDK 动态代理只能对实现了接口的类生成代理，而不能针对类；CGLIB 是针对类实现代理，主要是对指定的类生成一个子类，覆盖其中的方法，并覆盖其中方法实现增强，但是因为采用的是继承，所以该类或方法最好不要声明成 final，对于 final 类或方法，是无法继承的

### IOC 与 AOP 的协作

在 loadBeanDefinitions 的时候，会找到所有实现了 BeanPostProcessor (下面简称为 BPP)的类，并放在一个缓存里，相当于注册了所有的 BeanPpostProcessor 的实现类。在后续通过 getBean 方法获取 bean 的时候，会在 initializeBean 中遍历之前的那个 BPP 缓存，执行对应的 postProcessBeforeInitialization 和 postProcessAfterInitialization 方法。

这里只说明一下 postProcessAfterInitialization 方法的执行：

1. 如果是一些基础类则直接返回 bean
2. 先从 BeanFactory(从 xml 文件解析后所有的 bean 已经放入 BeanFactory 的缓存中) 查找 AspectJExpressionPointcutAdvisor 类型的对象，得到一个 list
3. 遍历该 list，使用 Pointcut 对象匹配当前 bean 对象，如果匹配成功执行下一步，否则继续匹配
4. 若匹配成功创建代理对象和对应的拦截器(Advice)，并返回代理对象(最终放入 Spring 容器的是代理对象)

在 main 函数中调用目标方法时，先执行拦截器中的方法，再执行目标方法，到此 IOC 与 AOP 的协作已经基本完成。

具体来说，在 simple-spring 中，AOP 和 IOC 产生联系的具体实现类是 AspectJAwareAdvisorAutoProxyCreator（下面简称 AutoProxyCreator），这个类实现了 BeanPostProcessor (该类会被存入之前的 BPP 缓存中) 和 BeanFactoryAware 接口。BeanFactory 在注册 BeanPostProcessor 接口相关实现类的阶段，会将其本身注入到 AutoProxyCreator 中，为后面 AOP 给 bean 生成代理对象做准备。

BeanFactory 初始化结束后，AOP 与 IOC 桥梁类 AutoProxyCreator(BeanPostProcessor 的实现类) 也完成了实例化，并被缓存在 BeanFactory 中，静待 BeanFactory 实例化 bean。当外部产生调用，BeanFactory 开始实例化 bean 时。AutoProxyCreator 就开始悄悄地工作了，细节如下(前面已经说过，现在再看一次)：

1. 从 BeanFactory 查找所有实现了 PointcutAdvisor(这里特指 AspectJExpressionPointcutAdvisor) 接口的切面对象，切面对象中包含了实现 Pointcut 和 Advice 接口的对象
2. 使用 Pointcut 中的表达式对象匹配当前的 bean 对象。如果匹配成功，进行下一步；否则继续用其它切面对象继续匹配当前 bean，直到逻辑正常结束
3. JdkDynamicAopProxy 对象为匹配成功的 bean 生成代理对象，并将代理对象返回给 BeanFactory

### 一点思考

**由 Spring 框架中的单例模式想到的：**

为什么对于原型模式的 bean ，Spring 使用懒加载，而对于单例模式的 bean 却在 Spring 初始化就注入了？

Spring 的 依赖注入（包括 lazy-init 方式）都是发生在 AbstractBeanFactory 的 getBean 中。getBean 的 doGetBean 方法调用 getSingleton 对单例的 bean 进行创建。如果是非 lazy-init 的方式，则在容器初始化时就被调用，而 lazy-init 的方式，则会在用户向容器第一次索要 bean 时调用。

单例的实现方式总共有以下几种：懒汉模式，饿汉线程非安全模式，饿汉线程安全模式，内部类模式，枚举模式。

而在 Spring 依赖注入时，使用了双重判断加锁的单例模式，首先从缓存中获取 bean 实例，如果为 null，对缓存 map 加锁，然后再从缓存中获取 bean，如果继续为 null，就创建一个 bean。这样双重判断，能够避免在加锁的瞬间，有其他依赖注入引发 bean 实例的创建，从而造成重复创建的结果。

那么为什么需要懒加载呢？当我们要访问的数据量过大时，因为内存容量有限，为了减少系统资源的消耗，我们让数据在需要的时候才进行加载，这时就用到懒加载。