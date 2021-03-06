# `MyBatis`概述

MyBatis3.4x版本，把他内部需要的三方jar都整合在一起了。

## 解决的问题

MyBatis 减少了样板代码，简化了持久层的开发。

## 快捷键基础

> win10快捷键

- Alt + Tab 选择活动窗口

> idea快捷键

- ctrl + Tab 切换窗口
- ctrl + E 最近编辑的窗口 
- Alt + 1 显示/隐藏侧边栏
- ctrl + F4 关闭当前窗口
- Alt + Insert 插入代码【如：生成set get方法】
- Alt + Shift + R 重命名
- Ctrl + Shift + F10 运行代码
- Ctrl + W 关闭侧边栏

# `MyBatis`设计模式

相对路径 `src/java/main/文件名.xml`

读配置文件 ①用类加载器，读类路径下的

​		  			②用`Servlet Context`对象的`getRealPath`

创建工程`MyBatis`用了构建者模式。告诉需求，根据需求创建我们想要的。

```java
build.build(in) // in形式下创建的工厂，多了几个类，操作看起来麻烦了，但是组合更加灵活的。
```

生成`SqlSession`用了工厂模式

创建`Dao`接口实现类用了代理模式

在看`MyBatis`源码的时候，通过一些类的名称大概知道了`MyBatis`用到了什么技术。`MyBatis`解析的时候应该用到了词法分析，分析字符串。在动态生成代理类的时候用到了字节码增强技术。

# `MyBatis`基础篇

## 基本环境搭建

Maven工程使用 `MyBatis` 的时候，配置文件需要放在 `resrouces` 目录下，否则无法找到。

整合 Druid 的时候，需要的是数据源，需要我们手动 new 出 Druid 的数据源。

- 基本配置文件
- mapper文件
- 日志文件

maven的 pom 文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.bbxx</groupId>
    <artifactId>MyBatis02</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>jar</packaging>

    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>8</source>
                    <target>8</target>
                </configuration>
            </plugin>
        </plugins>
    </build>

    <dependencies>
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.4.5</version>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.13</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.17</version>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.21</version>
        </dependency>
    </dependencies>
</project>
```

`SqlConfig` 配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!--配置包别名【resultType】-->
    <typeAliases>
        <package name="com.bbxx.pojo"/>
    </typeAliases>
    <!-- 配置 mybatis 的环境 -->
    <environments default="mysql">
        <!-- 配置 mysql 的环境 -->
        <environment id="mysql">
            <!-- 配置事务的类型 -->
            <transactionManager type="JDBC"></transactionManager>
            <!-- 配置连接数据库的信息：用的是数据源(连接池) 如果用的是三方数据源，如阿里的druid-->
            <!-- <dataSource type="xx.xx.DruidPoolConfig">这个DruidPoolConfig是我们自己new出来的 -->
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis?serverTimezone=UTC"/>
                <property name="username" value="root"/>
                <property name="password" value="root"/>
            </dataSource>
        </environment>
    </environments>
    <!-- 告知 mybatis 映射配置的位置 -->
    <!-- com/bbxx/dao/UserDao.xml是mavenresouce目录下的哦！ -->
    <mappers>
        <mapper resource="com/bbxx/dao/UserDao.xml"/>
        <!-- 如果是用的注解SQL，则采用,因为注解方式不用Mapper文件！ -->
        <mapper class="com.bbxx.dao.IUserDao"/>
    </mappers>
</configuration>
```

mapper文件示例

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!-- namespace是接口的类全名 resultType是返回类型的类全民，可通过配置简写 -->
<mapper namespace="xx.dao.xx">
    <select id="select" resultType="xx.Article">
		select title,username from article;
	</select>
</mapper>
```

## 集成Druid

集成 Druid 只需要在前面的基础上修改一点东西即可。

- 新建一个类，继承自 UnpooledDataSourceFactory 类（MyBatis官方文档的示例）

```java
public class DataSourceDruid extends UnpooledDataSourceFactory {

    @Override
    public DataSource getDataSource() {
        DruidDataSource druidDataSource = new DruidDataSource();
        // Druid的配置信息。看的源码知道的，可以通过这种方式进行配置。
        druidDataSource.setUrl("jdbc:mysql://localhost:3306/mybatis?serverTimezone=UTC");
        druidDataSource.setUsername("root");
        druidDataSource.setPassword("root");
        druidDataSource.setDriverClassName("com.mysql.cj.jdbc.Driver");
        return druidDataSource;
    }
}
```

- SqlConfig文件进行一小部分修改

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <typeAliases>
        <package name="com.bbxx.pojo"/>
    </typeAliases>
    <!-- 配置 mybatis 的环境 -->
    <environments default="mysql">
        <!-- 配置 mysql 的环境 -->
        <environment id="mysql">
            <!-- 配置事务的类型 -->
            <transactionManager type="JDBC"></transactionManager>
            <!-- 配置连接数据库的信息：用的是Druid数据源(连接池) 这个类是我们自己定义的，且重写了getDataSource方法！-->
            <dataSource type="com.bbxx.utils.DataSourceDruid">
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis?serverTimezone=UTC"/>
                <property name="username" value="root"/>
                <property name="password" value="root"/>
            </dataSource>
        </environment>
    </environments>
    <!-- 告知 mybatis 映射配置的位置 -->
    <mappers>
        <mapper resource="com/bbxx/dao/UserDao.xml"/>
    </mappers>
</configuration>
```

- pom文件中添加

```xml
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.2.5</version>
</dependency>
```

## 日志相关

**`log4j`日志配置**

