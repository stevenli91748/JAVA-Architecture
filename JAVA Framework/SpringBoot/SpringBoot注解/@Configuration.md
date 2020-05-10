
# @Configuration
相当于传统的xml配置文件，如果有些第三方库需要用到xml文件，建议仍然通过@Configuration类作为项目的配置主类——可以使用@ImportResource注解加载xml配置文件
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

--

这是 Spring 3.0 添加的一个注解，用来代替 applicationContext.xml 配置文件，所有这个配置文件里面能做到的事情都可以通过这个注解所在类来进行注册。

下面几个相关注解也是非常重要的！

@Bean

用来代替 XML 配置文件里面的 <bean ...> 配置。

@ImportResource

如果有些通过类的注册方式配置不了的，可以通过这个注解引入额外的 XML 配置文件，有些老的配置文件无法通过 @Configuration 方式配置的非常管用。

@Import

用来引入额外的一个或者多个 @Configuration 修饰的配置文件类。
