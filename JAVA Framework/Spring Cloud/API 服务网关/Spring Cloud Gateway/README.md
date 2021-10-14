
<a href="https://ibb.co/t8njdPT"><img src="https://i.ibb.co/D5mcqgH/GATEWAY.jpg" alt="GATEWAY" border="0"></a>
<a href="https://ibb.co/Z279qfK"><img src="https://i.ibb.co/Rjd8RgP/GATEWAY1.jpg" alt="GATEWAY1" border="0"></a>

<a href="https://ibb.co/6wMKmTs"><img src="https://i.ibb.co/nmGXbYk/PREDICATE.jpg" alt="PREDICATE" border="0"></a>

---

[Spring Cloud Gateway](https://weread.qq.com/web/reader/c9932ea07163ff6ac993e0dka1d32a6022aa1d0c6e83eb4)|[Spring Cloud Gateway上篇](https://weread.qq.com/web/reader/71d32370716443e271df020kec8325e0266ec89566376b5)|[Spring Cloud Gateway下篇](https://weread.qq.com/web/reader/71d32370716443e271df020k272329d026d2723d092b535)|
---|---|---|

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
* 过滤器（Filter）----一个标准的Spring webFilter。Spring Cloud Gateway中的Filter分为两种类型的Filter，分别是Gateway Filter和Global Filter。过滤器Filter将会对请求和响应进行修改处理
* 高可用
* Spring Cloud Gateway endpoint

----（Sprng Cloud Alibaba）深入理解springcloud与实战

* 整合注册中心和配置中心
* 整合Hystrix的断路器功能



# 参考

* [Spring cloud gateway 详解和配置使用（文章较长）](https://blog.csdn.net/qq_38380025/article/details/102968559)
