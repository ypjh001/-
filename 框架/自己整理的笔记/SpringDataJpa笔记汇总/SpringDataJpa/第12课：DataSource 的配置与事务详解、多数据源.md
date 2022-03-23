## 第12课：DataSource 的配置与事务详解、多数据源

本节内容来讲讲数据源那点事儿，及其在实际工作中的最佳实践技巧，包括 Druid 和 Hikari 及其内存数据库那些事儿，把 DataSource 彻底搞明白。

### 默认数据源

回顾第01课我们的 Demo：

```
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
spring.datasource.url=jdbc:mysql://127.0.0.1:3306/test?useSSL=false
spring.datasource.username=root
spring.datasource.password=123456
```

但是在实际工作中不可能这么简单，因为会用其他数据源，而不是用的默认数据源。我们先来一步一步了解一下，一起来开启探索它默认的数据源之旅吧。

#### 通过三种方法来查看默认的 DataSource 是什么

**（1）日志法：在 application.properties 增加如下配置**

```
logging.level.org.springframework=DEBUG
```

然后当我们启动成功之后，通过开发工具 Intellij IDEA 的 Debug 的 Console 控制台，搜索“DataSource”，找到了如下日志，发现它默认是 JDBC 的 Pool 的 DataSource。

spring.datasource.type=com.zaxxer.hikari.HikariDataSource，需要注意的是这是 Spring Boot 2.0 里面的新特性，代替了 1.5** 版本里面的 org.apache.tomcat.jdbc.pool.DataSource 的数据源，hikari 的口碑可以性能测试行业内的口碑逐渐代替了 Tomcat 的 datasource。

**（2）Debug 方法：在 manager 里面的如下代码**

在“userRepository.findByLastName(names);” 设置一个断点，然后请求一个 URL 让断点进来，然后通过开发工具 Intellij IDEA 的 Debug 的 Memory View 视图，里面搜索

![enter image description here](http://images.gitbook.cn/0f9f5e70-462f-11e8-981a-4183f6e5c19e)

也能发现 DataSource，然后双击就能看到我们想看的内容。

**（3）最原理的方法、最常用的、原理分析方法**

回到 QuickStartApplication，单击 @SpringBootApplication 查看其源码关键部分如下：

```
@SpringBootConfiguration
    @EnableAutoConfiguration
    public @interface SpringBootApplication {......}
```

打开 @EnableAutoConfiguration 所在 JAR 包，打开 spring-boot-autoconfigure-2.0.0.RELEASE.jar/META-INF/spring.factories 文件，发现如下内容：

```
//# Auto Configure
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
......
org.springframework.boot.autoconfigure.data.jpa.JpaRepositoriesAutoConfiguration
```

打开 JpaRepositoriesAutoConfiguration 类，内容如下：

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
}
```

这时候可以发现，如果使用了 Spring Boot 的注解方式和传统的 XML 配置方式是有优先级的，如果配置了 XML 中的 JpaRepositoryFactoryBean，那么就会沿用 XML 配置的一整套，而通过 @ConditionalOnMissingBean 这个注解来判断，就不会加载 Spring Boot 的 JpaRepositoriesAutoConfiguration 此类的配置，还有就是前提条件 DataSource 和 JpaRepository 必须有相关的 Jar 存在。

打开 HibernateJpaAutoConfiguration 类：

```
@Configuration
@ConditionalOnClass({ LocalContainerEntityManagerFactoryBean.class, EntityManager.class })
@Conditional(HibernateEntityManagerCondition.class)
@AutoConfigureAfter({ DataSourceAutoConfiguration.class })
public class HibernateJpaAutoConfiguration extends JpaBaseConfiguration {......}
```

这个时候发现了 DataSourceAutoConfiguration 的配置类即 datasource 的配置内容有哪些？

打开 DataSourceAutoConfiguration，此时发现了我们最关键的类出现了。

```
@Configuration
@ConditionalOnClass({ DataSource.class, EmbeddedDatabaseType.class })
@EnableConfigurationProperties(DataSourceProperties.class)
@Import({ DataSourcePoolMetadataProvidersConfiguration.class,
        DataSourceInitializationConfiguration.class })
