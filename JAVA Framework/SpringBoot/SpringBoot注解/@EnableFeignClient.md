



# @EnableFeignClient
我们在启动类上，添加注解，开启Feign功能

```java

@SpringCloudApplication
@EnableFeignClients//开启feign
public class ConsumerApplication {
    public static void main(String[] args) {
        SpringApplication.run(ConsumerApplication.class, args);
    }
	/*  如果前面有测试注意不会受影响
    @Bean
    @LoadBalanced
    public RestTemplate restTemplate() {
        // 这次我们使用了OkHttp客户端,只需要注入工厂即可
        return new RestTemplate();
    }*/
}

```
