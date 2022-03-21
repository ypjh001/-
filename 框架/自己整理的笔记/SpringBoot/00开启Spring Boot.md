## 开启Spring Boot

 2017-08-10 |  Visit count 644896

Spring Boot是在Spring框架上创建的一个全新的框架，其设计目的是简化Spring应用的搭建和开发过程。开启Spring Boot有许多种方法可供选择，这里仅介绍使用http://start.spring.io/来构建一个简单的Spring Boot项目。

## 生成项目文件

访问http://start.spring.io/，页面显示如下：

![mrbird_photo_20171129094017.png](https://mrbird.cc/img/mrbird_photo_20171129094017.png)

这里选择以Maven构建，语言选择Java，Spring Boot版本为1.5.9。然后点击Switch to the full version，可看到更多的配置以及依赖选择：

![mrbird_photo_20171129094312.png](https://mrbird.cc/img/mrbird_photo_20171129094312.png)

在项目信息里选择以jar包的方式部署，Java版本为7。在页面的下方还可以选择诸多的依赖，这里仅选择web进行演示：

![mrbird_photo_20171129094913.png](https://mrbird.cc/img/mrbird_photo_20171129094913.png)

最后点击页面的generate project按钮生成项目文件。文件下载后是一个压缩包，进行解压然后使用eclipse以Maven项目的形式导入。导入后eclipse会自动编译项目并下载相应的依赖，项目目录如下所示：

![mrbirdphoto20171129100037.png](https://mrbird.cc/img/mrbirdphoto20171129100037.png)

## 简单演示

项目根目录下生成了一个artifactId+Application命名规则的入口类，为了演示简单，不再新建控制器，直接在入口类中编写代码：

```java
package com.springboot.demo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@SpringBootApplication
public class DemoApplication {

    @RequestMapping("/")
    String index() {
        return "hello spring boot";
    }
    
    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }
}
```



然后右键点击DemoAppliction，选择run as → Java Application：

![mrbirdphoto20171129101138.png](https://mrbird.cc/img/mrbirdphoto20171129101138.png)

访问[http://localhost:8080](http://localhost:8080/)，页面显示如下：

![mrbirdphoto20171129101417.png](https://mrbird.cc/img/mrbirdphoto20171129101417.png)

## 打包发布

在eclipse中右击项目，选择run as → Maven build…，如下图所示：

![mrbird20171129145655.png](https://mrbird.cc/img/mrbird20171129145655.png)

在Goals中输入`clean package`命令，然后点击下方的run就将项目打包成jar包（初次打包会自动下载一些依赖）。打包完毕后可看到项目目录target文件夹下生成了一个jar文件：

![mrbird20171129150126.png](https://mrbird.cc/img/mrbird20171129150126.png)

生成jar包后，cd到target目录下，执行以下命令：

![mrbird20171129151008.png](https://mrbird.cc/img/mrbird20171129151008.png)

访问[http://localhost:8080](http://localhost:8080/)，效果如上。

## 聊聊pom.xml

打开pom.xml可看到配置如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    
    <groupId>com.springboot</groupId>
    <artifactId>demo</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <packaging>jar</packaging>
    
    <name>demo</name>
    <description>Demo project for Spring Boot</description>
    
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>1.5.9.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        <java.version>1.7</java.version>
    </properties>
    
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
    
</project>
```



### spring-boot-starter-parent

spring-boot-starter-parent指定了当前项目为一个Spring Boot项目，它提供了诸多的默认Maven依赖，具体可查看目录[D:\m2\repository\org\springframework\boot\spring-boot-dependencies\1.5.9.RELEASE](file:///D:/m2/repository/org/springframework/boot/spring-boot-dependencies/1.5.9.RELEASE)下的spring-boot-dependencies-1.5.9.RELEASE.pom文件，这里仅截取一小部分：

```xml
<properties>
...	
    <spring-security.version>4.2.3.RELEASE</spring-security.version>
    <spring-security-jwt.version>1.0.8.RELEASE</spring-security-jwt.version>
    <spring-security-oauth.version>2.0.14.RELEASE</spring-security-oauth.version>
    <spring-session.version>1.3.1.RELEASE</spring-session.version>
    <spring-social.version>1.1.4.RELEASE</spring-social.version>
    <spring-social-facebook.version>2.0.3.RELEASE</spring-social-facebook.version>
    <spring-social-linkedin.version>1.0.2.RELEASE</spring-social-linkedin.version>
    <spring-social-twitter.version>1.1.2.RELEASE</spring-social-twitter.version>
    <spring-ws.version>2.4.2.RELEASE</spring-ws.version>
    <sqlite-jdbc.version>3.15.1</sqlite-jdbc.version>
    <statsd-client.version>3.1.0</statsd-client.version>
    <sun-mail.version>${javax-mail.version}</sun-mail.version>
    <thymeleaf.version>2.1.6.RELEASE</thymeleaf.version>
    <thymeleaf-extras-springsecurity4.version>2.1.3.RELEASE</thymeleaf-extras-springsecurity4.version>
    <thymeleaf-extras-conditionalcomments.version>2.1.2.RELEASE</thymeleaf-extras-conditionalcomments.version>
    <thymeleaf-layout-dialect.version>1.4.0</thymeleaf-layout-dialect.version>
    <thymeleaf-extras-data-attribute.version>1.3</thymeleaf-extras-data-attribute.version>
    <thymeleaf-extras-java8time.version>2.1.0.RELEASE</thymeleaf-extras-java8time.version>
    <tomcat.version>8.5.23</tomcat.version>
...    
</properties>
```



需要说明的是，并非所有在``标签中配置了版本号的依赖都有被启用，其启用与否取决于您是否配置了相应的starter。比如tomcat这个依赖就是spring-boot-starter-web的传递性依赖（下面将会描述到）。

当然，我们可以手动改变这些依赖的版本。比如我想把thymeleaf的版本改为3.0.0.RELEASE，我们可以在pom.xml中进行如下配置：

```xml
<properties>
   <thymeleaf.version>3.0.0.RELEASE</thymeleaf.version>
</properties>
```



### spring-boot-starter-web

Spring Boot提供了许多开箱即用的依赖模块，这些模块都是以spring-boot-starter-XX命名的。比如要开启Spring Boot的web功能，只需要在pom.xml中配置spring-boot-starter-web即可：

```xml
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```



因为其依赖于spring-boot-starter-parent，所以这里可以不用配置version。保存后Maven会自动帮我们下载spring-boot-starter-web模块所包含的jar文件。如果需要具体查看spring-boot-starter-web包含了哪些依赖，我们可以右键项目选择run as → Maven Build…，在Goals中输入命令`dependency:tree`，然后点击run即可在eclipse控制台查看到如下信息：

```
[INFO] +- org.springframework.boot:spring-boot-starter-web:jar:1.5.9.RELEASE:compile
[INFO] |  +- org.springframework.boot:spring-boot-starter:jar:1.5.9.RELEASE:compile
[INFO] |  |  +- org.springframework.boot:spring-boot:jar:1.5.9.RELEASE:compile
[INFO] |  |  +- org.springframework.boot:spring-boot-autoconfigure:jar:1.5.9.RELEASE:compile
[INFO] |  |  +- org.springframework.boot:spring-boot-starter-logging:jar:1.5.9.RELEASE:compile
[INFO] |  |  |  +- ch.qos.logback:logback-classic:jar:1.1.11:compile
[INFO] |  |  |  |  \- ch.qos.logback:logback-core:jar:1.1.11:compile
[INFO] |  |  |  +- org.slf4j:jcl-over-slf4j:jar:1.7.25:compile
[INFO] |  |  |  +- org.slf4j:jul-to-slf4j:jar:1.7.25:compile
[INFO] |  |  |  \- org.slf4j:log4j-over-slf4j:jar:1.7.25:compile
[INFO] |  |  \- org.yaml:snakeyaml:jar:1.17:runtime
[INFO] |  +- org.springframework.boot:spring-boot-starter-tomcat:jar:1.5.9.RELEASE:compile
[INFO] |  |  +- org.apache.tomcat.embed:tomcat-embed-core:jar:8.5.23:compile
[INFO] |  |  |  \- org.apache.tomcat:tomcat-annotations-api:jar:8.5.23:compile
[INFO] |  |  +- org.apache.tomcat.embed:tomcat-embed-el:jar:8.5.23:compile
[INFO] |  |  \- org.apache.tomcat.embed:tomcat-embed-websocket:jar:8.5.23:compile
[INFO] |  +- org.hibernate:hibernate-validator:jar:5.3.6.Final:compile
[INFO] |  |  +- javax.validation:validation-api:jar:1.1.0.Final:compile
[INFO] |  |  +- org.jboss.logging:jboss-logging:jar:3.3.1.Final:compile
[INFO] |  |  \- com.fasterxml:classmate:jar:1.3.4:compile
[INFO] |  +- com.fasterxml.jackson.core:jackson-databind:jar:2.8.10:compile
[INFO] |  |  +- com.fasterxml.jackson.core:jackson-annotations:jar:2.8.0:compile
[INFO] |  |  \- com.fasterxml.jackson.core:jackson-core:jar:2.8.10:compile
[INFO] |  +- org.springframework:spring-web:jar:4.3.13.RELEASE:compile
[INFO] |  |  +- org.springframework:spring-aop:jar:4.3.13.RELEASE:compile
[INFO] |  |  +- org.springframework:spring-beans:jar:4.3.13.RELEASE:compile
[INFO] |  |  \- org.springframework:spring-context:jar:4.3.13.RELEASE:compile
[INFO] |  \- org.springframework:spring-webmvc:jar:4.3.13.RELEASE:compile
[INFO] |     \- org.springframework:spring-expression:jar:4.3.13.RELEASE:compile
```



上述这些依赖都是隐式依赖于spring-boot-starter-web，我们也可以手动排除一些我们不需要的依赖。

比如spring-boot-starter-web默认集成了tomcat，假如我们想把它换为jetty，可以在pom.xml中spring-boot-starter-web下排除tomcat依赖，然后手动引入jetty依赖：

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
        <exclusions>
            <exclusion>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-tomcat</artifactId>
            </exclusion>
        </exclusions>
    </dependency>
    
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-jetty</artifactId>
    </dependency>
</dependencies>
```



tips：依赖的坐标可以到上述的spring-boot-dependencies-1.5.9.RELEASE.pom文件里查找。再次运行`dependency:tree`：

```
[INFO] +- org.springframework.boot:spring-boot-starter-web:jar:1.5.9.RELEASE:compile
...
[INFO] +- org.springframework.boot:spring-boot-starter-jetty:jar:1.5.9.RELEASE:compile
[INFO] |  +- org.eclipse.jetty:jetty-servlets:jar:9.4.7.v20170914:compile
[INFO] |  |  +- org.eclipse.jetty:jetty-continuation:jar:9.4.7.v20170914:compile
[INFO] |  |  +- org.eclipse.jetty:jetty-http:jar:9.4.7.v20170914:compile
[INFO] |  |  +- org.eclipse.jetty:jetty-util:jar:9.4.7.v20170914:compile
[INFO] |  |  \- org.eclipse.jetty:jetty-io:jar:9.4.7.v20170914:compile
[INFO] |  +- org.eclipse.jetty:jetty-webapp:jar:9.4.7.v20170914:compile
[INFO] |  |  +- org.eclipse.jetty:jetty-xml:jar:9.4.7.v20170914:compile
[INFO] |  |  \- org.eclipse.jetty:jetty-servlet:jar:9.4.7.v20170914:compile
[INFO] |  |     \- org.eclipse.jetty:jetty-security:jar:9.4.7.v20170914:compile
[INFO] |  |        \- org.eclipse.jetty:jetty-server:jar:9.4.7.v20170914:compile
[INFO] |  +- org.eclipse.jetty.websocket:websocket-server:jar:9.4.7.v20170914:compile
[INFO] |  |  +- org.eclipse.jetty.websocket:websocket-common:jar:9.4.7.v20170914:compile
[INFO] |  |  |  \- org.eclipse.jetty.websocket:websocket-api:jar:9.4.7.v20170914:compile
[INFO] |  |  +- org.eclipse.jetty.websocket:websocket-client:jar:9.4.7.v20170914:compile
[INFO] |  |  |  \- org.eclipse.jetty:jetty-client:jar:9.4.7.v20170914:compile
[INFO] |  |  \- org.eclipse.jetty.websocket:websocket-servlet:jar:9.4.7.v20170914:compile
[INFO] |  |     \- javax.servlet:javax.servlet-api:jar:3.1.0:compile
[INFO] |  +- org.eclipse.jetty.websocket:javax-websocket-server-impl:jar:9.4.7.v20170914:compile
[INFO] |  |  +- org.eclipse.jetty:jetty-annotations:jar:9.4.7.v20170914:compile
[INFO] |  |  |  +- org.eclipse.jetty:jetty-plus:jar:9.4.7.v20170914:compile
[INFO] |  |  |  +- javax.annotation:javax.annotation-api:jar:1.2:compile
[INFO] |  |  |  +- org.ow2.asm:asm:jar:5.1:compile
[INFO] |  |  |  \- org.ow2.asm:asm-commons:jar:5.1:compile
[INFO] |  |  |     \- org.ow2.asm:asm-tree:jar:5.1:compile
[INFO] |  |  +- org.eclipse.jetty.websocket:javax-websocket-client-impl:jar:9.4.7.v20170914:compile
[INFO] |  |  \- javax.websocket:javax.websocket-api:jar:1.0:compile
[INFO] |  \- org.mortbay.jasper:apache-el:jar:8.0.33:compile
```



可看到tomcat已被替换为了jetty。

### spring-boot-maven-plugin

spring-boot-maven-plugin为Spring Boot Maven插件，提供了：

1. 把项目打包成一个可执行的超级JAR（uber-JAR）,包括把应用程序的所有依赖打入JAR文件内，并为JAR添加一个描述文件，其中的内容能让你用`java -jar`来运行应用程序。
2. 搜索`public static void main()`方法来标记为可运行类。