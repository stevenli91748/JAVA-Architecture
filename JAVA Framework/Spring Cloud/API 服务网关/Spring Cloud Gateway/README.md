
<a href="https://ibb.co/t8njdPT"><img src="https://i.ibb.co/D5mcqgH/GATEWAY.jpg" alt="GATEWAY" border="0"></a>
<a href="https://ibb.co/Z279qfK"><img src="https://i.ibb.co/Rjd8RgP/GATEWAY1.jpg" alt="GATEWAY1" border="0"></a>

<a href="https://ibb.co/6wMKmTs"><img src="https://i.ibb.co/nmGXbYk/PREDICATE.jpg" alt="PREDICATE" border="0"></a>

---

# 参考书籍

* 重新定义Spring Cloud实战.PDF---在线
* Spring Cloud微服务架构实战派.PDF
* Spring Cloud微服务架构进阶.PDF---在线
--

[Spring Cloud Gateway](https://weread.qq.com/web/reader/c9932ea07163ff6ac993e0dka1d32a6022aa1d0c6e83eb4)|[Spring Cloud Gateway上篇](https://weread.qq.com/web/reader/71d32370716443e271df020kec8325e0266ec89566376b5)|[Spring Cloud Gateway下篇](https://weread.qq.com/web/reader/71d32370716443e271df020k272329d026d2723d092b535)|[Spring Cloud Gateway入门案例](https://weread.qq.com/web/reader/71d32370716443e271df020k65b326f026965b9eea6e6e1)|
---|---|---|---|

[Spring Cloud Gateway实战](https://weread.qq.com/web/reader/71d32370716443e271df020k7f632b502707f6ffaa6bf2e)|
---|

[spring-cloud-gateway-sample](https://github.com/spring-cloud-samples/spring-cloud-gateway-sample)|
---|




# 目录

---Spring Cloud微服务架构实战派.pdf

* 认识微服务网关
  * 认识SpringCloud Gateway---Spring Cloud Gateway旨在提供一种简单而有效的途径来转发请求，并为它们提供横切关注点，例如：安全性、监控/指标和弹性,Gateway网关的定义是网间连接器或协议转化器，其作用就是将两个不同通信协议的网络段连接到一起
    * [SpringCloud Gateway和ZUUL的区别](https://weread.qq.com/web/reader/c9932ea07163ff6ac993e0dk17e328b022b17e62166fad4)
  * [SprlngCloudGateway的工作流程](https://weread.qq.com/web/reader/71d32370716443e271df020kc9e32940268c9e1074f5bc6)
  * 比较Zuul与SpringCloud Gateway
  * [SprlngCloud Gateway源码解析](https://weread.qq.com/web/reader/c9932ea07163ff6ac993e0dk6c8328f022d6c8349cc72d5)
  * SprlngCloud Gateway的功能
    * 路由（Route）---网关对外暴露的URL或者接口信息，我们统称为路由信息, 路由信息由一个ID、一个目的url、一组断言工厂和一组Filter组成。如果路由断言为真，则说明请求的url和配置的路由匹配, 网关中有两个重要的概念，那就是路由配置和路由规则
      * 路由配置---路由配置是指配置某请求路径路由到指定的目的地址
        * [WebSocket的网关路由](https://weread.qq.com/web/reader/c9932ea07163ff6ac993e0dkf7132c6022cf7177163c01c) 
      * 路由规则---路由规则是指匹配到路由配置之后，再根据路由规则进行转发处理 
        * [SprlngCloud Gateway基于服务发现的路由规则 ](https://weread.qq.com/web/reader/71d32370716443e271df020k5f9323e026e5f93f9835418)
        * [ Spring Cloud Gateway权重路由---Spring Cloud Gateway会根据权重路由规则，针对特定的服务，把95%的请求流量分发给服务的V1版本，把剩余5%的流量分发给服务的V2版本，进行权重路由](https://weread.qq.com/web/reader/71d32370716443e271df020k7f632b502707f6ffaa6bf2e)
    * [动态路由---前面章节介绍了Spring Cloud Gateway提供的配置路由规则的两种方法，但都是在Spring Cloud Gateway启动时将路由配置和规则加载到内存里，无法做到不重启网关就可以动态地对应路由的配置和规则进行增加、修改和删除](https://weread.qq.com/web/reader/71d32370716443e271df020k7f632b502707f6ffaa6bf2e)
    * 路由转发
      * 断言（Predicate）---Java 8中的断言函数。Spring Cloud Gateway中的断言函数输入类型是Spring 5.0框架中的ServerWebExchange。Spring CloudGateway中的断言函数允许开发者去定义匹配来自于Http Request中的任何信息，比如请求头和参数等
        * Header路由断言工厂---Header路由断言工厂用于根据配置的路由header信息进行断言匹配路由，匹配成功进行转发，否则不进行转发
        * Cookie路由断言工厂---Cookie路由断言工厂会取两个参数——cookie名称对应的key和value。当请求中携带的cookie和Cookied断言工厂中配置的cookie一致，则路由匹配成功进行转发，否则匹配不成功
        * After路由断言工厂---After Route Predicate Factory中会取一个UTC时间格式的时间参数，当请求进来的当前时间在配置的UTC时间之后，则会成功匹配进行转发，否则不能成功匹配。
        * Before路由断言工厂---Before路由断言工厂会取一个UTC时间格式的时间参数，当请求进来的当前时间在路由断言工厂之前会成功匹配进行转发，否则不能成功匹配
        * Between路由断言工厂---Between路由断言工厂会取一个UTC时间格式的时间参数，当请求进来的当前时间在配置的UTC时间工厂之间会成功匹配进行转发，否则不能成功匹配
        * Host路由断言工厂 ---Host路由断言工厂根据配置的Host，对请求中的Host进行断言处理，断言成功则进行路由转发，否则不转发
        * Method路由断言工厂---Method路由断言工厂会根据路由信息配置的method对请求方法是Get或者Post等进行断言匹配，匹配成功则进行转发，否则处理失败
        * Query路由断言工厂---Query路由断言工厂会从请求中获取两个参数，将请求中参数和Query断言路由中的配置进行匹配，比如http://localhost:8080/?foo=baz中的foo=baz和下面的r.query("foo", "baz")配置一致则进行转发，否则转发失败
        * RemoteAddr路由断言工厂---RemoteAddr路由断言工厂配置一个IPv4或IPv6网段的字符串或者IP。当请求IP地址在网段之内或者和配置的IP相同，则表示匹配成功进行转发，否则不能转发
      * 过滤器（Filter）----一个标准的Spring webFilter。Spring Cloud Gateway中的Filter分为两种类型的Filter，分别是Gateway Filter和Global Filter。路由过滤器允许以某种方式修改请求进来的http请求或返回的http响应。路由过滤器主要作用于需要处理的特定路由
        * Gateway Filter---Gateway Filter是从Web Filter中复制过来的，相当于一个Filter过滤器，可以对访问的URL过滤，进行横切处理（切面处理），应用场景包括超时、安全等
        * Global Filter---可以自定义实现自己的Global Filter。Global Filter是一个全局的Filter，作用于所有路由。
        * 过滤器的应用开发方式
          * [基于自定义过滤器接口](https://weread.qq.com/web/reader/c9932ea07163ff6ac993e0dkd9d320f022ed9d4f495e456)
          * [基于内置的限流过滤器配置实现](https://weread.qq.com/web/reader/c9932ea07163ff6ac993e0dkd9d320f022ed9d4f495e456)
        * 过滤器分7类---过滤器的实现类将近二十多个。总得来说，可以分为七类：Header、Parameter、Path、Status、Redirect跳转、Hytrix熔断和RateLimiter
          * Header
            * [AddRequestHeader过滤器工厂 ---AddRequestHeader过滤器工厂用于对匹配上的请求加上header ](https://weread.qq.com/web/reader/71d32370716443e271df020ka973204026ba97da629bd12)
            * [AddResponseHeader过滤器---AddResponseHeader过滤器工厂的作用是对从网关返回的响应添加Header](https://weread.qq.com/web/reader/71d32370716443e271df020ka973204026ba97da629bd12)
          * Parameter
            * [AddRequestParameter过滤器---AddRequestParameter过滤器作用是对匹配上的请求路由添加请求参数 ](https://weread.qq.com/web/reader/71d32370716443e271df020ka973204026ba97da629bd12)
          * Path
            * [RewritePath过滤器---Spring Cloud Gateway可以使用RewritePath替换Zuul的StripPrefix功能，而且功能更强大 ](https://weread.qq.com/web/reader/71d32370716443e271df020ka973204026ba97da629bd12)
            * [StripPrefix过滤器---StripPrefixGatewayFilterFactory是一个对针对请求url前缀进行处理的filter工厂，用于去除前缀。而PrefixPathGatewayFilterFactory是用于增加前缀](https://weread.qq.com/web/reader/71d32370716443e271df020ka973204026ba97da629bd12)
          * Status
          * Redirect跳转
          * Hytrix熔断
            * [Hystrix过滤器--Spring Cloud Gateway对Hystrix进行集成提供路由层面的服务熔断和降级，最简单的使用场景是当通过Spring CloudGateway调用后端服务，后端服务一直出现异常、服务不可用的状态。此时为了提高用户体验，就需要对服务降级，返回友好的提示信息，在保护网关自身可用的同时保护后端服务高可用](https://weread.qq.com/web/reader/71d32370716443e271df020ka973204026ba97da629bd12) 
            * [ 如何使用Hystrix过滤器熔断降级](https://weread.qq.com/web/reader/c9932ea07163ff6ac993e0dkd9d320f022ed9d4f495e456)
          * RateLimiter
      ---
           * [Retry过滤器---网关作为所有请求流量的入口，网关对路由进行协议适配和协议转发处理的过程中，如果出现异常或网络抖动，为了保证后端服务请求的高可用，一般处理方式会对网络请求进行重试](https://weread.qq.com/web/reader/71d32370716443e271df020ka973204026ba97da629bd12)
           * [网关重试过滤器](https://weread.qq.com/web/reader/c9932ea07163ff6ac993e0dkd9d320f022ed9d4f495e456)
    * 单点入口
    * 协议适配
    * 协议转发---在微服务的应用集群中会存在很多服务提供者和服务消费者，而这些服务提供者和服务消费者基本都是部署在企业内网中，没必要全部加Https进行调用，Spring Cloud Gateway对外部的请求协议是Https，对内部后端代理服务的请求协议是Http
      * [Spring Cloud Gateway中Https的使用技巧---Spring Cloud Gateway将Https前端请求转发调用Http后端服务的问题。](https://weread.qq.com/web/reader/71d32370716443e271df020k7f632b502707f6ffaa6bf2e) 
      * [Https转Http的问题处理思路 http://github.com/spring-cloud/spring-cloud-gateway/issues/160, http://github.com/spring-cloud/spring-cloud-gateway/issues/378 ](https://weread.qq.com/web/reader/71d32370716443e271df020k7f632b502707f6ffaa6bf2e)
    * 防刷
    * 流量控制
      * 熔断
      * [限流](https://weread.qq.com/web/reader/c9932ea07163ff6ac993e0dkd9d320f022ed9d4f495e456)
        * 限流的需求因素---在实际项目应用中对网关进行限流时，需要参考的因素比较多。可能会根据网络请求连接数、请求流量、CPU使用率等流控
          * 限制总并发数（比如数据库连接池、线程池）
          * 限制瞬时并发数（如nginx的limit_conn模块，用来限制瞬时并发连接数）
          * 限制时间窗口内的平均速率（如Guava的RateLimiter、nginx的limit_req模块，限制每秒的平均速率）
          * 限制远程接口调用速率
          * 限制MQ的消费速率
          * 对网络请求连接数进行限流
          * 对请求流量进行限流
            * 对请求的IP限流
            * 对请求的用户限流
            * 对请求的接口限流
          * [对CPU使用率进行限流](https://weread.qq.com/web/reader/71d32370716443e271df020k7f632b502707f6ffaa6bf2e)
          * 对内存负载进行限流
        * [限流算法](https://weread.qq.com/web/reader/c9932ea07163ff6ac993e0dkd9d320f022ed9d4f495e456)
          * 令牌桶算法
          * 漏桶算法  
        * 两种限流模式：
          * 控制速率模式
          * 控制并发模式
        * 单机网关限流  
          * 在Spring Cloud Gateway中实现限流的方法---在单机网关限流中用Spring Cloud Gateway中实现限流比较简单，只需要编写一个过滤器就可以。Guava中的RateLimiter、Bucket4j、RateLimitJ都是基于令牌桶算法实现的限流工具
            * 自定义过滤器实现限流 
              * 在单机网关限流中用Guava中的RateLimiter在Spring CloudGateway中实现限流
              * [在单机网关限流中用Guava中Bucket4j在Spring CloudGateway中实现限流](https://weread.qq.com/web/reader/71d32370716443e271df020k7f632b502707f6ffaa6bf2e)
              * 在单机网关限流中用Guava中RateLimitJ在Spring CloudGateway中实现限流
            * SprlngCloud Gateway内置过滤器工厂限流
              * [Spring CloudGateway内置了一个名为RequestRateLimiterGatewayFilterFactory的过滤器工厂，可以用来直接限流 ](https://weread.qq.com/web/reader/71d32370716443e271df020k7f632b502707f6ffaa6bf2e)
        * 分布式集群限流--采用Redis等分布式解决方案
    * 监控
      * 监控日志
    * 安全认证
      * 身份验证 
    * 应用检测
* 高可用
* Spring Cloud Gateway endpoint---Spring Cloud Gateway提供了内置的端点，用于提供路由相关的操作，如过滤器列表、路由列表、单个路由信息等等,Spring Cloud Gateway的内置端点纳管到Spring Boot-Actuator中。从网关服务的启动日志，可以看到网关的内置端点
  

----（Sprng Cloud Alibaba）深入理解springcloud与实战

* Spring Cloud Gateway整合注册中心---网关与服务注册与发现组件进行结合，通过serviceId转发到具体的服务实例。
* Spring Cloud Gateway配置中心
* Spring Cloud Gateway整合Hystrix断路器功能
* [Spring Cloud Gateway整合Swagger](https://weread.qq.com/web/reader/71d32370716443e271df020k7f632b502707f6ffaa6bf2e)









# 参考

* [Spring cloud gateway 详解和配置使用（文章较长）](https://blog.csdn.net/qq_38380025/article/details/102968559)
* [Spring cloud gateway 设置https 和http同时支持](https://blog.csdn.net/qq_38380025/article/details/106255171)
* [spring cloud gateway 处理跨域问题](https://blog.csdn.net/qq_38380025/article/details/104936705)
* [Spring cloud Gateway 指定执行过滤器 （在配置文件中配置所需要过滤器）](https://blog.csdn.net/qq_38380025/article/details/104894916)
* [spring cloud gateway 修改response 截断问题，乱码问题解决](https://blog.csdn.net/qq_38380025/article/details/100032490)
* [Spring cloud gateway 拦截请求404 等HTTP 状态码](https://blog.csdn.net/qq_38380025/article/details/104755040)
