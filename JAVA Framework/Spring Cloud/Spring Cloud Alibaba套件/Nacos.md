

# 在线书籍

* 深入理解springcloud与实战.pdf---2020 Spring Cloud Alibba创始人倾力打造，
* SpringCloud Alibaba全解(第三版).pdf---老男孩(网关原理)
* Spring Cloud Alibaba微服务原理与实战.pdf---2020 咕泡学院联合创始人
* 阿里巴巴：Sprng Cloud Alibaba 从入门到实战.pdf----怎样用Sprng Cloud Alibaba实现分布式原理


# Spring Cloud Alibaba 版本关系

[Spring Cloud Alibaba版本说明](https://github.com/alibaba/spring-cloud-alibaba/wiki/%E7%89%88%E6%9C%AC%E8%AF%B4%E6%98%8E)|
---|

#  环境配置

[N0acos Config Example]()|[ Nacos 服务注册中心环境配置](https://www.cnblogs.com/crazymakercircle/p/11992539.html)|[nacos_微服务注册中心nacos集群搭建指南](https://www.kancloud.cn/cehgnxuyuan_123/springcloud)|
---|---|---|

# 目录
* [Nacos 三大功能---致力于帮助您发现、配置和管理微服务](#Nacos-三大功能)
  * [nacos 实战（史上最全）](https://www.cnblogs.com/crazymakercircle/p/14231815.html) 
  * [服务发现与服务管理功能](#1-服务发现与服务管理功能)
    * 服务发现
      * 基于DNS
      * 基于RPC
      
  * [动态配置管理功能](#2-动态配置管理功能)
  * [动态DNS服务功能](#3-动态DNS服务功能)


# Nacos 三大功能

## 1 服务发现与服务管理功能
     Nacos同时支持基于DNS和基于RPC（如Dubbo/gRPC）的服务发现，并提供服务的实时健康检查，基于Nacos更方便的实现服务断路器。Nacos提供的服务的元数据管理，路由及流量管理策略能够帮助更好的构
     建更强壮的微服务平台
      
## 2 动态配置管理功能
     动态配置服务允许在所有环境中以集中和动态的方式管理所有应用程序或服务的配置。动态配置消除了配置更新时重新部署应用程序和服务的需要。可以更方便的实现无状态服务，更轻松地实现按需弹性扩展
     服务实例

## 3 动态DNS服务功能
     支持权重路由的动态DNS服务可以更轻松地在数据中心内的生产环境中实施中间层负载平衡，灵活的路由策略，流量控制和简单的DNS解析服务，更容易的实现DNS-based服务发现





# 有用的参考
* [微服务注册中心比较(ZooKeeper/Eureka/Consul/Nacos)](https://www.cnblogs.com/davidgu/p/14526467.html)
* [Nacos官方文档](https://nacos.io/zh-cn/docs/what-is-nacos.html)
* [阿里巴巴 Nacos 企业级落地上篇](https://mp.weixin.qq.com/s?__biz=MzUzNzYxNjAzMg==&mid=2247492337&idx=1&sn=71df8eee2b4ab038a502bebcb2d3ced2&chksm=fae6e93ecd91602888cfee80041c6b37566a5e5cd1066df16439d320438c07a9375d3345554d&scene=21#wechat_redirect)
* [阿里巴巴 Nacos 企业级落地中篇](https://mp.weixin.qq.com/s?__biz=MzUzNzYxNjAzMg==&mid=2247492504&idx=2&sn=292b1fd8019d5679cbe0857c51cb62ff&chksm=fae6e857cd916141c70fa1d046bf6ef80b9c94b5ebe917cda2c04ad9d88dbff3063d63c48f99&scene=21#wechat_redirect)
* [阿里巴巴  Nacos企业级落地下篇](https://www.kubernetes.org.cn/8186.html)
* [Nacos生产环境集群部署方案](https://blog.csdn.net/u010046908/article/details/101050759?ops_request_misc=%25257B%252522request%25255Fid%252522%25253A%252522161052021616780266261355%252522%25252C%252522scm%252522%25253A%25252220140713.130102334.pc%25255Fall.%252522%25257D&request_id=161052021616780266261355&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_v2~rank_v29-1-101050759.pc_search_result_cache&utm_term=nacos%E9%9B%86%E7%BE%A4%E9%83%A8%E7%BD%B2)
* [在Linux上部署nacos集群，史上超详细版](https://blog.csdn.net/zhoushanmin/article/details/109740231?utm_medium=distribute.pc_relevant.none-task-blog-baidujs_title-3&spm=1001.2101.3001.4242)
* [Nacos系列：Nacos的三种部署模式](https://blog.csdn.net/weixin_34245169/article/details/88575800?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-2.not_use_machine_learn_pai&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-2.not_use_machine_learn_pai)


- [开放一台Nacos服务端给各位Spring Cloud爱好者](http://blog.didispace.com/open-nacos-server-1-0-0/)
- [Spring Cloud Alibaba基础教程：使用Nacos实现服务注册与发现](http://blog.didispace.com/spring-cloud-alibaba-1/)
- [Spring Cloud Alibaba基础教程：Nacos 生产级版本 0.8.0](http://blog.didispace.com/spring-cloud-alibaba-nacos-1/)
- [Spring Cloud Alibaba基础教程：支持的几种服务消费方式（RestTemplate、WebClient、Feign）](http://blog.didispace.com/spring-cloud-alibaba-2/)
- [Spring Cloud Alibaba基础教程：使用Nacos作为配置中心](http://blog.didispace.com/spring-cloud-alibaba-3/)
- [Spring Cloud Alibaba基础教程：Nacos配置的加载规则详解](http://blog.didispace.com/spring-cloud-alibaba-nacos-config-1/)
- [Spring Cloud Alibaba基础教程：Nacos配置的多环境管理](http://blog.didispace.com/spring-cloud-alibaba-nacos-config-2/)
- [Spring Cloud Alibaba基础教程：Nacos配置的多文件加载与共享配置](http://blog.didispace.com/spring-cloud-alibaba-nacos-config-3/)
* [Nacos的数据持久化](http://blog.didispace.com/spring-cloud-alibaba-4/)
* [Nacos的集群部署](http://blog.didispace.com/spring-cloud-alibaba-5/)
* [Nacos部署中的一些常见问题汇总](http://blog.didispace.com/nacos-faqs/)
* [Spring Cloud Alibaba基础教程：Sentinel使用Nacos存储规则](http://blog.didispace.com/spring-cloud-alibaba-sentinel-2-1/)
* [Nacos配置中心原理](http://blog.didispace.com/nacos-yuanli-1/)
* [Nacos注册、发现、消费入门](https://www.jianshu.com/p/0f5b5f2bb1a8)


* [阿里巴巴NACOS（1）- 概述及简单使用](https://www.jianshu.com/p/c19ba3b971a5)
* [阿里巴巴NACOS（2）- 为Spring Cloud提供服务注册发现及配置](https://www.jianshu.com/p/fcf71fa840a4)
* [阿里巴巴NACOS（3）- 部署Nacos的生产集群环境](https://www.jianshu.com/p/abc1d405953c)
* [阿里巴巴NACOS（4）- 主流微服务配置中心产品比较 Spring Cloud Config、阿里云ACM、Nacos](https://www.jianshu.com/p/be168b0633a6)
* [阿里巴巴NACOS（5）- 主流微服务注册中心产品比较 Eureka、Consul、Nacos](https://www.jianshu.com/p/54f525334658)
* [阿里巴巴NACOS（6）- 在k8s上部署Nacos](https://www.jianshu.com/p/ce76e30f9247)
