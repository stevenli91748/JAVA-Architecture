# 目录

* [1. Eureka服务注册中心](#1-Eureka服务注册中心)

# 1 Eureka服务注册中心

Spring Cloud 封装了 Netflix 公司开发的 Eureka 模块来实现服务注册和发现。Eureka 采用了 C-S 的设计架构。Eureka Server 作为服务注册功能的服务器，它是服务注册中心。而系统中的其他微服务，使用 Eureka 的客户端连接到 Eureka Server，并维持心跳连接。这样系统的维护人员就可以通过 Eureka Server 来监控系统中各个微服务是否正常运行。Spring Cloud 的一些其他模块（比如Zuul）就可以通过 Eureka Server 来发现系统中的其他微服务，并执行相关的逻辑。

Eureka由两个组件组成：Eureka服务器和Eureka客户端。Eureka服务器用作服务注册服务器。Eureka客户端是一个java客户端，用来简化与服务器的交互、作为轮询负载均衡器，并提供服务的故障切换支持。Netflix在其生产环境中使用的是另外的客户端，它提供基于流量、资源利用率以及出错状态的加权负载均衡。





# 有用的参考

* [Eureka服务注册中心环境配置](https://www.cnblogs.com/crazymakercircle/p/12043538.html)
* [Eureka 入门，带视频](https://www.cnblogs.com/crazymakercircle/p/12043538.html)
* [开放一台Eureka注册中心给各位Spring Cloud爱好者](http://blog.didispace.com/spring-cloud-free-eureka/)
* [Eureka Client注册到Eureka Server的秘密](http://blog.didispace.com/spring-cloud-eureka-register-detail/)
* [Spring Cloud构建微服务架构：服务注册与发现（Eureka、Consul）](http://blog.didispace.com/spring-cloud-starter-dalston-1/)
* [Spring Cloud源码分析（一）Eureka](http://blog.didispace.com/springcloud-sourcecode-eureka/)
* [Spring Cloud构建微服务架构（六）高可用服务注册中心](http://blog.didispace.com/springcloud6/)
* [Spring Cloud构建微服务架构（二）服务消费者](http://blog.didispace.com/springcloud2/)
* [Spring Cloud构建微服务架构（一）服务注册与发现](http://blog.didispace.com/springcloud1/)
