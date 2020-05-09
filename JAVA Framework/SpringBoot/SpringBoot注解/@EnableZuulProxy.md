


# @EnableZuulProxy
通过@EnableZuulProxy注解开启Zuul的功能

```java

@SpringBootApplication
@EnableZuulProxy // 开启Zuul的网关功能
public class GatewayApplication {
    public static void main(String[] args) {
        SpringApplication.run(GatewayApplication.class, args);
    }
}


```
