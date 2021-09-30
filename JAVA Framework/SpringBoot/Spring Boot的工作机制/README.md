**Spring Boot的工作机制**

1 使用IDEA 等工具生成Hello应用程序
2 对项目所生成的主要文件（HelloApplication.java，application.properties，HelloApplicationTests.java，pom.xml）的内容进行详细讲解

HelloApplication.javaHelloApplication.java是程序的引导类，我们可以直接运行该类来启动SpringBoot应用

'''java
package com.itheima.web;
import org.springframework.boot.SpringApplication;
importorg.springframework.boot.autoconfigure.SpringBootApplication;
@SpringBootApplication
public class HelloApplication {public static void main(String[] args) 
  { 
   SpringApplication.run(HelloApplication.class, args);
  }
}

'''

# 参考

微服务架构基础(Spring Boot+ Spring Cloud + Docket).pdf
