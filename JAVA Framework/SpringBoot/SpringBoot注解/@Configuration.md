
# @Configuration

这个注解类标识这个类可以使用Spring IOC容器作为bean定义的来源。下面是@Configuration里的一个例子

```java

@Configuration
public class AppConfig {

    @Bean
    public TransferService transferService() {
        return new TransferServiceImpl();
    }

}

```

这个配置就等同于之前在xml里的配置
