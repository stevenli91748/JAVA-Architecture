
## [Spring Cloud面试题](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring%20Cloud/Interview/README.md)
## Spring Cloud就是用于构建微服务开发和治理的框架集合

<a href="https://ibb.co/ZVfBs5G"><img src="https://i.ibb.co/G0dWwyn/SPRING-CLOUD.png" alt="SPRING-CLOUD" border="0"></a>

[程序猿DD---非常好](http://blog.didispace.com/)|[ 芋道源码------非常好](http://www.iocoder.cn/)|
---|---|

[精尽 Spring Cloud 学习指南](http://svip.iocoder.cn/Spring-Cloud/tutorials/)|
---|

[相较于 Dubbo，Spring Cloud 有何优缺点？](https://www.zhihu.com/question/50806354/answer/1099399169)|
---|

---

# 目录
* [项目实战](#项目实战)
* [Spring Cloud构建微服务架构](#Spring Cloud构建微服务架构)

组件|Spring Cloud原生及其他整合组件|Spring Cloud Netflix套件|Spring Cloud Alibaba套件|其他|
---|---|---|---|---|
服务注册与发现|Consul|Eureka||Etcd Zoopkeeper Dubbo|
---|---|---|---|---|
分布式配置管理|Spring Cloud Config||[Nacos](https://nacos.io/zh-cn/docs/what-is-nacos.html)|[Apollo](https://github.com/ctripcorp/apollo) |
---|---|---|---|---|
网关路由|Gateway|Zuul||Kong|
---|---|---|---|---|
服务调用||Ribbon [Feign](https://blog.csdn.net/u012734441/article/details/77662617 'Web服务客户端，能够简化HTTP接口的调用') Hystrix|||
---|---|---|---|---|
链路监控中心| Sleuth Zipkin|||actuctor admin ELK |
---|---|---|---|---|
消息中心|Stream|||Bus|
---|---|---|---|---|
配置中心||||git Zoopkeeper |
---|---|---|---|---|
认证授权中心||||security oauth jwt|
---|---|---|---|---|
缓存中心|||||
---|---|---|---|---|
文档中心||||swagger|
---|---|---|---|---|

* Spring Cloud原生及其他整合组件
  * Consul 
  * [Config---分布式配置管理](#Config)
  * Gateway
  * Stream
  * Sleuth/Zipkin
* Spring Cloud Netflix套件
  * [Eureka--主要负责实现微服务架构中的服务治理功能](#Eureka)
  * Ribbon
  * Feign
  * Hystrix
  * Zuul
* Spring Cloud Alibaba套件
  * Nacos
  * Sentinel
  * RocketMQ

---

# 项目实战

* [实战SpringCloud微服务从青铜到王者](https://www.kancloud.cn/hanxt/springcloud/1718845)
* [springCloud- 搭建项目全部详细过程（创建一个父工程）](https://blog.csdn.net/star1210644725/article/details/104691945?utm_medium=distribute.pc_relevant_download.none-task-blog-baidujs-5.nonecase&depth_1-utm_source=distribute.pc_relevant_download.none-task-blog-baidujs-5.nonecase)
* [SpringCloud- Alibaba](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring%20Cloud/Spring%20Cloud%20Alibaba/README.md)
* [限流](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring%20Cloud/%E9%99%90%E6%B5%81/README.md)




# Spring Cloud Netflix套件
## Eureka
* [Eureka 提供方 demo](https://blog.csdn.net/KinseyGeek/article/details/78597605)
* [Eureka 消费端demo](https://blog.csdn.net/KinseyGeek/article/details/78597476)



---

* 服务注册与发现
  * Eureka--主要负责实现微服务架构中的服务治理功能
  * Etcd  
  * Consul
  * Zookeeper
  * Dubbo
* Config---分布式配置管理
  * Spring Cloud Config
  * [Apollo](https://github.com/ctripcorp/apollo)
  * [Nacos](https://nacos.io/zh-cn/docs/what-is-nacos.html)
* Cli---
* Task---
* 网关路由
  * zuul1.x
  * zuul2.x---API网关，提供路由转发、请求过滤等功能
  * gateway
* 服务调用
  * Ribbon---基于客户端的负载均衡组件
  * [feign---Web服务客户端，能够简化HTTP接口的调用](https://blog.csdn.net/u012734441/article/details/77662617)
  * Hystrix---容错框架，能够防止服务的雪崩效应
* 链路监控中心
  * 监控
    * actuctor---是Spring Boot 提供的对应用系统的自省和监控的集成功能，可以查看应用配置的详细信息
    * admin
  * 链路监控
    * sleuth---构建分布式链路跟踪功能
    * [zipkin---是一个致力于收集所有服务的监控数据的分布式跟踪系统，它提供了收集数据和查询数据两大接口服务。有了Zipkin 我们就可以很直观地对调用链
               进行查看，并且可以很方便地看出服务之间的调用关系以及调用耗费的时间，，我们只是集成了Spring Cloud Sleuth ，然后将跟踪信息输出到日
               志中。现在， Zipkin 的服务部署好了，需要将链路跟踪的信息发送给Zipkin 的收集服务]()
    * [ELK---我们已经实现了服务调用之间的链路追踪，但是这些日志是分散在各个机器上的，就算出现问题了，我们想快速定位，也得从各个机器把日志整合起
            来，再去查问题。这个时候就需要引人日志分析系统了，比如ELK ，可以将多台服务器上的日志信息统一收集起来，在出问题的时候我们可以轻松根
            据trace Id 来搜索出对应的请求链路信息]()
* 消息中心
  * Stream---构建消息驱动的微服务应用程序的框架
  * Bus---消息代理的集群消息总线。
* 配置中心
  * git
  * svn
  * zookeeper
* 认证授权中心
  * security
  * oauth
  * jwt
* 缓存中心
  * data
* 文档中心
  * swagger
  
  




### 知识点

服务注册与发现Eureka详解及源码分析

客户端负载均衡Ribbon详解及源码分析

声明式服务调用Fegin详解及源码分析

服务熔断保护Hystrix详解

@服务网关Zuul详解、服务路由、过滤、异常处理

分布式配置中心Spring Cloud Config详解

# SpringCloud视频

## Spring Cloud 基础教程视频
* [尚硅谷_SpringCloud 第一季](https://www.bilibili.com/video/av22613028?from=search&seid=11334929433863206245)
* [尚硅谷_SpringCloud 第二季（全）](https://www.bilibili.com/video/BV1rE411x7Hz?from=search&seid=10918068436806250854)

## Spring Cloud 项目从易到难路线图视频

* [课程 1  SpringCloud Finchley三版本微服务实战](https://www.bilibili.com/video/BV137411L7bX?from=search&seid=7267066419827355478)
* [SpringCloud微服务架构的分布式事务控制解决方案](https://www.bilibili.com/video/BV14J411P7B2?from=search&seid=5160135776548693165)
* [2小时演示从头搭建Spring Cloud完整环境。配置中心、注册中心、网关、消息中间件、链路跟踪](https://www.bilibili.com/video/BV14W411j7uH/?spm_id_from=333.788.videocard.1)

* [基于SpringCloud构建微服务电商项目](https://www.bilibili.com/video/av64134293/?spm_id_from=333.788.videocard.1)
* [SpringCloud高可用微服务架构实战](https://www.bilibili.com/video/av48894754?from=search&seid=5921434574097814719)
* [史上最新的SpringCloud互联网微服务架构项目实战](https://www.bilibili.com/video/av65636320/?spm_id_from=333.788.videocard.0)
* [SpringCloud互联网微服务电商架构项目实战](https://www.bilibili.com/video/av48884712/?spm_id_from=333.788.videocard.0)
* [Java微服务架构（Spring Boot + Spring Cloud）](https://www.bilibili.com/video/av36042649?from=search&seid=15871128637146048672)
* [70万年薪的大佬带你理解-springcloud Hystrix峰控、容错机制是如何实现的](https://www.bilibili.com/video/av48702767/?spm_id_from=333.788.videocard.1)
* [Spring Cloud 微服务全套教程---详解系统架构  重要](https://www.bilibili.com/video/av37162103?p=1)
* [Spring cloud 核心组件实战](https://www.imooc.com/video/19575)

# 有用的参考
* [Spring Cloud Stream 知识整理](https://blog.csdn.net/weixin_38399962/article/details/82192340?utm_medium=distribute.pc_relevant_download.none-task-blog-baidujs-7.nonecase&depth_1-utm_source=distribute.pc_relevant_download.none-task-blog-baidujs-7.nonecase)
* [Spring Cloud微服务云应用教](https://www.jdon.com/springcloud.html)
* [使用Spring Cloud的完整微服务项目](https://www.zhihu.com/question/263476834)
* [芋道源码---Spring Cloud](http://www.iocoder.cn/categories/Spring-Cloud/)
* [Spring Cloud 微服务教程汇总](http://www.springcloud.wiki/)
* [Spring cloud应该怎么入门？](https://www.zhihu.com/question/283286745/answer/763040709)
* [Spring Cloud面试题（2020最新版）](https://blog.csdn.net/ThinkWon/article/details/104397367)
* [Spring Cloud 学习资料汇总](http://www.ityouknow.com/springcloud/2016/12/30/springcloud-collect.html)
* [史上最简单的Spring Cloud教程源码](https://github.com/forezp/SpringCloudLearning)
* [纯洁的微笑---Spring Cloud](http://www.ityouknow.com/spring-cloud)
* [Spring Cloud Examples](https://github.com/ityouknow/spring-cloud-examples)
* [Spring Cloud 资源大全](https://github.com/ityouknow/awesome-spring-cloud)
* [Spring Cloud 从入门到精通](http://blog.didispace.com/spring-cloud-learning/) 
* [Spring Cloud 中文官网](https://springcloud.cc/)
* [SpringCloud微服务架构之服务注册与发现](http://objcoding.com/2017/05/07/SpringCloud%281%29/)
* [SpringCloud微服务架构之服务消费者](http://objcoding.com/2017/05/10/SpringCloud%282%29/)
* [SpringCloud微服务架构之断路器](http://objcoding.com/2017/05/15/SpringCloud%283%29/)
* [SpringCloud微服务架构之服务网关](http://objcoding.com/2017/05/20/SpringCloud%284%29/)
* [spring cloud 实战（干货）](https://blog.csdn.net/qq_27384769/article/details/79439801)
* [从 Spring Cloud 看一个微服务框架的「五脏六腑」](https://www.javazhiyin.com/17332.html)

* [SpringCloud微服务架构第一篇](https://mp.weixin.qq.com/s?__biz=MzI4Njc5NjM1NQ==&mid=2247485849&idx=1&sn=fe4f7a4f6bdf225b74e6a24ff35bc806&chksm=ebd636b5dca1bfa3c676068d0535a7676b051c82adf25f24886042ca20fe84effb6d295a08cb&scene=21)

* [SpringCloud微服务架构第二篇](https://mp.weixin.qq.com/s?__biz=MzI4Njc5NjM1NQ==&mid=2247485898&idx=1&sn=28ead1d02df25257fb8c2fd711c0a7cc&chksm=ebd636e6dca1bff02f23aa82d2669deb22371a9dde74d43e23a31e45702cea37e8c65d43c3e4&scene=21)

* [SpringCloud微服务架构第三篇](https://mp.weixin.qq.com/s?__biz=MzI4Njc5NjM1NQ==&mid=2247485960&idx=1&sn=d69acb712f26aec6ecb73815a9160261&chksm=ebd63524dca1bc32ec27531bff118e3f1892473eae2a6e8514e6791eddd97005db78146d3844&scene=21)

* [SpringCloud微服务架构第四篇](https://mp.weixin.qq.com/s?__biz=MzI4Njc5NjM1NQ==&mid=2247486002&idx=2&sn=8f1c76a3275e4d41bc1769d349efdfa6&chksm=ebd6351edca1bc08b190c4fc319def41111cba756333bad65dca3856c42842511b77c2506744&scene=21)

* [SpringCloud第五篇结束版](https://mp.weixin.qq.com/s?__biz=MzI4Njc5NjM1NQ==&mid=2247486043&idx=2&sn=8cb27e2def06717f3487f74f9194cf7b&chksm=ebd63577dca1bc61e1134958b2b20a7bbb7f45ce3746af163b2ba5d482778f366bbef060b509&scene=21)

* [全网最全微服务架构—Spring Cloud详解，没有比这更详细的了](https://juejin.im/post/5e7ca41c518825360310d3c5)

-石杉的架构笔记
* [拜托！面试请不要再问我Spring Cloud底层原理--石杉的架构笔记](https://juejin.im/post/5be13b83f265da6116393fc7)
* [【双11狂欢的背后】微服务注册中心如何承载大型系统的千万级访问？--石杉的架构笔记](https://mp.weixin.qq.com/s/qjMphuPiihBmU2QtFMIfzw)
* [【性能优化之道】每秒上万并发下的Spring Cloud参数优化实战！--石杉的架构笔记](https://mp.weixin.qq.com/s/aH0LHgfhxpvp1IY-XbEMWA)
* [微服务架构如何保障双11狂欢下的99.99%高可用？--石杉的架构笔记](https://mp.weixin.qq.com/s/lBeQSSPX7OeWO6SmWYf1Mg)
* [Spring Cloud微服务架构的生产实践与性能优化！--石杉的架构笔记](https://mp.weixin.qq.com/s/FhspnvbIl71uIZ61K8cUoA)
* [Spring Cloud微服务注册中心Eureka 2.x停止维护了咋办？](https://juejin.im/post/5c7431f2f265da2db0739755)
* [【Java高阶必备】如何优化Spring Cloud微服务注册中心架构？](https://juejin.im/post/5c76bc785188252d564283b9)

