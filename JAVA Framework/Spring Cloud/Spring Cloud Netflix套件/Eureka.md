[Eureka停更后的替换](https://blog.csdn.net/XJ0927/article/details/109405285?utm_medium=distribute.pc_relevant.none-task-blog-OPENSEARCH-6.not_use_machine_learn_pai&depth_1-utm_source=distribute.pc_relevant.none-task-blog-OPENSEARCH-6.not_use_machine_learn_pai)|
---|

[使用Eureka搭建微服务注册中心](https://www.kancloud.cn/mrbird/spring-cloud/1263688)|[在windows环境下构建Eureka Server集群](https://gjiazhi.blog.csdn.net/article/details/103348886?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-1.not_use_machine_learn_pai&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-1.not_use_machine_learn_pai)|[在linux环境下构建Eureka Server集群]()|
---|---|---|


# 例子程序


* [在IDEA环境中建立单机的Eureka注册中心](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring%20Cloud/%E5%AE%9E%E9%AA%8C/README.md#设置Eureka注册中心)


# 目录

* [1. Eureka服务注册中心](#1-Eureka服务注册中心)

# 1 Eureka服务注册中心

Spring Cloud 封装了 Netflix 公司开发的 Eureka 模块来实现服务注册和发现。Eureka 采用了 C-S 的设计架构。Eureka Server 作为服务注册功能的服务器，它是服务注册中心。而系统中的其他微服务，使用 Eureka 的客户端连接到 Eureka Server，并维持心跳连接。这样系统的维护人员就可以通过 Eureka Server 来监控系统中各个微服务是否正常运行。Spring Cloud 的一些其他模块（比如Zuul）就可以通过 Eureka Server 来发现系统中的其他微服务，并执行相关的逻辑。

Eureka由两个组件组成：Eureka服务器和Eureka客户端。Eureka服务器用作服务注册服务器。Eureka客户端是一个java客户端，用来简化与服务器的交互、作为轮询负载均衡器，并提供服务的故障切换支持。Netflix在其生产环境中使用的是另外的客户端，它提供基于流量、资源利用率以及出错状态的加权负载均衡。





# 有用的参考
* [springboot2.0+spring cloud+eureka（分布式项目）项目搭建详细教程（附加源码）](https://blog.csdn.net/hp_yangpeng/article/details/88803911?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-1.not_use_machine_learn_pai&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-1.not_use_machine_learn_pai)
* [eureka集群高可用配置](https://tianyalei.blog.csdn.net/article/details/78184793?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-6.not_use_machine_learn_pai&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-6.not_use_machine_learn_pai)
* [Spring Cloud Eureka集群部署到Linux环境](https://www.cnblogs.com/wuxun1997/p/11230848.html)
* [Linux下eureka服务器的部署](https://blog.csdn.net/qq_45012223/article/details/100896025)
* [在windows环境下构建Eureka Server集群](https://blog.csdn.net/weixin_33953249/article/details/92334749?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-3.not_use_machine_learn_pai&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-3.not_use_machine_learn_pai)
* [搭建Eureka集群并测试](https://blog.csdn.net/chengqiuming/article/details/81050852?utm_medium=distribute.pc_relevant.none-task-blog-searchFromBaidu-1.not_use_machine_learn_pai&depth_1-utm_source=distribute.pc_relevant.none-task-blog-searchFromBaidu-1.not_use_machine_learn_pai)
* [windows 搭建eureka注册中心](https://gjiazhi.blog.csdn.net/article/details/103348886?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-1.not_use_machine_learn_pai&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-1.not_use_machine_learn_pai)
* [Eureka服务注册中心环境配置](https://www.cnblogs.com/crazymakercircle/p/12043538.html)
* [Eureka 入门，带视频](https://www.cnblogs.com/crazymakercircle/p/12043538.html)
* [开放一台Eureka注册中心给各位Spring Cloud爱好者](http://blog.didispace.com/spring-cloud-free-eureka/)
* [Eureka Client注册到Eureka Server的秘密](http://blog.didispace.com/spring-cloud-eureka-register-detail/)
* [Spring Cloud构建微服务架构：服务注册与发现（Eureka、Consul）](http://blog.didispace.com/spring-cloud-starter-dalston-1/)
* [Spring Cloud源码分析（一）Eureka](http://blog.didispace.com/springcloud-sourcecode-eureka/)
* [Spring Cloud构建微服务架构（六）高可用服务注册中心](http://blog.didispace.com/springcloud6/)
* [Spring Cloud构建微服务架构（二）服务消费者](http://blog.didispace.com/springcloud2/)
* [Spring Cloud构建微服务架构（一）服务注册与发现](http://blog.didispace.com/springcloud1/)


# troubleshooting

* [SpringCloud 运行Eureka Server启动成功但是一直不能加载Eureka Server的Home首页](https://blog.csdn.net/qq_31150503/article/details/86079433?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-3.not_use_machine_learn_pai&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-3.not_use_machine_learn_pai)
* [SpringBoot集成Eureka踩坑集合](https://blog.csdn.net/Dan1374219106/article/details/109730883?utm_medium=distribute.pc_relevant.none-task-blog-OPENSEARCH-7.not_use_machine_learn_pai&depth_1-utm_source=distribute.pc_relevant.none-task-blog-OPENSEARCH-7.not_use_machine_learn_pai)
