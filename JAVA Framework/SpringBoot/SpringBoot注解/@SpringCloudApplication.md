



# @SpringCloudApplication

因为一个标准的Eureka服务，一般都需要@EnableDiscoveryClient、@SpringBootApplication以及@EnableCircuitBreaker三个注解，
所以Spring官方提供了这个注解

```java

@SpringCloudApplication
public class ConsumerApplication {
    public static void main(String[] args) {
        SpringApplication.run(ConsumerApplication.class, args);
    }

    @Bean
    @LoadBalanced
    public RestTemplate restTemplate() {
        // 这次我们使用了OkHttp客户端,只需要注入工厂即可
        return new RestTemplate();
    }
}

```
