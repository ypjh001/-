#### [MyBatis-Plus](https://github.com/baomidou/mybatis-plus)说明

1. [MyBatis-Plus](https://github.com/baomidou/mybatis-plus)（简称 MP）是一个 [MyBatis](http://www.mybatis.org/mybatis-3/) 的增强工具，在 MyBatis 的基础上只做增强不做改变，为简化开发、提高效率而生。官网地址：https://mp.baomidou.com/
2. 本文中数据库用的是mysql5.7。完整代码地址在结尾！！

#### 第一步，在pom.xml加入依赖，如下

```
<!-- MySQL驱动 -->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <scope>runtime</scope>
</dependency>
<!-- mybatisPlus 核心库 -->
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-boot-starter</artifactId>
    <version>3.3.1</version>
</dependency>
<!-- dynamic-datasource-spring-boot-starter 是一个基于springboot的快速集成多数据源的启动器 -->
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>dynamic-datasource-spring-boot-starter</artifactId>
    <version>3.1.0</version>
</dependency>
<!-- mybatisPlus 代码生成器 -->
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-generator</artifactId>
    <version>3.3.1.tmp</version>
</dependency>
<!-- mybatisPlus Velocity 模版引擎 -->
<dependency>
    <groupId>org.apache.velocity</groupId>
    <artifactId>velocity-engine-core</artifactId>
    <version>2.2</version>
</dependency>
<!-- mybatisPlus Freemarker 模版引擎 -->
<dependency>
    <groupId>org.freemarker</groupId>
    <artifactId>freemarker</artifactId>
    <version>2.3.29</version>
</dependency>
```

##### 注：

1、MyBatis-Plus 支持 Velocity（默认）、Freemarker、Beetl，用户可以选择自己熟悉的模板引擎，如果都不满足您的要求，可以采用自定义模板引擎。详情请自行查看官方文档。
2、dynamic-datasource-spring-boot-starter是一个基于springboot的快速集成多数据源的启动器。

#### 第二步，在application.yml配置多数据源，mybatis-plus相关配置

```
spring:
  # 配置数据源
  datasource:
    dynamic:
      primary: db1 # 设置默认的数据源或者数据源组,默认值即为master
      datasource:
        db1:
          driver-class-name: com.mysql.cj.jdbc.Driver
          url: jdbc:mysql://xxx:3306/mybatisplus_demo?useUnicode=true&characterEncoding=utf-8
          username: xxx
          password: xxx
          # hikari
          hikari:
            connection-test-query: SELECT 1
            ## 最小空闲连接数量
            minimum-idle: 5
            ## 连接池最大连接数，默认是10
            maximum-pool-size: 15
            ## 连接池名称
            pool-name: MyHikariCPOfMaster
            ## 此属性控制池中连接的最长生命周期，值0表示无限生命周期，默认1800000即30分钟
            max-lifetime: 1800000
            ## 数据库连接超时时间,默认30秒，即30000
            connection-timeout: 20000
            ## 空闲连接存活最大时间，默认600000（10分钟）
            idle-timeout: 180000
            ## 此属性控制从池返回的连接的默认自动提交行为,默认值：true
            auto-commit: true
        db2:
          driver-class-name: com.mysql.cj.jdbc.Driver
          url: jdbc:mysql://xxx:3306/mybatisplus_demo?useUnicode=true&characterEncoding=utf-8
          username: xxx
          password: xxx
          # hikari
          hikari:
            connection-test-query: SELECT 1
            ## 最小空闲连接数量
            minimum-idle: 5
            ## 连接池最大连接数，默认是10
            maximum-pool-size: 15
            ## 连接池名称
            pool-name: MyHikariCPOfMaster
            ## 此属性控制池中连接的最长生命周期，值0表示无限生命周期，默认1800000即30分钟
            max-lifetime: 1800000
            ## 数据库连接超时时间,默认30秒，即30000
            connection-timeout: 20000
            ## 空闲连接存活最大时间，默认600000（10分钟）
            idle-timeout: 180000
            ## 此属性控制从池返回的连接的默认自动提交行为,默认值：true
            auto-commit: true

# mybatis-plus相关配置
mybatis-plus:
  # xml扫描，多个目录用逗号或者分号分隔（告诉 Mapper 所对应的 XML 文件位置）
  mapper-locations: classpath:com/luoyu/mybatisplus/mapper/xml/*.xml
  # 以下配置均有默认值,可以不设置
  global-config:
    db-config:
      #主键类型  auto:"数据库ID自增" 1:"用户输入ID",2:"全局唯一ID (数字类型唯一ID)", 3:"全局唯一ID UUID";
      id-type: auto
      #字段策略 IGNORED:"忽略判断"  NOT_NULL:"非 NULL 判断")  NOT_EMPTY:"非空判断"
      field-strategy: NOT_EMPTY
      #数据库类型
      db-type: MYSQL
  configuration:
    # 是否开启自动驼峰命名规则映射:从数据库列名到Java属性驼峰命名的类似映射
    map-underscore-to-camel-case: true
    # 如果查询结果中包含空值的列，则 MyBatis 在映射的时候，不会映射这个字段
    call-setters-on-nulls: true
    # 这个配置会将执行的sql打印出来，在开发或测试的时候可以用
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
```

#### 第三步，创建数据库db1，创建表User，插入测试数据，查看测试数据；创建数据库db2，创建表Task，插入测试数据，查看测试数据

##### User表

```
CREATE DATABASE mybatisplus_demo;

use mybatisplus_demo;

CREATE TABLE user
(
    id INT NOT NULL COMMENT '主键ID',
    name VARCHAR(50) NULL DEFAULT NULL COMMENT '姓名',
    age INT NULL DEFAULT NULL COMMENT '年龄',
    email VARCHAR(50) NULL DEFAULT NULL COMMENT '邮箱',
    PRIMARY KEY (id)
)CHARSET=utf8 ENGINE=InnoDB COMMENT '用户表';

INSERT INTO user (id, name, age, email) VALUES
(1, 'Jone', 18, 'test1@baomidou.com'),
(2, 'Jack', 20, 'test2@baomidou.com'),
(3, 'Tom', 28, 'test3@baomidou.com'),
(4, 'Sandy', 21, 'test4@baomidou.com'),
(5, 'Billie', 24, 'test5@baomidou.com');

select * from user;
```

##### Task表

```
CREATE DATABASE mybatisplus_demo;

use mybatisplus_demo;

CREATE TABLE task
(
    id INT NOT NULL COMMENT '主键ID',
    name VARCHAR(50) NULL DEFAULT NULL COMMENT '姓名',
    age INT NULL DEFAULT NULL COMMENT '年龄',
    email VARCHAR(50) NULL DEFAULT NULL COMMENT '邮箱',
    PRIMARY KEY (id)
)CHARSET=utf8 ENGINE=InnoDB COMMENT '任务表';

INSERT INTO task (id, name, age, email) VALUES
(6, '李白', 18, 'test1@baomidou.com'),
(7, '露娜', 20, 'test2@baomidou.com'),
(8, '韩信', 28, 'test3@baomidou.com'),
(9, '橘右京', 21, 'test4@baomidou.com'),
(10, '百里玄刺', 24, 'test5@baomidou.com');

select * from task;
```

#### 第四步，使用mybatis-plus的代码生成器对数据库的表生成相应的类，创建CodeGenerator类

```
import com.baomidou.mybatisplus.core.exceptions.MybatisPlusException;
import com.baomidou.mybatisplus.core.toolkit.StringPool;
import com.baomidou.mybatisplus.core.toolkit.StringUtils;
import com.baomidou.mybatisplus.generator.AutoGenerator;
import com.baomidou.mybatisplus.generator.InjectionConfig;
import com.baomidou.mybatisplus.generator.config.*;
import com.baomidou.mybatisplus.generator.config.po.TableInfo;
import com.baomidou.mybatisplus.generator.config.rules.NamingStrategy;
import com.baomidou.mybatisplus.generator.engine.FreemarkerTemplateEngine;

import java.util.ArrayList;
import java.util.List;
import java.util.Scanner;

/**
 * @Description: 代码生成器
 * @Author: jinhaoxun
 * @Date: 2020/2/13 上午10:06
 * @Version: 1.0.0
 */
// 演示例子，执行 main 方法控制台输入模块表名回车自动生成对应项目目录中
public class CodeGenerator {

    /**
     * <p>
     * 读取控制台内容
     * </p>
     */
    public static String scanner(String tip) {
        Scanner scanner = new Scanner(System.in);
        StringBuilder help = new StringBuilder();
        help.append("请输入" + tip + "：");
        System.out.println(help.toString());
        if (scanner.hasNext()) {
            String ipt = scanner.next();
            if (StringUtils.isNotEmpty(ipt)) {
                return ipt;
            }
        }
        throw new MybatisPlusException("请输入正确的" + tip + "！");
    }

    public static void main(String[] args) {
        // 代码生成器
        AutoGenerator mpg = new AutoGenerator();

        // 全局配置
        GlobalConfig gc = new GlobalConfig();
        String projectPath = System.getProperty("user.dir");
        gc.setOutputDir(projectPath + "/mybatisplus-demo/src/main/java");
        gc.setAuthor("luoyu");
        gc.setOpen(false);
        // gc.setSwagger2(true); 实体属性 Swagger2 注解
        mpg.setGlobalConfig(gc);

        // 数据源配置
        DataSourceConfig dsc = new DataSourceConfig();
        dsc.setUrl("jdbc:mysql://xxx:3306/mybatisplus_demo?useUnicode=true&useSSL=false&characterEncoding=utf8");
        // dsc.setSchemaName("public");
        dsc.setDriverName("com.mysql.cj.jdbc.Driver");
        dsc.setUsername("xxx");
        dsc.setPassword("xxx");
        mpg.setDataSource(dsc);

        // 包配置
        PackageConfig pc = new PackageConfig();
        pc.setModuleName(scanner("模块名"));
        pc.setParent("com.luoyu.mybatisplus");
        mpg.setPackageInfo(pc);

        // 自定义配置
        InjectionConfig cfg = new InjectionConfig() {
            @Override
            public void initMap() {
                // to do nothing
            }
        };

        // 如果模板引擎是 freemarker
        String templatePath = "/templates/mapper.xml.ftl";
        // 如果模板引擎是 velocity
        // String templatePath = "/templates/mapper.xml.vm";

        // 自定义输出配置
        List<FileOutConfig> focList = new ArrayList<>();
        // 自定义配置会被优先输出
        focList.add(new FileOutConfig(templatePath) {
            @Override
            public String outputFile(TableInfo tableInfo) {
                // 自定义输出文件名 ， 如果你 Entity 设置了前后缀、此处注意 xml 的名称会跟着发生变化！！
                return projectPath + "/src/main/resources/mapper/" + pc.getModuleName()
                        + "/" + tableInfo.getEntityName() + "Mapper" + StringPool.DOT_XML;
            }
        });
        /*
        cfg.setFileCreate(new IFileCreate() {
            @Override
            public boolean isCreate(ConfigBuilder configBuilder, FileType fileType, String filePath) {
                // 判断自定义文件夹是否需要创建
                checkDir("调用默认方法创建的目录");
                return false;
            }
        });
        */
        cfg.setFileOutConfigList(focList);
        mpg.setCfg(cfg);

        // 配置模板
        TemplateConfig templateConfig = new TemplateConfig();

        // 配置自定义输出模板
        //指定自定义模板路径，注意不要带上.ftl/.vm, 会根据使用的模板引擎自动识别
        // templateConfig.setEntity("templates/entity2.java");
        // templateConfig.setService();
        // templateConfig.setController();

        // templateConfig.setXml(null);
        mpg.setTemplate(templateConfig);

        // 策略配置
        StrategyConfig strategy = new StrategyConfig();
        strategy.setNaming(NamingStrategy.underline_to_camel);
        strategy.setColumnNaming(NamingStrategy.underline_to_camel);
        // strategy.setSuperEntityClass("你自己的父类实体,没有就不用设置!");
        strategy.setEntityLombokModel(true);
        strategy.setRestControllerStyle(true);
        // 公共父类
        // strategy.setSuperControllerClass("你自己的父类控制器,没有就不用设置!");
        // 写于父类中的公共字段
        // strategy.setSuperEntityColumns("id");
        strategy.setInclude(scanner("表名，多个英文逗号分割").split(","));
        strategy.setControllerMappingHyphenStyle(true);
        strategy.setTablePrefix(pc.getModuleName() + "_");
        mpg.setStrategy(strategy);
        mpg.setTemplateEngine(new FreemarkerTemplateEngine());
        mpg.execute();
    }

}
```

##### 需要修改的地方：

（1）数据源配置，改成你自己的数据库
（2）包配置，改成你自己的包名
（3）全局配置，修改生成文件存放位置的路径以及作者：gc.setOutputDir(projectPath + "/mybatisplus-demo/src/main/java")，gc.setAuthor("luoyu")

##### 使用方式：

（1）直接运行main函数，在控制台输入模块名（其实就是加在上面配置的生成文件存放位置的路径的后面，比如输入test，最终生成的所有文件就都放在/mybatisplus-demo/src/main/java/test下面），然后按回车键
（2）输入要生成代码的表名，多个英文逗号分割，按回车键即可
（3）会自动生成包括：controller，service，mapper，entity等包，把生成的文件移动到自己项目里想放的位置就好了

#### 第五步，创建MybatisPlus配置类，MybatisPlusConfig，主要是配置一些插件的使用

```
import com.baomidou.mybatisplus.extension.plugins.PaginationInterceptor;
import com.baomidou.mybatisplus.extension.plugins.pagination.optimize.JsqlParserCountOptimize;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.transaction.annotation.EnableTransactionManagement;

/**
 * @Description: MybatisPlus配置类
 * @Author: jinhaoxun
 * @Date: 2020/2/13 上午11:34
 * @Version: 1.0.0
 */
@EnableTransactionManagement
@Configuration
public class MybatisPlusConfig {
    /**
     * mybatis-plus SQL执行效率插件【生产环境可以关闭】，设置 dev test 环境开启
     */
//    @Bean
//    @Profile({"dev", "qa"})
//    public PerformanceInterceptor performanceInterceptor() {
//        PerformanceInterceptor performanceInterceptor = new PerformanceInterceptor();
//        performanceInterceptor.setMaxTime(1000);
//        performanceInterceptor.setFormat(true);
//        return performanceInterceptor;
//    }

    /**
     * 分页插件
     */
    @Bean
    public PaginationInterceptor paginationInterceptor() {
        PaginationInterceptor paginationInterceptor = new PaginationInterceptor();
        // 设置请求的页面大于最大页后操作， true调回到首页，false 继续请求  默认false
         paginationInterceptor.setOverflow(false);
        // 设置最大单页限制数量，默认 500 条，-1 不受限制
         paginationInterceptor.setLimit(500);
        // 开启 count 的 join 优化,只针对部分 left join
        paginationInterceptor.setCountSqlParser(new JsqlParserCountOptimize(true));
        return paginationInterceptor;
    }
}
```

#### 第六步，踩坑！！！如果xml文件没有放在resources目录下的话，需要在pom.xml文件的标签里面新增以下配置，否则就算在application.yml文件配置了扫描xml文件的路径也没用，小坑！！！

```
<resources>
    <resource>
        <directory>src/main/java</directory>
        <!-- src/main/java下的指定资源放行 -->
        <includes>
            <include>**/*.xml</include>
        </includes>
    </resource>
</resources>
```

#### 第七步，在启动类MybatisplusApplication新增@MapperScan注解，里面写入生成的文件中的mapper存放的路径，用于扫描mapper文件。到此springboot2.2.5 整合MyBatis-Plus3.3.1 完毕，直接在mapper文件写sql语句，在service文件调用即可。

```
import org.mybatis.spring.annotation.MapperScan;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@MapperScan("com.luoyu.mybatisplus.mapper")
@SpringBootApplication
public class MybatisplusApplication {

    public static void main(String[] args) {
        SpringApplication.run(MybatisplusApplication.class, args);
    }

}
```

#### 第八步，新增自定义的sql方法，编写IUserService，UserServiceImpl，UserMapper，UserMapper.xml用于操作数据源db1；ITaskService，TaskServiceImpl，TaskMapper，TaskMapper.xml用于操作数据源db2，分别如下

##### 注：

在UserServiceImpl，TaskServiceImpl中，@DS()注解代表指定各自的数据源，@Transactional注解代表开启Spring事务。

##### 数据源db1，IUserService

```
import com.baomidou.mybatisplus.extension.service.IService;
import com.luoyu.mybatisplus.entity.User;

/**
 * <p>
 *  服务类
 * </p>
 *
 * @author jinhaoxun
 * @since 2020-02-13
 */
public interface IUserService extends IService<User> {

    /**
     * @Author: jinhaoxun
     * @Description:
     * @param name 姓名
     * @Date: 2020/2/13 下午12:06
     * @Return: com.luoyu.mybatisplus.entity.User
     * @Throws:
     */
    User selectByName(String name);

    /**
     * @Author: jinhaoxun
     * @Description:
     * @param id id
     * @param name 姓名
     * @Date: 2020/2/13 下午12:06
     * @Return: int
     * @Throws:
     */
    int updateName(int id, String name);

}
```

##### UserServiceImpl

```
import com.baomidou.dynamic.datasource.annotation.DS;
import com.baomidou.mybatisplus.extension.service.impl.ServiceImpl;
import com.luoyu.mybatisplus.entity.User;
import com.luoyu.mybatisplus.mapper.UserMapper;
import com.luoyu.mybatisplus.service.IUserService;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

import javax.annotation.Resource;

/**
 * <p>
 *  服务实现类
 * </p>
 *
 * @author jinhaoxun
 * @since 2020-02-13
 */
@DS("db1")
@Service
public class UserServiceImpl extends ServiceImpl<UserMapper, User> implements IUserService {

    @Resource
    private UserMapper userMapper;

    /**
     * @Author: jinhaoxun
     * @Description:
     * @param name 姓名
     * @Date: 2020/2/13 下午12:06
     * @Return: com.luoyu.mybatisplus.entity.User
     * @Throws:
     */
    @Override
    public User selectByName(String name) {
        return userMapper.selectByName(name);
    }

    /**
     * @Author: jinhaoxun
     * @Description:
     * @param id id
     * @param name 姓名
     * @Date: 2020/2/13 下午12:06
     * @Return: int
     * @Throws:
     */
    @Transactional
    @Override
    public int updateName(int id, String name) {
        int n = userMapper.updateName(id, name);
        // 此处报错事务回滚
        int i = 1/0;
        return n;
    }
}
```

##### UserMapper

```
import com.baomidou.mybatisplus.core.mapper.BaseMapper;
import com.luoyu.mybatisplus.entity.User;
import org.apache.ibatis.annotations.Param;

/**
 * <p>
 *  Mapper 接口
 * </p>
 *
 * @author jinhaoxun
 * @since 2020-02-13
 */
public interface UserMapper extends BaseMapper<User> {

    /**
     * @Author: jinhaoxun
     * @Description:
     * @param name 姓名
     * @Date: 2020/2/13 下午12:06
     * @Return: com.luoyu.mybatisplus.entity.User
     * @Throws:
     */
    User selectByName(String name);

    /**
     * @Author: jinhaoxun
     * @Description:
     * @param id id
     * @param name 姓名
     * @Date: 2020/2/13 下午12:06
     * @Return: int
     * @Throws:
     */
    int updateName(@Param("id") int id, @Param("name") String name);

}
```

##### UserMapper.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.luoyu.mybatisplus.mapper.UserMapper">

    <select id="selectByName" parameterType="string" resultType="com.luoyu.mybatisplus.entity.User">
        select * from user where name = #{name};
    </select>

    <update id="updateName">
        update user set name = #{name} where id = #{id};
    </update>

</mapper>
```

##### 数据源db2，ITaskService

```
import com.baomidou.mybatisplus.extension.service.IService;
import com.luoyu.mybatisplus.entity.Task;
import com.luoyu.mybatisplus.entity.User;

/**
 * <p>
 *  服务类
 * </p>
 *
 * @author jinhaoxun
 * @since 2020-02-13
 */
public interface ITaskService extends IService<Task> {

    /**
     * @Author: jinhaoxun
     * @Description:
     * @param name 姓名
     * @Date: 2020/2/13 下午12:06
     * @Return: com.luoyu.mybatisplus.entity.User
     * @Throws:
     */
    Task selectByName(String name);

    /**
     * @Author: jinhaoxun
     * @Description:
     * @param id id
     * @param name 姓名
     * @Date: 2020/2/13 下午12:06
     * @Return: int
     * @Throws:
     */
    int updateName(int id, String name);

}
```

##### TaskServiceImpl

```
import com.baomidou.dynamic.datasource.annotation.DS;
import com.baomidou.mybatisplus.extension.service.impl.ServiceImpl;
import com.luoyu.mybatisplus.entity.Task;
import com.luoyu.mybatisplus.entity.User;
import com.luoyu.mybatisplus.mapper.TaskMapper;
import com.luoyu.mybatisplus.mapper.UserMapper;
import com.luoyu.mybatisplus.service.ITaskService;
import com.luoyu.mybatisplus.service.IUserService;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

import javax.annotation.Resource;

/**
 * <p>
 *  服务实现类
 * </p>
 *
 * @author jinhaoxun
 * @since 2020-02-13
 */
@DS("db2")
@Service
public class TaskServiceImpl extends ServiceImpl<TaskMapper, Task> implements ITaskService {

    @Resource
    private TaskMapper taskMapper;

    /**
     * @Author: jinhaoxun
     * @Description:
     * @param name 姓名
     * @Date: 2020/2/13 下午12:06
     * @Return: com.luoyu.mybatisplus.entity.Task
     * @Throws:
     */
    @Override
    public Task selectByName(String name) {
        return taskMapper.selectByName(name);
    }

    /**
     * @Author: jinhaoxun
     * @Description:
     * @param id id
     * @param name 姓名
     * @Date: 2020/2/13 下午12:06
     * @Return: int
     * @Throws:
     */
    @Transactional
    @Override
    public int updateName(int id, String name) {
        return taskMapper.updateName(id, name);
    }
}
```

##### TaskMapper

```
import com.baomidou.mybatisplus.core.mapper.BaseMapper;
import com.luoyu.mybatisplus.entity.Task;
import org.apache.ibatis.annotations.Param;

/**
 * <p>
 *  Mapper 接口
 * </p>
 *
 * @author jinhaoxun
 * @since 2020-02-13
 */
public interface TaskMapper extends BaseMapper<Task> {

    /**
     * @Author: jinhaoxun
     * @Description:
     * @param name 姓名
     * @Date: 2020/2/13 下午12:06
     * @Return: com.luoyu.mybatisplus.entity.Task
     * @Throws:
     */
    Task selectByName(String name);

    /**
     * @Author: jinhaoxun
     * @Description:
     * @param id id
     * @param name 姓名
     * @Date: 2020/2/13 下午12:06
     * @Return: int
     * @Throws:
     */
    int updateName(@Param("id") int id, @Param("name") String name);

}
```

##### TaskMapper.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.luoyu.mybatisplus.mapper.TaskMapper">

    <select id="selectByName" parameterType="string" resultType="com.luoyu.mybatisplus.entity.Task">
        select * from task where name = #{name};
    </select>

    <update id="updateName">
        update task set name = #{name} where id = #{id};
    </update>
    
</mapper>
```

#### 第九步，编写单元测试类，MybatisplusApplicationTests，并进行测试，只列举了部分方法

```
import com.luoyu.mybatisplus.entity.Task;
import com.luoyu.mybatisplus.entity.User;
import com.luoyu.mybatisplus.service.ITaskService;
import com.luoyu.mybatisplus.service.IUserService;
import lombok.extern.slf4j.Slf4j;
import org.junit.jupiter.api.AfterEach;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

@Slf4j
// 获取启动类，加载配置，确定装载 Spring 程序的装载方法，它回去寻找 主配置启动类（被 @SpringBootApplication 注解的）
@SpringBootTest
class MybatisplusApplicationTests {

    @Autowired
    private IUserService iUserService;

    @Autowired
    private ITaskService iTaskService;

    @Test
    void selectApiTest() {
        log.info("使用MP提供的CRUD");
        User user = iUserService.getById("1");
        log.info(user.toString());
    }

    @Test
    void selectDb1Test() {
        // 验证多数据源db1
        log.info("使用自定义的方法，查看db1");
        User user = iUserService.selectByName("Jack");
        log.info(user.toString());
    }

    @Test
    void selectDb2Test() {
        // 验证多数据源db2
        log.info("使用自定义的方法，查看db2");
        Task task = iTaskService.selectByName("韩信");
        log.info(task.toString());
    }

    @Test
    void updateDb1Test() {
        // 验证事务
        iUserService.updateName(1, "改名啦");
    }

    @BeforeEach
    void testBefore(){
        log.info("测试开始!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!");
    }

    @AfterEach
    void testAfter(){
        log.info("测试结束!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!");
    }

}
```

##### 完整代码地址：https://github.com/Jinhx128/springboot-demo

##### 注：此工程包含多个module，本文所用代码均在mybatisplus-demo模块下