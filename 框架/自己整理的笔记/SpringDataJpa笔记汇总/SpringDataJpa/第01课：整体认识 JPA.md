## 第01课：整体认识 JPA

为什么要重新学习“Spring Data JPA”？俗话说的好：“未来已经来临，只是尚未流行”，纵观市场上的 ORM 框架，MyBatis 以灵活著称，但是要维护复杂的配置，并且不是 Spring 官方的天然全家桶，还得做额外的配置工作，如果资深的架构师还得做很多封装；Hibernate 以 HQL 和关系映射著称，但是就是使用起来不是特别灵活；那么 Spring Data JPA 来了，感觉要夺取 ORM 的 JPA 霸主地位了，底层以 Hibernate 为封装，对外提供了超级灵活的使用接口，又非常符合面向对象和 Rest 的风格，感觉是架构师和开发者的福音，并且 Spring Data JPA 与 Spring Boot 配合起来使用具有天然的优势，你会发现越来越多的公司的招聘要用会有传统的 SSH、Spring、MyBatis 要求，逐步的变为 Spring Boot、Spring Cloud、Spring Data 等 Spring 全家桶的要求，而很多新生代的架构师基于其生态的考虑，正在逐步推动者 Spring Data JPA 的更多的使用场景。

本章我们从整体到局部，先来整体认识一下 Spring Data JPA。

### 市场上 ORM 框架比对

- MyBatis：MyBatis 本是 Apache 的一个开源项目 iBatis，2010 年这个项目由 Apache Software Foundation 迁移到了 Google Code，并且改名为 MyBatis，其着力于 POJO 与 SQL 之间的映射关系，可以进行更为细致的 SQL，使用起来十分灵活、上手简单、容易掌握，所以深受开发者的喜欢，目前市场占有率最高，比较适合互联应用公司的 API 场景；缺点就是工作量比较大，需要各种配置文件的配置和 SQL 语句。
- Hibernate：Hibernate 是一个开放源代码的对象关系映射框架，它对 JDBC 进行了非常轻量级的对象封装，使得 Java 程序员可以随心所欲的使用对象编程思维来操纵数据库，并且对象有自己的生命周期，着力点对象与对象之间关系，有自己的 HQL 查询语言，所以数据库移植性很好。Hibernate 是完备的 ORM 框架，是符合 JPA 规范的，有自己的缓存机制，上手来说比较难，比较适合企业级的应用系统开发。
- Spring Data JPA：可以理解为 JPA 规范的再次封装抽象，底层还是使用了 Hibernate 的 JPA 技术实现，引用 JPQL（Java Persistence Query Language）查询语言，属于 Spring 的整个生态体系的一部分。由于 Spring Boot 和 Spring Cloud 在市场上的流行，Spring Data JPA 也逐渐进入大家的视野，他们有机的整体，使用起来比较方便，加快了开发的效率，使开发者不需要关系和配置更多的东西，完全可以沉浸在 Spring 的完整生态标准的实现下，上手简单、开发效率高，又对对象的支持比较好，又有很大的灵活性，市场的认可度越来越高。
- OpenJPA ：是 Apache 组织提供的开源项目，它实现了 EJB 3.0 中的 JPA 标准，为开发者提供功能强大、使用简单的持久化数据管理框架，但功能、性能、普及性等方面更加需要加大力度，所以用的人不人不是特别多。
- QueryDSL：QueryDSL 可以在任何支持的 ORM 框架或者 SQL 平台上以一种通用的 API 方式来构建查询，目前 QueryDSL 支持的平台包括 JPA、JDO、SQL、Java Collections、RDF、Lucene、Hibernate Search，同时 Spring Data JPA 也对 QueryDSL 做了很好的支持。

### JPA 的介绍以及哪些开源实现

JPA（Java Persistence API）中文名 Java 持久层 API，是 JDK 5.0 注解或 XML 描述对象－关系表的映射关系，并将运行期的实体对象持久化到数据库中。

Sun 引入新的 JPA ORM 规范出于两个原因：其一，简化现有 Java EE 和 Java SE 应用开发工作；其二，Sun 希望整合 ORM 技术，实现天下归一。

#### JPA 包括以下三方面的内容