```properties
#log4j基本配置
log4j.rootLogger=DEBUG,console,file
#设置输出控制台信息
log4j.appender.console=org.apache.log4j.ConsoleAppender
log4j.appender.console.Target = System.out
log4j.appender.console.Threshold=DEBUG     #控制台只会打印INFO级别及以上的日志信息
log4j.appender.console.layout = org.apache.log4j.PatternLayout
log4j.appender.console.layout.ConversionPattern=%c-%m%n
#设置输出文件信息
log4j.appender.file = org.apache.log4j.RollingFileAppender
log4j.appender.file.File=log/mybatis.log     #日志文件路径，在应用下的log/mybatis.log文件中
log4j.appender.file.Append=true   #追加
log4j.appender.file.MaxFileSize=100mb    #达到100m后产生一个新文件
log4j.appender.file.Threshold=ERROR      #只会写ERROR级别及以上的日志信息
log4j.appender.file.layout=org.apache.log4j.PatternLayout     #布局器
log4j.appender.file.layout.ConversionPattern=%c-%m%n   #布局器格式
```

**`log4j`仅打印`SQL`语句**

```properties
# 全局日志配置
log4j.rootLogger=ERROR, stdout
# MyBatis 日志配置  com.bbxx.dao是包名。
log4j.logger.com.bbxx.dao=TRACE
# 控制台输出
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%5p [%t] - %m%n
```

## Mapper映射文件

<a href="https://mybatis.org/mybatis-3/zh/configuration.html#mappers">官方链接</a>

Mapper 映射文件放在 maven 工程 resource 下 com/daily/mapper 也是 resource 的子目录

> 1、用文件路径引入

```xml
<mappers>
    <mapper resource="com/daily/mapper/UserMapper.xml" />
    <mapper resource="com/daily/mapper/ProductMapper.xml" />
    <mapper resource="com/daily/mapper/BlogMapper.xml" />
</mappers>
```

> 2、用包名引入★

这种引入方式相当于批量引入一个包下的所有映射器。此种方式要求xml和接口名称一致？

```xml
<mappers>
    <package name="com.daily.mapper"/>
</mappers>
```

> 3、用类注册引入

```xml
<mappers>
    <mapper class="com.daily.mapper.UserMapper" />
    <mapper class="com.daily.mapper.ProductMapper" />
    <mapper class="com.daily.mapper.BlogMapper" />
</mappers>
```

> 4、使用URL方式引入

```xml
<mappers>
    <mapper url="xml文件访问URL" />
</mappers>
```

一般我喜欢使用 `包名引入`。

maven项目下，所有的非 `*.java` 文件都要放在 resources 目录下。resources 是项目的`资源根目录！`

如：src/main/java 目录下的包和类都是以 classes 为根目录进行发布。resources 下的资源也是以classes 为根目录。

<img src="img/ibatis/maven.png">

mybatis 多对多是由两个一对一组成的，如：user 一对多role，role 一对多user，这样 user 和 role 就是多对多关系了。 数据库的多对多需要一个中间表来描述两表的多对多关系。

<a href="https://github.com/csliujw/MyBatis-Study">项目地址</a>

## 简单的CURD

```java
package com.bbxx.dao;

import com.bbxx.pojo.UserVO;
import java.util.List;

public interface IUserDao {
    // 查询所有
    List<UserVO> findAll();
    // 条件查询
    List<UserVO> findCondition(UserVO vo);
    // 删除
    Integer delete(Integer id);
    // 修改
    Boolean update(UserVO vo);
    // 新增
    Boolean insert(UserVO vo);
    // 模糊查询
    List<UserVO> findByName(String username);
    // 聚合函数
    Long findTotal();
}
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.bbxx.dao.IUserDao">
    <!-- 配置查询所有操作 -->
    <select id="findAll" resultType="com.bbxx.pojo.UserVO">
        select * from users
    </select>

    <select id="findCondition" resultType="UserVO">
        select * from users where 1 = 1
        <if test="id!=null">
            and id=#{id}
        </if>
        <if test="username!=null">
            and username=#{username}
        </if>
        <if test="birthday!=null">
            and birthday=#{birthday}
        </if>
    </select>

    <delete id="delete">
        delete from users where id = #{value}
    </delete>

    <update id="update" parameterType="UserVO">
        update users
        <set>
            <if test="username!=null">
                username = #{username}
            </if>
        </set>
        where id=#{id}
    </update>
	<!--
		让MyBatis自动地将自增id赋值给传入地employee对象的id属性。
		useGeneratedKeys="true";原生jdbc获取自增主键的方法：
			keyProperty="",将刚才自增的id封装给哪个属性。
	-->
    <insert id="insert" useGeneratedKeys="true" keyProperty="id">
        insert into users(username, birthday, address)
        values (#{username}, #{birthday}, #{address})
    </insert>

    <select id="findByName" resultType="UserVO">
        select *
        from users
        where username like concat("%", #{value}, "%")
    </select>

    <select id="findTotal" resultType="long">
        select count(1)
        from users
    </select>
</mapper>
```

## 参数占位符用法

> #{}等同于占位符 "?"

- 只有一个形式参数时：

```java
public Employee getXX(Integer id);
```

```xml
<select id="getXX" resultType="com.xx.xx.Employee">
	select * from xxx where id=#{随便写什么} <!-- 建议还是见名知意奥 -->
</select>
```

- 有多个形参时：

```java
public Employee getXX(Integer id, String name);
```

```xml
<!--
	直接用id，name作为#{}的话，会报错。
	Caused by: org.apache.ibatis.binding.BindingException:
	Parameter 'id' not found
	Available parameters are [0, 1, param1, param2]
-->
<select id="getXX" resultType="com.xx.xx.Employee">
	select * from xxx where id=#{param1} and name=#{param2}
</select>
<!-- 或者是 -->
<select id="getXX" resultType="com.xx.xx.Employee">
	select * from xxx where id=#{0} and name=#{1}
</select>
```

> 总结

- 要么写#{0} #{1} 要么写#{param1} #{param2} 
- 只有一个形参的话写什么都行#{asf} #{haha}都行
- 原因：只要传入了多个参数；MyBatis会自动的将这些参数封装在一个 map 中；封装的时候使用的 key 就是参数的索引和参数的第几个表示

