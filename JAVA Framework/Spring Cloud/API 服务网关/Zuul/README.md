


# 目录
* [Spring Cloud Zuul服务网关快速入门例子](https://mrbird.cc/Spring-Cloud-Zuul-Router.html)
* [ZUUL概念](#ZUUL概念)
  * [Zuul和Nginx区别](#Zuul和Nginx区别)
  * [Zuul和Ribbon实现负载均衡区别](#Zuul和Ribbon实现负载均衡区别)
  * [Zuul 详解，带视频---疯狂创客圈](https://www.cnblogs.com/crazymakercircle/p/12046484.html)
  * [Zuul 修改 请求头、响应头 （死磕）---疯狂创客圈](https://www.cnblogs.com/crazymakercircle/p/12037587.html)
  * [Zuul Swagger 整合---疯狂创客圈](https://www.cnblogs.com/crazymakercircle/p/12046131.html)
* ZUUL具备的功能
  * 认证与鉴权
    * [Spring Cloud认证鉴权实战案例2---Zuul+OAuth2.0+JWT实战](https://weread.qq.com/web/reader/71d32370716443e271df020k64232b60230642e92efb54c) 
  * 压力控制
  * 金丝雀测试
  * 动态路由
  * 负载削减
  * 静态响应处理
  * 主动流量管理
* [Spring Cloud Zuul Filter链](https://weread.qq.com/web/reader/71d32370716443e271df020k67c32d7022f67c6a1e7ce82)
  * [微服务网关Zuul过滤器Filter](https://www.cnblogs.com/Courage129/p/14517275.html) 
* [Spring Cloud Zuul权限集成](https://weread.qq.com/web/reader/71d32370716443e271df020k64232b60230642e92efb54c)
* [Spring Cloud Zuul限流](https://weread.qq.com/web/reader/71d32370716443e271df020kf4532c00231f457c545ad3c)
* [Spring Cloud Zuul动态路由](https://weread.qq.com/web/reader/71d32370716443e271df020kc0c320a0232c0c7c76d365a)
* [Spring Cloud Zuul文件上传](https://weread.qq.com/web/reader/71d32370716443e271df020k9a132c802349a1158154a83)
* [Spring Cloud Zuul多层负载](https://weread.qq.com/web/reader/71d32370716443e271df020k9f6326602389f61408e3715)
* [Spring Cloud Zuul实用小技巧](https://weread.qq.com/web/reader/71d32370716443e271df020kd8232f00235d82c8d161fb2)
* [Spring Cloud Zuul应用优化](https://weread.qq.com/web/reader/71d32370716443e271df020k72b327f023972b32a1f7e2d)
* [Spring Cloud Zuul灰度发布](https://weread.qq.com/web/reader/71d32370716443e271df020k283328802332838023a7529)

--- Spring Cloud微服务架构开发实战.pdf

* 启用Zuul 路由服务
* 路由配置规则
* Zuul 路由其他设置
* Zuul 容错与回退
* Zuul 过滤器
* @EnableZuu!Server 与＠EnableZuulProxy 比较

---

## ZUUL概念

**应用场景:**

假如当前有十几个微服务服务，订单，商品，用户等等，显然是客户端不需要和每个服务逐一打交道，这就需要有一个统一入口，它就是服务网关。API网关所有的客户端请求通过这个网关访问后台的服务。他可以使用一定的路由配置来判断某一个URL由哪个服务来处理。并从Eureka获取注册的服务来转发请求。

**核心功能:**

Zuul包含了对请求的路由和过滤两个最主要的功能，是各种服务的统一入口，同时还会用来提供监控、授权、安全、调度等等。
路由功能：负责将外部请求转发到具体的微服务实例上，是实现外部访问统一入口的基础。
过滤器功能：则负责对请求的处理过程进行干预，是实现请求校验、服务聚合等功能的基础。
Zuul和Eureka进行整合：将Zuul自身注册为Eureka服务治理下的应用，同时从Eureka中获得其他微服务的消息,也即以后的访问微服务都是通过Zuul跳转后获得。

**注意：Zuul服务最终还是会注册进Eureka，提供代理+路由+过滤三大功能。**

**核心原理**

Zuul的核心是一系列的filters，其作用可以类比Servlet框架的Filter，或者AOP。
过滤器之间没有直接进行通信,而是通过Request Context（上下文）进行数据传递。
Zuul的过滤器是由Groovy写成，这些过滤器文件被放在Zuul Server上的特定目录下面，Zuul会定期轮询这些目录，修改过的过滤器会动态的加载到Zuul Server中以便过滤请求使用。

**Zuul负载均衡：**

Zuul拦截对应的API前缀请求做转发，转发到对应的serverId上，在Eureka服务上同一个serverId可以对应多个服务，也就是说用同一个服务节点不同的端口注册两个实例，但是serverId是一样Zuul做转发的时候会结合eureka-server起到负载均衡的效果。

**过滤器的种类：**

PRE（前置）：这种过滤器在请求被路由之前调用。我们可利用这种过滤器实现鉴权、限流、参数校验调整等。
ROUTING（路由）：这种过滤器将请求路由到微服务。这种过滤器用于构建发送给微服务的请求，并使用Apache HttpClient或Netfilx Ribbon请求微服务。
POST（后置）：这种过滤器在路由到微服务以后执行。这种过滤器可用来为响应添加标准的HTTP Header、收集统计信息和指标、将响应从微服务发送给客户端、日志等。
ERROR（错误）：在其他阶段发生错误时执行该过滤器。

## Zuul和Nginx区别

Zuul虽然在性能上和Nginx没法比，但它也有它的优点。Zuul提供了认证鉴权，动态路由，监控，弹性，安全，负载均衡等边缘服务，在团队规模不大的情况下，没有专门负责路由开发时，使用Zuul当网关是一个快速上手的好方案。

Nginx和Zuul是可以配合使用的，发挥各自的优点，使用Nginx作为负载均衡实现高并发的请求转发，Zuul用作网关。

## Zuul和Ribbon实现负载均衡区别

Zuul支持Ribbon和Hystrix，也能够实现客户端的负载均衡。我们的Feign不也是实现客户端的负载均衡和Hystrix的吗？既然Zuul已经能够实现了，那我们的Feign还有必要吗？

可以这样理解：

**Zuul是对外暴露的唯一接口相当于路由的是controller的请求，而Ribbonhe和Fegin路由了service的请求。**

**Zuul做最外层请求的负载均衡，而Ribbon和Fegin做的是系统内部各个微服务的service的调用的负载均衡。**



# 参考
* [1. Zuul 网关入门，带视频  ](https://www.cnblogs.com/crazymakercircle/p/12046484.html)
* [2. Zuul 修改 请求头、响应头 （死磕）](https://www.cnblogs.com/crazymakercircle/p/12037587.html)
* [3. Zuul 整合 Swagger 方便开发和调试](https://www.cnblogs.com/crazymakercircle/p/12046131.html)
* [Netflix Zuul与Nginx的性能对比](http://blog.didispace.com/zuul-vs-nginx-performance/)
* [zuul和nginx](https://zhuanlan.zhihu.com/p/37385481)
* [API网关 Zuul1.0 和 2.0 我们该如何选择？](http://blog.didispace.com/api-gateway-Zuul-1-zuul-2-how-to-choose/)
* [开源：API文档汇总管理工具Swagger Butler](http://blog.didispace.com/opensource-api-mgt-Swagger-Butler/)
* [Spring Cloud Zuul中使用Swagger汇总API接口文档](http://blog.didispace.com/Spring-Cloud-Zuul-use-Swagger-API-doc/)
* [Spring Cloud Zuul重试机制探秘](http://blog.didispace.com/spring-cloud-zuul-retry-detail/)
* [Spring Cloud Zuul的fallback优化](http://blog.didispace.com/spring-cloud-zuul-fallback-improve/)
* [SpringCloud实战小贴士：Zuul的路径匹配](http://blog.didispace.com/spring-cloud-tips-zuul-path-config/)
* [Spring Cloud实战小贴士：Zuul的饥饿加载（eager-load）使用](http://blog.didispace.com/spring-cloud-tips-zuul-eager/)
* [Spring Cloud构建微服务架构：服务网关（过滤器）](http://blog.didispace.com/spring-cloud-starter-dalston-6-3/)
* [Spring Cloud构建微服务架构：服务网关（路由配置）](http://blog.didispace.com/spring-cloud-starter-dalston-6-2/)
* [Spring Cloud构建微服务架构：服务网关（基础）](http://blog.didispace.com/spring-cloud-starter-dalston-6-1/)
* [Spring Cloud实战小贴士：Zuul统一异常处理（三）](http://blog.didispace.com/spring-cloud-zuul-exception-3/)
* [Spring Cloud实战小贴士：Zuul统一异常处理（二）](http://blog.didispace.com/spring-cloud-zuul-exception-2/)
* [Spring Cloud实战小贴士：Zuul统一异常处理（一）](http://blog.didispace.com/spring-cloud-zuul-exception/)
* [Spring Cloud源码分析（四）Zuul：核心过滤器](http://blog.didispace.com/spring-cloud-source-zuul/)
* [Spring Cloud实战小贴士：Zuul处理Cookie和重定向](http://blog.didispace.com/spring-cloud-zuul-cookie-redirect/)
* [Netflix Zuul与Nginx的性能对比](http://blog.didispace.com/zuul-vs-nginx-performance/)
* [Spring Cloud构建微服务架构（五）服务网关](http://blog.didispace.com/springcloud5/)
* [Spring Cloud中Zuul网关原理及其配置，看它就够了](https://developer.51cto.com/art/202104/658258.htm)
