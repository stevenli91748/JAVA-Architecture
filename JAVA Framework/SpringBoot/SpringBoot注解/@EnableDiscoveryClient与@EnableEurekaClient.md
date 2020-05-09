


# @EnableDiscoveryClient与@EnableEurekaClient

开启客户端

@EnableDiscoveryClient注解是基于spring-cloud-commons依赖，并且在classpath中实现；

@EnableEurekaClient注解是基于spring-cloud-netflix`依赖，只能为eureka作用；

如果你的classpath中添加了eureka，则它们的作用是一样的。
