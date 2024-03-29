# ZUUL
  * [Spring Cloud Zuul服务网关快速入门例子](https://mrbird.cc/Spring-Cloud-Zuul-Router.html)

# Spring Cloud Gateway
* [Gateway实例](https://weread.qq.com/web/reader/1c632610725a69631c6c26fkd8232f00235d82c8d161fb2)
* 开发者如何通过响应式、函数式和Lambda表达式灵活地构建微服务网关
* 整合 Spring Cloud 组件
  * 整合服务注册中心
    * Spring Cloud Gateway整合Eureka例子
      * Spring Cloud Gateway整合单个Eureka例子
      * Spring Cloud Gateway整合Eureka集群例子
    * Spring Cloud Gateway整合单个Consul例子
      * Spring Cloud Gateway整合Consul例子
        * [Spring Cloud Gateway 注册到服务中心 Consul，代理服务中心的所有服务](https://juejin.cn/post/6844903945341501447)
      * Spring Cloud Gateway整合Consul集群例子
    * Spring Cloud Gateway整合alibaba Nacos例子  
      * Spring Cloud Gateway整合alibaba Nacos注册中心例子
      * Spring Cloud Gateway整合alibaba Nacos注册中心集群例子
    * [服务网关如何配合服务注册中心进行路由转发实例](https://weread.qq.com/web/reader/1223205071ccfab912296c2ke0032e0028be00da03b6659)
  * 整合配置中心
    * Spring Cloud Config
    * Nacos配置中心 
  * 整合监控中心
    * Spring Boot Admin
    * Skywalking
    * ZipKin +  Sleuth 
  * 整合日志中心
    * ELK(Logstash采集 ---->ElasticSearch 存储----> Kibana 显示)
    * ELK2(filebeat采集 ---> Kafka消息----> Logstash采集 ---->ElasticSearch存储  ----> Kibana显示) 
  * 整合负载均衡
    * Ribbon 
    * Loadbalance
  * 整合限流,降级，溶断
    * Sentiel
    *  
    * 整合Hystrix的断路器功能例子
  * 整合服务调用
    * 整合Feign
    * 整合OpenFeign 
  * [Spring Cloud Gateway整合Swagger例子](https://weread.qq.com/web/reader/71d32370716443e271df020k7f632b502707f6ffaa6bf2e)
* 限流
  * 限流的需求---在实际项目应用中对网关进行限流时，需要参考的因素比较多。可能会根据网络请求连接数、请求流量、CPU使用率等流控
    * 限制总并发数（比如数据库连接池、线程池）
    * 限制瞬时并发数（如nginx的limit_conn模块，用来限制瞬时并发连接数）
    * 限制时间窗口内的平均速率（如Guava的RateLimiter、nginx的limit_req模块，限制每秒的平均速率）
    * 限制远程接口调用速率
    * 限制MQ的消费速率
    * 对网络请求连接数进行限流
    * [对CPU使用率进行限流](https://weread.qq.com/web/reader/71d32370716443e271df020k7f632b502707f6ffaa6bf2e)
    * 对请求流量进行限流 
      * Spring Cloud Gateway 对请求IP限流例子
      * Spring Cloud Gateway 对请求用户限流例子
      * Spring Cloud Gateway 对请求接口限流例子
  * 单机网关限流 --- Guava中的RateLimiter、Bucket4j、RateLimitJ都是基于令牌桶算法实现的限流工具
    * 自定义过滤器实现限流
      * Guava中的RateLimiter在Spring CloudGateway中实现限流例子
      * [Guava中Bucket4j在Spring CloudGateway中实现限流例子](https://weread.qq.com/web/reader/71d32370716443e271df020k7f632b502707f6ffaa6bf2e)
      * Guava中RateLimitJ在Spring CloudGateway中实现限流例子
    * SprlngCloud Gateway内置过滤器工厂限流
      * [Spring CloudGateway内置了一个名为RequestRateLimiterGatewayFilterFactory的过滤器工厂，可以用来直接限流例子  ](https://weread.qq.com/web/reader/71d32370716443e271df020k7f632b502707f6ffaa6bf2e)
  * 分布式集群限流--采用Redis等分布式解决方案
* 路由
  * 路由配置---路由配置是指配置某请求路径路由到指定的目的地址
    * [基于WebSocket的网关路由](https://weread.qq.com/web/reader/c9932ea07163ff6ac993e0dkf7132c6022cf7177163c01c) 
    * [基于微服务实例进行路由 9.6.1](https://weread.qq.com/web/reader/2fe329c071e041322feb53dkc7432af0210c74d97b01b1c)
    * [基于服务发现进行路由](https://weread.qq.com/web/reader/2fe329c071e041322feb53dkc7432af0210c74d97b01b1c)
  * 路由规则---路由规则是指匹配到路由配置之后，再根据路由规则进行转发处理 
    * [Gateway基于服务发现的路由规则例子](https://weread.qq.com/web/reader/71d32370716443e271df020k5f9323e026e5f93f9835418)
    * [权重路由案例例子](https://weread.qq.com/web/reader/71d32370716443e271df020k7f632b502707f6ffaa6bf2e)
  * [动态路由---前面章节介绍了Spring Cloud Gateway提供的配置路由规则的两种方法，但都是在Spring Cloud Gateway启动时将路由配置和规则加载到内存里，无法做到不重启网关就可以动态地对应路由的配置和规则进行增加、修改和删除](https://weread.qq.com/web/reader/71d32370716443e271df020k7f632b502707f6ffaa6bf2e)
    * [度量和动态更新路由 9.6.2](https://weread.qq.com/web/reader/2fe329c071e041322feb53dkc7432af0210c74d97b01b1c) 
  * 路由转发
    * 断言（Predicate）
      * [断言的开发既可以使用编码的方式实现，也可以使用配置的方式实现](https://weread.qq.com/web/reader/2fe329c071e041322feb53dkc7432af0210c74d97b01b1c)
      * Header路由断言工厂例子
      * Cookie路由断言工厂例子
      * After路由断言工厂例子
      * Before路由断言工厂例子
      * Between路由断言工厂例子
      * Host路由断言工厂例子 
      * Method路由断言工厂例子
      * Query路由断言工厂例子
      * RemoteAddr路由断言工厂例子
      * Weight路由断言工厂
      * [Path 路由断言工厂](https://weread.qq.com/web/reader/1223205071ccfab912296c2k76d325c028076dc611d6d8c)
    * 过滤器（Filter）
      * [Global Filter](https://weread.qq.com/web/reader/71d32370716443e271df020k6983268026f698d51a198ff)
      * [Gateway Filter 例子](https://weread.qq.com/web/reader/71d32370716443e271df020k6983268026f698d51a198ff)
      * [基于自定义过滤器接口](https://weread.qq.com/web/reader/c9932ea07163ff6ac993e0dkd9d320f022ed9d4f495e456)
      * [基于内置的限流过滤器配置实现](https://weread.qq.com/web/reader/c9932ea07163ff6ac993e0dkd9d320f022ed9d4f495e456)
      * 内置过滤器
        * [AddRequestHeader过滤器工厂例子](https://weread.qq.com/web/reader/2fe329c071e041322feb53dkc7432af0210c74d97b01b1c)
        * AddResponseHeader过滤器例子
        * [AddRequestParameter过滤器例子](https://weread.qq.com/web/reader/2fe329c071e041322feb53dkc7432af0210c74d97b01b1c)
        * RewritePath过滤器例子
        * StripPrefix过滤器例子
        * Hystrix过滤器例子
        * [RequestRateLimiter过滤器工厂](https://weread.qq.com/web/reader/2fe329c071e041322feb53dkc7432af0210c74d97b01b1c)
        * [Retry过滤器例子](https://weread.qq.com/web/reader/2fe329c071e041322feb53dkc7432af0210c74d97b01b1c)
      * 自定义过滤器接口
        * [自定义过滤器,使用Resilience4j限流 ](https://weread.qq.com/web/reader/2fe329c071e041322feb53dkc7432af0210c74d97b01b1c)
  * 协议转发
    * [Spring Cloud Gateway中Https的使用技巧---Spring Cloud Gateway将Https前端请求转发调用Http后端服务的问题](https://weread.qq.com/web/reader/71d32370716443e271df020k7f632b502707f6ffaa6bf2e)  