```java
Map<String,Object> map = new HashMap<>();
map.put("1","传入的值1");
map.put("2","传入的值2");
```

> 如果我们不想这样做，想指定 key，那么我们如何指定封装时使用的key？

- 使用注解 `@Param` 指定 map 的 key 的值！具体看看源码。

```java
Employee getXX(@Param("id")Integer id, @Param("enmName")String empName);
```

```xml
<select id="getXX" resultType="com.xx.xx.Employee">
	select * from xxx where id=#{id} and name=#{empName}
    <!-- 这个id是因为@Pamra注解的value是id；这个empName是因为@Param注解的value是empName -->
</select>
```

## MyBatis 取值总结

1）单个参数

- 基本类型：取值用 #{随便写}
- 传入POJO：取值用 #{POJO字段名称}，是使用 OGNL 表达式语言来实现的

2）多个参数：

- public Employee getXXX(Integer id, String name)，取值：#{参数名}是无效了
- 可以用：0，1（参数索引）或 param1,param2（第几个参数paramN）来取值，#{0},#{1} / #{param1},#{param2}
- 原因：只要传入了多个参数；MyBatis 会自动的将这些参数封装在一个 map 中；封装时使用的 key 就是参数的索引和参数的第几个表示。

```java
Map<String,Object> map = new HashMap<>()
map.put("1","传入的值1");
map.put("2","传入的值2");
// #{1},就是取出 map 中 key=1 的 value
```

3）@Param,为参数指定key；命名参数；推荐这种做法。我们可以使用 @Param 注解告诉 MyBatis，封装参数 map 的时候别乱来，使用我们指定的 key。

4）传入了Map：将多个要使用的参数封装起来，取值#{key}

5）扩展：多个参数；自动封装map。

```java
public XX method(@Param("id")Integer id, String empName,Employee employee);
Integer id ==> #{id}
String empName ==> #{param2}
Employee employee（取出它的email）==> #{param3.email}
```

无论传入什么参数都要能正确的取出值；

- #{key/属性名}
- id=#{id, JdbcType=INT}
    - javaType、jdbcType、mode、numericScale、resultMap、typeHandler
    - 只有jdbcType才可能需要被指定；
    - 默认不指定  jdbcType 的话：mysql 没问题；oracle 没问题；但是万一传入的数据是 null，mysql 插入 null 没问题；oracle 不知道 null 到底是什么类型！会出问题！

mybatis 的取值方式可分为两类：

- #{属性名}：是参数预编译的方式，参数的位置都是用？替代，参数后来都是预编译设置进去的，安全，不会有sql注入问题。
- ${属性名}：不是参数预编译，而是直接和sql语句进行拼串，不安全
    - eg：id=1 or 1 = 1 and empname=
    - 传入一个1 or 1=1 or

## MyBatis 取值源码分析

MapperMethod类

```java
public Object execute(SqlSession sqlSession, Object[] args) {
    Object result;
    switch (command.getType()) {
        case INSERT: {
            Object param = method.convertArgsToSqlCommandParam(args);
            result = rowCountResult(sqlSession.insert(command.getName(), param));
            break;
        }
        case UPDATE: {
            Object param = method.convertArgsToSqlCommandParam(args);
            result = rowCountResult(sqlSession.update(command.getName(), param));
            break;
        }
        case DELETE: {
            Object param = method.convertArgsToSqlCommandParam(args);
            result = rowCountResult(sqlSession.delete(command.getName(), param));
            break;
        }
        case SELECT:
            if (method.returnsVoid() && method.hasResultHandler()) {
                executeWithResultHandler(sqlSession, args);
                result = null;
            } else if (method.returnsMany()) {
                result = executeForMany(sqlSession, args);
            } else if (method.returnsMap()) {
                result = executeForMap(sqlSession, args);
            } else if (method.returnsCursor()) {
                result = executeForCursor(sqlSession, args);
            } else {
                // 我用的 UserVO selectOne(String name);
                // 转换为SQL参数
                Object param = method.convertArgsToSqlCommandParam(args);
                result = sqlSession.selectOne(command.getName(), param);
            }
            break;
        case FLUSH:
            result = sqlSession.flushStatements();
            break;
        default:
            throw new BindingException("Unknown execution method for: " + command.getName());
    }
    if (result == null && method.getReturnType().isPrimitive() && !method.returnsVoid()) {
        throw new BindingException("Mapper method '" + command.getName() 
                                   + " attempted to return null from a method with a primitive return type (" + method.getReturnType() + ").");
    }
    return result;
}
```

继续看method.convertArgsToSqlCommandParam(args);【MapperMethod类中】

```java
public Object convertArgsToSqlCommandParam(Object[] args) {
    return paramNameResolver.getNamedParams(args);
}
```

点进去，进入了ParamNameResolver类

```java
public Object getNamedParams(Object[] args) {
    // names存放的是key-value
    //			  key的取值是0，1，2的取值
    //			  value如果没有用注解的话，就是0，1，2的取值，如果用了注解，就是注解中的值。
    final int paramCount = names.size();
    if (args == null || paramCount == 0) {
        return null;
    } else if (!hasParamAnnotation && paramCount == 1) { // 只有一个参数且没有加注解，则直接返回这个参数的值。
        return args[names.firstKey()];
    } else {
        final Map<String, Object> param = new ParamMap<Object>();
        int i = 0;
        for (Map.Entry<Integer, String> entry : names.entrySet()) {
            param.put(entry.getValue(), args[entry.getKey()]);
            // add generic param names (param1, param2, ...)
            // GENERIC_NAME_PREFIX = "param";
            final String genericParamName = GENERIC_NAME_PREFIX + String.valueOf(i + 1);
            // ensure not to overwrite parameter named with @Param
            if (!names.containsValue(genericParamName)) {
                param.put(genericParamName, args[entry.getKey()]);
            }
            i++;
        }
        return param;
    }
}
```

