


# @EnableDiscoveryClient与@EnableEurekaClient

[spring cloud服务发现注解之@EnableDiscoveryClient与@EnableEurekaClient](https://blog.csdn.net/u012734441/article/details/78256256?locationNum=1&fps=1)

开启客户端

@EnableDiscoveryClient注解是基于spring-cloud-commons依赖，并且在classpath中实现；

@EnableEurekaClient注解是基于spring-cloud-netflix`依赖，只能为eureka作用；

如果你的classpath中添加了eureka，则它们的作用是一样的。