- 一套 API 标准，在 javax.persistence 的包下面，用来操作实体对象，执行 CRUD 操作，框架在后台替代我们完成所有的事情，开发者从繁琐的 JDBC 和 SQL 代码中解脱出来。
- 面向对象的查询语言：Java Persistence Query Language（JPQL），这是持久化操作中很重要的一个方面，通过面向对象而非面向数据库的查询语言查询数据，避免程序的 SQL 语句紧密耦合。
- ORM（Object/Relational Metadata）元数据的映射，JPA 支持 XML 和 JDK 5.0 注解两种元数据的形式，元数据描述对象和表之间的映射关系，框架据此将实体对象持久化到数据库表中。

#### JPA 的开源实现

JPA 的宗旨是为 POJO 提供持久化标准规范，由此可见，经过这几年的实践探索，能够脱离容器独立运行，方便开发和测试的理念已经深入人心了。Hibernate 3.2+、TopLink 10.1.3 以及 OpenJPA、QueryDSL 都提供了 JPA 的实现，以及最后的 Spring 的整合 Spring Data JPA。目前互联网公司和传统公司大量使用了 JPA 的开发标准规范。

![enter image description here](http://images.gitbook.cn/6fa75240-252e-11e8-8c14-19da63913af3)

### 了解 Spring Data

#### Spring Data 介绍

Spring Data 项目是从 2010 年开发发展起来的，从创立之初 Spring Data 就想提供一个大家熟悉的、一致的、基于 Spring 的数据访问编程模型，同时仍然保留底层数据存储的特殊特性。它可以轻松地让开发者使用数据访问技术包括：关系数据库、非关系数据库（NoSQL）和基于云的数据服务。

Spring Data Common 是 Spring Data 所有模块的公用部分，该项目提供跨 Spring 数据项目的共享基础设施，它包含了技术中立的库接口以及一个坚持 Java 类的元数据模型。

Spring Data 不仅对传统的数据库访问技术：JDBC、Hibernate、JDO、TopLick、JPA、MyBatis 做了很好的支持和扩展、抽象、提供方便的 API，还对 NoSQL 等非关系数据做了很好的支持：MongoDB 、Redis、Apache Solr 等。

#### Spring Data 的子项目有哪些

主要项目（Main Modules）：

- Spring Data Commons
- Spring Data Gemfire
- Spring Data JPA
- Spring Data KeyValue
- Spring Data LDAP
- Spring Data MongoDB
- Spring Data REST
- Spring Data Redis
- Spring Data for Apache Cassandra
- Spring Data for Apache Solr

社区支持的项目（Community Modules）：

- Spring Data Aerospike
- Spring Data Couchbase
- Spring Data DynamoDB
- Spring Data Elasticsearch
- Spring Data Hazelcast
- Spring Data Jest
- Spring Data Neo4j
- Spring Data Vault

其他（Related Modules）：

- Spring Data JDBC Extensions
- Spring for Apache Hadoop
- Spring Content

当然了还有许多开源社区做出的许多贡献如 MyBatis 等。

市面上主要的如图所示：

![enter image description here](http://images.gitbook.cn/67fa9980-252e-11e8-a863-110ad122986d)

#### Spring Data 操作的主要特性

Spring Data 项目旨在为大家提供一种通用的编码模式，数据访问对象实现了对物理数据层的抽象，为编写查询方法提供了方便。通过对象映射，实现域对象和持续化存储之间的转换，而模板提供的是对底层存储实体的访问实现，操作上主要有如下特征：

- 提供模板操作，如 Spring Data Redis 和 Spring Data Riak；
- 强大的 Repository 和定制的数据储存对象的抽象映射；
- 对数据访问对象的支持（Auting 等）。

![enter image description here](http://images.gitbook.cn/860a1090-252e-11e8-a332-931f85438b0b)

#### Spring Data JPA 的主要类及结构图

##### 我们需要掌握和使用到的类

七个大 Repository 接口：

- Repository（org.springframework.data.repository）；
- CrudRepository（org.springframework.data.repository）；
- PagingAndSortingRepository（org.springframework.data.repository）；
- JpaRepository（org.springframework.data.jpa.repository）；
- QueryByExampleExecutor（org.springframework.data.repository.query）；
- JpaSpecificationExecutor（org.springframework.data.jpa.repository）；
- QueryDslPredicateExecutor（org.springframework.data.querydsl）。

两大 Repository 实现类：

- SimpleJpaRepository（org.springframework.data.jpa.repository.support）；
- QueryDslJpaRepository（org.springframework.data.jpa.repository.support）。

##### 类的结构关系图如图所示

![enter image description here](http://images.gitbook.cn/bbc3ba10-252e-11e8-a332-931f85438b0b)

基本上面都是我们要关心的类和接口，先做到大体心中有个数，后面章节会一一做讲解。

##### 需要了解到的类，真正的 JPA 的底层封装类

- EntityManager（javax.persistence）；
- EntityManagerImpl（org.hibernate.jpa.internal）。

### MySQL 的快速开始实例

以 Spring Boot 2.0 和 Spring JDBC 为技术场景，选用 MySQL 来做一个实例。

环境要求：

- JDK 1.8
- Maven 3.0+
- IntelliJ IDEA

#### 第一步：创建数据库并建立 user 表

1）创建一个数据的新用户并附上权限

```
mysql> create database db_example;
mysql> create user 'springuser'@'localhost' identified by 'ThePassword';
mysql> grant all on db_example.* to 'springuser'@'localhost';
```

2）创建一个表

```
CREATE TABLE `user` (  
`id` int(11) NOT NULL AUTO_INCREMENT,  
`name` varchar(50) DEFAULT NULL,  
`email` varchar(200) DEFAULT NULL,  
PRIMARY KEY (`id`))
```

#### 第二步：利用 Intellij IDEA 创建 Example1

![enter image description here](http://images.gitbook.cn/c6b368d0-252e-11e8-a332-931f85438b0b)

![enter image description here](http://images.gitbook.cn/dcd85ad0-252e-11e8-92db-817ab3b7ffb7)

上面的信息是 maven 的 pom 里面所需要的都可以修改。

![enter image description here](http://images.gitbook.cn/9bf140d0-252f-11e8-a332-931f85438b0b)

选择 JPA 和 MySQL 和 Web 一路单击 Next 按钮，然后完成得到一个工程，完成后如下结构：

![enter image description here](http://images.gitbook.cn/12e0a410-2530-11e8-a863-110ad122986d)

#### 第三步：创建或者修改 application.properties 文件

在工程的 sources 下面的 src/main/resources/application.properties 内容如下：

```
spring.datasource.url=jdbc:mysql://localhost:3306/db_example
spring.datasource.username=springuser
spring.datasource.password=ThePassword
```

#### 第四步：创建一个 @Entity

src/main/java/example/example1/User.java：

```
package com.example.example1;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
@Entity 
public class User {
   @Id
   @GeneratedValue(strategy=GenerationType.AUTO)
   private Long id;
   private String name;
   private String email;
   public Long getId() {
      return id;
   }
   public void setId(Long id) {
      this.id = id;
   }
   public String getName() {
      return name;
   }
   public void setName(String name) {
      this.name = name;
   }
   public String getEmail() {
      return email;
   }
   public void setEmail(String email) {
      this.email = email;
   }
}
```

#### 第五步：创建一个 Repository

src/main/java/example/example1/UserRepository.java：

```
package com.example.example1;
import org.springframework.data.repository.CrudRepository;
public interface UserRepository extends CrudRepository<User, Long> {
}
```

#### 第六步：创建一个 controller

```
package com.example.example1;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.ResponseBody;
@Controller
@RequestMapping(path = "/demo")
public class UserController {
   @Autowired
   private UserRepository userRepository;
   @GetMapping(path = "/add")
   public void addNewUser(@RequestParam String name, @RequestParam String email) {
      User n = new User();
      n.setName(name);
      n.setEmail(email);
      userRepository.save(n);
   }
   @GetMapping(path = "/all")
   @ResponseBody
   public Iterable<User> getAllUsers() {
      return userRepository.findAll();
   }
}
```

#### 第七步：直接运行 Example1Application 的 main() 函数即可

打开 Example1Application 内容如下：

```
package com.example.example1;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
@SpringBootApplication
public class Example1Application {
   public static void main(String[] args) {
      SpringApplication.run(Example1Application.class, args);
   }
}
```

访问的 URL 如下：

```
$ curl 'localhost:8080/demo/add?name=First&email=someemail@someemailprovider.com'
$ curl 'localhost:8080/demo/all'
```

这时候已经可以看到效果了。