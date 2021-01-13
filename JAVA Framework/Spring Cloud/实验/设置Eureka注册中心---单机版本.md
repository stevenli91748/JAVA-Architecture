


第一步： 在IDEA中创建一个项目， 用 spring init工具
第二步： 在application.yml 中增加eureka server 的依赖

        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
        </dependency>


第三步： 设置主程序代码

```java

package com.gzpengli.demoeureka;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.server.EnableEurekaServer;


@EnableEurekaServer
@SpringBootApplication
public class DemoEurekaApplication {

    public static void main(String[] args) {
        SpringApplication.run(DemoEurekaApplication.class, args);

    }
 
}


```
