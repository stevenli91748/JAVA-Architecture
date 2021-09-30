# Spring Boot的工作机制

### 1 使用IDEA 等工具生成Hello应用程序
### 2 对项目所生成的主要文件（HelloApplication.java，application.properties，HelloApplicationTests.java，pom.xml）的内容进行详细讲解

* 2.1  **HelloApplication.java**
 
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



# 参考

微服务架构基础(Spring Boot+ Spring Cloud + Docket).pdf
