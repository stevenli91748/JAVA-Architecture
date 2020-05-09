

# @EnableCircuitBreaker
启动断路器

方法RestTemplate restTemplate()：初始化RestTemplate 对象，并使用 @LoadBalanced作负载均衡

```java

@SpringBootApplication
@EnableCircuitBreaker 
@EnableEurekaClient // 配置本应用将使用服务注册和服务发现
public class HystrixSimpleCloudConsumerApplication {

    public static void main(String[] args) {
        args = new String[1];
        args[0] = "--spring.profiles.active=hystrix-simple";
        SpringApplication.run(HystrixSimpleCloudConsumerApplication.class, args);
    }

    /**
     * 初始RestTemplate
     * @return
     */
    @LoadBalanced
    @Bean
    public RestTemplate restTemplate() {
        return new RestTemplate();
    }

    /**
     * 使用fastjson做为json的解析器
     * @return
     */
    @Bean
    public HttpMessageConverters fastJsonHttpMessageConverters() {
        FastJsonHttpMessageConverter fastConverter = new FastJsonHttpMessageConverter();
        FastJsonConfig fastJsonConfig = new FastJsonConfig();
        fastJsonConfig.setSerializerFeatures(SerializerFeature.PrettyFormat);
        fastConverter.setFastJsonConfig(fastJsonConfig);
        HttpMessageConverter<?> converter = fastConverter;
        return new HttpMessageConverters(converter);
    }
}


```
