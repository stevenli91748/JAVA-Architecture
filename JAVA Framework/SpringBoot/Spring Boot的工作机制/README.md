# Spring Boot的工作机制

### 1 使用IDEA 等工具生成Hello应用程序
### 2 对项目所生成的主要文件（HelloApplication.java，application.properties，HelloApplicationTests.java，pom.xml）的内容进行详细讲解

* 2.1  **HelloApplication.java**

```java

package com.itheima.web;
import org.springframework.boot.SpringApplication;
importorg.springframework.boot.autoconfigure.SpringBootApplication;
@SpringBootApplication
public class HelloApplication {public static void main(String[] args) 
  { 
   SpringApplication.run(HelloApplication.class, args);
  }
}

```

**HelloApplication.java**, HelloApplication.java是程序的引导类，我们可以直接运行该类来启动SpringBoot应用

    @SpringBootApplication是Spring Boot的核心注解，它是一个复合注解，用于开启组件扫描和自动配置。在main()方法中，通过调用SpringApplication类的run()方法将业务委托给了Spring Boot
    的SpringApplication类，SpringApplication类将引导应用程序启动Spring，并相应地启动被自动配置的Tomcat服务器。只需要将 HelloApplication.class作为参数传递给run()方法，以此来通
    知SpringApplication谁是主要的Spring组件，并传递args数组作为参数即可

**@SpringBootApplication**

    @SpringBootApplication源码中使用了多个注解来标注信息，其中最重要的三个注解是@SpringBootConfiguration、@EnableAutoConfiguration 和@ComponentScan。实际上，@SpringBootApplication 
    就是这三个注解的结合。虽然在项目中使用这三个注解可以替换@SpringBootApplication，但显然使用@SpringBootApplication注解更加简单。
    
    ·@SpringBootConfiguration：该注解与@Configuration的作用相同，它表示其标注的类是IoC容器的配置类。·
     @EnableAutoConfiguration：用于将所有符合自动配置的Bean加载到当前Spring Boot创建并使用的IoC容器中。·
     @ComponentScan：用于自动扫描和加载符合条件的组件或Bean，并将Bean加载到IoC容器中。

**SpringApplication**

    在Spring Boot项目的main()方法中，SpringApplication.run(HelloApplication.class, args)是唯一执行的方法体，该方法体的执行过程可分为两部分来看，具体如下。
    
    1.创建SpringApplication对象在SpringApplication实例初始化时，它会做如下几项工作。
       （1）根据classpath内是否存在某个特征类来判断是否为Web应用，并使用webEnvironment标记是否为Web应用。
       （2）使用SpringFactoriesLoader在classpath中的spring.factories文件查找并加载所有可用的ApplicationContextInitializer。
       （3）使用SpringFactoriesLoader在classpath中的spring.factories文件查找并加载所有可用的ApplicationListener。
       （4）推断并设置main()方法的定义类。
    
    2.调用实例的run()方法run()方法是Spring Boot执行流程的主要方法，该方法执行时，主要做了如下工作。
       （1）查找并加载 spring.factories 中配置的 SpringApplicationRunListener，并调用它们的started()方法。告诉SpringApplicationRunListener，Spring Boot应用要执行了。
       （2）创建并配置当前 Spring Boot 应用要使用的 Environment，然后遍历调用所有SpringApplicationRunListener的environmentPrepared()方法。告诉SpringBoot应用使用的环境准备好了。
       （3）如果SpringApplication的showBanner属性被设置为true，则打印banner。
       （4）根据用户是否明确设置了 applicationContextClass 类型，以及初始化阶段（创建SpringApplication对象的第（1）步）的推断结果来决定当前Spring Boot应用创建什么类型ApplicationContext。        （5）创建故障分析器。故障分析器用于提供错误和诊断信息。
       （6）对ApplicationContext进行后置处理。对所有可用的ApplicationContextInitializer遍历执行initialize()方法；遍历调用所有SpringApplicationRunListener的environmentPrepared()方法；
           将之前通过@EnableAutoConfiguration 获取的所有配置以及其他形式的 IoC 容器配置加载到已经准备完毕的 ApplicationContext；遍历调用所有SpringApplicationRunListeners 
           的contextLoaded()方法。   
       （7）调用refreshContext()方法执行applicationContext的refresh()方法。
       （8）查找当前ApplicationContext中是否有ApplicationRunner和CommandLineRunner，如果有，则遍历执行它们
       （9）正常情况下，会遍历执行所有 SpringApplicationRunListener 的 finished()方法；如果出现异常，也会调用该方法，只不过这种情况下会将异常信息一并传入处理。



