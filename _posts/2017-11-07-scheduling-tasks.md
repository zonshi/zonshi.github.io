---
layout: post
title: 调度任务
date: 2017-11-07 08:11:45 +0800
key: 20171107
tags: Spring&nbsp;Boot Schedule
---

本指南将引导您完成使用Spring安排任务的步骤。

## 您将要构建的东西

您将使用Spring的`@Scheduled`注释构建一个每五秒打印一次当前时间的应用程序。

## 您需要的东西

- 大约15分钟
- 一个您最喜欢的文本编辑器或IDE
- [JDK 1.8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) or later
- [Gradle 2.3+](http://www.gradle.org/downloads) or [Maven 3.0+](https://maven.apache.org/download.cgi)
- 您也可以直接将代码导入到这些IDE中：
  - [Spring Tool Suite (STS)](https://spring.io/guides/gs/sts)
  - [IntelliJ IDEA](https://spring.io/guides/gs/intellij-idea/)
  <!--more-->

## 如何完成本指南

像大多数Spring[入门指南](https://spring.io/guides)一样，您可以从头开始去完成每个步骤，也可以跳过已经熟悉的基本设置步骤。 无论哪种方式，您最终会得到可以正常工作的代码。

**如果您选择从头开始**，就去看章节“[用Gradle构建](#用gradle构建)”或“[用maven构建](#用maven构建)”。
**如果您要跳过这些基础设置**，请执行以下操作：

* [Download](https://github.com/spring-guides/gs-scheduling-tasks/archive/master.zip) and unzip the source repository for this guide, or clone it using [Git](http://spring.io/understanding/Git):
   `git clone https://github.com/spring-guides/gs-scheduling-tasks.git`
* 通过`cd`命令进入`gs-scheduling-tasks/initial`目录
* 然后去阅读章节[创建一个计划任务](http://spring.io/guides/gs/scheduling-tasks/#initial)。

**当您完成了这个指南后**，可以使用`gs-scheduling-tasks/complete`中的代码检查您构建的代码。

## 用Gradle构建

首先您得建立一个基本的构建脚本。 在使用Spring构建应用程序时，您可以使用任何构建系统，您需要使用到的[Gradle](http://gradle.org/)和[Maven](https://maven.apache.org/)的代码我都写在文档里了。 如果您不熟悉这两个构建系统，请参阅[使用Gradle构建Java项目](https://spring.io/guides/gs/gradle)或[使用Maven构建Java项目](https://spring.io/guides/gs/maven)。

### 创建目录结构

在您选择的项目目录下，创建以下子目录结构; 例如，您可以在类Unix系统上使用命令`mkdir -p src/main/java/hello`来创建目录：

```text
└── src
    └── main
        └── java
            └── hello
```

创建一个Gradle构建文件
下面是[最初的Gradle构建文件](https://github.com/spring-guides/gs-scheduling-tasks/blob/master/initial/build.gradle)。

`build.gradle`

```text
buildscript {
    repositories {
        mavenCentral()
    }
    dependencies {
        classpath("org.springframework.boot:spring-boot-gradle-plugin:1.5.8.RELEASE")
    }
}

apply plugin: 'java'
apply plugin: 'eclipse'
apply plugin: 'idea'
apply plugin: 'org.springframework.boot'

jar {
    baseName = 'gs-scheduling-tasks'
    version =  '0.1.0'
}

repositories {
    mavenCentral()
}

sourceCompatibility = 1.8
targetCompatibility = 1.8

dependencies {
    compile("org.springframework.boot:spring-boot-starter")
    testCompile("junit:junit")
}
```

[Spring Boot 的 Gradle插件](https://github.com/spring-projects/spring-boot/tree/master/spring-boot-tools/spring-boot-gradle-plugin)提供了许多方便的功能：

- 它收集类路径（classpath）上的所有jar，并构建一个可运行的“über-jar”，这使得您执行和传递服务程序更加方便。
- 它搜索`public static void main()`方法标，并把它记为可运行类。
- 它提供了一个内置的依赖解析器，设置了版本号以匹配Spring Boot的依赖关系。 你可以用任何你想要的版本覆盖，否则它会默认使用Boot选择的版本。

## 用maven构建

首先您得建立一个基本的构建脚本。 在使用Spring构建应用程序时，您可以使用任何构建系统，[Maven](https://maven.apache.org/)的代码我都写在文档里了。 如果您不熟悉这个构建系统，[使用Maven构建Java项目](https://spring.io/guides/gs/maven)。

### 创建目录结构

在您选择的项目目录下，创建以下子目录结构; 例如，您可以在类Unix系统上使用命令`mkdir -p src/main/java/hello`来创建目录：

```text
└── src
    └── main
        └── java
            └── hello
```

`pom.xml`

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.springframework</groupId>
    <artifactId>gs-scheduling-tasks</artifactId>
    <version>0.1.0</version>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>1.5.8.RELEASE</version>
    </parent>

    <properties>
        <java.version>1.8</java.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
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
~~~

[Spring Boot 的 Maven插件](https://github.com/spring-projects/spring-boot/tree/master/spring-boot-tools/spring-boot-maven-plugin)提供了许多方便的功能：

- 它收集类路径（classpath）上的所有jar，并构建一个可运行的“über-jar”，这使得您执行和传递服务程序更加方便。
- 它搜索`public static void main()`方法标，并把它记为可运行类。
- 它提供了一个内置的依赖解析器，设置了版本号以匹配Spring Boot的依赖关系。 你可以用任何你想要的版本覆盖，否则它会默认使用Boot选择的版本。

## 用你的IDE构建

- 阅读如何将本指南的代码直接导入到[Spring Tool Suite](https://spring.io/guides/gs/sts/)中。
- 阅读如何在[IntelliJ IDEA](https://spring.io/guides/gs/intellij-idea)中使用本指南。

## 创建一个计划任务

现在您已经设置了您的项目，您可以创建一个计划任务。

`src/main/java/hello/ScheduledTasks.java`

```java
package hello;

import java.text.SimpleDateFormat;
import java.util.Date;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.stereotype.Component;

@Component
public class ScheduledTasks {

    private static final Logger log = LoggerFactory.getLogger(ScheduledTasks.class);

    private static final SimpleDateFormat dateFormat = new SimpleDateFormat("HH:mm:ss");

    @Scheduled(fixedRate = 5000)
    public void reportCurrentTime() {
        log.info("The time is now {}", dateFormat.format(new Date()));
    }
}
```

`Scheduled`注释定义了特定方法的运行时间。 注意：本示例使用`fixedRate`，它指定每次方法调用的时间间隔。 这个注释的参数列表还有[其他的选项](https://docs.spring.io/spring/docs/current/spring-framework-reference/html/scheduling.html#scheduling-annotation-support-scheduled)，比如`fixedDelay`，它指定了任务完成之后的调用间隔。 您还可以[使用`@Scheduled（cron ="..."）`表达式来实现更复杂的任务调度](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/scheduling/support/CronSequenceGenerator.html)。

## 启用计划

尽管计划任务可以嵌入到Web应用程序和WAR文件中，但下面演示的更简单的方法会创建一个独立的应用程序。 你把所有东西都封装在一个单独的，可执行的JAR文件中，由一个好的旧的Java `main()`方法驱动。

`src/main/java/hello/Application.java`

```java
package hello;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.scheduling.annotation.EnableScheduling;

@SpringBootApplication
@EnableScheduling
public class Application {

    public static void main(String[] args) throws Exception {
        SpringApplication.run(Application.class);
    }
}
```

`@SpringBootApplication` 是一个方便的注释，增加了以下所有内容：

`@SpringBootApplication`是一个方便的注释，增加了以下所有内容：

- `@Configuration`将类标记为应用程序上下文的bean定义的来源。
- `@EnableAutoConfiguration`通知Spring Boot开始添加根据类路径设置的beans、其他bean和各种属性设置。
- 通常情况下，您会为Spring MVC应用程序添加`@EnableWebMvc`，但Spring Boot会在类路径中有spring-webmvc时自动添加它。 这会将该应用程序标记为Web应用程序，并激活关键行为，如设置`DispatcherServlet`。
- `@ComponentScan`告诉Spring在hello包中查找其他组件，配置和服务，以便找到控制器。

`main()`方法使用Spring Boot的`SpringApplication.run()`方法启动应用程序。 你有没有注意这里到没有一行XML？ 也没有web.xml文件。 这个Web应用程序是100％纯Java，您不必处理配置任何管道或基础设施。

[`@EnableScheduling`](https://docs.spring.io/spring/docs/current/spring-framework-reference/htmlsingle/#scheduling-enable-annotation-support) 确保创建后台任务执行程序。 没有它，没有任何计划。

### 构建一个可执行的JAR

您可以使用Gradle或Maven从命令行运行应用程序。 或者，您可以构建一个包含所有必需的依赖项，类和资源的可执行JAR文件，然后运行该文件。 这使得在整个开发生命周期内将服务作为应用程序发布，版本化、部署和跨越不同的环境等等，变得容易。

如果您正在使用Gradle，则可以使用`./gradlew bootRun`运行该应用程序。 或者您可以使用`./gradlew build`生成JAR文件。 然后你可以运行JAR文件：

```text
java -jar build/libs/gs-scheduling-tasks-0.1.0.jar
```

如果您正在使用Maven，则可以使用`./mvnw spring-boot：run`来运行该应用程序。 或者你也可以使用`./mvnw clean package`建立JAR文件。 然后你可以运行JAR文件：

```text
java -jar target/gs-scheduling-tasks-0.1.0.jar
```

> 上面的过程将创建一个可运行的JAR。 您也可以选择[构建一个经典的WAR文件](http://spring.io/guides/gs/convert-jar-to-war/)。

日志输出显示，您可以从日志中看到它在后台线程上。 你应该看到你的计划任务每5秒触发一次：

```text
[...]
2016-08-25 13:10:00.143  INFO 31565 --- [pool-1-thread-1] hello.ScheduledTasks : The time is now 13:10:00
2016-08-25 13:10:05.143  INFO 31565 --- [pool-1-thread-1] hello.ScheduledTasks : The time is now 13:10:05
2016-08-25 13:10:10.143  INFO 31565 --- [pool-1-thread-1] hello.ScheduledTasks : The time is now 13:10:10
2016-08-25 13:10:15.143  INFO 31565 --- [pool-1-thread-1] hello.ScheduledTasks : The time is now 13:10:15
```

## 概要

恭喜！ 您使用计划任务创建了一个应用程序。 检查一下，实际的代码比构建文件更短！ 这种技术适用于任何类型的应用程序。

## 其他内容

以下指南也可能有所帮助：

- [Building an Application with Spring Boot](https://spring.io/guides/gs/spring-boot/)
- [Creating a Batch Service](https://spring.io/guides/gs/batch-processing/)

想写一个新的指南或贡献一个现有的？ 查看我们的[贡献准则](https://github.com/spring-guides/getting-started-guides/wiki)。

> 所有指南均以代码的ASLv2许可证发布，以及用于撰写的[Attribution，NoDerivatives创意共用许可证](https://creativecommons.org/licenses/by-nd/3.0/)。