public class DataSourceAutoConfiguration {......}
```

先看 DataSourcePoolMetadataProvidersConfiguration 类吧，内容如下：

```
@Configuration
public class DataSourcePoolMetadataProvidersConfiguration {
    //tomcat.jdbc.pool.DataSource前提条件需要引入tomcat-jdbc.jar
    @Configuration
    @ConditionalOnClass(org.apache.tomcat.jdbc.pool.DataSource.class)
    static class TomcatDataSourcePoolMetadataProviderConfiguration {
        @Bean
        public DataSourcePoolMetadataProvider tomcatPoolDataSourceMetadataProvider() {
            return (dataSource) -> {
                if (dataSource instanceof org.apache.tomcat.jdbc.pool.DataSource) {
                    return new TomcatDataSourcePoolMetadata(
                            (org.apache.tomcat.jdbc.pool.DataSource) dataSource);
                }
                return null;
            };
        }
    }
    //HikariDataSource.class前提需要引入HikariCP-2.7.8.jar
    @Configuration
    @ConditionalOnClass(HikariDataSource.class)
    static class HikariPoolDataSourceMetadataProviderConfiguration {
        @Bean
        public DataSourcePoolMetadataProvider hikariPoolDataSourceMetadataProvider() {
            return (dataSource) -> {
                if (dataSource instanceof HikariDataSource) {
                    return new HikariDataSourcePoolMetadata(
                            (HikariDataSource) dataSource);
                }
                return null;
            };
        }
    }
    //CommonsDbcp 数据源，前提也是需要引入CommonsDbcp**.jar
    @Configuration
    @ConditionalOnClass(BasicDataSource.class)
    static class CommonsDbcp2PoolDataSourceMetadataProviderConfiguration {
        @Bean
        public DataSourcePoolMetadataProvider commonsDbcp2PoolDataSourceMetadataProvider() {
            return (dataSource) -> {
                if (dataSource instanceof BasicDataSource) {
                    return new CommonsDbcp2DataSourcePoolMetadata(
                            (BasicDataSource) dataSource);
                }
                return null;
            };
        }
    }
}
```

通过查看它的代码可发现，Spring Boot 为我们的 DataSource 提供了最常见的三种默认配置：

- HikariDataSource
- tomcat的JDBC
- apache的dbcp

而最终用哪个？就看你引用了哪个 datasoure 的 jar 包了。因为开篇的案例用的是 Spring Boot 2.0 的默认配置，而 2.0 放弃了默认引用的 Tomcat 的容器，而选用了 HikariDataSource 的配置，成为了 Java 语言里面公认的好的 data source，所以默认用的是 Hikari 的 DataSource 及其 HikariDataSourcePoolMetadata 连接池。当我们引用了 Jetty 或者 netty 等容器，连接池和 datasource 的实现方式也会跟着变的。

#### Datasource 和 JPA 都有哪些配置属性

我们接着上面的类 DataSourceAutoConfiguration，通过 @EnableConfigurationProperties(DataSourceProperties.class) 找到了 datasource 该如何配置，打开 DataSourceProperties 源码：

```
@ConfigurationProperties(prefix = "spring.datasource")
public class DataSourceProperties
      implements BeanClassLoaderAware, EnvironmentAware, InitializingBean {
/**
 * Name of the datasource.
 */
private String name = "testdb";
/**
 * Generate a random datasource name.
 */
private boolean generateUniqueName;
/**
 * Fully qualified name of the JDBC driver. Auto-detected based on the URL by default.
 */
private String driverClassName;
/**
 * JDBC url of the database.
 */
private String url;
/**
 * Login user of the database.
 */
private String username;
/**
 * Login password of the database.
 */
private String password;
/**
 * JNDI location of the datasource. Class, url, username & password are ignored when
 * set.
 */
private String jndiName;
......//如果还有一些特殊的配置直接看这个类的源码即可。
}
```

看到了配置数据的关键的几个属性的配置，及其一共有哪些属性值可以去配置。@ConfigurationProperties(prefix = "spring.datasource") 这个告诉我们 application.properties 里面的 datasource 相关的配置必须有 spring.datasource 开头，这样当启动的时候 DataSourceProperties 就会自动加载进来 datasource 的一切配置。正如我们前面配置的一样：

```
spring.datasource.url=jdbc:mysql://127.0.0.1:3306/test
spring.datasource.username=jack
spring.datasource.password=jack123
......这里省略了一些其他datasource的key配置。
```

> 其实到这里如果自定义配置时也可以学习 Spring Data 的这种 properteis 的加载方式自定义**Properties.java，包括 DataSource 的配置方法，可以借鉴当时写的framework.jar。

#### JpaBaseConfiguration

我们回过头来再来看 HibernateJpaAutoConfiguration 的父类 JpaBaseConfiguration 打开关键内容如下：

```
@EnableConfigurationProperties(JpaProperties.class)
@Import(DataSourceInitializedPublisher.Registrar.class)
public abstract class JpaBaseConfiguration implements BeanFactoryAware {
   private final DataSource dataSource;
   private final JpaProperties properties;
......}
```

这个时候发现了 JpaProperties 类：

```
@ConfigurationProperties(prefix = "spring.jpa")
public class JpaProperties {
  //jpa原生的一些特殊属性
   private Map<String, String> properties = new HashMap<String, String>();
   //databasePlatform名字，默认和Database一样。
   private String databasePlatform;
   //数据库平台MYSQL、DB2、H2......
   private Database database;
   //是否根据实体创建Ddl
   private boolean generateDdl = false;
   //是否显示sql，默认不显示
   private boolean showSql = false;
   private Hibernate hibernate = new Hibernate();
......
}
```

此时再打开 Hibernate 类：

```
public static class Hibernate {
private String ddlAuto;
/**
 * Use Hibernate's newer IdentifierGenerator for AUTO, TABLE and SEQUENCE. This is
 * actually a shortcut for the "hibernate.id.new_generator_mappings" property.
 * When not specified will default to "false" with Hibernate 5 for back
 * compatibility.
 */
private Boolean useNewIdGeneratorMappings;
@NestedConfigurationProperty
private final Naming naming = new Naming();
......//我们看到Hibernate类就这三个属性。
}
```

再打开 Naming 源码看一下命名规范：

```
public static class Naming {
   private static final String DEFAULT_HIBERNATE4_STRATEGY = "org.springframework.boot.orm.jpa.hibernate.SpringNamingStrategy";
   private static final String DEFAULT_PHYSICAL_STRATEGY = "org.springframework.boot.orm.jpa.hibernate.SpringPhysicalNamingStrategy";
   private static final String DEFAULT_IMPLICIT_STRATEGY = "org.springframework.boot.orm.jpa.hibernate.SpringImplicitNamingStrategy";
   /**
    * Hibernate 5 implicit naming strategy fully qualified name.
    */
   private String implicitStrategy;
   /**
    * Hibernate 5 physical naming strategy fully qualified name.
    */
   private String physicalStrategy;
   /**
    * Hibernate 4 naming strategy fully qualified name. Not supported with Hibernate
    * 5.
    */
   private String strategy;
......}
```

看到这里面 Naming 命名策略，兼容了 Hibernate4 和 Hibernate5 并且给出了默认的策略，后面章节我们做详细解释。

所以配置文件中关于 JPA 的配置基本上就这些配置项。

```
spring.jpa.database-platform=mysql
spring.jpa.generate-ddl=false
spring.jpa.hibernate.ddl-auto=none
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true
spring.jpa.properties.hibernate.type=trace
spring.jpa.properties.hibernate.use_sql_comments=true
spring.jpa.properties.hibernate.jdbc.batch_size=50
```

#### Configuration 思路，实战学习方法

> 其实在实际工作中，若遇到问题，经常看到开发人员去百度进行狂搜，看看怎么配置的，然后试了了半天发现怎么配置都没效果。其实这里给大家提供了一个思路，我们在找配置项的时候，看看源码都支持哪些 key，而这些 key 分别代表什么意思然后再到百度搜索，这样我们能对症下药，正确完美的完成我们的配置文件的配置。

### AliDruidDataSource 的配置

#### AliDruid 配置方法

（1）在实际工作中，由于 HikariCP 和 Druid 应该各有千秋，会发现偏向于监控，有很多国内开发 者使用频次最高的 AliDruid，我们来看看看如何配置。

```
<!--druid-->
    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>druid</artifactId>
        <version>1.1.5</version>
    </dependency>
