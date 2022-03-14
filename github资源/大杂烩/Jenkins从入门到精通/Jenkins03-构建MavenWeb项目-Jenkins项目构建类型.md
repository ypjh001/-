# Jenkins项目构建类型

配合Github项目食用[jenkins-web-demo]( https://github.com/PAcee1/jenkins-web-demo )

## Jenkins构建的项目类型介绍

Jenkins中自动构建项目的类型有很多，常用的有以下三种：

- 自用风格项目（Freestyle Project）：项目是由多种方式构建的，是默认自带的构建类型
- Maven项目（Maven Project）：项目由Maven构建
- 流水线项目（Pipeline Project）：灵活性很高，可调整，是由编码形式来编写构建过程

每种类型的构建都是对于一个项目来说，可以完成一模一样的构建结构，只是在操作，灵活性上各有不同，开发中可以根据需求和习惯选择。

一般来说流水线项目是最常使用的，因为灵活性非常高

## Freestyle项目构建

下面演示创建一个自由风格项目来完成项目集成过程：

> 拉取代码-》编译打包-》部署

1）拉取代码

首先创建一个`web-demo-freestyle`项目，并关联我们之前的Gitlab

![1584676373215](../image/1584676373215.png)

然后让他构建一次，把代码拉取下来

![1584676424806](../image/1584676424806.png)

2）编译打包

项目-》配置-》构建-》添加构建步骤-》Executor Shell

![1584676508670](../image/1584676508670.png)

再次Build，使他编译打包

![1584676542197](../image/1584676542197.png)

3）部署

将war包部署到远程Tomcat中

项目-》配置-》构建后操作-》添加构建后操作步骤-》Deploy war/ear to a container

![1584676727657](../image/1584676727657.png)

4）测试

然后我们进入Tomcat Manager

![1584676782848](../image/1584676782848.png)

可以看到我们刚刚构建的项目，然后点击项目名称

![1584676802407](../image/1584676802407.png)

进入到index页面，说明一套流程完美实现自动化部署

## Maven项目构建

Maven项目构建不是Jenkins自带的，我们需要安装相应插件

1）安装Maven Integration插件

![1584677257261](../image/1584677257261.png)

2）创建项目持续集成

2.1）新建一个任务

![1584677300989](../image/1584677300989.png)

这里我们选择构建Maven项目

2.2）配置项目

这里和自由风格项目最大的不同是构建那里，在Maven项目中，构建改成了Build，会根据你pom文件进行打包部署，而不是使用之前的Shell脚本编写了

![1584677392006](../image/1584677392006.png)

其他地方的配置都是一样的，Git，构建后操作

3）修改代码执行自动构建

我们修改项目中的代码，并提交，这样与之前的区分开来

修改提交后进行Build

![1584677617373](../image/1584677617373.png)

成功实现自动化部署持续集成

## Pipeline项目构建

### Pipeline介绍

1）概念

Pipeline简单来说，就是运行在Jenkins上的工作流创建，将原来独立运行与单个或多个节点的任务连接起来，实现单个任务难以完成的复杂流程编排和可视化工作。

大白话说，我们只有不管使用Freestyle还是Maven，都需要先在配置中添加源码管理，添加构建，添加构建后操作，并且这些模块都是相互独立配置的，而使用Pipeline就将这些操作统一起来，统一管理，使用编码的形式串联起来。

2）使用Pipeline好处（官方文档）

- 代码：Pipeline以代码形式实现，通常被检入源代码控制，是团队能编辑审查迭代
- 持久：无论是计划内的还是计划外的服务器重启，Pipeline都可以恢复
- 可停止：Pipeline可接收交互式输入，以确定是否继续执行Pipeline
- 多功能：Pipeline支持现实世界中复杂的持续交付要求，支持fork/join，循环执行，并行执行等
- 可扩展：Pipeline插件支持DSL自定义扩展，以及与其他插件集成

其实最主要的就是可以使用编码的形式控制整个流程，管理起来也很方便

3）如何创建Jenkins Pipeline呢

- Pipeline脚本由Groovy语言实现，但是没必要单独去学
- Pipeline支持两种语法：**Declaration**声明式和**Scripted Pipeline**脚本式，我们常用还是声明式
- Pipeline有两种创建方式：
  - 在Jenkins的Web界面输入脚本
  - 将脚本放到项目的源码库中（推荐）

### Pipeline插件安装

![1584678666620](../image/1584678666620.png)

安装完毕后，新建项目可以选择流水线类型

![1584678737145](../image/1584678737145.png)

然后我们可以看到之前那些拉取代码，构建以及构建后操作都没有了

![1584678807998](../image/1584678807998.png)

我们只需在流水线里写脚本便可统一管理

### Pipeline语法快速入门

1）声明式语法

```groovy
pipeline {
   agent any

   stages {
      stage('pull code') {
         steps {
            echo 'pull code'
         }
      }
      stage('build project') {
         steps {
            echo 'build project'
         }
      }
      stage('publish project') {
         steps {
            echo 'publish project'
         }
      }
   }
}
```

分为`stages`部分和`stages`前面的部分

- `stages`前面：可以写一些代理，比如docker的代理，也可以写一些pipeline工具
- `stages`：里面就是存放之前自用风格所有阶段，写在这个里面，他分为多个`stage`（阶段）
  - `stage`：就是之前各个阶段，比如拉取代码，构建，发布

Build后可以在项目中看到阶段视图

![1584681556658](../image/1584681556658.png)

2）脚本式语法

```groovy
node {
   def mvnHome
   stage('pull code') {
      echo 'pull code'
   }
   stage('build code') {
      echo 'build code'
   }
   stage('publish code') {
      echo 'publish code'
   }
}
```

脚本式语法简单了解下即可，也是分为阶段，但是没有一个大阶段包裹了

3）如何编写Pipeline语法

我们不需要特别去学习Groovy代码，因为Jenkins为我们提供了代码生成器，我们只需要配置一些东西，就像之前自由风格阶段一样，就可以生成代码

![1584682437961](../image/1584682437961.png)

### Pipeline拉取代码

1）使用片段生成器生成拉取代码

![1584682512195](../image/1584682512195.png)

2）完善声明式流水线

```groovy
pipeline {
   agent any

   stages {
      stage('pull code') {
         steps {
            echo 'pull code'
            checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'e536df57-70be-4cab-ae06-448a0c1db793', url: 'git@192.168.56.130:pace-group/web-demo.git']]])
         }
      }
      stage('build project') {
         steps {
            echo 'build project'
         }
      }
      stage('publish project') {
         steps {
            echo 'publish project'
         }
      }
   }
}
```

然后保存后Build构建，查看控制台日志是否成功，查看服务器是否拉取下来代码

![1584682719397](../image/1584682719397.png)



### Pipeline编译打包

1）使用片段生成器生成代码

![1584682821235](../image/1584682821235.png)

2）完善Pipeline代码

```groovy
pipeline {
   agent any

   stages {
      stage('pull code') {
         steps {
            echo 'pull code'
            checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'e536df57-70be-4cab-ae06-448a0c1db793', url: 'git@192.168.56.130:pace-group/web-demo.git']]])
         }
      }
      stage('build project') {
         steps {
            echo 'build project'
            sh label: '', script: 'mvn clean package'
         }
      }
      stage('publish project') {
         steps {
            echo 'publish project'
         }
      }
   }
}
```

Build后查看服务器

![1584682886800](../image/1584682886800.png)

### Pipeline部署

1）使用片段生成器生成代码

![1584682935634](../image/1584682935634.png)

2）完善Pipeline代码

```groovy
pipeline {
   agent any

   stages {
      stage('pull code') {
         steps {
            echo 'pull code'
            checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'e536df57-70be-4cab-ae06-448a0c1db793', url: 'git@192.168.56.130:pace-group/web-demo.git']]])
         }
      }
      stage('build project') {
         steps {
            echo 'build project'
            sh label: '', script: 'mvn clean package'
         }
      }
      stage('publish project') {
         steps {
            echo 'publish project'
            deploy adapters: [tomcat8(credentialsId: '52d40548-72e5-48d5-8e22-aef835e4001c', path: '', url: 'http://192.168.56.132:8080/')], contextPath: null, war: 'target/*.war'
         }
      }
   }
}
```

3）修改代码提交后测试部署

和之前一样，我们先修改项目代码，以供更好的演示，并commit

然后Build自动化部署

![1584683147407](../image/1584683147407.png)

成功部署

### Pipeline保存到项目中

刚刚我们都是在Jenkins的UIWeb界面编写的Pipeline代码，这样不方便维护，而且如果Jenkins挂了，脚本也就找不到了，所以我们一般都是将Pipeline代码放在项目中，和项目一起维护

1）项目根目录创建Jenkinsfile文件，把内容复制进去

![1584685026021](../image/1584685026021.png)

2）在Jenkins中配置从项目中读取脚本

![1584685091903](../image/1584685091903.png)

