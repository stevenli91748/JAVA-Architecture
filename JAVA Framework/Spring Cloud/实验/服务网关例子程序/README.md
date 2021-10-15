
# Spring Cloud Gateway
* 整合 Spring Cloud 组件
  * 整合服务注册中心
    * Spring Cloud Gateway整合Eureka例子
    * Spring Cloud Gateway整合Consul例子
    * Spring Cloud Gateway整合Nacos例子  
  * 整合Hystrix的断路器功能例子
  * [Spring Cloud Gateway集成Swagger例子](https://weread.qq.com/web/reader/71d32370716443e271df020k7f632b502707f6ffaa6bf2e)
* 限流
  * Spring Cloud Gateway IP限流例子
  * Spring Cloud Gateway 用户限流例子
  * Spring Cloud Gateway 接口限流例子
  * 单机网关限流 --- Guava中的RateLimiter、Bucket4j、RateLimitJ都是基于令牌桶算法实现的限流工具
    * 自定义过滤器实现限流
      * Guava中的RateLimiter在Spring CloudGateway中实现限流例子
      * [Guava中Bucket4j在Spring CloudGateway中实现限流例子](https://weread.qq.com/web/reader/71d32370716443e271df020k7f632b502707f6ffaa6bf2e)
      * Guava中RateLimitJ在Spring CloudGateway中实现限流例子
    * SprlngCloud Gateway内置过滤器工厂限流
      * [Spring CloudGateway内置了一个名为RequestRateLimiterGatewayFilterFactory的过滤器工厂，可以用来直接限流例子  ](https://weread.qq.com/web/reader/71d32370716443e271df020k7f632b502707f6ffaa6bf2e)
  * 分布式集群限流--采用Redis等分布式解决方案
* 断言（Predicate）
  * Header路由断言工厂例子
  * Cookie路由断言工厂例子
  * After路由断言工厂例子
  * Before路由断言工厂例子
  * Between路由断言工厂例子
  * Host路由断言工厂例子 
  * Method路由断言工厂例子
  * Query路由断言工厂例子
  * RemoteAddr路由断言工厂例子
* 过滤器（Filter）
  * [Global Filter](https://weread.qq.com/web/reader/71d32370716443e271df020k6983268026f698d51a198ff)
  * [Gateway Filter 例子](https://weread.qq.com/web/reader/71d32370716443e271df020k6983268026f698d51a198ff)
  * AddRequestHeader过滤器工厂例子
  * AddResponseHeader过滤器例子
  * AddRequestParameter过滤器例子
  * RewritePath过滤器例子
  * StripPrefix过滤器例子
  * Hystrix过滤器例子
  * Retry过滤器例子
* 路由
  * [Gateway基于服务发现的路由规则例子](https://weread.qq.com/web/reader/71d32370716443e271df020k5f9323e026e5f93f9835418)
  * [权重路由案例例子](https://weread.qq.com/web/reader/71d32370716443e271df020k7f632b502707f6ffaa6bf2e)
* 协议转发
  * [Spring Cloud Gateway中Https的使用技巧---Spring Cloud Gateway将Https前端请求转发调用Http后端服务的问题](https://weread.qq.com/web/reader/71d32370716443e271df020k7f632b502707f6ffaa6bf2e)  
