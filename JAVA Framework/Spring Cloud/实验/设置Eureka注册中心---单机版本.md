


第一步： 在IDEA中创建一个项目， 用 spring init工具
第二步： 在application.yml 中增加eureka server 的依赖

        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
        </dependency>


第三步： 设置主程序代码, 在主类上添加 @EnableEurekaServer

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

第四步： 设置application.yml文件

                server:
                  port: 8123     //可以任意端口号

                eureka:
                  client:
                    register-with-eureka: false
                    fetch-registry: false
                    service-url:
                      defaultZone: http://localhost:8123/eureka/


第五步： 运行程序 run

第六步： 在浏览器上输入

        http://localhost:8123/eureka  如果不能看到eureka的页面， 就输入http://localhost:8123 如果还是不能看到Eureka的页面
        
        就在application.yml里添加spring.freemarker.prefer-file-system-access: false, 因为 eureka的页面是用freemarker编写的
        
        
