SpringBoot 提供了以下 2 种方式进行配置绑定：

     使用 @ConfigurationProperties 注解
     使用 @Value 注解

      所谓“配置绑定”就是把配置文件中的值与 JavaBean 中对应的属性进行绑定。通常，我们会把一些配置信息（例如，数据库配置）放在配置文件中，然后通过 Java 代码去读取该配置文件，并且把配置文件中指定的配置封装到 JavaBean（实体类） 中。


# @ConfigurationProperties
* [@ConfigurationProperties ---Spring Boot @ConfigurationProperties example](https://mkyong.com/spring-boot/spring-boot-configurationproperties-example/)
* [Spring Boot读取配置的几种方式](https://mp.weixin.qq.com/s?__biz=MzI3ODcxMzQzMw==&mid=2247484575&idx=1&sn=56c88cd7283374345d891e85a800539b&scene=21#wechat_redirect)
* [@ConfigurationProperties](http://c.biancheng.net/spring_boot/config-bind.html)

用来加载额外的配置（如 .properties 文件），可用在 @Configuration 注解类，或者 @Bean 注解方法上面

将properties属性和一个Bean及其属性关联,从而实现类型安全的配置

@ConfigurationProperties(prefix = "author",locations = {"classpath:config/author.properties"})

通过@ConfigurationProperties加载配置,通过prefix属性指定配置前缀,通过location指定配置文件位置
