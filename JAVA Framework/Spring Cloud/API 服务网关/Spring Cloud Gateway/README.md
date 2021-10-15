
<a href="https://ibb.co/t8njdPT"><img src="https://i.ibb.co/D5mcqgH/GATEWAY.jpg" alt="GATEWAY" border="0"></a>
<a href="https://ibb.co/Z279qfK"><img src="https://i.ibb.co/Rjd8RgP/GATEWAY1.jpg" alt="GATEWAY1" border="0"></a>

<a href="https://ibb.co/6wMKmTs"><img src="https://i.ibb.co/nmGXbYk/PREDICATE.jpg" alt="PREDICATE" border="0"></a>

---

[Spring Cloud Gateway](https://weread.qq.com/web/reader/c9932ea07163ff6ac993e0dka1d32a6022aa1d0c6e83eb4)|[Spring Cloud Gateway上篇](https://weread.qq.com/web/reader/71d32370716443e271df020kec8325e0266ec89566376b5)|[Spring Cloud Gateway下篇](https://weread.qq.com/web/reader/71d32370716443e271df020k272329d026d2723d092b535)|[Spring Cloud Gateway入门案例](https://weread.qq.com/web/reader/71d32370716443e271df020k65b326f026965b9eea6e6e1)|
---|---|---|---|

[spring-cloud-gateway-sample](https://github.com/spring-cloud-samples/spring-cloud-gateway-sample)|
---|

# 目录

---Spring Cloud微服务架构实战派.pdf

* 认识微服务网关
  * 认识SpringCloudGateway 
  * [SprlngCloudGateway的工作流程](https://weread.qq.com/web/reader/71d32370716443e271df020kc9e32940268c9e1074f5bc6)
  * 比较Zuul与SpringCloud Gateway
  * SprlngCloudGateway的功能
    * 路由转发
    * 单点入口
    * 协议适配
    * 协议转发
    * 防刷
    * 流量控制
      * 熔断
      * 限流
        * IP限流
        * 用户限流
        * 接口限流  
    * 监控日志
    * 安全认证
* 路由（Route）---网关对外暴露的URL或者接口信息，我们统称为路由信息, 路由信息由一个ID、一个目的url、一组断言工厂和一组Filter组成。如果路由断言为真，则说明请求的url和配置的路由匹配
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
  * 过滤器分7类---过滤器的实现类将近二十多个。总得来说，可以分为七类：Header、Parameter、Path、Status、Redirect跳转、Hytrix熔断和RateLimiter
    * Header
    * Parameter
    * Path
    * Status
    * Redirect跳转
    * Hytrix熔断
    * RateLimiter
* 高可用
* Spring Cloud Gateway endpoint

----（Sprng Cloud Alibaba）深入理解springcloud与实战

* 整合注册中心和配置中心
* 整合Hystrix的断路器功能



# 参考

* [Spring cloud gateway 详解和配置使用（文章较长）](https://blog.csdn.net/qq_38380025/article/details/102968559)