* 2.2 **application.properties文件**

      在新创建的Spring Boot项目中，application.properties文件是一个空文件，并且是可选文件，我们可以通过该文件对Spring Boot应用的服务端口号、注册中心地址等内容进行调整。例如将嵌入在项目
      中的Tomcat端口号8080修改为8081，那么就可以在配置文件中添加如下信息：server.port=8081需要注意的是，项目中不需要显示的配置Spring Boot加载application.properties文件的信息，只要该文
      件存在，就会被自动加载，Spring和应用程序代码就可以获取其中的属性信息
      
* 2.3  **HelloApplicationTests.java**     

      HelloApplicationTests.java是Spring Initializr提供的一个基本的集成测试类，可以基于该类为应用程序编写测试代码
      
      @RunWith 注解表示测试运行在 Spring 测试环境中，@SpringBootTest 是Spring Boot 提供的注解，该注解通过 SpringApplication在测试中创建应用上下文来工作。contextLoads()方法是一个空
      方法，虽然在方法体中没有编写任何代码，但通过此方法可以确定应用程序上下文的加载是否有问题。如果HelloApplication中定义的配置是正确的，那么就可以通过测试，否则，不能通过测试
      
```java

package com.itheima.web;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;
@RunWith(SpringRunner.class)
@SpringBootTest8　
public class HelloApplicationTests {
 @Test
 public void contextLoads() {
　　 
    }
}

```

* 2.4 **pom.xml文件**

       生成的pom.xml中包含Spring Boot项目的版本、依赖和插件等信息。其中，<parent>元素中的spring-boot-starter-parent是所有Spring Boot依赖包的父依赖，它提供了很多有用的默认设置，
       如application.properties 的位置等。其他的依赖包只是简单地提供了开发特定类型应用的功能，如上述代码中<dependencies>元素中的 spring-bootstarter-web，就是我们在官网项目生成器页面
       中选择的Web依赖信息，该信息中包含了Tomcat、Spring MVC等内容。spring-boot-starter-test是一个通用的测试模块，该模块中包含了JUnit、Hamcrest等测试框架


<?xml version="1.0" encoding="UTF-8"?>
　<project xmlns="http://maven.apache.org/POM/4.0.0"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://maven.apache.org/POM/4.0.05　　　　　　　　　　　　　 http://maven.apache.org/xsd/maven-4.0.0.xsd">6　　　<!--项目基本信息 -->7　　　<modelVersion>4.0.0</modelVersion>8　　　<groupId>com.itheima</groupId>9　　　<artifactId>hello</artifactId>10　　 <version>0.0.1-SNAPSHOT</version>11　　 <packaging>jar</packaging>12　　 <name>hello</name> <description>Demo project for Spring Boot</description>14　　 <!--父依赖 -->15　　 <parent>16　　　　 <groupId>org.springframework.boot</groupId>17　　　　 <artifactId>spring-boot-starter-parent</artifactId>18　　　　 <version>1.5.6.RELEASE</version>19　　　　 <relativePath /> <!-- lookup parent from repository -->20　　 </parent>21　　 <!-- 编码和Java版本 -->22　　 <properties>23　　　　 <project.build.sourceEncoding>24　　　　　　 UTF-825　　　　 </project.build.sourceEncoding>26　　　　 <project.reporting.outputEncoding>27　　　　　　 UTF-8</project.reporting.outputEncoding>29　　　　 <java.version>1.8</java.version>30　　 </properties>31　　 <!--其他依赖 -->32　　 <dependencies>33　　　　 <dependency>34　　　　　　 <groupId>org.springframework.boot</groupId>35　　　　　　 <artifactId>spring-boot-starter-web</artifactId>36　　　　 </dependency>37　　　　 <dependency>38　　　　　　 <groupId>org.springframework.boot</groupId>39　　　　　　 <artifactId>spring-boot-starter-test</artifactId>40　　　　　　 <scope>test</scope>41　　　　 </dependency>42　　 </dependencies>43　　 <build>44　　　　 <plugins><!--Spring Boot包含的Maven插件，它可以将项目打包成一个可执行JAR -->46　　　　　　 <plugin>47　　　　　　　　<groupId>org.springframework.boot</groupId>48　　　　　　　　<artifactId>spring-boot-maven-plugin</artifactId>49　　　　　　 </plugin>50　　　　 </plugins>51　　 </build>52 </project>


# 参考

微服务架构基础(Spring Boot+ Spring Cloud + Docket).pdf
