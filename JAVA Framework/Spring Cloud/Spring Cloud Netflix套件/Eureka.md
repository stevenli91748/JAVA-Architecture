[Spring Cloud Eureka上篇](https://weread.qq.com/web/reader/71d32370716443e271df020kc51323901dc51ce410c121b)|[Spring Cloud Eureka下篇](https://weread.qq.com/web/reader/71d32370716443e271df020k6f4322302126f4922f45dec)|
---|---|


[Eureka停更后的替换](https://blog.csdn.net/XJ0927/article/details/109405285?utm_medium=distribute.pc_relevant.none-task-blog-OPENSEARCH-6.not_use_machine_learn_pai&depth_1-utm_source=distribute.pc_relevant.none-task-blog-OPENSEARCH-6.not_use_machine_learn_pai)|
---|

[使用Eureka搭建微服务注册中心](https://www.kancloud.cn/mrbird/spring-cloud/1263688)|[在windows环境下构建Eureka Server集群](https://gjiazhi.blog.csdn.net/article/details/103348886?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-1.not_use_machine_learn_pai&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-1.not_use_machine_learn_pai)|[在linux环境下构建Eureka Server集群]()|
---|---|---|


# 例子程序


* [在IDEA环境中建立单机的Eureka注册中心](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring%20Cloud/%E5%AE%9E%E9%AA%8C/README.md#设置Eureka注册中心)


# 目录

* [1. Eureka服务注册中心](#1-Eureka服务注册中心)
  *  
# 1 Eureka服务注册中心

Spring Cloud 封装了 Netflix 公司开发的 Eureka 模块来实现服务注册和发现。Eureka 采用了 C-S 的设计架构。Eureka Server 作为服务注册功能的服务器，它是服务注册中心。而系统中的其他微服务，使用 Eureka 的客户端连接到 Eureka Server，并维持心跳连接。这样系统的维护人员就可以通过 Eureka Server 来监控系统中各个微服务是否正常运行。Spring Cloud 的一些其他模块（比如Zuul）就可以通过 Eureka Server 来发现系统中的其他微服务，并执行相关的逻辑。

Eureka由两个组件组成：Eureka服务器和Eureka客户端。Eureka服务器用作服务注册服务器。Eureka客户端是一个java客户端，用来简化与服务器的交互、作为轮询负载均衡器，并提供服务的故障切换支持。Netflix在其生产环境中使用的是另外的客户端，它提供基于流量、资源利用率以及出错状态的加权负载均衡。

**Eureka主要功能如下**

（1）Eureka是一个服务发现工具。该体系结构主要是Client/Server，通常每个客户端都使用嵌入式SDK进行注册和发现服务。

（2）Eureka的健康检查主要依靠HTTP进行。服务在注册时有一个缓冲时间，在刚注册到Eureka时是无法使用的（虽然时间非常短暂）。

（3）Eureka提供了一个弱一致性的服务方案。当客户端向服务器注册时，该服务器将尝试复制服务到其他服务器，但又不提供任何保证。可能有很短暂的一瞬间Eureka用过去版本的陈旧代码提供服务。弱一致性架构模式使Eureka具备高度可伸缩性的集群管理能力


**Eureka Server的自我保护机制**

如果在15分钟内超过85%的节点都没有正常的心跳，那么Eureka就认为客户端与注册中心出现了网络故障，此时会出现以下几种情况：

  Eureka不再从注册列表中移除因为长时间没收到心跳而应该过期的服务
  
  Eureka仍然能够接受新服务的注册和查询请求，但是不会被同步到其它节点上（即保证当前节点依然可用）
  
  当网络稳定时，当前实例新的注册信息会被同步到其它节点中

因此， Eureka可以很好的应对因网络故障导致部分节点失去联系的情况，而不会像ZooKeeper那样使整个注册服务瘫痪

**Eureka和ZooKeeper的比较**

著名的CAP理论指出，一个分布式系统不可能同时满足C（一致性）、A（可用性）和P（分区容错性）。由于分区容错性在是分布式系统中必须要保证的，因此我们只能在A和C之间进行权衡。

ZooKeeper保证CP

当向注册中心查询服务列表时，我们可以容忍注册中心返回的是几分钟以前的注册信息，但不能接受服务直接down掉不可用。也就是说，服务注册功能对可用性的要求要高于一致性。但是ZooKeeper会出现这样一种情况，当Master节点因为网络故障与其他节点失去联系时，剩余节点会重新进行leader选举。问题在于，选举leader的时间太长，30 ~ 120s，且选举期间整个ZooKeeper集群都是不可用的，这就导致在选举期间注册服务瘫痪。在云部署的环境下，因网络问题使得ZooKeeper集群失去Master节点是较大概率会发生的事，虽然服务能够最终恢复，但是漫长的选举时间导致的注册长期不可用是不能容忍的。

Eureka保证AP

Eurek在设计时就优先保证可用性。Eureka各个节点都是平等的，几个节点挂掉不会影响正常节点的工作，剩余的节点依然可以提供注册和查询服务。而Eureka的客户端在向某个Eureka注册或时如果发现连接失败，则会自动切换至其它节点，只要有一台Eureka还在，就能保证注册服务可用（保证可用性），只不过查到的信息可能不是最新的（不保证强一致性）。

**Eureka可以很好的应对因网络故障导致部分节点失去联系的情况，而不会像ZooKeeper那样使整个注册服务瘫痪。

**Eureka作为单纯的服务注册中心来说要比ZooKeeper更加“专业”，因为注册服务更重要的是可用性，我们可以接受短期内达不到一致性的状况


# 有用的参考
* [微服务注册中心比较(ZooKeeper/Eureka/Consul/Nacos)](https://www.cnblogs.com/davidgu/p/14526467.html)
* [Eureka与Consul的区别](https://weread.qq.com/web/reader/f6732e8071dbddd6f674178k6f4322302126f4922f45dec)
* [Spring Cloud Eureka详解](https://blog.csdn.net/sunhuiliang85/article/details/76222517)
* [《Spring Cloud Netflix》 -- 服务注册和服务发现-Eureka 的使用](https://zhuanlan.zhihu.com/p/26472547)
* [微服务架构：Eureka参数配置项详解](https://www.cnblogs.com/fangfuhai/p/7070325.html)
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
