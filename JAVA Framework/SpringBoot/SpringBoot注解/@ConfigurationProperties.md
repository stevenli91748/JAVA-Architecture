



# @ConfigurationProperties

[Spring Boot读取配置的几种方式](https://mp.weixin.qq.com/s?__biz=MzI3ODcxMzQzMw==&mid=2247484575&idx=1&sn=56c88cd7283374345d891e85a800539b&scene=21#wechat_redirect)

用来加载额外的配置（如 .properties 文件），可用在 @Configuration 注解类，或者 @Bean 注解方法上面

将properties属性和一个Bean及其属性关联,从而实现类型安全的配置

@ConfigurationProperties(prefix = "author",locations = {"classpath:config/author.properties"})

通过@ConfigurationProperties加载配置,通过prefix属性指定配置前缀,通过location指定配置文件位置