```

（2）一样的思路，我们打开 DruidDataSourceAutoConfigure 配置类。

```
@Configuration
@ConditionalOnClass(com.alibaba.druid.pool.DruidDataSource.class)
@AutoConfigureBefore(DataSourceAutoConfiguration.class)
@EnableConfigurationProperties({DruidStatProperties.class, DataSourceProperties.class})
@Import({DruidSpringAopConfiguration.class,
        DruidStatViewServletConfiguration.class,
        DruidWebStatFilterConfiguration.class,
        DruidFilterConfiguration.class})
public class DruidDataSourceAutoConfigure {
    @Bean
    @ConditionalOnMissingBean
    public DataSource dataSource() {
        return new DruidDataSourceWrapper();
    }
    /**
     * Register the {@link DataSourcePoolMetadataProvider} instances to support DataSource metrics.
     *
     * @see DataSourcePoolMetadataProvidersConfiguration
     */
    @Bean
    public DataSourcePoolMetadataProvider druidDataSourcePoolMetadataProvider() {
        return new DataSourcePoolMetadataProvider() {
            @Override
            public DataSourcePoolMetadata getDataSourcePoolMetadata(DataSource dataSource) {
                if (dataSource instanceof DruidDataSource) {
                    return new DruidDataSourcePoolMetadata((DruidDataSource) dataSource);
                }
                return null;
            }
        };
    }
}
```

我们发现 Druid 继承了 DataSourceProperties 的配置。

（3）我们打开 DruidDataSourceWrapper：

```
@ConfigurationProperties("spring.datasource.druid")
class DruidDataSourceWrapper extends DruidDataSource implements InitializingBean {
    @Autowired
    private DataSourceProperties basicProperties;
    @Override
    public void afterPropertiesSet() throws Exception {
        //if not found prefix 'spring.datasource.druid' jdbc properties ,'spring.datasource' prefix jdbc properties will be used.
        if (super.getUsername() == null) {
            super.setUsername(basicProperties.determineUsername());
        }
        if (super.getPassword() == null) {
            super.setPassword(basicProperties.determinePassword());
        }
        if (super.getUrl() == null) {
            super.setUrl(basicProperties.determineUrl());
        }
        if (super.getDriverClassName() == null) {
            super.setDriverClassName(basicProperties.determineDriverClassName());
        }
    }
......}
```

发现了 DataSource 的配置方法：

```
spring.datasource.druid.url=jdbc:mysql://127.0.0.1:3306/test # 或spring.datasource.url= 
spring.datasource.druid.username=jack # 或spring.datasource.username=
spring.datasource.druid.password=jack123 # 或spring.datasource.password=
spring.datasource.druid.driver-class-name=com.mysql.jdbc.Driver #或 spring.datasource.driver-class-name=
```

（4）如果我们再打开 DruidDataSource 类，就会发现连接池的配置方法：

```
spring.datasource.druid.initial-size=
spring.datasource.druid.max-active=
spring.datasource.druid.min-idle=
spring.datasource.druid.max-wait=
spring.datasource.druid.pool-prepared-statements=
spring.datasource.druid.max-pool-prepared-statement-per-connection-size= 
spring.datasource.druid.max-open-prepared-statements= #和上面的等价
spring.datasource.druid.validation-query=
spring.datasource.druid.validation-query-timeout=
spring.datasource.druid.test-on-borrow=
spring.datasource.druid.test-on-return=
spring.datasource.druid.test-while-idle=
spring.datasource.druid.time-between-eviction-runs-millis=
spring.datasource.druid.min-evictable-idle-time-millis=
spring.datasource.druid.max-evictable-idle-time-millis=
spring.datasource.druid.filters= #配置多个英文逗号分隔
....//more
```

如果再继续往上面看 DruidAbstractDataSource，会发现很多默认值。

（5）如果依次打开以下这些类，也会发现 Druid 的更多配置：

```
@Import({DruidSpringAopConfiguration.class,
        DruidStatViewServletConfiguration.class,
        DruidWebStatFilterConfiguration.class,
        DruidFilterConfiguration.class})
