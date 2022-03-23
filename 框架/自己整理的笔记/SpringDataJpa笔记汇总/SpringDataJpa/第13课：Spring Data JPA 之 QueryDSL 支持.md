## 第13课：Spring Data JPA 之 QueryDSL 支持

在实际工作中，可能业务复杂的场景比较多，这篇来学习一下 QueryDSL 是什么？

### QueryDSL 介绍

QueryDSL 诞生于以类型安全的方式维护 HQL 查询的需求，HQL 查询的增量构造需要字符串连接并导致难以读取的代码。通过普通字符串对域类型和属性的不安全引用是基于 String 的 HQL 构造的另一个问题。而针对 Hibernate 的 HQL 是 QueryDSL 的第一个目标语言，但现在它支持 JPA、JDO、JDBC、Lucene、Hibernate Search、MongoDB、Collections 和 RDFBean 作为后端。直观来看就是使我们的负责查询变得简单有趣。

随着不断变化的领域模型类型——安全性在软件开发中带来巨大的好处 域更改直接反映在查询中，查询构建中的自动完成使查询构建更快更安全。类型安全是 QueryDSL 的核心原则。查询是基于生成的查询类型构建的，这些查询类型反映了您的域类型的属性。函数/方法调用也是以完全类型安全的方式构造的，所谓的类型安全就是利用 Java 的泛型机制。