# `MyBatis`中级篇

## 查询返回map

> 常规情况

```java
public Map<String, Object> getEmpByIdReturnMap(Integer id);
// key是列名，value是字段对应的值。
id	name	email
1	a		afsdf
2	b		afsf
// 此处 id就是列名，1，2就是value
// 这个如果查询出的是多条数据，value应该会是一个集合。
```

```xml
<!-- pamramater一般可以不写。 -->
<select id="getEmpByIdReturnMap" resultType="map">
    select * from emplate where id=#{id}
</select>
```

> POJO情况

```java
// key	 就是这个记录的主键
// value 就是这条记录封装好的对象
// 把查询记录的id的值作为key封装这个map（注解@MapKey）
@MapKey("id")
public Map<String, Employee> getAllEmp();
```

```xml
<!-- 查询多个返回一个map，查询多个情况下，集合里面写元素类型 视频P252 10分30秒左右 -->
<select id="getAllEmp" resultType="com.xx.xx.Employee">
	select * from employee;
</select>
```

## 自定义结果集

type：指定为哪个javaBean自定义封装规则；全类名。

id：唯一标识符，让别名在后面引用

```xml
<resultMap type="com.xx.xx.Cat" id="mycat">
    <!--
	column="id"：指定哪一列是主键列
	property=""：指定cat的哪个属性封装id这一列数据
	-->
    <!-- 主键列 -->
	<id property="pojoid" column="id">
    <!-- 普通列 -->
    <result property="" column=""></result>
</resultMap>
<!--
	resultMap="mycat"：查出数据封装结果的时候，使用mycat自定义的规则。
-->
<select id="getAllCat" resultMap="mycat">
	select * from cat where id=#{id} 
</select>
```

## 一对一查询

**association**：只是表示对象

## 一对多查询

**collection**

collection：定义集合元素的封装

property：指定哪个属性是集合属性

javaType：指定对象类型

ofType：指定集合里面元素的类型

```xml
<!-- 这个property应该是用注解标记了，使用keys作为property -->
<collection property="keys" ofType="com.xx.Key">
	<id></id>
    <result></result>
</collection>
```

## 分步查询

```xml
<select id="getXX" resultMap="mykey02">
	select * from key where id=#{id}
</select>
<!--
	告诉mybatis自己去调用一个查询
	select:指定一个查询sql的唯一标识；mybatis自动调用指定的sql将查询出的lock封装起来
		public Lock getLockByIdSimple(Integer id); 需要传入锁子id
	column:指定将哪一列的数据传递过去。
		getLockByIdSimple(Integer id)不是需要一个查询条件 id吗，column就是把指定列的数据传递过去。
-->
<resultMap type="com.xx.key" id="mykey02">
    <id></id>
    <result></result>
    <association property="lock" select="getLockByIdSimple" column="lockid"></association>
</resultMap>
```

分布查询，两个查询都会执行，即便没有用到第二个查询的数据。这样严重浪费了数据库的性能。

我们可以采用按需加载，需要的时候再去查询：全局开启按需加载策略！

## 按需加载

```xml
<settings>
    <!-- 开启延迟加载开关 -->
	<setting name="lazyLoadingEnable" value="true"></setting>
    <!-- 开启属性按需加载 -->
    <setting name="aggressiveLazyLoading" value="true"></setting>
</settings>

<!-- Mapper xml文件中按需加载的写法 -->
<!-- fetchType	可选的。有效值为 lazy 和 eager。 指定属性后，将在映射中忽略全局配置参数 lazyLoadingEnabled，使用属性的值 -->
<association xx fetchType="eager"></association>
```

## 动态SQL

> where标签

where标签可以帮我们去除掉前面的and

> trim标签

```xml
<!--
	prefix=""	前缀：为我们下面的sql整体添加一个前缀
	prefixOverrides	取出整体字符串前面多余的字符
	suffix	为整体添加一个后缀
	suffixOverrides	后面哪个多了可以去掉
-->
<trim prefix="where" prefixOverrides="and">
	<if test="id!=null">
    	id > #{id} and
    </if>
    <!--
		有些字符是xml的标记，所以需要转义
	-->
    <if test="name != null &amp;&amp; !name.equals(&quot;&quot;)">
    	teacherName like #{name} and
    </if>
    <if test="birth != null">
    	birth_date &lt; #{birth} and
    </if>
</trim>
```

> foreach

`foreach` 元素的功能非常强大，它允许你指定一个集合，声明可以在元素体内使用的集合项（`item`）和索引（`index`）变量。它也允许你指定开头与结尾的字符串以及集合项迭代之间的分隔符。这个元素也不会错误地添加多余的分隔符，看它多智能！

**提示** 你可以将任何可迭代对象（如 `List`、`Set` 等）、`Map` 对象或者数组对象作为集合参数传递给 `*foreach*`。当使用可迭代对象或者数组时，`index` 是当前迭代的序号，item 的值是本次迭代获取到的元素。当使用 `Map` 对象（或者 `Map.Entry` 对象的集合）时，`index` 是键，`item` 是值。

```xml
select xxxxx where id in
<!--
	collection	指定要遍历的集合的key
	close		以什么结束
	open		以什么开始
	index		索引
		如果遍历的是一个list，index指定的变量保存了当前索引
		如果遍历的是一个map，index 指定的变量就是保存了当前遍历元素的key
	item		变量名
	separator	每次遍历元素的分隔符
	(#{id_item},#{id_item},#{id_item})
	这里collection可以用ids 是因为 用了@Param("ids")为key重新命名了。没有这个的话，List类型默认用的key是list
-->
<foreach collection="ids" item="id_item" separator="," open="(" close=")">
    #{id_item}
</foreach>
```

