## 11课：Spring Data JPA 的配置之 Spring Boot 2.0 加载详解

### Spring Boot Autoconfigure 介绍

本章节我们简单的介绍一下 Spring Boot 都在帮我们做些什么事情，看一下下面这个图对 SpringBoot 做整体认识：

![enter image description here](http://images.gitbook.cn/3d4fdb50-4603-11e8-88ee-fb64bbe1a046)

这里给大家介绍一个学习方法，就是学习任何东西之前都要对整体有个认识，然后了解知识点处于什么位置。Spring Boot 核心的五大功能，如上图所示。

Spring Boot 帮我们封装的 Autoconfigure，实现自动加载配置文件的核心能力，所有的 Spring Boot 项目一开始就会引用 @SpringBootApplication 注解，查看其源码如下。

```
package org.springframework.boot.autoconfigure;
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(
    excludeFilters = {@Filter(
    type = FilterType.CUSTOM,
    classes = {TypeExcludeFilter.class}
), @Filter(
    type = FilterType.CUSTOM,
    classes = {AutoConfigurationExcludeFilter.class}
)}
)
public @interface SpringBootApplication {
    @AliasFor(
        annotation = EnableAutoConfiguration.class
    )
    Class<?>[] exclude() default {};
    @AliasFor(
        annotation = EnableAutoConfiguration.class
    )
    String[] excludeName() default {};
    @AliasFor(
        annotation = ComponentScan.class,
        attribute = "basePackages"
    )
    String[] scanBasePackages() default {};
    @AliasFor(
        annotation = ComponentScan.class,
        attribute = "basePackageClasses"
    )
    Class<?>[] scanBasePackageClasses() default {};
}
```

其是 Spring Boot 的核心能力自动加载扫描包的能力，其中自动装配了 @EnableAutoConfiguration，通过 @EnableAutoConfiguration 启用 Spring 应用程序上下文的自动配置，这个注解会导入一个 EnableAutoConfigurationImportSelector 的类，而这个类会去读取一个 spring.factories 下 key 为 EnableAutoConfiguration 对应的全限定名的值。

spring.factories 里面配置的那些类，主要作用是告诉 Spring Boot 这个 stareter 所需要加载的那些 xxxAutoConfiguration 类，也就是你真正的要自动注册的那些 bean 或功能。然后，我们实现一个 spring.factories 指定的类，标上 @Configuration 注解，一个 starter 就定义完了。

**SpringBoot 中的 META-INF/spring.factories**（完整路径：spring-boot/spring-boot-autoconfigure/src/main/resources/META-INF/spring.factories）中关于 EnableAutoConfiguration 的这段配置如下 ：

```
org.springframework.boot.autoconfigure.admin.SpringApplicationAdminJmxAutoConfiguration,\
org.springframework.boot.autoconfigure.aop.AopAutoConfiguration,\
org.springframework.boot.autoconfigure.data.jpa.JpaRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.orm.jpa.HibernateJpaAutoConfiguration,\
```

可以发现 JpaRepositoriesAutoConfiguration 和 HibernateJpaAutoConfiguration 帮我们装置了 JPA 相关的配置。

#### Starter

Spring Boot 中的 starter 概念是非常重要的机制，能够抛弃以前繁杂的配置，统一集成进 starter，应用者只需要引入 starter jar 包，Spring Boot 就能自动扫描到要加载的信息。有以下非常重要的两点：

- starter 让我们摆脱了各种依赖库的处理，N 多项目的 jar 依赖配置等都不需要我们关心，也不需要配置装载 Bean 等帮我们摆脱各种信息的困扰。
- Spring Boot 会自动通过 classpath 路径下的类发现需要的 Bean，并织入 bean。

就像前面讲过的，如果你想使用 Spring 和用 JPA 访问数据库，只要依赖 spring-boot-starter-data-jpa 即可。

#### spring-boot-actuator

提供 SpringBoot 应用的外围支撑性功能，比如：

- Endpoints，SpringBoot 应用状态监控管理；
- HealthIndicator，SpringBoot 应用健康指示表；
- 提供 metrics 支持；
- 提供远程 shell 支持。

就是帮我们提供了各种监控指标，默认的访问链接如下：

![enter image description here](http://images.gitbook.cn/a4261f30-4606-11e8-981a-4183f6e5c19e)

#### spring-boot-tools

spring-boot-tools 提供了 SpringBoot 开发者的常用工具集。诸如，spring-boot-gradle-plugin、spring-boot-maven-plugin，就是这个工程里面的。

#### spring-boot-cli

spring-boot-cli 是 Spring Boot 命令行交互工具，可用于使用 Spring 进行快速原型搭建，可以用它直接运行 Groovy 脚本。如果你不喜欢 Maven 或 Gradle，Spring 提供了 CLI（Command Line Interface）来开发运行 Spring 应用程序，你可以使用它来运行 Groovy 脚本，甚至编写自定义命令。

### Spring Data JPA 都有哪些扩展配置

#### 传统的 XML 的配置方法

我们要集成 Spring Data JPA，在没有引用 Spring Boot 的时候通常的做法如下。

**（1）XML 配置**

```
<?xml version="1.0" encoding="UTF-8"?>
<beans:beans xmlns:beans="http://www.springframework.org/schema/beans"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns="http://www.springframework.org/schema/data/jpa"
  xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd
    http://www.springframework.org/schema/data/jpa
    http://www.springframework.org/schema/data/jpa/spring-jpa.xsd">
  <repositories base-package="com.acme.repositories" />
</beans:beans>
```

Spring Data 通过 repositories 标签，允许简单地定义一个基本方案。Spring 是扫描 com.acme.repositories 和所有的子包接口扩展 Repository 或其 sub-interfaces 之一。对于发现的每个接口，注册持久性特定于技术的基础设施 FactoryBean 创建合适的代理，处理调用查询的方法。每个 bean 注册在 bean 名称来源于接口名称，所以一个接口 UserRepository 将注册下 userRepository 的 base-package 属性允许通配符，可以定义一个模式扫描包。

**（2）使用过滤器**

大家可能希望更细粒度的控制接口获得创建 bean 实例，要做到这一点，使用 `< include-filter / >` 和 `` 元素内 ``。 元素的语义是完全等价的 Spring’s 上下文名称空间。要从实例库中排除某些接口，可以使用以下配置：

```
<repositories base-package="com.acme.repositories">
  <context:exclude-filter type="regex" expression=".*SomeRepository" />
</repositories>
```

#### @EnableJpaRepositories 详解

**（1）基于 JavaConfig，基于注解的存储库配置示例**

```
@Configuration
@EnableJpaRepositories("com.acme.repositories")
class ApplicationConfiguration {
}
```

**（2）@EnableJpaRepositories 加载时机**

通过前面讲的，当引用 SpringBoot 之后，添加 @SpringBootApplication 什么都不需要做就自动加载了。正如前面讲的我们查看其源码，找到 spring.factories 默认加载了 org.springframework.boot.autoconfigure.data.jpa.JpaRepositoriesAutoConfiguration，查看 JpaRepositoriesAutoConfiguration 源码：

```
class JpaRepositoriesAutoConfigureRegistrar extends AbstractRepositoryConfigurationSourceSupport {
    JpaRepositoriesAutoConfigureRegistrar() {
    }
    @EnableJpaRepositories
    private static class EnableJpaRepositoriesConfiguration {
        private EnableJpaRepositoriesConfiguration() {
        }
    }
}
```

发现其里面帮我们加载了 @EnableJpaRepositories，当然了也可以在我们的 javaconfig 里面直接配置 @EnableJpaRepositories 覆盖默认配置。

#### @EnableJpaRepositories 详解

```
@Import(JpaRepositoriesRegistrar.class)
public @interface EnableJpaRepositories {
   String[] value() default {};
   String[] basePackages() default {};
   Class<?>[] basePackageClasses() default {};
   Filter[] includeFilters() default {};
   Filter[] excludeFilters() default {};
   String repositoryImplementationPostfix() default "Impl";
   String namedQueriesLocation() default "";
   Key queryLookupStrategy() default Key.CREATE_IF_NOT_FOUND;
   Class<?> repositoryFactoryBeanClass() default JpaRepositoryFactoryBean.class;
   Class<?> repositoryBaseClass() default DefaultRepositoryBaseClass.class;
   String entityManagerFactoryRef() default "entityManagerFactory";
   String transactionManagerRef() default "transactionManager";
   boolean considerNestedRepositories() default false;
   boolean enableDefaultTransactions() default true;
}
```

**（1）value 等于 basePackage**

用于配置扫描 Repositories 所在的 package 及子 package。简单配置中的配置则等同于此项配置值，basePackages 可以配置为单个字符串，也可以配置为字符串数组形式。

```
@EnableJpaRepositories(basePackages = "com.jack")
@EnableJpaRepositories(
basePackages = {"com.jack.sample.repository",               "com.jack.tower.repository"})
```

**（2）basePackageClasses 指定 Repository 所在包的类**

```
@EnableJpaRepositories(basePackageClasses = BookRepository.class)
@EnableJpaRepositories(basePackageClasses = {ShopRepository.class, OrganizationRepository.class})
```

备注：测试的时候发现，配置包类的一个 Repositories 类，该包内其他 Repositores 也会被加。

**（3）includeFilters**

包含过滤器，该过滤区采用 ComponentScan 的过滤器类，哪些包含在内。

```
@EnableJpaRepositories( includeFilters={@ComponentScan.Filter(type=FilterType.ANNOTATION, value=Repository.class)})
```

**（4）excludeFilters**

不包含过滤器，该过滤区采用 ComponentScan 的过滤器类，哪些不包含在内。

```
@EnableJpaRepositories(
         excludeFilters={
                 @ComponentScan.Filter(type=FilterType.ANNOTATION, value=Service.class),
                 @ComponentScan.Filter(type=FilterType.ANNOTATION, value=Controller.class)})
```

> 注意：实际使用场景：1、2、3、4 等我们 DATA 部分不止有关系数据库的时候，当我们 MongoDB 或者 Redis 或者关系数据库 JPA 混合使用的时候，就会用到前面 4 种方式，来指定不同的 package 用不同的 Spring Data 的实现。

**（5）repositoryImplementationPostfix 实现类的默认尾部结束字符**

```
@EnableJpaRepositories(value="com.jackzhang.example.quickstart.repository",repositoryImplementationPostfix="Impl")默认"Impl"结尾。比如：ShopRepository，对应的为ShopRepositoryImpl
```

**（6）namedQueriesLocation**

当我们用原始 SQL 的时候：named SQL 存放的位置，默认为 META-INF/jpa-named-queries.properties，内容如下：

```
Todo.findBySearchTermNamedFile=SELECT t FROM Table t WHERE LOWER(t.description) LIKE LOWER(CONCAT('%', :searchTerm, '%')) ORDER BY t.title ASC
```

**（7）queryLookupStrategy**

构建条件查询的策略，包含三种方式 CREATE、`USE_DECLARED_QUERY`、`CREATE_IF_NOT_FOUND`：

- CREATE：按照接口名称自动构建查询；
- `USE_DECLARED_QUERY`：用户声明查询；
- `CREATE_IF_NOT_FOUND`：先搜索用户声明的，不存在则自动构建，该策略针对如下通过接口名称自动生成查询的场景。

**（8）repositoryFactoryBeanClass**

指定 Repository 的工厂类。

**（9）entityManagerFactoryRef**

实体管理工厂引用名称，对应到 @Bean 注解对应的方法。

```
@Bean
 public LocalContainerEntityManagerFactoryBean entityManagerFactory() {
         LocalContainerEntityManagerFactoryBean entityManagerFactoryBean = new LocalContainerEntityManagerFactoryBean();
    ...
         return entityManagerFactoryBean;
}
```

**（10）transactionManagerRef**

事务管理工厂引用名称，对应到 @Bean 注解对应的方法。

```
@Bean
public JpaTransactionManager transactionManager() {
JpaTransactionManager transactionManager = new JpaTransactionManager();
transactionManager.setEntityManagerFactory(entityManagerFactory().getObject());
return transactionManager;
}
```

### JpaRepositoriesAutoConfiguration 关键源码解析

我们再来回头看一下关键源码，做到心中有数：

```
@Configuration
@ConditionalOnBean(DataSource.class)
@ConditionalOnClass(JpaRepository.class)
@ConditionalOnMissingBean({ JpaRepositoryFactoryBean.class,
      JpaRepositoryConfigExtension.class })
@ConditionalOnProperty(prefix = "spring.data.jpa.repositories", name = "enabled", havingValue = "true", matchIfMissing = true)
@Import(JpaRepositoriesAutoConfigureRegistrar.class)
@AutoConfigureAfter(HibernateJpaAutoConfiguration.class)
public class JpaRepositoriesAutoConfiguration {
....
}
```

通过源码发现此类加载了 HibernateJpaAutoConfiguration 开启了 JPA 的默认配置文件源码如下：

```
@Configuration
@ConditionalOnClass({ LocalContainerEntityManagerFactoryBean.class, EntityManager.class })
@Conditional(HibernateEntityManagerCondition.class)
@AutoConfigureAfter({ DataSourceAutoConfiguration.class })
public class HibernateJpaAutoConfiguration extends JpaBaseConfiguration {
......}
```

还有类 JpaRepositoriesAutoConfigureRegistrar 开启了 @EnableJpaRepositories，源码如下：

```
class JpaRepositoriesAutoConfigureRegistrar
      extends AbstractRepositoryConfigurationSourceSupport {
   @Override
   protected Class<? extends Annotation> getAnnotation() {
      return EnableJpaRepositories.class;
   }
   @EnableJpaRepositories
   private static class EnableJpaRepositoriesConfiguration {
   }
......
}
```

类关系图：

![enter image description here](http://images.gitbook.cn/a467ad50-460c-11e8-a222-fb7d71af208a)

DataSource 部分的内容后面章节会详细介绍。