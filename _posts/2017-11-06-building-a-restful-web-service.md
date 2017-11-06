---
layout: post
title: 构建一个REST风格的Web服务
key: 20171106
tags: spring boot
---

本指南将引导您使用Spring创建一个REST风格的“Hello World”Web服务。

### 您将要构建的东西

您将构建一个接受HTTP GET请求的服务：
`http://localhost:8080/greeting`
然后响应JSON格式的问候信息：
~~~text
{"id":1,"content":"Hello, World!"}
~~~
您可以使用查询字符串（query string）中的可选参数`name`来自定义问候信息：
`http://localhost:8080/greeting?name=User`
`name`参数的值将会覆盖默认值“World”，并反映在响应中：
~~~text
{"id":1,"content":"Hello, User!"}
~~~
  <!--more-->
### 您需要的东西

* 大约15分钟
* 一个您最喜欢的文本编辑器或IDE
* [JDK 1.8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) or later
* [Gradle 2.3+](http://www.gradle.org/downloads) or [Maven 3.0+](https://maven.apache.org/download.cgi)
* 您也可以直接将代码导入到这些IDE中：
  * [Spring Tool Suite (STS)](https://spring.io/guides/gs/sts)
  * [IntelliJ IDEA](https://spring.io/guides/gs/intellij-idea/)

### 如何完成本指南

像大多数Spring[入门指南](https://spring.io/guides)一样，您可以从头开始去完成每个步骤，也可以跳过已经熟悉的基本设置步骤。 无论哪种方式，您最终会得到可以正常工作的代码。
**如果您选择从头开始**，就去看章节“[用Gradle构建](#用gradle构建)”或“[用maven构建](#用maven构建)”。
**如果您要跳过这些基础设置**，请执行以下操作：
* [下载](https://github.com/spring-guides/gs-rest-service/archive/master.zip)并解压缩本指南的源代码库，或使用[Git](https://spring.io/understanding/Git)克隆它：
`git clone https://github.com/spring-guides/gs-rest-service.git`
* 通过`cd`命令进入`gs-rest-service/initial` 目录
* 然后去阅读章节“[创建资源表示类](#创建一个资源表示类)”。

**当您完成了这个指南后**，可以使用gs-rest-service/complete中的代码检查您构建的代码。

## 用Gradle构建

首先您得建立一个基本的构建脚本。 在使用Spring构建应用程序时，您可以使用任何构建系统，您需要使用到的[Gradle](http://gradle.org/)和[Maven](https://maven.apache.org/)的代码我都写在文档里了。 如果您不熟悉这两个构建系统，请参阅[使用Gradle构建Java项目](https://spring.io/guides/gs/gradle)或[使用Maven构建Java项目](https://spring.io/guides/gs/maven)。

### 创建目录结构

在您选择的项目目录下，创建以下子目录结构; 例如，您可以在类Unix系统上使用命令`mkdir -p src/main/java/hello`来创建目录：
~~~text
└── src
    └── main
        └── java
            └── hello
~~~

创建一个Gradle构建文件
下面是[最初的Gradle构建文件](https://github.com/spring-guides/gs-rest-service/blob/master/initial/build.gradle)。
`build.gradle`
~~~
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
    baseName = 'gs-rest-service'
    version =  '0.1.0'
}

repositories {
    mavenCentral()
}

sourceCompatibility = 1.8
targetCompatibility = 1.8

dependencies {
    compile("org.springframework.boot:spring-boot-starter-web")
    testCompile('org.springframework.boot:spring-boot-starter-test')
}
~~~
[Spring Boot 的 Gradle插件](https://github.com/spring-projects/spring-boot/tree/master/spring-boot-tools/spring-boot-gradle-plugin)提供了许多方便的功能：
* 它收集类路径（classpath）上的所有jar，并构建一个可运行的“über-jar”，这使得您执行和传递服务程序更加方便。
* 它搜索`public static void main()`方法标，并把它记为可运行类。
* 它提供了一个内置的依赖解析器，设置了版本号以匹配Spring Boot的依赖关系。 你可以用任何你想要的版本覆盖，否则它会默认使用Boot选择的版本。

## 用maven构建

首先您得建立一个基本的构建脚本。 在使用Spring构建应用程序时，您可以使用任何构建系统，[Maven](https://maven.apache.org/)的代码我都写在文档里了。 如果您不熟悉这个构建系统，[使用Maven构建Java项目](https://spring.io/guides/gs/maven)。

### 创建目录结构

在您选择的项目目录下，创建以下子目录结构; 例如，您可以在类Unix系统上使用命令`mkdir -p src/main/java/hello`来创建目录：
~~~text
└── src
    └── main
        └── java
            └── hello
~~~
`pom.xml`
~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.springframework</groupId>
    <artifactId>gs-rest-service</artifactId>
    <version>0.1.0</version>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>1.5.8.RELEASE</version>
    </parent>

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
        <dependency>
            <groupId>com.jayway.jsonpath</groupId>
            <artifactId>json-path</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <properties>
        <java.version>1.8</java.version>
    </properties>


    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

    <repositories>
        <repository>
            <id>spring-releases</id>
            <url>https://repo.spring.io/libs-release</url>
        </repository>
    </repositories>
    <pluginRepositories>
        <pluginRepository>
            <id>spring-releases</id>
            <url>https://repo.spring.io/libs-release</url>
        </pluginRepository>
    </pluginRepositories>
</project>
~~~
[Spring Boot 的 Maven插件](https://github.com/spring-projects/spring-boot/tree/master/spring-boot-tools/spring-boot-maven-plugin)提供了许多方便的功能：
* 它收集类路径（classpath）上的所有jar，并构建一个可运行的“über-jar”，这使得您执行和传递服务程序更加方便。
* 它搜索`public static void main()`方法标，并把它记为可运行类。
* 它提供了一个内置的依赖解析器，设置了版本号以匹配Spring Boot的依赖关系。 你可以用任何你想要的版本覆盖，否则它会默认使用Boot选择的版本。

## 用你的IDE构建

* 阅读如何将本指南的代码直接导入到[Spring Tool Suite](https://spring.io/guides/gs/sts/)中。
* 阅读如何在[IntelliJ IDEA](https://spring.io/guides/gs/intellij-idea)中使用本指南。

## 创建一个资源表示类

现在你已经建立了项目和构建系统，然后就可以开始创建你的Web服务了。
在开始动手前，先考虑一下服务的交互过程。
该服务将处理`/greeting`的GET请求，查询字符串（query string）中的`name`参数是可选的。`GET`请求应该返回一个状态码为`200 OK`且响应体（body）为`JSON`格式的响应结果。 它应该看起来像这样：
~~~json
{
    "id": 1,
    "content": "Hello, World!"
}
~~~
`id`字段是问候信息的唯一标识符，content是问候信息的文字内容。
为了给问候信息建模，您可以创建一个资源表示类。 为`id`和`content`数据提供一个普通的java对象，包含字段，构造函数和访问器：
`src/main/java/hello/Greeting.java`
~~~java
package hello;

public class Greeting {

    private final long id;
    private final String content;

    public Greeting(long id, String content) {
        this.id = id;
        this.content = content;
    }

    public long getId() {
        return id;
    }

    public String getContent() {
        return content;
    }
}
~~~
> 正如您在下面的步骤中看到的，Spring使用[Jackson JSON](http://wiki.fasterxml.com/JacksonHome)库自动将类型为`Greeting`的实例组织成为JSON格式的数据。

接下来，你需要创建一个提供这些问候的资源控制器。

## 创建一个资源控制器

在Spring构建REST风格的Web服务的方法中，HTTP请求由控制器处理。 这些组件可以通过[`@RestController`](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/bind/annotation/RestController.html)批注轻松识别，下面的`GreetingController`通过返回`Greeting`类的新实例来处理`/greeting`的`GET`请求：
`src/main/java/hello/GreetingController.java`
~~~java
package hello;

import java.util.concurrent.atomic.AtomicLong;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class GreetingController {

    private static final String template = "Hello, %s!";
    private final AtomicLong counter = new AtomicLong();

    @RequestMapping("/greeting")
    public Greeting greeting(@RequestParam(value="name", defaultValue="World") String name) {
        return new Greeting(counter.incrementAndGet(),
                            String.format(template, name));
    }
}
~~~
这个控制器简洁明了，但是它涵盖了很多事物。 让我们一步一步分解。
`@RequestMapping`注释可以确保`/greeting`的HTTP请求被映射到`greeting()`方法。

> 上面的例子没有指定`GET`与`PUT`，`POST`等等，因为`@RequestMapping`默认映射所有的HTTP操作。 使用`@RequestMapping（method = GET）`缩小这个映射范围。

`@RequestParam`将查询字符串（query string）参数`name`的值绑定到`greeting()`方法的`name`参数中。此查询字符串参数显式标记为可选（默认情况下为`required = true`）：如果请求中不存在，则使用`defaultValue`，“World”。

方法体的实现是利用`counter`的下一个值创建并返回具有`id`和`content`属性的新的`Greeting`对象，并使用问候模板`template`格式化给定的`name`值。

传统的MVC控制器和上面的REST风格的Web服务控制器之间的主要区别在于HTTP响应体的创建方式。这个REST风格的Web服务控制器并不依赖于[视图技术](https://spring.io/understanding/view-templates)来将问候数据的服务器端呈现给HTML，而是简单地填充并返回一个`Greeting`对象。对象数据将作为JSON直接写入HTTP响应。

这段代码使用了Spring 4的新的`@RestController`注解，它将类标记为一个控制器，其中每个方法都返回一个域对象（domain object）而不是一个视图（view）。这是`@Controller`和`@ResponseBody`的缩写。

`Greeting`对象必须转换为JSON。由于Spring的HTTP消息转换器支持，您不需要手动执行此转换。由于[Jackson 2](http://wiki.fasterxml.com/JacksonHome)在类路径（classpath）上，所以自动选择Spring的`MappingJackson2HttpMessageConverte`r将`Greeting`实例转换为JSON。

## 使应用程序可执行

尽管可以将此服务作为传统WAR文件打包以部署到外部应用程序服务器，但下面演示的更简单的方法创建了独立的应用程序。 你把所有东西都封装在一个单独的，可执行的JAR文件中，由一个好的旧的Java `main()`方法驱动。 一路上，您使用Spring的支持来将Tomcat servlet容器作为HTTP运行时嵌入，而不是部署到外部实例。
~~~text
src/main/java/hello/Application.java
~~~
~~~java
package hello;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
~~~
`@SpringBootApplication`是一个方便的注释，增加了以下所有内容：
* `@Configuration`将类标记为应用程序上下文的bean定义的来源。
* `@EnableAutoConfiguration`通知Spring Boot开始添加根据类路径设置的beans、其他bean和各种属性设置。
* 通常情况下，您会为Spring MVC应用程序添加`@EnableWebMvc`，但Spring Boot会在类路径中有spring-webmvc时自动添加它。 这会将该应用程序标记为Web应用程序，并激活关键行为，如设置`DispatcherServlet`。
* `@ComponentScan`告诉Spring在hello包中查找其他组件，配置和服务，以便找到控制器。

`main()`方法使用Spring Boot的`SpringApplication.run()`方法启动应用程序。 你有没有注意这里到没有一行XML？ 也没有web.xml文件。 这个Web应用程序是100％纯Java，您不必处理配置任何管道或基础设施。

### 构建一个可执行的JAR

您可以使用Gradle或Maven从命令行运行应用程序。 或者，您可以构建一个包含所有必需的依赖项，类和资源的可执行JAR文件，然后运行该文件。 这使得在整个开发生命周期内将服务作为应用程序发布，版本化、部署和跨越不同的环境等等，变得容易。

如果您正在使用Gradle，则可以使用`./gradlew bootRun`运行该应用程序。 或者您可以使用`./gradlew build`生成JAR文件。 然后你可以运行JAR文件：
~~~text
java -jar build/libs/gs-rest-service-0.1.0.jar
~~~
如果您正在使用Maven，则可以使用`./mvnw spring-boot：run`来运行该应用程序。 或者你也可以使用`./mvnw clean package`建立JAR文件。 然后你可以运行JAR文件：
~~~text
java -jar target/gs-rest-service-0.1.0.jar
~~~

> 上面的过程将创建一个可运行的JAR。 您也可以选择构建一个经典的WAR文件。
 
日志记录输出显示。 该服务应该在几秒钟内启动并运行。

## 测试服务

现在服务已经启动，请访问[http://localhost:8080/greeting](http://localhost:8080/greeting)，在这里您可以看到：
~~~json
{"id":1,"content":"Hello, World!"}
~~~
使用[http://localhost:8080/greeting?name=User](http://localhost:8080/greeting?name=User)提供`name`查询字符串（query string）参数。 注意`content`属性的值是如何从“Hello, World! 到“Hello User!”：
~~~json
{"id":2,"content":"Hello, User!"}
~~~
这个改变演示了`GreetingControlle`r中的`@RequestParam`安排按预期工作了。 `name`参数的默认值是“World”，但可以通过查询字符串（query string）显式覆盖。

还要注意`id`属性是如何从`1`改变为`2`。这证明你正在多个请求中的同一个`GreetingController`实例中起作用，并且它的`counter`字段在每个调用中按照预期递增。

## 概要

恭喜！ 您刚刚用Spring开发了一个REST风格的Web服务。

## 其他内容

以下指南也可能有所帮助：

* [Accessing GemFire Data with REST](https://spring.io/guides/gs/accessing-gemfire-data-rest/)
* [Accessing MongoDB Data with REST](https://spring.io/guides/gs/accessing-mongodb-data-rest/)
* [Accessing data with MySQL](https://spring.io/guides/gs/accessing-data-mysql/)
* [Accessing JPA Data with REST](https://spring.io/guides/gs/accessing-data-rest/)
* [Accessing Neo4j Data with REST](https://spring.io/guides/gs/accessing-neo4j-data-rest/)
* [Consuming a RESTful Web Service](https://spring.io/guides/gs/consuming-rest/)
* [Consuming a RESTful Web Service with AngularJS](https://spring.io/guides/gs/consuming-rest-angularjs/)
* [Consuming a RESTful Web Service with jQuery](https://spring.io/guides/gs/consuming-rest-jquery/)
* [Consuming a RESTful Web Service with rest.js](https://spring.io/guides/gs/consuming-rest-restjs/)
* [Securing a Web Application](https://spring.io/guides/gs/securing-web/)
* [Building REST services with Spring](https://spring.io/guides/tutorials/bookmarks/)
* [React.js and Spring Data REST](https://spring.io/guides/tutorials/react-and-spring-data-rest/)
* [Building an Application with Spring Boot](https://spring.io/guides/gs/spring-boot/)
* [Creating API Documentation with Restdocs](https://spring.io/guides/gs/testing-restdocs/)
* [Enabling Cross Origin Requests for a RESTful Web Service](https://spring.io/guides/gs/rest-service-cors/)
* [Building a Hypermedia-Driven RESTful Web Service](https://spring.io/guides/gs/rest-hateoas/)
* [Circuit Breaker](https://spring.io/guides/gs/circuit-breaker/)

想写一个新的指南或贡献一个现有的？ 查看我们的[贡献准则](https://github.com/spring-guides/getting-started-guides/wiki)。

> 所有指南均以代码的ASLv2许可证发布，以及用于撰写的[Attribution，NoDerivatives创意共用许可证](https://creativecommons.org/licenses/by-nd/3.0/)。