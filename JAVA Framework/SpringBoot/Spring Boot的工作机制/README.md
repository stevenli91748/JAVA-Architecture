**Spring Boot的工作机制**

### 1 使用IDEA 等工具生成Hello应用程序
### 2 对项目所生成的主要文件（HelloApplication.java，application.properties，HelloApplicationTests.java，pom.xml）的内容进行详细讲解

**HelloApplication.java**, HelloApplication.java是程序的引导类，我们可以直接运行该类来启动SpringBoot应用

    @SpringBootApplication是Spring Boot的核心注解，它是一个复合注解，用于开启组件扫描和自动配置。在main()方法中，通过调用SpringApplication类的run()方法将业务委托给了Spring Boot
    的SpringApplication类，SpringApplication类将引导应用程序启动Spring，并相应地启动被自动配置的Tomcat服务器。只需要将 HelloApplication.class作为参数传递给run()方法，以此来通
    知SpringApplication谁是主要的Spring组件，并传递args数组作为参数即可

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




# 参考

微服务架构基础(Spring Boot+ Spring Cloud + Docket).pdf
