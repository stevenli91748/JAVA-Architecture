
# @Configuration

@Configuration标注在类上，相当于把该类作为spring的xml配置文件中的<beans>，作用为：配置spring容器(应用上下文)

表示将该类作用springboot配置文件类

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
