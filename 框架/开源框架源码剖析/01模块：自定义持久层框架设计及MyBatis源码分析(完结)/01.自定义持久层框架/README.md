# 自定义持久层框架

## 说在前面
>**本章相关代码及笔记地址：**[**飞机票🚀**](https://github.com/EayonLee/JavaGod/tree/main/1%E9%98%B6%E6%AE%B5%EF%BC%9A%E5%BC%80%E6%BA%90%E6%A1%86%E6%9E%B6%E6%BA%90%E7%A0%81%E5%89%96%E6%9E%90/01%E6%A8%A1%E5%9D%97%EF%BC%9A%E8%87%AA%E5%AE%9A%E4%B9%89%E6%8C%81%E4%B9%85%E5%B1%82%E6%A1%86%E6%9E%B6%E8%AE%BE%E8%AE%A1%E5%8F%8AMyBatis%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90/01.%E8%87%AA%E5%AE%9A%E4%B9%89%E6%8C%81%E4%B9%85%E5%B1%82%E6%A1%86%E6%9E%B6)
>
>🌍Github：[🚀Java超神之路：【🍔Java全生态技术学习笔记，一起超神吧🍔】](https://github.com/EayonLee/JavaGod)<br>
>🪐CSDN：[🚀Java超神之路：【🍔Java全生态技术学习笔记，一起超神吧🍔】](https://blog.csdn.net/qq_20492277/article/details/114269863)



## 目录

- [自定义持久层框架](#自定义持久层框架)
  - [说在前面](#说在前面)
  - [目录](#目录)
  - [概述](#概述)
  - [一.  分析JDBC操作问题](#一--分析jdbc操作问题)
    - [1.1 一张图解析传统JDBC代码的缺陷](#11-一张图解析传统jdbc代码的缺陷)
    - [1.2  原始JDBC开发存在问题解决方案](#12--原始jdbc开发存在问题解决方案)
  - [二. 自定义持久层框架设计思路](#二-自定义持久层框架设计思路)
    - [2.1 客户端](#21-客户端)
      - [（1）提供配置文件供持久层框架读取：](#1提供配置文件供持久层框架读取)
    - [2.2 自定义持久层框架端](#22-自定义持久层框架端)
      - [（1）读取客户端提供的配置文件：](#1读取客户端提供的配置文件)
      - [（2）创建两个容器对象：](#2创建两个容器对象)
      - [（3）解析配置文件：](#3解析配置文件)
      - [（4）创建SqlSessionFactory接口及实现类DefaultSqlSessionFactory：](#4创建sqlsessionfactory接口及实现类defaultsqlsessionfactory)
      - [（5）创建SqlSession接口及实现类DefaultSqlSession：](#5创建sqlsession接口及实现类defaultsqlsession)
      - [（6）创建Executor接口及实现类SimpleExecutor：](#6创建executor接口及实现类simpleexecutor)
      - [（7）创建SqlSessionFactoryBuilder：](#7创建sqlsessionfactorybuilder)
    - [2.3 执行流程图](#23-执行流程图)
  - [三. 自定义持久层框架实现](#三-自定义持久层框架实现)
    - [3.1 读取配置文件](#31-读取配置文件)
    - [3.2 容器对象定义](#32-容器对象定义)
    - [3.3 解析核心配置文件](#33-解析核心配置文件)
    - [3.4 创建SqlSessionFactory接口及实现类DefaultSqlSessionFactory](#34-创建sqlsessionfactory接口及实现类defaultsqlsessionfactory)
    - [3.5 创建Executor接口及实现类SimpleExecutor](#35-创建executor接口及实现类simpleexecutor)
    - [3.6 定义SqlSession中的CRUD方法](#36-定义sqlsession中的crud方法)
    - [3.7 客户端测试运行](#37-客户端测试运行)
    - [3.8 自定义持久层框架优化](#38-自定义持久层框架优化)


## 概述

​	市面上有许多的持久层框架，如：``Mybatis``、``SpringData JPA``......。他们都是基于JDBC的封装，那么我们想要去自定义持久层框架就得了解JDBC，相信大家都了解过JDBC，知道使用JDBC进行持久化操作非常的复杂，那么我们下面就来分析JDBC的代码缺陷，这样才能更好的自定义持久层框架。



## 一.  分析JDBC操作问题

以下是通过JDBC连接数据库进行查询操作的示例代码，我们来分析，这样进行数据查询会有哪些问题。

![](https://cdn.jsdelivr.net/gh/EayonLee/IMG-Cloud@master/data/jdbc代码.png)

### 1.1 一张图解析传统JDBC代码的缺陷

![](https://cdn.jsdelivr.net/gh/EayonLee/IMG-Cloud@master/data/jdbc问题分析.png)

**原始JDBC开发存在的问题如下：**

1. 数据库连接创建、释放频繁造成系统资源浪费，从而影响系统性能。
2. SQL语句在代码中硬编码，造成代码不易维护，实际应用中sql变化的可能较大，SQL变动需要改变Java代码。
3. 使用``preparedStatement``向占有位符号传参数存在硬编码，因为SQL语句的``where``条件不一定，可能多也可能少，修改SQL还要修改代码，系统不易维护。
4. 对结果集解析存在硬编码(查询列名)，SQL变化导致解析代码变化，系统不易维护，如果能将数据库记录封装成pojo对象解析比较方便。

### 1.2  原始JDBC开发存在问题解决方案

![img](https://cdn.jsdelivr.net/gh/EayonLee/IMG-Cloud@master/data/javagod成神之路.png)



## 二. 自定义持久层框架设计思路

> 以下所有使用到的文件名、类名或变量名等都是我自定义的，如果和MyBatis雷同纯属巧合（哈哈哈），大家也可以随便定义。

### 2.1 客户端

**啥是客户端？**：可理解为是一个使用我们自定义持久层框架的项目。

​	按照我们原来使用Mybatis的经验，我们知道客户端需要提供两个核心配置信息：数据库配置信息、SQL配置信息。

​	而且根据上面我们分析JDBC的缺陷时，数据库配置信息和SQL配置信息都是写死在代码中的，那么我们自定义的时候肯定不能写死在代码里，我们需要写在配置文件中。

#### （1）提供配置文件供持久层框架读取：

* **sqlMapConfig.xml**：存放数据库配置信息

* **mapper.xml**：SQL配置信息：SQL语句、参数类型、返回值类型



### 2.2 自定义持久层框架端

**啥是自定义持久层框架端？**：其实就是对JDBC封装的一个工厂，供客户端使用。

​	我们知道自定义持久层框架其实本质是对JDBC代码进行封装，因此我们需要做以下一些工作：

#### （1）读取客户端提供的配置文件：

* 创建``Resources``类，并定义``getResourcesAsStream(String path)``方法将配置文件读取成字节流存储与内存中并返回。

  **思考**：我们现在可以用``getResourceAsStream()``方法去加载配置文件，但是客户端有两个配置文件难道我们就要去加载两次吗？

  ​			其实我们可以将``*mapper.xml``配置文件的全路径存放于``sqlMapConfig.xml``中，这样我们只需要加载一次``sqlMapConfig.xml就			可以全部获取了。

#### （2）创建两个容器对象：

​		从第一步来看，我们只是将配置文件读取到了内存中，而内存中的数据我们是不是不方便操作？所以我们要基于Java面向对象的思	想，将这两个配置文件解	析为	两个Java Bean：``Configuration`` 、``MappedStatement``

* **Configuration（核心配置类）**: 存放数据库基本信息，也就是客户端提供的``sqlMapConfig.xml``配置文件的内容

* **MappedStatement（映射配置类）**：存放SQL语句、参数类型、返回值类型，也就是存放``*mapper.xml``配置文件的内容

#### （3）解析配置文件：

* 创建``XMLConfigBuilder``类并定义``parseConfig(InputStream is)``方法，该方法使用``dom4j``将``sqlMapConfig.xml``在内存中的字节流封装到``Configuration``配置实体并返回。

* 创建``XMLMapperBuilder``类并定义``parse(InputStream is)``方法，该方法使用``dom4j``将``*mapper.xml``在内存中的字节流封装到``MappedStatement``配置实体，并将该实体赋值给``Configuration``中的属性。

- 创建一个构建者类SqlSessionFactoryBuilder，类中有个方法：build(InputStream is)，那build中的参数也就是内存中客户端提供的sqlMapConfig.xml文件流。SqlSessionFactoryBuilder会通过配置文件创建一个SqlSessionFactory工厂，通过session工厂生产session。

#### （4）创建SqlSessionFactory接口及实现类DefaultSqlSessionFactory：

- 创建``SqlSessionFactory``接口及实现类``DefaultSqlSessionFactory``Session工厂，并提供一个openSession() 方法来生产sqlSession（会话对象），那其实我们对数据的增删改查方法都封装在sqlSession中。

#### （5）创建SqlSession接口及实现类DefaultSqlSession：

​	``SqlSession``主要封装了一些对数据库CRUD（增删改查）操作的方法

- 提供``selectList()``方法：查询所有数据
- 提供``selectOne()``：查询单个
- 提供``update()``：修改
- 提供``delete()``：删除

#### （6）创建Executor接口及实现类SimpleExecutor：

- 创建Executor接口及实现类SimpleExecutor并提供``query(Configuration c, MappedStatement m, Object...param)``方法，用于执行封装的jdbc代码

  **解释**：本步操作其实就是将jdbc的增删改查方法封装到``Executor``接口及实现类``SimpleExcutor``来做进一步的封装，这个qeury方法其实就是对jdbc原始查询操作的封装。参数分别为：核心配置文件、*mapper映射文件、查询参数。那么比如使用``DefaultSqlSession``中``selectList()``方法调用的也就是``SimpleExcutor``中封装的``query()``方法。

#### （7）创建SqlSessionFactoryBuilder：

- 创建``SqlSessionFactoryBuilder``类，并定义``build(InputStream is)``方法，``build``中的参数也就是``sqlMapConfig.xml``文件流。

- ``build()``中会调用``XMLConfigBuilder.parseConfig(inputStream)``方法将文件流使用``dom4j``解析成``Configuration``对象。

- 然后通过``Configuration``配置实体作为参数构建DefaultSqlSessionFactory工厂并返回。

  **解释**：当客户端使用``Resources.getResourceAsStream(sqlmapConfig.xml)``获取到配置文件字节流后，通过``SqlSessionFactoryBuilder.build(resourceAsStream)``方法将字节流构建成``SqlSessionFactory``工厂。所以``SqlSessionFactory``中包含了客户端提供的配置信息。然后客户端再通过``SqlSessionFactory.openSession()``方法去生产``SqlSession``，那这个时候SqlSession中也包含配置信息。那么我们就可以通过``SqlSession``中的CRUD方法，将配置信息在给到``SimpleExecutor``中具体的JDBC代码中实现完整的一套流程。

  

### 2.3 执行流程图

![image-20210301162130434](https://cdn.jsdelivr.net/gh/EayonLee/IMG-Cloud@master/data/image-20210301162130434.png)



## 三. 自定义持久层框架实现

> 源码地址：[飞机票✈](https://github.com/EayonLee/JavaGod/tree/main/1%E9%98%B6%E6%AE%B5%EF%BC%9A%E5%BC%80%E6%BA%90%E6%A1%86%E6%9E%B6%E6%BA%90%E7%A0%81%E5%89%96%E6%9E%90/01%E6%A8%A1%E5%9D%97%EF%BC%9A%E8%87%AA%E5%AE%9A%E4%B9%89%E6%8C%81%E4%B9%85%E5%B1%82%E6%A1%86%E6%9E%B6%E8%AE%BE%E8%AE%A1%E5%8F%8AMyBatis%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90/01.%E8%87%AA%E5%AE%9A%E4%B9%89%E6%8C%81%E4%B9%85%E5%B1%82%E6%A1%86%E6%9E%B6/01.%E8%AF%BE%E7%A8%8B%E8%B5%84%E6%96%99)

### 3.1 读取配置文件

**自定义持久层框架端**

* **第一步：创建自定义持久层框架项目，项目名：``Ipersistence``**

* **第二步：添加pom坐标**

  ```xml
  <dependencies>
      <!--mysql-->
      <dependency>
          <groupId>mysql</groupId>
          <artifactId>mysql-connector-java</artifactId>
          <version>5.1.17</version>
      </dependency>
      <!--dom4j-->
      <dependency>
          <groupId>dom4j</groupId>
          <artifactId>dom4j</artifactId>
          <version>1.6.1</version>
      </dependency>
      <!--c3p0-->
      <dependency>
          <groupId>c3p0</groupId>
          <artifactId>c3p0</artifactId>
          <version>0.9.1.2</version>
      </dependency>
      <!--junit-->
      <dependency>
          <groupId>junit</groupId>
          <artifactId>junit</artifactId>
          <version>4.10</version>
      </dependency>
      <!--jaxen-->
      <dependency>
          <groupId>jaxen</groupId>
          <artifactId>jaxen</artifactId>
          <version>1.1.6</version>
      </dependency>
  </dependencies>
  ```

* **第三步：创建Resources类，用于读取客户端提供的配置文件**

  > 在``com.eayon.io``目录下创建``Resources``类，类中定义``getResourceAsStream(String path)``方法去加载配置文件，方法返回值``InputStream``

  ```java
  package com.eayon.io;
  
  import java.io.InputStream;
  
  /**
   * 根据客户端的配置文件路径将配置文件读取成字节输入流的形式存储于内存中
   */
  public class Resources {
  
      /**
       * 可将文件读取成字节流的形式存储于内存并返回
       *
       * @param path 配置文件路径
       * @return
       */
      public static InputStream getResourceAsStream(String path) {
          InputStream resourceAsStream = Resources.class.getClassLoader().getResourceAsStream(path);
          return resourceAsStream;
      }
  
  }
  ```

* **第四步：将自定义持久层框架项目打包至本地Maven仓库**



**客户端**

* **第一步：创建客户端项目，项目名：``Ipersistence_test``**

* **第二步：创建数据库核心配置文件：SqlMapConfig.xml**

  在``resources``目录下创建如下``SqlMapConfig.xml``核心配置文件

  ```xml
  <!--数据库核心配置文件-->
  <configuration>
  
      <!--数据源配置信息-->
      <dataSource>
          <property name="driverClass" value="com.mysql.jdbc.Driver"></property><!--Mysql数据库驱动-->
          <property name="jdbcUrl" value="jdbc:mysql:///eayon_mybatis"></property><!--连接的数据库地址  数据库自行创建-->
          <property name="username" value="root"></property><!--数据库账号-->
          <property name="password" value="1234"></property><!--数据库密码-->
      </dataSource>
  
      <!--存放 *mapper.xml的全路径-->
      <mapper resource="UserMapper.xml"></mapper>
  </configuration>
  ```

* **第三步：创建Mapper映射配置文件**

  在``resources``目录下创建如下``UserMapper.xml``配置文件

  ```xml
  <!-- namespace：当前UserMapper.xml的唯一标识-->
  <mapper namespace="user">
  
      <!--查询所有-->
      <!-- id：当前select标签的唯一标识-->
      <!--想要精准定位到当前这条SQL语句：是通过 namespace和该条select标签中的id来组成一个statementId来进行定位的 ， 如该条SQL的statementId为 user.selectList-->
      <!--resultType：该条SQL语句返回结果的返回值类型-->
      <select id="selectList" restType="com.eayon.pojo.User">
          select * from user
      </select>
  
      <!--条件查询-->
      <!--paramterType：参数类型-->
      <select id="selectOne" restType="com.eayon.pojo.User"  paramterType="com.eayon.pojo.User">
          select * from user where id = #{id} and username = #{username}
      </select>
  
  </mapper>
  ```

* **第四步：添加pom坐标（自定义持久层框架打包后的坐标）**

  ```xml
  <!--引入自定义持久层框架的依赖-->
      <dependencies>
          <dependency>
              <groupId>com.eayon</groupId>
              <artifactId>Ipersistence</artifactId>
              <version>1.0-SNAPSHOT</version>
          </dependency>
      </dependencies>
  ```

* **第五步：测试读取SqlMapConfig.xml配置文件**

  在``com.eayon.test``下创建``MyTest``类并测试是否可以读取成功

  ```java
  package com.eayon.test;
  
  import com.eayon.io.Resources;
  
  import java.io.InputStream;
  
  /**
   * 测试类
   */
  public class MyTest {
  
      public static void main(String[] args) {
          InputStream resourceAsStream = Resources.getResourceAsStream("sqlMapConfig.xml");
          System.out.println(resourceAsStream);
      }
  }
  ```



### 3.2 容器对象定义

**自定义持久层框架端**

* **第一步：创建MappedStatement容器对象**

  在``com.eayon.pojo``下创建``MappedStatement``容器对象

  > 比如``UserMapper.xml``中有两个``select``标签，那么加载``UserMapper.xml``的时候就会产生两个``MappedStatement``容器对象。

  ```java
  package com.eayon.pojo;
  
  /**
   * 映射配置类：存放SQL语句、参数类型、返回值类型，也就是*mapper.xml配置文件中每一个持久化操作标签的内容，如某一个select标签
   * 一个*mapper.xml配置文件可能会解析出来多个MappedStatement
   */
  public class MappedStatement {
  
      //id标识
      private String id;
      //返回值类型
      private String resultType;
      //参数值类型
      private String paramterType;
      //sql语句
      private String sql;
  
      public String getId() {
          return id;
      }
  
      public void setId(String id) {
          this.id = id;
      }
  
      public String getResultType() {
          return resultType;
      }
  
      public void setResultType(String resultType) {
          this.resultType = resultType;
      }
  
      public String getParamterType() {
          return paramterType;
      }
  
      public void setParamterType(String paramterType) {
          this.paramterType = paramterType;
      }
  
      public String getSql() {
          return sql;
      }
  
      public void setSql(String sql) {
          this.sql = sql;
      }
  }
  ```

* **第二步：创建Configuration容器对象**

  在``com.eayon.pojo``下创建``Configuration``核心配置类

  > 我们在使用``dom4j``加载``sqlMapConfig.xml``核心配置文件时就将``dataSource``这个标签中的属性值解析并创建成数据源对象赋值到该``Configuration``核心配置类的``dataSource``属性中
  >
  > 而且在之前我们提到``sqlMapConfig.xml``中配置了`` *mapper.xml``的全路径，所以解析的时候同样会解析到 ``*mapper.xml``，比如``Usermapper.xml``中存在多个持久化标签就会创建多个``MappedStatement``容器对象，那么我们可以用Map集合来存放这多个``MappedStatement``。

  Map集合的Key： ``statementId``  (namespace.id)，通过``statementId``可以定位到每一条SQL

  Map集合中的Value：每条SQL的配置类``MappedStatement``

  ```java
  package com.eayon.pojo;
  
  import javax.sql.DataSource;
  import java.util.HashMap;
  import java.util.Map;
  
  /**
   * 核心配置类：存放数据库基本信息，也就是客户端提供的sqlMapConfig.xml配置文件的内容
   */
  public class Configuration {
  
      //数据源配置信息：使用dom4j解析sqlMapConfig.xml时会解析到dataSource这个标签中的属性值并创建成数据源对象赋值给本类的dataSource属性
      private DataSource dataSource;
  
      //在sqlMapConfig.xml中会存放*mapper.xml的全路径并加载
      //那一个*mapper.xml可能会解析出来多个MappedStatement(SQL配置信息)，所以在Configuration我们使用Map来进行多个存储
      //Map集合中的Key：statementId（namespace.id），通过statementId可以定位到每一条SQL，statementId是由mapper.xml配置文件中的namespace + "." + id 组成的
      //Map集合中的Value：每条SQL的配置类MappedStatement
      Map<String, MappedStatement> mappedStatementMap = new HashMap<>();
  
      public DataSource getDataSource() {
          return dataSource;
      }
  
      public void setDataSource(DataSource dataSource) {
          this.dataSource = dataSource;
      }
  
      public Map<String, MappedStatement> getMappedStatementMap() {
          return mappedStatementMap;
      }
  
      public void setMappedStatementMap(Map<String, MappedStatement> mappedStatementMap) {
          this.mappedStatementMap = mappedStatementMap;
      }
  }
  ```



### 3.3 解析核心配置文件

**自定义持久层框架端**

> 我们回想一下，是不是客户端通过自定义框架的``Resources.getResourceAsStream()``方法把配置文件读取成字节流后，需要通过``SqlSessionFactoryBuilder``的``Builder``方法，根据配置文件字节流获取``SqlSessionFactory``工厂？通过``SqlSessionFactory``去创建``session``？再通过``session``对数据库进行操作？那我们就像来创建``SqlSessionFactoryBuilder``！

* **第一步：创建SqlSessionFactoryBuilder**

  在``com.eayon.sqlSession``下创建``SqlSessionFactoryBuilder``类，并定义``build(InputStream is)方法

  ``build``方法中具体进行两步操作：（暂不编写具体操作内容）

  ​		1) ：使用``dom4j``解析配置文件，将解析出来的内容封装到``Configuration``核心配置类中

  ​		2) ：创建``SqlSessionFactory``对象并返回

  ```java
  package com.eayon.sqlSession;
  
  
  import java.io.InputStream;
  
  /**
   * 构建者类：用于创建SqlSessionFactory
   */
  public class SqlSessionFactoryBuilder {
  
      public SqlsessionFactory build(InputStream is){
          //第一：使用dom4j解析配置文件，将解析出来的内容封装到Configuration中
  
  
          //第二：创建SqlsessionFactory工厂
  
          return null;
      }
  }
  ```

  > ``SqlSessionFactory``我们还没有创建，会报红，我们先不管他。

  我们先来分析代码中的第一步：如何通过dom4j解析文件流封装到``Configuration中``？

  这就需要创建``XMLConfigBuilder``类来提供解析封装的方法，那么我们进入下一步

* **第二步：创建XMLConfigBuilder来解析SqlMapConfig.xml**

  在``com.eayon.config``下创建``XMLConfigBuilder``并提供``parseConfig``方法

  ``parseConfig``方法分为两步：

  ​		1) ：使用``dom4j``解析配置文件，将解析出来的内容封装到``Configuration``核心配置类中

  ​		2) ：创建``SqlMapConfig.xml``中配置的``*mapper.xml``的全路径来解析``*mapper.xml``配置文件并封装到``MappedStatement``配置类，				再将MappedStatement赋值给Configuration的mappedStatementMap属性。

  > 本步骤只完成第一步，第二步解析``*mapper.xml``请往下一步继续看

  ```java
  package com.eayon.config;
  
  import com.eayon.pojo.Configuration;
  import com.mchange.v2.c3p0.ComboPooledDataSource;
  import org.dom4j.Document;
  import org.dom4j.DocumentException;
  import org.dom4j.Element;
  import org.dom4j.io.SAXReader;
  
  import java.beans.PropertyVetoException;
  import java.io.InputStream;
  import java.util.List;
  import java.util.Properties;
  
  /**
   * 使用dom4j解析sqlMapConfig.xml的字节流封装到Configuration核心配置类
   */
  public class XMLConfigBuilder {
  
      //声明核心配置类成员变量
      private Configuration configuration;
  
      //在new XMLConfigBuilder的时候就会通过无参构造给成员变量configuration进行赋值
      public XMLConfigBuilder() {
          this.configuration = new Configuration();
      }
  
      /**
       * 该方法使用dom4j将sqlMapConfig.xml在内存中的字节流封装到Configuration核心配置类并返回。
       *
       * @param is 核心配置文件qlMapConfig.xml的字节流
       * @return
       */
      public Configuration parseConfig(InputStream is) throws DocumentException, PropertyVetoException {
  
          /**
           * 第一步：通过dom4j解析核心配置文件封装到Configuration配置类
           */
          //通过dom4j读取核心配置文件字节流生成Dom
          Document document = new SAXReader().read(is);
          //拿到根元素，也就是sqlMapConfig.xml文件中的configuration标签
          Element configElement = document.getRootElement();
          //查找根元素下所有的property标签元素
          List<Element> propertyElements = configElement.selectNodes("//property");
          //通过Properties存储数据源信息name以及value值
          Properties dataSources = new Properties();
          //获取每一个property元素的name以及value值
          for (Element propertyElement : propertyElements) {
              String name = propertyElement.attributeValue("name");
              String value = propertyElement.attributeValue("value");
              //将name、value对应的存入Properties
              dataSources.setProperty(name,value);
          }
  
          //创建数据库连接池
          ComboPooledDataSource comboPooledDataSource = new ComboPooledDataSource();
          //设置数据源信息
          comboPooledDataSource.setDriverClass(dataSources.getProperty("driverClass"));
          comboPooledDataSource.setJdbcUrl(dataSources.getProperty("jdbcUrl"));
          comboPooledDataSource.setUser(dataSources.getProperty("username"));
          comboPooledDataSource.setPassword(dataSources.getProperty("password"));
  
          //将数据源封装到Configuration对象
          configuration.setDataSource(comboPooledDataSource);
  
          /**
           * 第二步：通过sqlMapConfig.xml中配置的*mapper.xml的全路径来解析*mapper.xml并封装到MappedStatement配置类
           *        再将MappedStatement赋值给Configuration的mappedStatementMap属性
           */
  
  
          //返回核心配置类
          return configuration;
      }
  }
  ```

* **第三步：创建XMLMapperBuilder类，来解析mapper.xml**

  在``com.eayon.config``下创建``XMLMapperBuilder``并提供``parse()``方法

  该方法使用``dom4j``将``mapper.xml``字节流进行解析并封装到``MappedStatement``配置实体，并将该实体赋值给``Configuration``中的属性。

  ```java
  package com.eayon.config;
  
  import com.eayon.pojo.Configuration;
  import com.eayon.pojo.MappedStatement;
  import org.dom4j.Document;
  import org.dom4j.DocumentException;
  import org.dom4j.Element;
  import org.dom4j.io.SAXReader;
  
  import java.io.InputStream;
  import java.util.List;
  import java.util.Map;
  
  /**
   * 使用dom4j解析mapper.xml
   */
  public class XMLMapperBuilder {
  
      //声明核心配置类成员变量
      private Configuration configuration;
  
      //在使用有参构造new的时候会将传递的configuration进行赋值
      public XMLMapperBuilder(Configuration configuration) {
          this.configuration = configuration;
      }
  
      /**
       * 该方法使用dom4j将mapper.xml字节流进行解析并封装到MappedStatement配置实体，并将该实体赋值给Configuration中的属性。
       *
       * @param is *mapper.xml的字节流
       */
      public void parse(InputStream is) throws DocumentException {
          //通过dom4j读取mapper.xml字节流生成Dom
          Document document = new SAXReader().read(is);
          //拿到根元素，也就是mapper.xml文件中的mapper标签
          Element mapperElement = document.getRootElement();
          //查找根元素下所有的select标签元素
          List<Element> selectElements = mapperElement.selectNodes("//select");
  
          //获取每个select操作的SQL配置
          for (Element selectElement : selectElements) {
              String id = selectElement.attributeValue("id");
              String resultType = selectElement.attributeValue("resultType");
              String paramterType = selectElement.attributeValue("paramterType");
              String sql = selectElement.getTextTrim();//sql语句
              //封装到MappedStatement
              MappedStatement mappedStatement = new MappedStatement();
              mappedStatement.setId(id);
              mappedStatement.setResultType(resultType);
              mappedStatement.setParamterType(paramterType);
              mappedStatement.setSql(sql);
              //将每个MappedStatement存储到Configuration的MappedStatementMap属性中
              Map<String, MappedStatement> mappedStatementMap = configuration.getMappedStatementMap();
              //构建mappedStatementMap的key ：statementId（namespace.id），通过statementId可以定位到每一条SQL
              String namespace = mapperElement.attributeValue("namespace");
              String statementId = namespace + "." + id;
              mappedStatementMap.put(statementId, mappedStatement);
          }
  
      }
  }
  ```

* **第四步：完善XMLConfigBuilder类中parseConfig()方法的第二步**

  ```java
  package com.eayon.config;
  
  import com.eayon.io.Resources;
  import com.eayon.pojo.Configuration;
  import com.mchange.v2.c3p0.ComboPooledDataSource;
  import org.dom4j.Document;
  import org.dom4j.DocumentException;
  import org.dom4j.Element;
  import org.dom4j.io.SAXReader;
  
  import java.beans.PropertyVetoException;
  import java.io.InputStream;
  import java.util.List;
  import java.util.Properties;
  
  /**
   * 使用dom4j解析sqlMapConfig.xml的字节流封装到Configuration核心配置类
   */
  public class XMLConfigBuilder {
  
      //声明核心配置类成员变量
      private Configuration configuration;
  
      //在new XMLConfigBuilder的时候就会通过无参构造给成员变量configuration进行赋值
      public XMLConfigBuilder() {
          this.configuration = new Configuration();
      }
  
      /**
       * 该方法使用dom4j将sqlMapConfig.xml在内存中的字节流封装到Configuration核心配置类并返回。
       *
       * @param is 核心配置文件qlMapConfig.xml的字节流
       * @return
       */
      public Configuration parseConfig(InputStream is) throws DocumentException, PropertyVetoException {
  
          /**
           * 第一步：通过dom4j解析核心配置文件封装到Configuration配置类
           */
          //通过dom4j读取核心配置文件字节流生成Dom
          Document document = new SAXReader().read(is);
          //拿到根元素，也就是sqlMapConfig.xml文件中的configuration标签
          Element configElement = document.getRootElement();
          //查找根元素下所有的property标签元素
          List<Element> propertyElements = configElement.selectNodes("//property");
          //通过Properties存储数据源信息name以及value值
          Properties dataSources = new Properties();
          //获取每一个property元素的name以及value值
          for (Element propertyElement : propertyElements) {
              String name = propertyElement.attributeValue("name");
              String value = propertyElement.attributeValue("value");
              //将name、value对应的存入Properties
              dataSources.setProperty(name,value);
          }
  
          //创建数据库连接池
          ComboPooledDataSource comboPooledDataSource = new ComboPooledDataSource();
          //设置数据源信息
          comboPooledDataSource.setDriverClass(dataSources.getProperty("driverClass"));
          comboPooledDataSource.setJdbcUrl(dataSources.getProperty("jdbcUrl"));
          comboPooledDataSource.setUser(dataSources.getProperty("username"));
          comboPooledDataSource.setPassword(dataSources.getProperty("password"));
  
          //将数据源封装到Configuration对象
          configuration.setDataSource(comboPooledDataSource);
  
          /**
           * 第二步：通过sqlMapConfig.xml中配置的*mapper.xml的全路径来解析*mapper.xml并封装到MappedStatement配置类
           *        再将MappedStatement赋值给Configuration的mappedStatementMap属性
           */
          //mapper.xml解析：拿到mapper.xml路径、通过dom4j解析
          List<Element> mapperElements = configElement.selectNodes("//mapper");
          //sqlMapConfig.xml中可能会配置多个*mapper.xml的地址，所以这里我们会获取到多个地址
          for (Element mapperElement : mapperElements) {
              String mapperPath = mapperElement.attributeValue("resource");
              //获取*mapper.xml的字节输入流
              InputStream mapperStream = Resources.getResourceAsStream(mapperPath);
              XMLMapperBuilder xmlMapperBuilder = new XMLMapperBuilder(configuration);
              xmlMapperBuilder.parse(mapperStream);
          }
  
          //返回核心配置类
          return configuration;
      }
  }
  ```

* **第五步：完善SqlSessionFactoryBuild类中的build方法中的第一步**

  ```java
  package com.eayon.sqlSession;
  
  
  import com.eayon.config.XMLConfigBuilder;
  import com.eayon.pojo.Configuration;
  import org.dom4j.DocumentException;
  
  import java.beans.PropertyVetoException;
  import java.io.InputStream;
  
  /**
   * 构建者类：用于创建SqlSessionFactory
   */
  public class SqlSessionFactoryBuilder {
  
      public SqlsessionFactory build(InputStream is) throws PropertyVetoException, DocumentException {
          //第一：使用dom4j解析配置文件，将解析出来的内容封装到Configuration中
          XMLConfigBuilder xmlConfigBuilder = new XMLConfigBuilder();
          Configuration configuration = xmlConfigBuilder.parseConfig(is);
  
          //第二：创建SqlsessionFactory工厂
  
          return null;
      }
  }
  ```

  这个时候我们已经把``sqlMapConfig.xml``以及``*mapper.xml``配置文件全部解析到了``Configuration``核心配置类中，

  接下来进入下一步：创建``SqlSessionFactory``接口及实现类``DefaultSqlSessionFactory``



### 3.4 创建SqlSessionFactory接口及实现类DefaultSqlSessionFactory

**自定义持久层框架端**

> 我们先想想``DefaultSqlSessionFactory``是一个``Session``工厂，通过``Session``封装的持久化方法去操作数据库，那么由此来看我们先创建``SqlSession``比较好，然后在创建``DefaultSqlSessionFactory``

* **第一步：创建SqlSession接口**

  在``com.eayon.sqlSession``下创建``SqlSession``接口（暂不编写具体内容）

  ```java
  package com.eayon.sqlSession;
  
  /**
   * SqlSession
   */
  public interface SqlSession {
      
  }
  ```

* **第二步：创建实现类DefaultSqlSession**

  在``com.eayon.sqlSession``下创建``SqlSession``接口（暂不编写具体内容）

  ```java
  package com.eayon.sqlSession;
  
  import com.eayon.pojo.Configuration;
  
  /**
   * SqlSession实现类
   */
  public class DefaultSqlSession implements SqlSession {
      //声明核心配置类成员变量
      private Configuration configuration;
  
      //在使用有参构造new的时候会将传递的configuration进行赋值
      public DefaultSqlSession(Configuration configuration) {
          this.configuration = configuration;
      }
      
      //暂不编写其他具体内容
  
  }
  ```

* **第三步：创建SqlSessionFactory接口**

  在``com.eayon.sqlSession``下创建``SqlSessionFactory``接口，提供``openSession()``方法用于生产``session``

  ```java
  package com.eayon.sqlSession;
  
  /**
   * SqlsessionFactory工厂
   */
  public interface SqlsessionFactory {
  
      //生产session
      public SqlSession openSession();
  }
  ```

* **第四步：创建DefaultSqlSessionFactory实现类**

  在``com.eayon.sqlSession``下创建``DefaultSqlSessionFactory``实现类，实现``openSession()``方法用于生产``session``

  ```java
  package com.eayon.sqlSession;
  
  import com.eayon.pojo.Configuration;
  
  /**
   * SqlSessionFactory工厂
   */
  public class DefaultSqlSessionFactory implements SqlessionFactory {
  
      //声明核心配置类成员变量
      private Configuration configuration;
  
      //在使用有参构造new的时候会将传递的configuration进行赋值
      public DefaultSqlSessionFactory(Configuration configuration) {
          this.configuration = configuration;
      }
  
  
      /**
       * 生产SqlSession
       * @return
       */
      @Override
      public SqlSession openSession() {
          return new DefaultSqlSession(configuration);
      }
  }
  ```

* **第五步：完善SqlSessionFactoryBuilder中的第二步**

  ```java
  package com.eayon.sqlSession;
  
  
  import com.eayon.config.XMLConfigBuilder;
  import com.eayon.pojo.Configuration;
  import org.dom4j.DocumentException;
  
  import java.beans.PropertyVetoException;
  import java.io.InputStream;
  
  /**
   * 构建者类：用于创建SqlSessionFactory
   */
  public class SqlSessionFactoryBuilder {
  
      public SqlsessionFactory build(InputStream is) throws PropertyVetoException, DocumentException {
          //第一：使用dom4j解析配置文件，将解析出来的内容封装到Configuration中
          XMLConfigBuilder xmlConfigBuilder = new XMLConfigBuilder();
          Configuration configuration = xmlConfigBuilder.parseConfig(is);
  
          //第二：创建SqlsessionFactory工厂
          DefaultSqlSessionFactory sqlSessionFactory = new DefaultSqlSessionFactory(configuration);
          return sqlSessionFactory;
      }
  }
  ```



### 3.5 创建Executor接口及实现类SimpleExecutor

> 通过上面的步骤我们以及可以通过``SqlSessionFactory.build()``方法创建``sqlSessionFactory``工厂，并且可以通过``sqlSessionFactory.openSession()``方法生产``session``，然后在``session``中封装CRUD方法对数据库进行持久化操作。我们可以直接在``session``的CRUD方法中写jdbc代码，但是我们最好再封装一层。创建``Executor``接口及实现类``SimpleExcutor``，在``SimpleExcutor``写jdbc代码，那么``session``中的CRUD方法通过调用``SimpleExcutor``中的jdbc代码实现对数据库的操作。

**自定义持久层框架端**

* **第一步：创建Executor接口**

  在``com.eayon.sqlSession``下创建``Executor``接口并定义一个``query``方法

  ```java
  package com.eayon.sqlSession;
  
  import com.eayon.pojo.Configuration;
  import com.eayon.pojo.MappedStatement;
  
  import java.util.List;
  
  /**
   * 封装CRUD方法，其实就是对jdbc代码的进一步封装
   */
  public interface Executor {
  
      /**
       * 查询
       * @param configuration 核心配置类
       * @param mappedStatement SQL配置信息
       * @param params 可变参数
       * @param <E>
       * @return
       */
      public <E> List<E> query(Configuration configuration, MappedStatement mappedStatement, Object... params);
  }
  ```

* **第二步：添加标记处理工具类**

  在``com.eayon.util``下添加下面四个工具类，都是我从Mybatis复制过来的。主要用于对客户端自定义SQL中的#{}符号进行解析等处理，解析为jdbc可识别的格式。**可以暂时不用关心这些是干什么的**。如果感觉比较乱，可以下载代码直接复制：[链接](https://github.com/EayonLee/JavaGod/tree/main/1阶段：开源框架源码剖析/01模块：自定义持久层框架设计及MyBatis源码分析/01.自定义持久层框架/01.课程资料)

  **GenericTokenParser**

  ```java
  /**
   *    Copyright 2009-2017 the original author or authors.
   *
   *    Licensed under the Apache License, Version 2.0 (the "License");
   *    you may not use this file except in compliance with the License.
   *    You may obtain a copy of the License at
   *
   *       http://www.apache.org/licenses/LICENSE-2.0
   *
   *    Unless required by applicable law or agreed to in writing, software
   *    distributed under the License is distributed on an "AS IS" BASIS,
   *    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
   *    See the License for the specific language governing permissions and
   *    limitations under the License.
   */
  package com.eayon.util;
  
  /**
   * @author Clinton Begin
   */
  public class GenericTokenParser {
  
    private final String openToken; //开始标记
    private final String closeToken; //结束标记
    private final TokenHandler handler; //标记处理器
  
    public GenericTokenParser(String openToken, String closeToken, TokenHandler handler) {
      this.openToken = openToken;
      this.closeToken = closeToken;
      this.handler = handler;
    }
  
    /**
     * 解析${}和#{}
     * @param text
     * @return
     * 该方法主要实现了配置文件、脚本等片段中占位符的解析、处理工作，并返回最终需要的数据。
     * 其中，解析工作由该方法完成，处理工作是由处理器handler的handleToken()方法来实现
     */
    public String parse(String text) {
      // 验证参数问题，如果是null，就返回空字符串。
      if (text == null || text.isEmpty()) {
        return "";
      }
  
      // 下面继续验证是否包含开始标签，如果不包含，默认不是占位符，直接原样返回即可，否则继续执行。
      int start = text.indexOf(openToken, 0);
      if (start == -1) {
        return text;
      }
  
     // 把text转成字符数组src，并且定义默认偏移量offset=0、存储最终需要返回字符串的变量builder，
      // text变量中占位符对应的变量名expression。判断start是否大于-1(即text中是否存在openToken)，如果存在就执行下面代码
      char[] src = text.toCharArray();
      int offset = 0;
      final StringBuilder builder = new StringBuilder();
      StringBuilder expression = null;
      while (start > -1) {
       // 判断如果开始标记前如果有转义字符，就不作为openToken进行处理，否则继续处理
        if (start > 0 && src[start - 1] == '\\') {
          builder.append(src, offset, start - offset - 1).append(openToken);
          offset = start + openToken.length();
        } else {
          //重置expression变量，避免空指针或者老数据干扰。
          if (expression == null) {
            expression = new StringBuilder();
          } else {
            expression.setLength(0);
          }
          builder.append(src, offset, start - offset);
          offset = start + openToken.length();
          int end = text.indexOf(closeToken, offset);
          while (end > -1) {////存在结束标记时
            if (end > offset && src[end - 1] == '\\') {//如果结束标记前面有转义字符时
              // this close token is escaped. remove the backslash and continue.
              expression.append(src, offset, end - offset - 1).append(closeToken);
              offset = end + closeToken.length();
              end = text.indexOf(closeToken, offset);
            } else {//不存在转义字符，即需要作为参数进行处理
              expression.append(src, offset, end - offset);
              offset = end + closeToken.length();
              break;
            }
          }
          if (end == -1) {
            // close token was not found.
            builder.append(src, start, src.length - start);
            offset = src.length;
          } else {
            //首先根据参数的key（即expression）进行参数处理，返回?作为占位符
            builder.append(handler.handleToken(expression.toString()));
            offset = end + closeToken.length();
          }
        }
        start = text.indexOf(openToken, offset);
      }
      if (offset < src.length) {
        builder.append(src, offset, src.length - offset);
      }
      return builder.toString();
    }
  }
  ```

  **ParameterMapping**

  ```java
  package com.eayon.util;
  
  public class ParameterMapping {
  
      private String content;
  
      public ParameterMapping(String content) {
          this.content = content;
      }
  
      public String getContent() {
          return content;
      }
  
      public void setContent(String content) {
          this.content = content;
      }
  }
  ```

  **ParameterMappingTokenHandler**

  ```java
  package com.eayon.util;
  
  import java.util.ArrayList;
  import java.util.List;
  
  
  public class ParameterMappingTokenHandler implements TokenHandler {
     private List<ParameterMapping> parameterMappings = new ArrayList<ParameterMapping>();
  
     // context是参数名称 #{id} #{username}
  
     @Override
     public String handleToken(String content) {
        parameterMappings.add(buildParameterMapping(content));
        return "?";
     }
  
     private ParameterMapping buildParameterMapping(String content) {
        ParameterMapping parameterMapping = new ParameterMapping(content);
        return parameterMapping;
     }
  
     public List<ParameterMapping> getParameterMappings() {
        return parameterMappings;
     }
  
     public void setParameterMappings(List<ParameterMapping> parameterMappings) {
        this.parameterMappings = parameterMappings;
     }
  
  }
  ```

  **TokenHandler**

  ```java
  /**
   *    Copyright 2009-2015 the original author or authors.
   *
   *    Licensed under the Apache License, Version 2.0 (the "License");
   *    you may not use this file except in compliance with the License.
   *    You may obtain a copy of the License at
   *
   *       http://www.apache.org/licenses/LICENSE-2.0
   *
   *    Unless required by applicable law or agreed to in writing, software
   *    distributed under the License is distributed on an "AS IS" BASIS,
   *    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
   *    See the License for the specific language governing permissions and
   *    limitations under the License.
   */
  package com.eayon.util;
  
  /**
   * @author Clinton Begin
   */
  public interface TokenHandler {
    String handleToken(String content);
  }
  ```

* **第三步：创建BoundSql类**

  在``com.eayon.config``下创建``BoundSql``，该类主要用于封装存储通过上面工具类解析后的SQL语句。**可以暂时不用关心这些是干什么的**

  ```java
  package com.eayon.config;
  
  import com.eayon.util.ParameterMapping;
  
  import java.util.ArrayList;
  import java.util.List;
  
  /**
   * 转换并赋值后的SQL语句
   */
  public class BoundSql {
  
      private String sql;//解析后的sql
      private List<ParameterMapping> parameterMappingList = new ArrayList<>();//#{}里面解析出来的参数名称
  
      public BoundSql(String sql, List<ParameterMapping> parameterMappingList) {
          this.sql = sql;
          this.parameterMappingList = parameterMappingList;
      }
  
      public String getSql() {
          return sql;
      }
  
      public void setSql(String sql) {
          this.sql = sql;
      }
  
      public List<ParameterMapping> getParameterMappingList() {
          return parameterMappingList;
      }
  
      public void setParameterMappingList(List<ParameterMapping> parameterMappingList) {
          this.parameterMappingList = parameterMappingList;
      }
  }
  ```

* **第四步：创建实现类SimpleExecutor并定义query方法**

  在``com.eayon.sqlSession``下创建``SimpleExecutor``实现类并实现``query``方法

  ```java
  package com.eayon.sqlSession;
  
  import com.eayon.config.BoundSql;
  import com.eayon.pojo.Configuration;
  import com.eayon.pojo.MappedStatement;
  import com.eayon.util.GenericTokenParser;
  import com.eayon.util.ParameterMapping;
  import com.eayon.util.ParameterMappingTokenHandler;
  
  import java.beans.PropertyDescriptor;
  import java.lang.reflect.Field;
  import java.lang.reflect.Method;
  import java.sql.*;
  import java.util.ArrayList;
  import java.util.List;
  
  /**
   * 封装CRUD方法，其实就是对jdbc代码的进一步封装
   */
  public class SimpleExecutor implements Executor {
  
      /**
       * 查询
       *
       * @param configuration   核心配置类
       * @param mappedStatement SQL配置信息
       * @param params          可变参数
       * @param <E>
       * @return
       */
      @Override
      public <E> List<E> query(Configuration configuration, MappedStatement mappedStatement, Object... params) throws Exception {
          // 1. 注册驱动，获取连接
          Connection connection = configuration.getDataSource().getConnection();
  
          //2.获取本次操作的sql语句
          String sql = mappedStatement.getSql();
          //比如获取到的SQL格式：select * from user where id = #{id} and username = #{username} jdbc无法识别，所以我们需要转换
          //比如转为select * from user where id = ? and username = ? 这样的格式，并且还需要对#{}里面的值进行解析存储
          //转换SQL
          BoundSql boundSql = getBoundSql(sql);
  
          // 3.获取预处理对象：preparedStatement
          PreparedStatement preparedStatement = connection.prepareStatement(boundSql.getSql());
  
          //4.设置参数
          //获取查询参数类型的全路径  如：com.eayon.pojo.User
          String paramterType = mappedStatement.getParamterType();
          //获取到查询参数类型的Class字节码对象   如：class com.eayon.pojo.User
          Class<?> paramtertypeClass = getClassType(paramterType);
  
          //获取原始SQL中#{}里面设置的参数名称集合  如：id，username
          List<ParameterMapping> parameterMappingList = boundSql.getParameterMappingList();
          //遍历参数名称集合，
          for (int i = 0; i < parameterMappingList.size(); i++) {
              //循环取出原始SQL中#{}里面设置的参数名称
              ParameterMapping parameterMapping = parameterMappingList.get(i);
              String content = parameterMapping.getContent();
  
              //使用反射，根据参数名获取实体对象的属性值， 再根据传递的参数进行赋值
              //通过Class获取到某一个属性对象
              Field declaredField = paramtertypeClass.getDeclaredField(content);
              //暴力访问
              declaredField.setAccessible(true);
              //获取该属性对象的值
              Object o = declaredField.get(params[0]);
              //设置参数
              preparedStatement.setObject(i+1,o);
          }
  
          // 5. 执行sql
          ResultSet resultSet = preparedStatement.executeQuery();
  
          //获取返回结果的实体全路径
          String resultType = mappedStatement.getResultType();
          //获取返回结果实体全路径的Class，主要用于反射封装结果集时使用
          Class<?> resultTypeClass = getClassType(resultType);
  
          //返回结果集
          ArrayList<Object> objects = new ArrayList<>();
  
          // 6. 封装返回结果集
          while (resultSet.next()){
              //获取返回结果实体对象
              Object o =resultTypeClass.newInstance();
              //metaData.getColumnCount()  查询结果集中的总列数  循环获取每列数据
              ResultSetMetaData metaData = resultSet.getMetaData();
              for (int i = 1; i <= metaData.getColumnCount(); i++) {
                  //获取数据库中的字段名称
                  String columnName = metaData.getColumnName(i);
                  //通过字段名称去查询结果集中取出字段值
                  Object value = resultSet.getObject(columnName);
                  //PropertyDescriptor：可以通过有参构造获取该字段在返回值实体中的get、set方法
                  PropertyDescriptor propertyDescriptor = new PropertyDescriptor(columnName, resultTypeClass);
                  //然后我们就可以获取到这个返回值实体的set方法
                  Method writeMethod = propertyDescriptor.getWriteMethod();
                  //把该字段值set到实体里面即可
                  writeMethod.invoke(o,value);
              }
              //封装到返回结果集
              objects.add(o);
          }
  
          //返回查询结果
          return (List<E>) objects;
      }
  
  
      /**
       * 通过查询参数的全路径获取到Class
       *
       * @param paramterType 参数的全路径
       * @return
       * @throws ClassNotFoundException
       */
      private Class<?> getClassType(String paramterType) throws ClassNotFoundException {
          if (paramterType != null) {
              Class<?> aClass = Class.forName(paramterType);
              return aClass;
          }
          return null;
      }
  
      /**
       * 完成对SQL语句的解析：
       * 1、将#{}使用?进行代替
       * 2、解析出#{}里面的值进行存储
       *
       * @param sql
       * @return
       */
      public BoundSql getBoundSql(String sql) {
          //标记处理类：配置标记解析器来完成对占位符的解析处理工作
          ParameterMappingTokenHandler parameterMappingTokenHandler = new ParameterMappingTokenHandler();
          GenericTokenParser genericTokenParser = new GenericTokenParser("#{", "}", parameterMappingTokenHandler);
          //解析出来的sql
          String parseSql = genericTokenParser.parse(sql);
          //#{}里面解析出来的参数名称
          List<ParameterMapping> parameterMappings = parameterMappingTokenHandler.getParameterMappings();
  
          //解析并封装好的SQL进行返回
          BoundSql boundSql = new BoundSql(parseSql, parameterMappings);
          return boundSql;
      }
  }
  ```



### 3.6 定义SqlSession中的CRUD方法

> 通过上面的步骤我们以及可以通过``SqlSessionFactory.build()``方法创建``sqlSessionFactory``工厂，并且可以通过``sqlSessionFactory.openSession()``方法生产``session``，而且也在``Executor``中封装好jdbc代码。但是``session``中还没有定义调用``Executor``的方法。那我们就来完善SqlSession

**自定义持久层框架端**

* **第一步：完善SqlSession接口**

  ```java
  package com.eayon.sqlSession;
  
  import java.util.List;
  
  /**
   * SqlSession
   */
  public interface SqlSession {
  
      /**
       * 查询所有
       *
       * @param statementId statementId（namespace.id），通过statementId可以定位到每一条SQL
       * @param params 可变参数
       * @param <E>
       * @return
       */
      public <E> List<E> selectList(String statementId, Object... params);
  
      /**
       * 根据条件查询单个
       *
       * @param statementId statementId（namespace.id），通过statementId可以定位到每一条SQL
       * @param params 可变参数
       * @return
       */
      public <T> T selectOne(String statementId, Object... params);
  }
  ```

* **第二步：完善DefaultSqlSession实现类**

  ```java
  package com.eayon.sqlSession;
  
  import com.eayon.pojo.Configuration;
  import com.eayon.pojo.MappedStatement;
  
  import java.util.List;
  import java.util.Map;
  
  /**
   * SqlSession实现类
   */
  public class DefaultSqlSession implements SqlSession {
      //声明核心配置类成员变量
      private Configuration configuration;
  
      //在使用有参构造new的时候会将传递的configuration进行赋值
      public DefaultSqlSession(Configuration configuration) {
          this.configuration = configuration;
      }
  
      /**
       * 查询所有
       *
       * @param statementId statementId（namespace.id），通过statementId可以定位到每一条SQL
       * @param params 可变参数
       * @param <E>
       * @return
       */
      @Override
      public <E> List<E> selectList(String statementId, Object... params) throws Exception {
          //创建SimpleExecutor
          SimpleExecutor simpleExecutor = new SimpleExecutor();
          //通过核心配置文件获取所有的MappedStatement集合
          Map<String, MappedStatement> mappedStatementMap = configuration.getMappedStatementMap();
          //通过本次查询参数中的statementId，从Map集合中找出本次查询的SQL配置（SQL语句，返回值类型，参数类型等等）
          MappedStatement mappedStatement = mappedStatementMap.get(statementId);
          //调用SimpleExecutor中对jdbc封装好的query方法进行查询
          List<E> list = simpleExecutor.query(configuration, mappedStatement, params);
          return list;
      }
  
      /**
       * 根据条件查询单个
       *
       * @param statementId statementId（namespace.id），通过statementId可以定位到每一条SQL
       * @param params 可变参数
       * @return
       */
      @Override
      public <T> T selectOne(String statementId, Object... params) throws Exception {
          List<Object> objects = selectList(statementId, params);
          if(objects.size() == 1){
              return (T) objects.get(0);
          }else {
              throw new RuntimeException("查询结果为空或返回结果过多");
          }
      }
  }
  ```



### 3.7 客户端测试运行

> 别忘了将自定义框架项目打包

**客户端**

* **第一步：测试**

  ```java
  package com.eayon.test;
  
  
  import com.eayon.io.Resources;
  import com.eayon.pojo.User;
  import com.eayon.sqlSession.SqlSession;
  import com.eayon.sqlSession.SqlSessionFactoryBuilder;
  import com.eayon.sqlSession.SqlsessionFactory;
  import java.io.InputStream;
  import java.util.List;
  
  /**
   * 测试类
   */
  public class MyTest {
  
      @Test
      public void test1() throws Exception {
          //获取核心配置文件字节流
          InputStream resourceAsStream = Resources.getResourceAsStream("sqlMapConfig.xml");
          //创建session工厂
          SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
          SqlsessionFactory sqlsessionFactory = sqlSessionFactoryBuilder.build(resourceAsStream);
          //通过session工厂创建session
          SqlSession sqlSession = sqlsessionFactory.openSession();
          //通过session操作数据库查询
          User user = new User();
          user.setId(1);
          user.setUsername("张三");
          //参数一：statementId  (statementId.id)   参数二：查询参数
          List<User> users = sqlSession.selectList("user.selectOne", user);
          for (User user1 : users) {
              System.out.println(user1);
          }
      }
  }
  ```

  至此简单的自定义持久层框架就完成了，但是我们发现在客户端现在还是有些问题的。



### 3.8 自定义持久层框架优化

**客户端**

> 存在的问题：在上面的单元测试test1中有许多代码，显得非常臃肿，按照正常思路这些代码应该写在持久层的方法中(dao层)

* **第一步：创建UserDao及实现类UserDaoImpl**

  在``com.eayon.dao``下创建``UserDao``接口

  ```java
  package com.eayon.dao;
  
  import com.eayon.pojo.User;
  
  import java.util.List;
  
  /**
   * UserDao
   */
  public interface UserDao {
  
      //查询所有用户
      public List<User> findAll() throws Exception;
  
      //根据条件查询
      public User findByCondition(User user) throws Exception;
  }
  ```

* **第二步：创建实现类UserDaoImpl**

  在``com.eayon.dao``下创建实现类``UserDaoImpl``，将创建``session``工厂等代码转移至``dao``层实现类

  ```java
  package com.eayon.dao;
  
  import com.eayon.io.Resources;
  import com.eayon.pojo.User;
  import com.eayon.sqlSession.SqlSession;
  import com.eayon.sqlSession.SqlSessionFactoryBuilder;
  import com.eayon.sqlSession.SqlsessionFactory;
  import java.io.InputStream;
  import java.util.List;
  
  /**
   * UserDao
   */
  public class UserDaoImpl implements UserDao {
      @Override
      public List<User> findAll() throws Exception {
          //获取核心配置文件字节流
          InputStream resourceAsStream = Resources.getResourceAsStream("sqlMapConfig.xml");
          //创建session工厂
          SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
          SqlsessionFactory sqlsessionFactory = sqlSessionFactoryBuilder.build(resourceAsStream);
          //通过session工厂创建session
          SqlSession sqlSession = sqlsessionFactory.openSession();
  
          //参数一：statementId  (statementId.id)
          List<User> users = sqlSession.selectList("user.selectList");
          return users;
      }
  
      @Override
      public User findByCondition(User user) throws Exception {
          //获取核心配置文件字节流
          InputStream resourceAsStream = Resources.getResourceAsStream("sqlMapConfig.xml");
          //创建session工厂
          SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
          SqlsessionFactory sqlsessionFactory = sqlSessionFactoryBuilder.build(resourceAsStream);
          //通过session工厂创建session
          SqlSession sqlSession = sqlsessionFactory.openSession();
          //通过session操作数据库查询
          //参数一：statementId  (statementId.id)   参数二：查询参数
          User user2 = sqlSession.selectOne("user.selectOne",user);
          return user2;
      }
  }
  ```

* **第三步：测试**

  ```java
  @Test
  public void test2() throws Exception {
      UserDaoImpl userDao = new UserDaoImpl();
      List<User> users = userDao.findAll();
      System.out.println(users);
  }
  ```



**多说一嘴**：

>**本章相关代码及笔记地址：**[**飞机票🚀**](https://github.com/EayonLee/JavaGod/tree/main/1阶段：开源框架源码剖析/01模块：自定义持久层框架设计及MyBatis源码分析)
>
>🌍Github：[🚀Java超神之路：【🍔Java全生态技术学习笔记，一起超神吧🍔】](https://github.com/EayonLee/JavaGod)
>🪐CSDN：[🚀Java超神之路：【🍔Java全生态技术学习笔记，一起超神吧🍔】](https://blog.csdn.net/qq_20492277/article/details/114269863)