> choose选择

用到的时候查文档吧，感觉很少会用到。

> 抽取sql片段

```xml
<sql id="selectSql">
	select xxx sfaf
</sql>
<select id="xx" xx>
	<include refid="selectSql"></include>
    where id=#{id}
</select>
```

## 缓存机制

暂时存储一些数据；加快系统的查询速度

MyBatis缓存机制：Map；能保存查询出的一些数据；

一级缓存：线程级别的缓存；本地缓存；SqlSession级别的缓存

二级缓存：全局范围的缓存；除过当前线程；SqlSession能用外其他也可以用

- 一级缓存
  - 一级缓存是 `SqlSession` 范围的缓存，当调用 `SqlSession` 的修改，添加，删除，`commit()，close()`等方法时，就会清空一级缓存。
- 二级缓存
  - 二级缓存是 `mapper` 映射级别的缓存，多个 `SqlSession` 去操作同一个 `Mapper` 映射的 `sql` 语句，多个 `SqlSession` 可以共用二级缓存，二级缓存是跨 `SqlSession` 的

### 一级缓存失效

一级缓存是 `SqlSession` 级别的缓存，只要 `SqlSession` 没有 flush 或 close，它就存在！

```xml
<mapper namespace="com.itheima.dao.IUserDao">
<!-- 根据 id 查询 -->
<select id="findById" resultType="UsEr" parameterType="int" useCache="true">
select * from user where id = #{uid}
</select>
</mapper>
```

请自行编码验证！

看下MyBatis缓存部分的源码就知道，这个缓存机制真的很弱鸡。

一级缓存是SqlSession级别的缓存

1）不同的sqlSession，使用不同的一级缓存

​	只有在同一个sqlSession期间查询到的数据会保存在这个sqlSession的缓存中。

​	下次使用这个sqlSession查询会从缓存中拿

2）同一个方法，不同的参数，由于可能之前没查询过，所以还有发新的sql；

3）在这个sqlSession期间执行任何一次增删改操作，增删改都会把缓存清空。（不管你改的是不是我的数据，我都清空）

4）手动清空缓存 openSession.clearCache()

MyBatis缓存是在

Cache类 - org.apache.ibatis.cache

	- PerpetualCache变量中

```java
/**
 * @author Clinton Begin
 */
public class PerpetualCache implements Cache {

  private final String id;
	
  // 所谓的缓存其实就是一个Map
  private Map<Object, Object> cache = new HashMap<Object, Object>();

  // some method
}
```

### 二级缓存失效

全局作用域缓存

二级缓存默认不开启，需要手动配置

MyBatis提供二级缓存的接口及其实现，缓存实现要求POJO实现Serializable接口

这些 用不到，不想记，要用再说。

## #和$

`#{}`表示一个占位符号

通过`#{}`可以实现 `preparedStatement` 向占位符中设置值，自动进行 `java` 类型和 `jdbc` 类型转换，

`#{}`可以有效防止 `sql` 注入。 `#{}`可以接收简单类型值或 `pojo` 属性值。 如果 `parameterType` 传输单个简单类
型值，`#{}`括号中可以是 value 或其它名称。

`${}`表示拼接 `sql` 串

通过`${}`可以将 `parameterType` 传入的内容拼接在 `sql` 中且不进行 `jdbc` 类型转换， `${}`可以接收简
单类型值或 `pojo` 属性值，如果 `parameterType` 传输单个简单类型值，`${}`括号中只能是 value。

> 源码级别解析

```java
class A{
    @Override
    public String handleToken(String content) {
      Object parameter = context.getBindings().get("_parameter");
      if (parameter == null) {
        context.getBindings().put("value", null);
      } else if (SimpleTypeRegistry.isSimpleType(parameter.getClass())) {
        context.getBindings().put("value", parameter);
      }
      Object value = OgnlCache.getValue(content, context.getBindings());
      String srtValue = (value == null ? "" : String.valueOf(value)); // issue #274 return "" instead of "null"
      checkInjection(srtValue);
      return srtValue;
    }
}
```

>读取的 key 的名字就是”value”，所以我们在绑定参数时就只能叫 value 的名字

## 深入理解

- `MyBatis`可自己写`Dao`实现类也可不写实现类。推荐不写实现类。
- 不写实现类采用的是基于代理的CRUD操作。
- `MyBatis`用到了`OGNL`表达式
  - `Object Graphic Navigation Language`
    	对象	图	导航	   语言
  - 它是通过对象的取值方法来获取数据。在写法上把get给省略了。
    比如：我们获取用户的名称
    	类中的写法：`user.getUsername();`
    	`OGNL`表达式写法：`user.username`
    `mybatis`中为什么能直接写`username`,而不用user.呢：
    	因为在`parameterType`中已经提供了属性所属的类，所以此时不需要写对象名

## 简单实现`MyBatis`

先空着

## 连接池及事务控制

### 连接池介绍

相当于实现分配好很多数据库连接在容器中，需要用时从容器中拿连接，不需要用时把连接归还到容器中，可避免频繁的打开关闭，节约资源（打开关闭连接很消耗资源）。

优点：减少获取连接所消耗的时间

缺点：初始化连接时速度慢

### `MyBatis`中的连接池

**提供三种方式**

- 配置的位置，主配置文件（我命名为`SqlConfig.xml`）中的`dataSource`标签，type表示采用何种连接。
- type取值
  - POOLED： 采用传统的`javax.sql.DataSource`规范中的连接池，`mybatis`中有针对规范的实现。我们可以用其他连接池替代，如`Druid`，`type="我们的druid"`，因为`druid`是遵循规范的，所以把类全名加上就行了。
  - `UNPOOLED`：采用传统的获取连接的方式，虽然也实现`Javax.sql.DataSource`接口，但是并没有使用池的思想。
  - `JNDI`：采用服务器提供的`JNDI`技术实现，来获取`DataSource`对象，不同的服务器所能拿到`DataSource`是不一样。
    		   注意：如果不是web或者`maven`的`war`工程，是不能使用的。
          		  使用`tomcat`服务器的话，采用连接池就是`dbcp`连接池。

