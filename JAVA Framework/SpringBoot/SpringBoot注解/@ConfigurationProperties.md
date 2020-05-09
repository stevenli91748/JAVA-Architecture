



# @ConfigurationProperties

将properties属性和一个Bean及其属性关联,从而实现类型安全的配置

@ConfigurationProperties(prefix = "author",locations = {"classpath:config/author.properties"})

通过@ConfigurationProperties加载配置,通过prefix属性指定配置前缀,通过location指定配置文件位置