[点击这里，详见官方地址](http://www.querydsl.com/)， 而我们主要是讲 JPA，所以先看下 Spring Data JPA 之 QueryDSL 是怎么一回事。

### JPA 之 QueryDSL 快速开始

#### pom.xml 添加如下依赖和插件

```
<!--query dsl jar依赖-->
<dependency>
    <groupId>com.querydsl</groupId>
    <artifactId>querydsl-jpa</artifactId>
    <version>${querydsl.version}</version>
</dependency>
<dependency>
    <groupId>com.querydsl</groupId>
    <artifactId>querydsl-apt</artifactId>
    <version>${querydsl.version}</version>
</dependency>
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
        <!--增加apt-maven-plugin插件-->
        <plugin>
            <groupId>com.mysema.maven</groupId>
            <artifactId>apt-maven-plugin</artifactId>
            <version>1.1.3</version>
            <executions>
                <execution>
                    <goals>
                        <goal>process</goal>
                    </goals>
                    <configuration>
                        <outputDirectory>target/generated-sources/java</outputDirectory>
            <processor>com.querydsl.apt.jpa.JPAAnnotationProcessor</processor>
                    </configuration>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>
```

#### 我们用之前的例子创建两个实体如下

```
@Entity
@Table
public class User {
    private int id;
    private String name;
    private String email;
    private Collection<UserAddress> userAddressesById;
    @Id
    @Column(name = "id", nullable = false)
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    public int getId() {
        return id;
    }
    .......
}
@Entity
@Table(name = "user_address")
public class UserAddress {
    private int id;
    private Integer userId;
    private String city;
    ......
}
```

#### mvn编译 # mvn compile

![img](http://images.gitbook.cn/b37d6d10-5689-11e8-9020-71b3b8e21c2d)

如图所示，会发现 target 会生成我们上面配置的 target/generated-sources/java 源码 QUser.java 和 QUserAddress.java。而开发工具也会将它们作为 Java 源文件目录，而 target->classes 里面也会生产 QUser.class 和 QUserAddress.class，也就是我们正常 comile 即可不需要做任何关心。

#### 直接使用 DSLRepository

新建 UserQueryDSLRepository 继承 QuerydslPredicateExecutor 如下：

```
package com.example.example2.repository;
import com.example.example2.entity.User;
import org.springframework.data.querydsl.QuerydslPredicateExecutor;
import org.springframework.data.repository.CrudRepository;
public interface UserQueryDSLRepository extends CrudRepository<User, Long>, QuerydslPredicateExecutor<User> {
}
```

新增 UserQueryDSLController 直接调用 UserQueryDSLRepository 使用其 API：

```
package com.example.example2;
import com.example.example2.entity.QUser;
import com.example.example2.entity.User;
import com.example.example2.repository.UserQueryDSLRepository;
import com.querydsl.core.types.Predicate;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.querydsl.binding.QuerydslPredicate;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
@RestController
@RequestMapping("query/dsl/")
public class UserQueryDSLController {
    @Autowired
    private UserQueryDSLRepository userQueryDSLRepository;
    @GetMapping("users/all")
    public Iterable<User> find() {
        QUser user = QUser.user;
        //直接引用QUser通过下面的操作直接做查询
        Predicate predicate = user.name.startsWith("jack")
                .and(user.email.startsWithIgnoreCase("jack"));
        return userQueryDSLRepository.findAll(predicate);
    }
}
```

我们运行一下，就可以直接通过 http://127.0.0.1:8080/query/dsl/user/all 访问，打印的 SQL 如下：

```
2018-05-13 17:38:35.248  INFO 13307 --- [nio-8010-exec-3] o.h.h.i.QueryTranslatorFactoryInitiator  : HHH000397: Using ASTQueryTranslatorFactory
Hibernate: 
    /* select
        user 
    from
        User user 
    where
        user.name like ?1 escape '!' 
        and lower(user.email) like ?1 escape '!' */ select
            user0_.id as id1_0_,
            user0_.email as email2_0_,
            user0_.name as name3_0_ 
        from
            user user0_ 
        where
            (
                user0_.name like ? escape '!'
            ) 
            and (
                lower(user0_.email) like ? escape '!'
            )
2018-05-13 17:38:35.481 TRACE 13307 --- [nio-8010-exec-3] o.h.type.descriptor.sql.BasicBinder      : binding parameter [1] as [VARCHAR] - [jack%]
2018-05-13 17:38:35.481 TRACE 13307 --- [nio-8010-exec-3] o.h.type.descriptor.sql.BasicBinder      : binding parameter [2] as [VARCHAR] - [jack%]
```

### JPA 之 QueryDSL 语法介绍

#### 看一下 QuerydslPredicateExecutor 的基本语法

![enter image description here](http://images.gitbook.cn/7917c050-568c-11e8-ada6-fb519e5070eb)

语法比较简单和我们之前讲的 JpaSpecificationExecutor 差不多。而唯一需要注意的是里面的 Predicate 的参数不一样，它是由 QueryDSL 里面生成的。其实通过上面的例子，我们也不难发现，就是由 QUser 等等生成的 Q 开头的 Java 里面生成各种的查询条件，而分页和排序用法和之前一样。

#### 通过源码一步一步分析 StringPath、NumberPath，可以发现 N 多查询方法如下

![enter image description here](http://images.gitbook.cn/461387f0-568e-11e8-ada6-fb519e5070eb)

![enter image description here](http://images.gitbook.cn/4d0e55d0-568e-11e8-ada6-fb519e5070eb)

#### 简单看几个 Demo

分页、排序、动态查询条件：

```
//查找出Id小于3，并且名称带有 shanghai 的记录.
//动态条件
QUser qUser = QUser.user;
//该Predicate为querydsl下的类，支持嵌套组装复杂查询条件
Predicate predicate = qUser.id.longValue().lt(3)
        .and(qUser.name.like("shanghai"));
//分页排序
Sort sort = Sort.by(new Sort.Order(Sort.Direction.ASC,"id"));
PageRequest pageRequest = PageRequest.of(0,10,sort);
//查找结果
Page<User> tCityPage = userQueryDSLRepository.findAll(predicate,pageRequest);
```

也可以直接用 BooleanExpression、StringExpression 等 Expression。

```
BooleanExpression customerHasBirthday = customer.birthday.eq(today);
BooleanExpression isLongTermCustomer = customer.createdAt.lt(today.minusYears(2));
customerRepository.findAll(customerHasBirthday.and(isLongTermCustomer));
```

也可以利用 JPAQueryFactory 实现更复杂的查询：

```
//导入全局JPA EntityManager
@Autowired
@PersistenceContext
private EntityManager entityManager;
......
JPAQueryFactory queryFactory = new JPAQueryFactory(entityManager);
//queryFactory用法
QDepartment department = QDepartment.department;
QDepartment d = new QDepartment("d");
queryFactory.selectFrom(department)
    .where(department.size.eq(
        JPAExpressions.select(d.size.max()).from(d)))
     .fetch();
// 多表动态分页查询
JPAQueryFactory queryFactory = new JPAQueryFactory(em);
JPAQuery<Tuple> jpaQuery = queryFactory
             .select(QTCity.tCity.id,QTHotel.tHotel)
           .from(QTCity.tCity)
           .leftJoin(QTHotel.tHotel)
           .on(QTHotel.tHotel.city.longValue().eq(QTCity.tCity.id.longValue()))
           .where(predicate)
           .offset(pageable.getOffset())
           .limit(pageable.getPageSize());
//拿到分页结果
return jpaQuery.fetchResults();
```

> 其实语法还是比较简单，我们主要看中它的类型安全和 stream 查询方法和查询 exprecession 的封装，更详细的 API 直接看源码就知道了哦。

### QueryDSL 之 Web 支持

就像 Spring Data JPA 一样对 Web MVC 开发有一定的扩展支持，而 QuerDSL 已有一些支持。上面的一大堆查询条件可以简化成如下写法：

```
@RestController
@RequestMapping("query/dsl/")
public class UserQueryDSLController {
    @Autowired
    private UserQueryDSLRepository userQueryDSLRepository;
    @GetMapping("users")
    public Page<User> findByParam(@QuerydslPredicate(root = User.class) Predicate predicate, Pageable pageable) {
        return userQueryDSLRepository.findAll(predicate,pageable);
    }
}
```

可以将 Predicate 直接作为 controller 的参数接收查询条件，访问 URL 的方法如下：

```
http://127.0.0.1:8010/query/dsl/users?name=jack&size=10
```

#### QueryDSL 之 Web 实现原理

![enter image description here](http://images.gitbook.cn/eb51c2c0-5695-11e8-84ca-a1affd38b2ee)

通过源码能看出来 QuseryDSL 自动加载的时候覆盖了 WebMvcConfigurer 的 addArgumentResolvers，实现了 QuerydslPredicateArgumentResolver 的参数解析，自定帮我们转成了 Predicate predicate。

#### REST API Query Language 扩展

由于 Rest 的服务的 API 越来越成熟，进而很多人提出来 REST API Query Language 的概念，类似如下：

```
http://localhost:8080/myusers?search=lastName:doe,age>25
```

可以利用 QueryDSL 做到如下扩展：

```
@Controller
public class UserController {
    @Autowired
    private MyUserRepository myUserRepository;
    @RequestMapping(method = RequestMethod.GET, value = "/myusers")
    @ResponseBody
    public Iterable<MyUser> search(@RequestParam(value = "search") String search) {
        MyUserPredicatesBuilder builder = new MyUserPredicatesBuilder();
        if (search != null) {
            Pattern pattern = Pattern.compile("(\w+?)(:|<|>)(\w+?),");
            Matcher matcher = pattern.matcher(search + ",");
            while (matcher.find()) {
                builder.with(matcher.group(1), matcher.group(2), matcher.group(3));
            }
        }
        BooleanExpression exp = builder.build();
        return myUserRepository.findAll(exp);
    }
}
```

上面涉及到的关键的几个类的 UML 图如下：

![enter image description here](http://images.gitbook.cn/4365f320-5699-11e8-8cc0-c185220cc3bc)

基本上通过这么多篇的内容对 Spring Data JPA 会产生完整的整体上的认识。最后希望大家遇到问题多多交流，此课程内容与《Spring Data JPA 从入门到精通》书上的内容形成了互补。