### `MyBatis`中的事务

事务的四大特性ACID
	不考虑隔离性会产生的3个问题
	解决办法：四种隔离级别

它是通过`sqlsession`对象的commit方法和rollback方法实现事务的提交和回滚

### 动态`SQL`全部代码

#### `java`代码

```java
public interface IUserDao {

    // 删除 -- 测试事务
    Integer delete(Integer id);

    // 查询所有 -- 查看事务是否成功提交
    List<UserVO> findAll();

    // 条件查询 -- 动态SQL 之if
    List<UserVO> findCondition(UserVO vo);

    // 条件查询 -- 动态SQL 之where
    List<UserVO> findCondition2(UserVO vo);

    // 新增 -- 动态SQL 之 set
    boolean update(UserVO vo);

    // 循环新增 -- 动态SQL 之 foreach
    Long insertBatch(List<UserVO> vos);

    // 循环删除 -- 动态SQL之 foreach 数组
    Long deleteBatch(Integer[] ids);

    // 循环删除 -- 动态SQL之 foreach 集合
    Long deleteBatch(List<Integer> lists);
}
```

#### `xml`文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.bbxx.dao.IUserDao">
    <!-- 配置查询所有操作 -->
    <select id="findAll" resultType="UserVO">
        select *
        from users
    </select>

    <!--动态SQL if-->
    <select id="findCondition" resultType="UserVO">
        select * from users where 1 = 1
        <if test="id!=null">
            and id=#{id}
        </if>
        <if test="username!=null">
            and username like concat("%",#{username},"%")
        </if>
        <if test="birthday!=null">
            and birthday=#{birthday}
        </if>
    </select>
    <!--动态SQL where 去除前面多余的and-->
    <select id="findCondition2" resultType="UserVO">
        select * from users
        <where>
            <if test="id!=null">
                and id=#{id}
            </if>
            <if test="username!=null">
                and username like concat("%",#{username},"%")
            </if>
            <if test="birthday!=null">
                and birthday=#{birthday}
            </if>
        </where>
    </select>
    <!--测试事务-->
    <delete id="delete">
        delete
        from users
        where id = #{value}
    </delete>

    <!--动态SQL测试set 去除后面多余的逗号-->
    <update id="update" parameterType="UserVO">
        update users
        <set>
            <if test="username!=null">username=#{username},</if>
            <if test="birthday!=null">,birthday=#{birthday}</if>
        </set>
        where id=#{id}
    </update>

    <!-- 循环新增 ==> 动态SQL 之 foreach 使用ArrayList集合，collection中写的是参数的类型！这里是list集合 -->
    <insert id="insertBatch" parameterType="UserVO">
        insert into users(username,birthday,address)
        values
        <foreach collection="list" item="data" separator=",">
            (#{data.username},#{data.birthday},#{data.address})
        </foreach>
    </insert>

    <!--
    循环删除 ==> 动态SQL 之 动态SQL之 foreach 数组
    Map的话，查文档得知 index是key item是value
    -->
    <insert id="deleteBatch">
        delete from users where id in
        <foreach collection="array" item="data" open="(" separator="," close=")">
            #{data}
        </foreach>
    </insert>
</mapper>
```

#### 单元测试代码

```java
public class Demo {
    InputStream in = null;
    SqlSessionFactoryBuilder builder = null;
    SqlSessionFactory factory = null;
    SqlSession sqlSession = null;
    IUserDao mapper = null;

    @Before
    public void init() throws IOException {
        in = Resources.getResourceAsStream("SqlConfig.xml");
        builder = new SqlSessionFactoryBuilder();
        factory = builder.build(in);
        sqlSession = factory.openSession();
        mapper = sqlSession.getMapper(IUserDao.class);
    }

    @Test
    public void findAll() {
        List<UserVO> all = mapper.findAll();
    }

    /**
     * 测试事务
     */
    @Test
    public void affairs() {
        sqlSession.commit(false);
        Integer delete = mapper.delete(5);
        System.out.println(delete);
        sqlSession.commit();// 提交事务后数据才会真的删除。 // 对比数据库中的信息查看即可。
    }

    /**
     * 测试动态SQL ==> if
     */
    @Test
    public void testIf() {
        // 查出四条数据
        List<UserVO> condition = mapper.findCondition(new UserVO(null, null, null));
        // 查出三条数据
        List<UserVO> o = mapper.findCondition(new UserVO(null, "o", null));
    }

    /**
     * 测试where，会去掉前面多余的and
     */
    @Test
    public void testWhere() {
        // 查出四条数据
        List<UserVO> condition = mapper.findCondition2(new UserVO(null, null, null));
        // 查出三条数据
        List<UserVO> o = mapper.findCondition2(new UserVO(null, "o", null));
    }

    /**
     * 测试set，会去掉末尾多余的 逗号(,)
     */
    @Test
    public void testSet() {
        boolean kkx = mapper.update(new UserVO(2, "kkx", null));
        sqlSession.commit();
    }

    /**
     * 批量新增
     */
    @Test
    public void testForeach1() {
        ArrayList<UserVO> userVOS = new ArrayList<>();
        userVOS.add(new UserVO(null, "001", "1988-11-11"));
        userVOS.add(new UserVO(null, "002", "1988-02-01"));
        userVOS.add(new UserVO(null, "003", "1999-11-11"));
        userVOS.add(new UserVO(null, "004", "1995-02-21"));
        Long aLong = mapper.insertBatch(userVOS);
        System.out.println(aLong);
        sqlSession.commit();
        findAll();
    }

    /**
     * 循环删除
     */
    @Test
    public void testForeach2() {
        Integer[] ids = {10, 11, 12, 13};
        Long aLong = mapper.deleteBatch(ids);
        System.out.println(aLong);
        sqlSession.commit();
    }

    @After
    public void destroy() throws IOException {
        sqlSession.close();
        in.close();
    }
}
```

## 多表操作

如果POJO字段的名称和数据库的名称不对应则采用

```xml
<resultMap type="类型 如xx类" id="标识符">
	<id column="数据库字段名" property="代码中的字段名"></id> // 主键
    <result column="数据库字段名" property="代码中的字段名"></result> // 普通字段
</resultMap>
```

如果是一对一采用

```xml
<resultMap type="类型 如xx类" id="标识符">
	<id column="数据库字段名" property="代码中的字段名"></id> // 主键
    <result column="数据库字段名" property="代码中的字段名"></result> // 普通字段
    <association property="代码字段名" javaType="POJO属性的类型">
        <id column="数据库字段名" property="代码中的字段名"></id> 
    	<result column="数据库字段名" property="代码中的字段名"></result>
    </association>
</resultMap>
```

如果是一对多采用

```xml
<resultMap type="类型 如xx类" id="标识符">
	<id column="数据库字段名" property="代码中的字段名"></id> // 主键
    <result column="数据库字段名" property="代码中的字段名"></result> // 普通字段
    <collection property="代码字段名" ofType="指定的是映射到list集合属性中pojo的类型。">
        <id column="数据库字段名" property="代码中的字段名"></id> 
    	<result column="数据库字段名" property="代码中的字段名"></result>
    </collection>
</resultMap>
```

## `javaType`和`ofType`

`JavaType`和`ofType`都是用来指定对象类型的，但是`JavaType`是用来指定`pojo`中属性的类型，而`ofType`指定的是映射到list集合属性中`pojo`的类型。


## 延迟加载

### 一对一的延迟加载

#### 概要

举例：用户和账户之间是 一个用户对应多个账户。一个账户对应一个用户。所以账户和用户是一对一的关系。

我们对用户信息进行懒加载。

- `proerty`是`java`字段的名称
- `javaType`是查询出来的数据类型
- select是要调用的查询方法，通过这个查询方法把懒数据查询出来
- column是查询的条件，即where xx = column的值。这个column取自`resultMap`。

```xml
<association property="user" javaType="User" select="com.bbxx.dao.lazy.IUserDao.findOne" column="uid">
</association>
```

#### 具体代码

```java
public interface IAccountDao {
    // 懒加载案例，只查账号不查用户信息 一对一
    List<Account> findAll();
}

public interface IUserDao {
    // User是一对一查询 懒加载中的那个懒数据
    User findOne(Integer id);
}
```

```xml
<mapper namespace="com.bbxx.dao.lazy.IAccountDao">
	<resultMap id="accountMap" type="Account">
        <id column="id" property="id"/>
        <result column="uid" property="uid"/>
        <result column="money" property="money"/>
        <association property="user" javaType="User" select="com.bbxx.dao.lazy.IUserDao.findOne" column="uid">
        </association>
    </resultMap>

    <select id="findAll" resultMap="accountMap">
        select *
        from account
    </select>
</mapper>

<mapper namespace="com.bbxx.dao.lazy.IUserDao">
    <resultMap id="userMap" type="User">
        <id column="id" property="id"/>
        <result column="username" property="username"/>
        <result column="address" property="address"/>
        <result column="sex" property="sex"/>
        <result column="birthday" property="birthday"/>
    </resultMap>

    <select id="findOne" resultMap="userMap">
        select * from user where id = #{uid}
    </select>
</mapper>
```

### 一对多延迟加载

#### 概要

用户和账户是一对多查询。我们对“多”进行懒加载，要用时在查询。

对多的查询采用

- `proerty`是数据对应的`java`字段的名称
- `ofType`是查询出来集合中存储的数据类型
- `select`是要调用的查询方法，通过这个查询方法把懒数据查询出来
- `column`是查询的条件，即`where xx = column`的值。这个`column`取自`resultMap`。

```xml
<collection property="accounts" ofType="Account" select="com.bbxx.dao.lazy.IAccountDao.findById" column="id">
</collection>
```
#### 具体代码

```java
public interface IUserDao {
    // 懒加载 一对多查询 查询每个用户的所有账户信息
    List<User> findAll();
}

public interface IAccountDao {
	// 懒加载  对“多”的懒加载
    List<Account> findById(Integer id);
}
```

```xml
IUserDao的mapper文件
<mapper namespace="com.bbxx.dao.lazy.IUserDao">
    <resultMap id="userMap" type="User">
        <id column="id" property="id"/>
        <result column="username" property="username"/>
        <result column="address" property="address"/>
        <result column="sex" property="sex"/>
        <result column="birthday" property="birthday"/>
        <collection property="accounts" ofType="Account" select="com.bbxx.dao.lazy.IAccountDao.findById" column="id">

        </collection>
    </resultMap>

    <select id="findAll" resultMap="userMap">
        select * from user
    </select>
</mapper>

IAccountDao的mapper文件
<mapper namespace="com.bbxx.dao.lazy.IAccountDao">
<!--  一对一的查询  -->
    <resultMap id="accountMap" type="Account">
        <id column="id" property="id"/>
        <result column="uid" property="uid"/>
        <result column="money" property="money"/>
    </resultMap>

    <select id="findById" resultMap="accountMap">
        select * from account where uid = #{id}
    </select>
</mapper>
```

# `MyBatis`高级篇

## MyBatis生成Mapper

测试语句 `select * from users where id=4`

方法代码 `List<UserVO> findByCondition(UserVO vo);`

MapperRegister 类的 getMapper 方法

```java
public <T> T getMapper(Class<T> type, SqlSession sqlSession) {
    // 从hashmap中看是否有此类型的，有就可以创建，无就抛出异常。
    final MapperProxyFactory<T> mapperProxyFactory = (MapperProxyFactory<T>) knownMappers.get(type);
    if (mapperProxyFactory == null) {
        throw new BindingException("Type " + type + " is not known to the MapperRegistry.");
    }
    try {
        // 通过sqlSession创建代理对象
        return mapperProxyFactory.newInstance(sqlSession);
    } catch (Exception e) {
        throw new BindingException("Error getting mapper instance. Cause: " + e, e);
    }
}
```

继续看`mapperProxyFactory.newInstance(sqlSession);` 位于MapperProxyFactory类中

```java
public T newInstance(SqlSession sqlSession) {
    final MapperProxy<T> mapperProxy = new MapperProxy<T>(sqlSession, mapperInterface, methodCache);
    return newInstance(mapperProxy);
}
```

点进`new MapperProxy<T>(sqlSession, mapperInterface, methodCache)`一看

```java
public class MapperProxy<T> implements InvocationHandler, Serializable {

  private static final long serialVersionUID = -6424540398559729838L;
  private final SqlSession sqlSession;
  private final Class<T> mapperInterface;
  private final Map<Method, MapperMethod> methodCache;

  public MapperProxy(SqlSession sqlSession, Class<T> mapperInterface, Map<Method, MapperMethod> methodCache) {
    this.sqlSession = sqlSession;
    this.mapperInterface = mapperInterface;
    this.methodCache = methodCache;
  }
    // 当我们执行代理对象.method的时候会执行到这个方法
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        try {
            if (Object.class.equals(method.getDeclaringClass())) {
                return method.invoke(this, args);
            } else if (isDefaultMethod(method)) {
                return invokeDefaultMethod(proxy, method, args);
            }
        } catch (Throwable t) {
            throw ExceptionUtil.unwrapThrowable(t);
        }
        // 查看缓存有没有，没有就添加，再从缓存中拿数据。
        final MapperMethod mapperMethod = cachedMapperMethod(method);
        // 这里 执行的sql语句。
        return mapperMethod.execute(sqlSession, args);
    }
  // ...
}
```

点击mapperMethod.excute方法一看。(MapperMethod方法中的)

```java
public Object execute(SqlSession sqlSession, Object[] args) {
    Object result;
    switch (command.getType()) {
        case INSERT: {
            Object param = method.convertArgsToSqlCommandParam(args);
            result = rowCountResult(sqlSession.insert(command.getName(), param));
            break;
        }
        case UPDATE: {
            Object param = method.convertArgsToSqlCommandParam(args);
            result = rowCountResult(sqlSession.update(command.getName(), param));
            break;
        }
        case DELETE: {
            Object param = method.convertArgsToSqlCommandParam(args);
            result = rowCountResult(sqlSession.delete(command.getName(), param));
            break;
        }
        case SELECT:
            // 方法返回值，结果集处理器。结果可能是单条记录或多条记录。
            if (method.returnsVoid() && method.hasResultHandler()) {
                executeWithResultHandler(sqlSession, args);
                result = null;
            // 判断多条记录是 根据返回值来的？当前sql之能查询到一条数据，
            // returnsMany=True，应该是按返回值的类型来的。
            } else if (method.returnsMany()) {
                // 执行此方法
                result = executeForMany(sqlSession, args);
            } else if (method.returnsMap()) {
                result = executeForMap(sqlSession, args);
            } else if (method.returnsCursor()) {
                result = executeForCursor(sqlSession, args);
            } else {
                Object param = method.convertArgsToSqlCommandParam(args);
                result = sqlSession.selectOne(command.getName(), param);
            }
            break;
        case FLUSH:
            result = sqlSession.flushStatements();
            break;
        default:
            throw new BindingException("Unknown execution method for: " + command.getName());
    }
    if (result == null && method.getReturnType().isPrimitive() && !method.returnsVoid()) {
        throw new BindingException("Mapper method '" + command.getName() 
                                   + " attempted to return null from a method with a primitive return type (" + method.getReturnType() + ").");
    }
    return result;
}
```

看`executeForMany方法`

```java
private <E> Object executeForMany(SqlSession sqlSession, Object[] args) {
    List<E> result;
    Object param = method.convertArgsToSqlCommandParam(args);
    if (method.hasRowBounds()) {
        RowBounds rowBounds = method.extractRowBounds(args);
        result = sqlSession.<E>selectList(command.getName(), param, rowBounds);
    } else {
        result = sqlSession.<E>selectList(command.getName(), param);
    }
    // issue #510 Collections & arrays support
    if (!method.getReturnType().isAssignableFrom(result.getClass())) {
        if (method.getReturnType().isArray()) {
            return convertToArray(result);
        } else {
            return convertToDeclaredCollection(sqlSession.getConfiguration(), result);
        }
    }
    return result;
}
```

点进`selectList`方法

```java
public <E> List<E> selectList(String statement, Object parameter, RowBounds rowBounds) {
    try {
        // 这段看不懂，没事
        MappedStatement ms = configuration.getMappedStatement(statement);
        // 这个是关键
        return executor.query(ms, wrapCollection(parameter), rowBounds, Executor.NO_RESULT_HANDLER);
    } catch (Exception e) {
        throw ExceptionFactory.wrapException("Error querying database.  Cause: " + e, e);
    } finally {
        ErrorContext.instance().reset();
    }
}
```

点进`query`方法

```java
public <E> List<E> query(MappedStatement ms, Object parameterObject, RowBounds rowBounds, ResultHandler resultHandler) throws SQLException {
    // 获得解析后的SQL语句
    BoundSql boundSql = ms.getBoundSql(parameterObject);
    CacheKey key = createCacheKey(ms, parameterObject, rowBounds, boundSql);
    return query(ms, parameterObject, rowBounds, resultHandler, key, boundSql);
}
```