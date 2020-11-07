
## [Spring Cloud面试题](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring%20Cloud/Interview/README.md)
## Spring Cloud就是用于构建微服务开发和治理的框架集合

<a href="https://ibb.co/51YtVKL"><img src="https://i.ibb.co/fX8B7DH/springcloud.png" alt="springcloud" border="0"></a>

[程序猿DD---非常好](http://blog.didispace.com/)|[ 芋道源码------非常好](http://www.iocoder.cn/)|[周立的博客](http://itmuch.com/)|[My bird](https://mrbird.cc/archives/)|[纯洁的微笑---Spring Cloud](http://www.ityouknow.com/spring-cloud)|
---|---|---|---|---|

[精尽 Spring Cloud 学习指南](http://svip.iocoder.cn/Spring-Cloud/tutorials/)|[Spring官方文档： Spring Cloud AWS---如何把SpringCloud项目放到AWS](https://cloud.spring.io/spring-cloud-aws/spring-cloud-aws.html#_basic_setup)|
---|---|

[相较于 Dubbo，Spring Cloud 有何优缺点？](https://www.zhihu.com/question/50806354/answer/1099399169)|
---|

[【项目实战】实战SpringCloud微服务从青铜到王者](https://www.kancloud.cn/hanxt/springcloud/1718845)|
---|

[【项目实战】Spring Cloud 微服务权限系统搭建教程---手把手教你从零搭建到Docker部署（新增K8S集群部署教程）](https://www.kancloud.cn/mrbird/spring-cloud/1263681)|
---|

[【项目实战】微服务多租户系统后台管理系统---基于SpringBoot2.x、SpringCloud和SpringCloudAlibaba并采用前后端分离的企业级微服务多租户系统架构](https://gitee.com/zlt2000/microservices-platform)|
---|

----

 [【项目实战】2020最新Java项目实战APM性能监控系统](https://www.bilibili.com/video/BV15t4y117xN/?spm_id_from=333.788.videocard.5)|[SpringBoot 2.x ShardingSphere分库分表实战](https://juejin.im/post/6844903857336614920)|
 ---|---|



# Spring Cloud项目部署到AWS的例子

* [Spring Cloud 微服务(一) 什么是微服务](https://www.jianshu.com/p/2ba8c687d835)
* [Spring Cloud 微服务(二) 创建一个简单的服务](https://www.jianshu.com/p/98a4d25e7f5d)
* [Spring Cloud 微服务(三) 服务发现Eureka](https://www.jianshu.com/p/67b0d2108f67)
* [Spring Cloud 微服务(四) Docker化](https://www.jianshu.com/p/1ce2bc8ce674)
* [Spring Cloud 微服务(五) 部署到AWS ECS](https://www.jianshu.com/p/1617d6948ee8)
* [Spring Cloud 微服务(六) 服务消费Feign](https://www.jianshu.com/p/07303bc4b015)
* [Spring Cloud 微服务 (遇到的问题汇总）](https://www.jianshu.com/p/2e2820269dd2)
* [AWS 上的项目：将整体式应用程序拆分为微服务（使用 Amazon Elastic Container Service、Docker 和 Amazon EC2）](https://aws.amazon.com/cn/getting-started/hands-on/break-monolith-app-microservices-ecs-docker-ec2/)
---

# 目录
* [项目实战](#项目实战)
* [Spring Cloud构建微服务架构](#SpringCloud构建微服务架构)

# Spring Cloud各组件专题

组件|Spring Cloud原生及其他整合组件|Spring Cloud Netflix套件|Spring Cloud Alibaba套件|其他|
---|---|---|---|---|
服务注册与发现|[Consul](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring%20Cloud/Spring%20Cloud%E5%8E%9F%E7%94%9F%E5%8F%8A%E5%85%B6%E4%BB%96%E6%95%B4%E5%90%88%E7%BB%84%E4%BB%B6/Consul.md)|[Eureka](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring%20Cloud/Spring%20Cloud%20Netflix%E5%A5%97%E4%BB%B6/Eureka.md '主要负责实现微服务架构中的服务治理功能')||Etcd Zoopkeeper Dubbo|
---|---|---|---|---|
分布式配置管理|[Spring Cloud Config](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring%20Cloud/Spring%20Cloud%E5%8E%9F%E7%94%9F%E5%8F%8A%E5%85%B6%E4%BB%96%E6%95%B4%E5%90%88%E7%BB%84%E4%BB%B6/Config.md)||[Nacos](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring%20Cloud/Spring%20Cloud%20Alibaba%E5%A5%97%E4%BB%B6/Nacos.md)|[Apollo](https://github.com/ctripcorp/apollo) |
---|---|---|---|---|
网关路由|[Gateway](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring%20Cloud/Spring%20Cloud%E5%8E%9F%E7%94%9F%E5%8F%8A%E5%85%B6%E4%BB%96%E6%95%B4%E5%90%88%E7%BB%84%E4%BB%B6/Gateway.md)|[Zuul](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring%20Cloud/Spring%20Cloud%20Netflix%E5%A5%97%E4%BB%B6/Zuul.md 'API网关，提供路由转发、请求过滤等功能')||Kong|
---|---|---|---|---|
服务调用||[Ribbon](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring%20Cloud/Spring%20Cloud%20Netflix%E5%A5%97%E4%BB%B6/Ribbon.md '基于客户端的负载均衡组件') [Feign](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring%20Cloud/Spring%20Cloud%20Netflix%E5%A5%97%E4%BB%B6/Feign.md 'Web服务客户端，能够简化HTTP接口的调用') [Hystrix](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring%20Cloud/Spring%20Cloud%20Netflix%E5%A5%97%E4%BB%B6/hystrix.md '容错框架，能够防止服务的雪崩效应')|||
---|---|---|---|---|
链路监控中心| [Sleuth](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring%20Cloud/Spring%20Cloud%E5%8E%9F%E7%94%9F%E5%8F%8A%E5%85%B6%E4%BB%96%E6%95%B4%E5%90%88%E7%BB%84%E4%BB%B6/Sleuth.md '构建分布式链路跟踪功能') [Zipkin](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring%20Cloud/Spring%20Cloud%E5%8E%9F%E7%94%9F%E5%8F%8A%E5%85%B6%E4%BB%96%E6%95%B4%E5%90%88%E7%BB%84%E4%BB%B6/Zipkin.md '收集所有服务的监控数据的分布式跟踪系统')|||actuctor admin ELK |
---|---|---|---|---|
消息中心|[Stream](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring%20Cloud/Spring%20Cloud%E5%8E%9F%E7%94%9F%E5%8F%8A%E5%85%B6%E4%BB%96%E6%95%B4%E5%90%88%E7%BB%84%E4%BB%B6/Stream.md '构建消息驱动的微服务应用程序的框架')|||Bus|
---|---|---|---|---|
配置中心||||git Zoopkeeper |
---|---|---|---|---|
认证授权中心||||security oauth jwt|
---|---|---|---|---|
缓存中心|||||
---|---|---|---|---|
文档中心||||swagger|
---|---|---|---|---|
[RocketMQ](https://github.com/stevenli91748/Message-Server-System/blob/master/RocketMQ/README.md)|||||
---|---|---|---|---|
[Sentinel](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring%20Cloud/Spring%20Cloud%20Alibaba%E5%A5%97%E4%BB%B6/Sentinel.md)|||||
---|---|---|---|---|

---

# 项目实战
* [Demo](#Demo)
* [实战项目](#实战项目)

## [Demo](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring%20Cloud/%E5%AE%9E%E9%AA%8C/README.md)


## 实战项目
* [实战SpringCloud微服务从青铜到王者](https://www.kancloud.cn/hanxt/springcloud/1718845)
* [springCloud- 搭建项目全部详细过程（创建一个父工程）](https://blog.csdn.net/star1210644725/article/details/104691945?utm_medium=distribute.pc_relevant_download.none-task-blog-baidujs-5.nonecase&depth_1-utm_source=distribute.pc_relevant_download.none-task-blog-baidujs-5.nonecase)
* [SpringCloud- Alibaba](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring%20Cloud/Spring%20Cloud%20Alibaba/README.md)
* [限流](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring%20Cloud/%E9%99%90%E6%B5%81/README.md)
* [2小时演示从头搭建Spring Cloud完整环境。配置中心、注册中心、网关、消息中间件、链路跟踪](https://www.bilibili.com/video/BV14W411j7uH/?spm_id_from=333.788.videocard.1)
* [史上最新的SpringCloud互联网微服务架构项目实战](https://www.bilibili.com/video/av65636320/?spm_id_from=333.788.videocard.0)
* [SpringCloud互联网微服务电商架构项目实战](https://www.bilibili.com/video/av48884712/?spm_id_from=333.788.videocard.0)

# SpringCloud构建微服务架构
- [服务注册与发现（Eureka、Consul）](http://blog.didispace.com/spring-cloud-starter-dalston-1/)
- [服务消费者（基础）](http://blog.didispace.com/spring-cloud-starter-dalston-2-1/)
- [服务消费者（Ribbon）](http://blog.didispace.com/spring-cloud-starter-dalston-2-2/)
- [服务消费者（Feign）](http://blog.didispace.com/spring-cloud-starter-dalston-2-3/)
- [服务消费者（Feign）传文件](http://blog.didispace.com/spring-cloud-starter-dalston-2-4/)
- [分布式配置中心](http://blog.didispace.com/spring-cloud-starter-dalston-3)
- [服务容错保护(Hystrix服务降级)](http://blog.didispace.com/spring-cloud-starter-dalston-4-1)
- [服务容错保护(Hystrix依赖隔离)](http://blog.didispace.com/spring-cloud-starter-dalston-4-2)
- [服务容错保护(Hystrix断路器)](http://blog.didispace.com/spring-cloud-starter-dalston-4-3)
- [Hystrix监控面板](http://blog.didispace.com/spring-cloud-starter-dalston-5-1/)
- [Hystrix监控数据聚合](http://blog.didispace.com/spring-cloud-starter-dalston-5-2/)
- [服务网关（基础）](http://blog.didispace.com/spring-cloud-starter-dalston-6-1/)
- [服务网关（路由配置）](http://blog.didispace.com/spring-cloud-starter-dalston-6-2/)
- [服务网关（过滤器）](http://blog.didispace.com/spring-cloud-starter-dalston-6-3/)
- [服务网关（API文档汇总）](http://blog.didispace.com/Spring-Cloud-Zuul-use-Swagger-API-doc/)
- [消息驱动的微服务（入门）](http://blog.didispace.com/spring-cloud-starter-dalston-7-1/)
- [消息驱动的微服务（核心概念）](http://blog.didispace.com/spring-cloud-starter-dalston-7-2/)
- [消息驱动的微服务（消费组）](http://blog.didispace.com/spring-cloud-starter-dalston-7-3/)
- [消息驱动的微服务（消费组案例：解决消息重复消费）](http://blog.didispace.com/spring-cloud-starter-dalston-7-5/)
- [消息驱动的微服务（消息分区）](http://blog.didispace.com/spring-cloud-starter-dalston-7-4/)
- [分布式服务跟踪（入门）](http://blog.didispace.com/spring-cloud-starter-dalston-8-1/)
- [分布式服务跟踪（跟踪原理）](http://blog.didispace.com/spring-cloud-starter-dalston-8-2/)
- [分布式服务跟踪（整合logstash）](http://blog.didispace.com/spring-cloud-starter-dalston-8-3/)
- [分布式服务跟踪（整合zipkin）](http://blog.didispace.com/spring-cloud-starter-dalston-8-4/)
- [分布式服务跟踪（收集原理）](http://blog.didispace.com/spring-cloud-starter-dalston-8-5/)
- [分布式服务跟踪（抽样收集）](http://blog.didispace.com/spring-cloud-starter-dalston-8-6/)

- 1-Brixton版教程示例/chapter1-1-1：[Spring Cloud构建微服务架构（一）服务注册与发现](http://blog.didispace.com/springcloud1/)
- 1-Brixton版教程示例/chapter1-1-2：[Spring Cloud构建微服务架构（二）服务消费者](http://blog.didispace.com/springcloud2/)
- 1-Brixton版教程示例/chapter1-1-3：[Spring Cloud构建微服务架构（三）断路器](http://blog.didispace.com/springcloud3/)
- 1-Brixton版教程示例/chapter1-1-4：[Spring Cloud构建微服务架构（四）分布式配置中心](http://blog.didispace.com/springcloud4/)
- 1-Brixton版教程示例/chapter1-1-8：[Spring Cloud构建微服务架构（四）分布式配置中心（续）](http://blog.didispace.com/springcloud4-2/)
- 1-Brixton版教程示例/chapter1-1-5：[Spring Cloud构建微服务架构（五）服务网关](http://blog.didispace.com/springcloud5/)
- 1-Brixton版教程示例/chapter1-1-6：[Spring Cloud构建微服务架构（六）高可用服务注册中心](http://blog.didispace.com/springcloud6/)
- 1-Brixton版教程示例/chapter1-1-7：[Spring Cloud构建微服务架构（七）消息总线(Rabbit)](http://blog.didispace.com/springcloud7/)
- 1-Brixton版教程示例/chapter1-1-7：[Spring Cloud构建微服务架构（七）消息总线(Kafka)](http://blog.didispace.com/springcloud7-2/)






---

# SpringCloud视频

## Spring Cloud 基础教程视频
* [尚硅谷_SpringCloud 第一季](https://www.bilibili.com/video/av22613028?from=search&seid=11334929433863206245)
* [尚硅谷_SpringCloud 第二季（全）](https://www.bilibili.com/video/BV1rE411x7Hz?from=search&seid=10918068436806250854)

## Spring Cloud 项目从易到难路线图视频

* [课程 1  SpringCloud Finchley三版本微服务实战](https://www.bilibili.com/video/BV137411L7bX?from=search&seid=7267066419827355478)
* [SpringCloud微服务架构的分布式事务控制解决方案](https://www.bilibili.com/video/BV14J411P7B2?from=search&seid=5160135776548693165)
* [基于SpringCloud构建微服务电商项目](https://www.bilibili.com/video/av64134293/?spm_id_from=333.788.videocard.1)
* [SpringCloud高可用微服务架构实战](https://www.bilibili.com/video/av48894754?from=search&seid=5921434574097814719)
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
- [Spring Cloud实战小贴士：版本依赖关系](http://blog.didispace.com/spring-cloud-tips-1/)
- [Spring Cloud实战小贴士：随机端口](http://blog.didispace.com/spring-cloud-tips-2/)
- [Spring Cloud实战小贴士：健康检查](http://blog.didispace.com/spring-cloud-tips-3/)
- [Spring Cloud实战小贴士：Zuul处理Cookie和重定向](http://blog.didispace.com/spring-cloud-zuul-cookie-redirect/)
- [Spring Cloud实战小贴士：Zuul统一异常处理（一）](http://blog.didispace.com/spring-cloud-zuul-exception/)
- [Spring Cloud实战小贴士：Zuul统一异常处理（二）](http://blog.didispace.com/spring-cloud-zuul-exception-2/)
- [Spring Cloud实战小贴士：Zuul统一异常处理（三）【Dalston版】](http://blog.didispace.com/spring-cloud-zuul-exception-3/)
- [Spring Cloud实战小贴士：Turbine如何聚合设置了context-path的Hystrix数据](http://blog.didispace.com/spring-cloud-tips-4/)
- [Spring Cloud实战小贴士：Feign的继承特性(伪RPC模式)](http://blog.didispace.com/spring-cloud-tips-feign-rpc/)
- [Spring Cloud实战小贴士：Ribbon的饥饿加载(eager-load)模式](http://blog.didispace.com/spring-cloud-tips-ribbon-eager/)
- [Spring Cloud实战小贴士：Zuul的饥饿加载(eager-load)使用](http://blog.didispace.com/spring-cloud-tips-zuul-eager/)
- **[Spring Cloud在国内中小型公司能用起来吗？](http://www.ityouknow.com/springcloud/2017/09/11/can-use-springcloud.html)**   
- **[中小型互联网公司微服务实践-经验和教训](http://www.ityouknow.com/springcloud/2017/10/19/micro-service-practice.html)**    
- **[从架构演进的角度聊聊Spring Cloud都做了些什么？](http://www.ityouknow.com/springcloud/2017/11/02/framework-and-springcloud.html)**   
- **[阿里Dubbo疯狂更新，关Spring Cloud什么事？](http://www.ityouknow.com/springcloud/2017/11/20/dubbo-update-again.html)**   
- [使用Intellij中的Spring Initializr来快速构建Spring Boot/Cloud工程](http://blog.didispace.com/spring-initializr-in-intellij/)
- [使用Spring Boot Actuator、Jolokia和Grafana实现准实时监控](http://blog.didispace.com/spring-boot-jolokia-grafana-monitor/)
- [Netflix Zuul与Nginx的性能对比](http://blog.didispace.com/zuul-vs-nginx-performance/)
* [Spring Cloud与Dubbo的完美融合](http://blog.didispace.com/spring-cloud-alibaba-dubbo-1/)
* [Spring Cloud Alibaba与Spring Boot、Spring Cloud之间不得不说的版本关系](http://blog.didispace.com/spring-cloud-alibaba-version/)


-石杉的架构笔记
* [拜托！面试请不要再问我Spring Cloud底层原理--石杉的架构笔记](https://juejin.im/post/5be13b83f265da6116393fc7)
* [【双11狂欢的背后】微服务注册中心如何承载大型系统的千万级访问？--石杉的架构笔记](https://mp.weixin.qq.com/s/qjMphuPiihBmU2QtFMIfzw)
* [【性能优化之道】每秒上万并发下的Spring Cloud参数优化实战！--石杉的架构笔记](https://mp.weixin.qq.com/s/aH0LHgfhxpvp1IY-XbEMWA)
* [微服务架构如何保障双11狂欢下的99.99%高可用？--石杉的架构笔记](https://mp.weixin.qq.com/s/lBeQSSPX7OeWO6SmWYf1Mg)
* [Spring Cloud微服务架构的生产实践与性能优化！--石杉的架构笔记](https://mp.weixin.qq.com/s/FhspnvbIl71uIZ61K8cUoA)
* [Spring Cloud微服务注册中心Eureka 2.x停止维护了咋办？](https://juejin.im/post/5c7431f2f265da2db0739755)
* [【Java高阶必备】如何优化Spring Cloud微服务注册中心架构？](https://juejin.im/post/5c76bc785188252d564283b9)

## Spring Cloud精讲
- [springcloud(一)：大话Spring Cloud](http://www.ityouknow.com/springcloud/2017/05/01/simple-springcloud.html)
- [springcloud(二)：注册中心Eureka](http://www.ityouknow.com/springcloud/2017/05/10/springcloud-eureka.html)
- [springcloud(三)：服务提供与调用](http://www.ityouknow.com/springcloud/2017/05/12/eureka-provider-constomer.html)
- [springcloud(四)：熔断器Hystrix](http://www.ityouknow.com/springcloud/2017/05/16/springcloud-hystrix.html)
- [springcloud(五)：熔断监控Hystrix Dashboard和Turbine](http://www.ityouknow.com/springcloud/2017/05/18/hystrix-dashboard-turbine.html)
- [springcloud(六)：配置中心git示例](http://www.ityouknow.com/springcloud/2017/05/22/springcloud-config-git.html)
- [springcloud(七)：配置中心svn示例和refresh](http://www.ityouknow.com/springcloud/2017/05/23/springcloud-config-svn-refresh.html)
- [springcloud(八)：配置中心服务化和高可用](http://www.ityouknow.com/springcloud/2017/05/25/springcloud-config-eureka.html)
- [springcloud(九)：配置中心和消息总线（配置中心终结版）](http://www.ityouknow.com/springcloud/2017/05/26/springcloud-config-eureka-bus.html)
- [springcloud(十)：服务网关zuul](http://www.ityouknow.com/springcloud/2017/06/01/gateway-service-zuul.html)  
- [springcloud(十一)：服务网关Zuul高级篇](http://www.ityouknow.com/springcloud/2018/01/20/spring-cloud-zuul.html)  
- [springcloud(十二)：使用Spring Cloud Sleuth和Zipkin进行分布式链路跟踪](http://www.ityouknow.com/springcloud/2018/02/02/spring-cloud-sleuth-zipkin.html)  
- [springcloud(十三)：Spring Cloud Consul 使用详解](http://www.ityouknow.com/springcloud/2018/07/20/spring-cloud-consul.html)  
- [Spring Cloud (十四)：Spring Cloud 开源软件都有哪些？](http://www.ityouknow.com/springcloud/2018/08/06/spring-cloud-open-source.html)  
- [springcloud(十五)：服务网关 Spring Cloud GateWay 初级篇](http://www.ityouknow.com/springcloud/2018/12/12/spring-cloud-gateway-start.html)  
- [springcloud(十六)：服务网关 Spring Cloud GateWay 服务化和过滤器](http://www.ityouknow.com/springcloud/2019/01/19/spring-cloud-gateway-service.html)  
- [springcloud(十七)：服务网关 Spring Cloud GateWay 熔断、限流、重试](http://www.ityouknow.com/springcloud/2019/01/26/spring-cloud-gateway-limit.html)  