```

WebStatFilter 配置，说明请参考 Druid Wiki，配置 WebStatFilter：

```
spring.datasource.druid.web-stat-filter.enabled= #是否启用StatFilter默认值true
spring.datasource.druid.web-stat-filter.url-pattern=
```

StatViewServlet 配置，说明请参考 Druid Wiki，配置 StatViewServlet：

```
spring.datasource.druid.stat-view-servlet.enabled= #是否启用StatViewServlet默认值true
spring.datasource.druid.stat-view-servlet.login-username=
spring.datasource.druid.stat-view-servlet.login-password=
Druid的更多配置请参看官方文档吧，只是给大家举例如何一步一步的查看这些配置，而得到如何配置。
```

#### 事务的处理及其讲解

##### 默认 @Transactional 注解式事务

（1）@EnableTransactionManagement

正常情况下，我们是需要在 ApplicationConfig 类加上 @EnableTransactionManagement 注解才能开启事务管理。通过 DataSource 的研究步骤 spring.factories 里面默认加载 TransactionAutoConfiguration 类，而我们看源码，其里面已经加了此注解，默认采用的 AdviceMode.PROXY，所以默认情况的事务管理机制是代理方式的，通过添加 @Transactional 注解式配置方法，查看 SimpleJpaRepository：

```
@Repository
@Transactional(readOnly = true)
public class SimpleJpaRepository<T, ID> implements JpaRepository<T, ID>, JpaSpecificationExecutor<T> {...}
```

所以每个 Respository 的方法是都是有默认的只读事务的。

（2）我们来查看一下 @Transactional 源码：

```
@Target({ElementType.METHOD, ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Inherited
@Documented
public @interface Transactional {
   @AliasFor("transactionManager")
   String value() default "";
   @AliasFor("value")
   String transactionManager() default "";
   Propagation propagation() default Propagation.REQUIRED;
   Isolation isolation() default Isolation.DEFAULT;
   int timeout() default TransactionDefinition.TIMEOUT_DEFAULT;
   boolean readOnly() default false;
   Class<? extends Throwable>[] rollbackFor() default {};
   String[] rollbackForClassName() default {};
   Class<? extends Throwable>[] noRollbackFor() default {};
   String[] noRollbackForClassName() default {};
}
```

@Transactional 注解中常用参数说明

| 参数名称               | 功能描述                                                     |                                                              |
| ---------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| readOnly               | 该属性用于设置当前事务是否为只读事务，设置为 true 表示只读，false 则表示可读写，默认值为 false。例如，@Transactional(readOnly=true) |                                                              |
| rollbackFor            | 该属性用于设置需要进行回滚的异常类数组，当方法中抛出指定异常数组中的异常时，则进行事务回滚。例如，指定单一异常类：@Transactional(rollbackFor=RuntimeException.class) 指定多个异常类：@Transactional(rollbackFor={RuntimeException.class, Exception.class}) |                                                              |
| rollbackForClassName   | 该属性用于设置需要进行回滚的异常类名称数组，当方法中抛出指定异常名称数组中的异常时，则进行事务回滚。例如，指定单一异常类名称：@Transactional(rollbackForClassName="RuntimeException") | 指定多个异常类名称：@Transactional(rollbackForClassName={"RuntimeException","Exception"}) |
| noRollbackFor          | 该属性用于设置不需要进行回滚的异常类数组，当方法中抛出指定异常数组中的异常时，不进行事务回滚。例如，指定单一异常类：@Transactional(noRollbackFor=RuntimeException.class) 指定多个异常类：@Transactional(noRollbackFor={RuntimeException.class, Exception.class}) |                                                              |
| noRollbackForClassName | 该属性用于设置不需要进行回滚的异常类名称数组，当方法中抛出指定异常名称数组中的异常时，不进行事务回滚。例如，指定单一异常类名称：@Transactional(noRollbackForClassName="RuntimeException") 指定多个异常类名称： @Transactional(noRollbackForClassName={"RuntimeException","Exception"}) |                                                              |
| propagation            | 该属性用于设置事务的传播行为例如：@Transactional(propagation=Propagation.NOT_SUPPORTED,readOnly=true) |                                                              |
| isolation              | 该属性用于设置底层数据库的事务隔离级别，事务隔离级别用于处理多事务并发的情况，通常使用数据库的默认隔离级别即可，基本不需要进行设置 |                                                              |

timeout 该属性用于设置事务的超时秒数，默认值为-1表示永不超时 transactionManager/value | 指定transactionManager，当有多个 datassource 的时候

（3）propagation：传播行为

所谓事务的传播行为是指，如果在开始当前事务之前，一个事务上下文已经存在，此时有若干选项可以指定一个事务性方法的执行行为。

可以看 org.springframework.transaction.annotation.Propagation 枚举类中定义了 7 个表示传播行为的枚举值：

```
public enum Propagation {
    REQUIRED(0),
    SUPPORTS(1),
    MANDATORY(2),
    REQUIRES_NEW(3),
    NOT_SUPPORTED(4),
    NEVER(5),
    NESTED(6);
}
```

- REQUIRED：如果当前存在事务，则加入该事务；如果当前没有事务，则创建一个新的事务。
- SUPPORTS：如果当前存在事务，则加入该事务；如果当前没有事务，则以非事务的方式继续运行。
- MANDATORY：如果当前存在事务，则加入该事务；如果当前没有事务，则抛出异常。
- REQUIRES_NEW：创建一个新的事务，如果当前存在事务，则把当前事务挂起。
- NOT_SUPPORTED：以非事务方式运行，如果当前存在事务，则把当前事务挂起。
- NEVER：以非事务方式运行，如果当前存在事务，则抛出异常。
- NESTED：如果当前存在事务，则创建一个事务作为当前事务的嵌套事务来运行；如果当前没有事务，则该取值等价于REQUIRED。

设置方法：通过使用propagation属性设置，例如：

```
@Transactional(propagation = Propagation.REQUIRED)
```

（4）Isolation 隔离级别

隔离级别是指若干个并发的事务之间的隔离程度，与我们开发时候主要相关的场景包括：脏读取、重复读、幻读。

我们可以看 org.springframework.transaction.annotation.Isolation 枚举类中定义了四个表示隔离级别的值：

```
public enum Isolation {
    DEFAULT(-1),
    READ_UNCOMMITTED(1),
    READ_COMMITTED(2),
    REPEATABLE_READ(4),
    SERIALIZABLE(8);
}
```

- DEFAULT：这是默认值，表示使用底层数据库的默认隔离级别，对大部分数据库而言，通常这值就是`READ_COMMITTED`。
- `READ_UNCOMMITTED`：该隔离级别表示一个事务可以读取另一个事务修改但还没有提交的数据。该级别不能防止脏读和不可重复读，因此很少使用该隔离级别。
- `READ_COMMITTED`：该隔离级别表示一个事务只能读取另一个事务已经提交的数据。该级别可以防止脏读，这也是大多数情况下的推荐值。
- `REPEATABLE_READ`：该隔离级别表示一个事务在整个过程中可以多次重复执行某个查询，并且每次返回的记录都相同。即使在多次查询之间有新增的数据满足该查询，这些新增的记录也会被忽略。该级别可以防止脏读和不可重复读。
- `SERIALIZABLE`：所有的事务依次逐个执行，这样事务之间就完全不可能产生干扰，也就是说，该级别可以防止脏读、不可重复读以及幻读，但是这将严重影响程序的性能。通常情况下也不会用到该级别。

设定方法为通过使用 isolation 属性设置，例如：

```
@Transactional(isolation = Isolation.DEFAULT)
```

（5）所以 Spring Boot 的这种默认机制，只需要在我们用事务时，在方法上或者此方法的类上加上 @Transactional注解即可。

而实际工作中，我们一般都要在 Service 层的某些方法上加事务，以保证整个方法的事务。示例如下：

```
@Transactional(rollbackOn = Exception.class)
public void saveUserInfo() throws Exception {
   userCustomerRepository.save(new UserCustomerEntity("jackzhang@mail.com","jackzhang"));
   userRepository.save(new UserInfoEntity("jack_test","name"));
   throw new Exception("test");......//此方法体有多个repository的调用，模拟异常，事务会回滚的
}
```

（6）注意的几点

- @Transactional 只能被应用到 public 方法上，对于其他非 public 的方法，如果标记了 @Transactional 也不会报错，但方法没有事务功能。
- 用 spring 事务管理器，由 spring 来负责数据库的打开、提交、回滚，默认遇到运行期例外（throw new RuntimeException("注释");）会回滚，即遇到不受检查（unchecked）的例外时回滚；而遇到需要捕获的例外（throw new Exception("注释");）不会回滚，即遇到受检查的例外（就是非运行时抛出的异常，编译器会检查到的异常叫受检查例外或说受检查异常）时，需我们指定方式来让事务回滚要想所有异常都回滚，要加上 @Transactional( rollbackFor={Exception.class,其他异常})，如果让 unchecked 例外不回滚：@Transactional(notRollbackFor=RunTimeException.class)。
- @Transactional 注解应该只被应用到 public 可见度的方法上。如果你在 protected、private 或者 package-visible 的方法上使用 @Transactional 注解，它也不会报错，但是这个被注解的方法将不会展示已配置的事务设置。
- @Transactional 注解可以被应用于接口定义和接口方法、类定义和类的 public 方法上。然而，请注意仅仅 @Transactional 注解的出现不足于开启事务行为，它仅仅是一种元数据，能够被可以识别 @Transactional 注解和上述的配置适当的具有事务行为的 beans 所使用。上面的例子中，其实正是 元素的出现开启了事务行为。
- Spring 团队的建议是你在具体的类（或类的方法）上使用 @Transactional 注解，而不要使用在类所要实现的任何接口上。当然可以在接口上使用 @Transactional 注解，但是这将只能当你设置了基于接口的代理时它才生效。因为注解是不能继承的，这就意味着如果你正在使用基于类的代理时，那么事务的设置将不能被基于类的代理所识别，而且对象也将不会被事务代理所包装（将被确认为严重的）。因此，请接受 Spring 团队的建议并且在具体的类上使用 @Transactional 注解。
- 事务有两种配置方法，一种是我们现在说的显式的注解式事务，当我们注解式事务下，不加注解 service 方法上是没有任何事务的。还有一种是隐式事务，ASPECTJ 的思路配置方法，所以不是没有加 @Transactional 注解就一定没有事务。

#### 声明式事务，又叫隐式事务，或者叫 ASPECTJ 事务

**配置方法：**

在实际工作中，每个方法都让我们加上 @Transactional 注解，可能工作量有点大，也有时候会忘，所以经常看到有开发团队配置拦截式事务。虽然 spring 官方不太推荐。只需要在我们的项目中新增一个类 AspectjTransactionConfig 即可，如下：

```
@Configuration
@EnableTransactionManagement
public class AspectjTransactionConfig {
   public static final String transactionExecution = "execution (* com.jackzhang.example..service.*.*(..))";
   @Autowired
   private PlatformTransactionManager transactionManager;
   @Bean
   public DefaultPointcutAdvisor defaultPointcutAdvisor() {
      //指定一般要拦截哪些类
      AspectJExpressionPointcut pointcut = new AspectJExpressionPointcut();
      pointcut.setExpression(transactionExecution);
      //配置advisor
      DefaultPointcutAdvisor advisor = new DefaultPointcutAdvisor();
      advisor.setPointcut(pointcut);
      //指定不同的方法用不通的策略
      Properties attributes = new Properties();
      attributes.setProperty("get*", "PROPAGATION_REQUIRED,-Exception");
      attributes.setProperty("add*", "PROPAGATION_REQUIRED,-Exception");
      attributes.setProperty("save*", "PROPAGATION_REQUIRED,-Exception");
      attributes.setProperty("update*", "PROPAGATION_REQUIRED,-Exception");
      attributes.setProperty("delete*", "PROPAGATION_REQUIRED,-Exception");
      //创建Interceptor
      TransactionInterceptor txAdvice = new TransactionInterceptor(transactionManager, attributes);
      advisor.setAdvice(txAdvice);
      return advisor;
   }
}
```

这样我们的 Service 就会自动拥有了事务，可以加 @Transactional 来覆盖全局的配置。

#### 如何配置多数据源

##### **在 application.properties 中定义两个 DataSource**

定义两个 DataSource 用来读取 application.properties 中的不同配置。如下例子中，主数据源配置为 spring.datasource.one 开头的配置，第二数据源配置为 spring.datasource.two 开头的配置。

```
//这是默认配置，我们做一下对比
spring.datasource.url=db1
spring.datasource.username=db1_username
spring.datasource.password=db1_password
//# Druid 数据源配置，继承spring.datasource.* 配置，相同则覆盖
...
spring.datasource.druid.initial-size=5
spring.datasource.druid.max-active=5
...
//#Druid 数据源 1 配置，继承spring.datasource.druid.* 配置，相同则覆盖
//#db1的配置会上面的配置
...
spring.datasource.druid.one.url=db1
spring.datasource.druid.one.username=db1_username
spring.datasource.druid.one.password=db1_password
spring.datasource.druid.one.max-active=10
spring.datasource.druid.one.max-wait=10000
...
//# Druid 数据源 2 配置，继承spring.datasource.druid.* 配置，相同则覆盖
...
spring.datasource.druid.two.url=db2
spring.datasource.druid.two.username=db2_username
spring.datasource.druid.two.password=db2_password
spring.datasource.druid.two.max-active=20
spring.datasource.druid.two.max-wait=20000
...
```

##### **定义两个 DataSourceConfigJava 类**

两个 DataSourceConfig 类内容如下：

```
@Configuration
@EnableTransactionManagement
@EnableJpaRepositories(
      entityManagerFactoryRef="entityManagerFactoryOne",
      transactionManagerRef="transactionManagerOne",
      basePackages= { "com.jackzhang.example.one" }) //设置Repository所在位置
@EnableConfigurationProperties(JpaProperties.class)
public class DataSourceOneConfig {
   /**
    * 配置数据源1
    */
   @Primary
   @Bean(name = "dataSourceOne")
   @ConfigurationProperties("spring.datasource.druid.one")
   public DataSource dataSourceOne(){
      return DruidDataSourceBuilder.create().build();
   }
   @Autowired
   @Qualifier("dataSourceOne")
   private DataSource oneDataSource;
   @Primary
   @Bean(name = "entityManagerOne")
   public EntityManager entityManager(EntityManagerFactoryBuilder builder) {
      return entityManagerFactoryOne(builder).getObject().createEntityManager();
   }
   @Primary
   @Bean(name = "entityManagerFactoryOne")
   public LocalContainerEntityManagerFactoryBean entityManagerFactoryOne (EntityManagerFactoryBuilder builder) {
      return builder
            .dataSource(oneDataSource)
            .properties(getVendorProperties(oneDataSource))
            .packages("com.jackzhang.example.one") //设置实体类所在位置
            .persistenceUnit("onePersistenceUnit")
            .build();
   }
   @Autowired
   private JpaProperties jpaProperties;
   private Map<String, String> getVendorProperties(DataSource dataSource) {
      return jpaProperties.getHibernateProperties(dataSource);
   }
   @Primary
   @Bean(name = "transactionManagerOne")
   public PlatformTransactionManager transactionManagerOne(EntityManagerFactoryBuilder builder) {
      return new JpaTransactionManager(entityManagerFactoryOne(builder).getObject());
   }
}
@Configuration
@EnableTransactionManagement
@EnableJpaRepositories(
      entityManagerFactoryRef="entityManagerFactoryTwo",
      transactionManagerRef="transactionManagerTwo",
      basePackages= { "com.jackzhang.example.two" }) //设置Repository所在位置
@EnableConfigurationProperties(JpaProperties.class)
public class DataSourceTwoConfig {
   /**
    * 配置数据源2
    */
   @Primary
   @Bean(name = "dataSourceTwo")
   @ConfigurationProperties("spring.datasource.druid.two")
   public DataSource dataSourceTwo(){
      return DruidDataSourceBuilder.create().build();
   }
   @Autowired
   @Qualifier("dataSourceTwo")
   private DataSource twoDataSource;
   @Primary
   @Bean(name = "entityManagerTwo")
   public EntityManager entityManager(EntityManagerFactoryBuilder builder) {
      return entityManagerFactoryTwo(builder).getObject().createEntityManager();
   }
   @Primary
   @Bean(name = "entityManagerFactoryTwo")
   public LocalContainerEntityManagerFactoryBean entityManagerFactoryTwo (EntityManagerFactoryBuilder builder) {
      return builder
            .dataSource(twoDataSource)
            .properties(getVendorProperties(twoDataSource))
            .packages("com.jackzhang.example.two") //设置实体类所在位置
            .persistenceUnit("twoPersistenceUnit")
            .build();
   }
   @Autowired
   private JpaProperties jpaProperties;
   private Map<String, String> getVendorProperties(DataSource dataSource) {
      return jpaProperties.getHibernateProperties(dataSource);
   }
   @Primary
   @Bean(name = "transactionManagerTwo")
   public PlatformTransactionManager transactionManagerTwo(EntityManagerFactoryBuilder builder) {
      return new JpaTransactionManager(entityManagerFactoryTwo(builder).getObject());
   }
}
```

我们发现 DataSourceTwoConfig、DataSourceOneConfig 内容基本一样，思路就是管理两套 datasource，从而带来了两套 transactionManager，分别在这两个 package 下创建各自的实体和数据访问接口即可。当然了也可以通过 @Transactional(rollbackFor = Exception.class, transactionManager= "transactionManagerOne") 来手动选择哪个数据源。

##### **多数据源的场景**

随着微服务的推行，其实很少有多数据源的场景的，作者不建议出现多数据源，当出现的时候就要想想，模块划分的是否合理，是否可以通过服务去解决，但不排除 Job 等。

### Naming 命名策略详解及其实践

用 JPA 离不开 @Entity 实体，我都知道实体里面有字段映射，而字段映射的方法有两种：

- 显式命名：在映射配置时，设置的数据库表名、列名等，就是进行显式命名，即通过 @Column 注解配置。
- 隐式命名：显式命名一般不是必要的，所以可以选择当不设置名称，这时就交由 Hibernate 进行隐式命名，另外隐式命名还包括那些不能进行显式命名的数据库标识符，即不加 @Column 注解时的默认映射规则。

#### Naming 命名策略详解

我们通过源码发现：Naming 的源码发现 Hibernate 4 的时候隐式命名策略是 org.springframework.boot.orm.jpa.hibernate.SpringNamingStrategy；而我们发现 Hibernate 5 将隐式命名策略拆分成了两步：implicitStrategy 和 physicalStrategy。从官方的文档中得知这样做的目的是提高灵活性，减少构建命名策略过程中用到的重复的信息。

- 第一个阶段是从对象模型中提取一个合适的逻辑名称，这个逻辑名称可以由用户指定，通过 @Column 和 @Table 等注解完成，也可以通过被 Hibernate 的 ImplicitNamingStrategy 指定。
- 第二个阶段是将上述的逻辑名称解析成物理名称，物理名称是由 Hibernate 中的 PhysicalNamingStrategy 决定，两个阶段是有先后顺序的。

**（1）ImplicitNamingStrategy**

当一个实体对象没有显式的指明它要映射的数据库表或者列的名称时，在 Hibernate 内部就要为我们隐式处理，比如一个实体没有在 @Table 中的指明表名，那么表名隐式的被认为是实体名，或者 @Entity 中的提供的名称。比如一个实体没有在 @Column 中的指明列名，那么列名隐式的被认为是该实体对应的字段名，而我们看到源码默认的隐式策略采用的类是：“org.springframework.boot.orm.jpa.hibernate.SpringImplicitNamingStrategy”。

Hibernate 中定义了多个 ImplicitNamingStrategy 的实现，可以开箱即用，而之前的逻辑名名称就是物理名称这种策略只是其中一种，其他还包括：

- ImplicitNamingStrategyJpaCompliantImpl：默认的命名策略，兼容 JPA 2.0 的规范；
- ImplicitNamingStrategyLegacyHbmImpl：兼容 Hibernate 老版本中的命名规范；
- ImplicitNamingStrategyLegacyJpaImpl：兼容 JPA 1.0 规范中的命名规范；
- ImplicitNamingStrategyComponentPathImpl：大部分与 ImplicitNamingStrategyJpaCompliantImpl，但是对于 @Embedded 等注解标志的组件处理是通过使用 attributePath 完成的，因此如果我们在使用 @Embedded 注解的时候，如果要指定命名规范，可以直接继承这个类来实现。

看一下 UML 类图：

![enter image description here](http://images.gitbook.cn/6e671cb0-4641-11e8-b223-3d54456420f0)

SpringImplicitNamingStrategy 继承 ImplicitNamingStrategyJpaCompliantImpl，从源码可以看到，对外键、链表查询、索引如果未定义，都有下划线的处理策略，而 table 和 column 名字都默认与字段一样。

**（2）PhysicalNamingStrategy**

在这个阶段中，是根据业务需要制定自己的命名规范，通过使用 PhysicalNamingStrategy 可以实现这些规则，而不需要将表名和列名通过 @Table 和 @Column 等注解显式指定，无论对象模型中是否显式地指定列名或者已经被隐式决定，PhysicalNamingStrategy 都会被调用。但是对于 ImplicitNamingStrategy，仅仅只有当没有显式地提供名称时才会使用，也就是说当对象模型中已经指定了 @Table 或者 @Entity 等 name 时，设置的 ImplicitNamingStrategy 并不会起作用。
所以可以看应用场景，我们可以根据实际需求来定义自己的策略是继承 ImplicitNamingStrategy 还是继承 PhysicalNamingStrategy，比如加上前缀 t_ 等等，或者使用分隔符“-”等等。

> 需要注意的是：PhysicalNamingStrategy 永远是在 ImplicitNamingStrategy 之后执行的，并且永远会被执行到。我们看下 PhysicalNamingStrategyStandardImpl 的源码：

```
public class PhysicalNamingStrategyStandardImpl implements PhysicalNamingStrategy, Serializable {
   public static final PhysicalNamingStrategyStandardImpl INSTANCE = new PhysicalNamingStrategyStandardImpl();
   @Override
   public Identifier toPhysicalCatalogName(Identifier name, JdbcEnvironment context) {
      return name;
   }
   @Override
   public Identifier toPhysicalSchemaName(Identifier name, JdbcEnvironment context) {
      return name;
   }
   @Override
   public Identifier toPhysicalTableName(Identifier name, JdbcEnvironment context) {
      return name;
   }
   @Override
   public Identifier toPhysicalSequenceName(Identifier name, JdbcEnvironment context) {
      return name;
   }
   @Override
   public Identifier toPhysicalColumnName(Identifier name, JdbcEnvironment context) {
      return name;
   }
}
```

默认情况下，使用就是这种策略，将 ImplicitNamingStrategy 传过来的逻辑名直接作为数据库中的物理名称，什么都没干直接返回。看下类图：

![enter image description here](http://images.gitbook.cn/73a4d140-4641-11e8-a222-fb7d71af208a)

#### 实际工作中的一些扩展

实际工作中，我们有这样的应用场景：当我们使用 MySQL 数据库的时候，一般架构师会给我们定义规范和要求，字段一般都是小写加 "_" 下划线组成，而我们的 Java 类当中都是驼峰式的字段命名方式。如果我们一个新的微服务，每个表都非常标准和规范，那么就可以让实体当中省去 @column 指定名称的过程。

定义自己的 PhysicalNamingStrategy 继承 PhysicalNamingStrategyStandardImpl 类即可内容如下：

```
package com.jack.model.naming;
public class MyPhysicalNamingStrategy extends PhysicalNamingStrategyStandardImpl {
//重载PhysicalColumnName方法，修改字段的物理名称。
    @Override
    public Identifier toPhysicalColumnName(Identifier name, JdbcEnvironment context) {
        String text = warp(name.getText());
        if(Objects.equals(text.charAt(0) , '_')){
            text = text.replaceFirst("_","");
        }
        return super.toPhysicalColumnName(new Identifier(text,name.isQuoted()), context);
    }
//将驼峰式命名转化成下划线分割的形式
    public static String warp(String text){
        text = text.replaceAll("([A-Z])","_$1").toLowerCase();
        if(Objects.equals(text.charAt(0) , '_')){
            text = text.replaceFirst("_","");
        }
        return text;
    }
}
```

修改 application.properties 添加如下内容即可：

```
spring.jpa.hibernate.naming.physical-strategy=com.jack.model.naming.MyPhysicalNamingStrategy
```

总结：

不过在实际工作中还是建议大家用显式 @Table @Column 等注解的时候就指明比较好，而可以在我们扩展的 MyPhysicalNamingStrategy 做规则验证工作，避免有些程序员没有按照我们的数据库规范命名。

### H2 Database

[H2 Database](http://www.h2database.com/html/main.html) 是一个开源的嵌入式数据库引擎，采用 Java 语言编写，支持 SQL，同时 H2 提供了一个十分方便的 Web 控制台用于操作和管理数据库内容。是一种内存数据库，Spring Data JPA 官方也推荐使用，不过作者建议读者在实际生产环境，我们都是用来跑测试用例的。这样可以对各种关键的 DB 没有影响，因为它是每次运行创建一份新的数据库的。

使用起来比较方便，不需要安装 Server 和 Client 的任何软件和工具，只需要我们用的时候引用一个 Jar 就可以自带 SQL 的 Server 和 Client 的操作了，非常适合演示项目使用。

我们来看一个使用案例。

**（1）Gradle 引入 H2 的 jar 包**

```
testCompile group: 'com.h2database', name: 'h2', version: '1.3.148'
```

**（2）配置 application.properties 如下：**

```
//# JDBC Url to use H2 DB File for persisting
spring.datasource.url:jdbc:h2:./database/samspledb;AUTO_SERVER=TRUE
//# Use H2 DB Driver
spring.datasource.driverClassName:org.h2.Driver
```

**（3）其他什么都用改，直接启动项目就可以操作我们默认的 JPA @Entity了。**

```
spring.jpa.hibernate.ddl-auto=update
```

我们再加上 ddl-auto 的配置成 update，就可以完美实现内存数据库方式的 test case 了。