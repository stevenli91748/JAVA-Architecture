
# 在线书籍

* [3 微服务架构实战： 基于Spring Boot, Spring Cloud, Docker---2018 可跟据书中的核心接口往下深究，组件包括ZUUL + Eureka + Ribbon + Hystrix + Config + Spring Cloud Stream + Spring Cloud  Bus + Spring Cloud Security + Spring Cloud Sleuth + Spring Test + Docker部署](https://weread.qq.com/web/reader/4ff32e10717d34624ff9b0a)

* [4 （先看） Spring Cloud微服务架构进阶---2018 （Spring Cloud 深入系列 ）各组件讲得非常详细, 值的反复阅读的书 ，包括Spring Cloud Gateway + Eureka + OpenFeign + Hystrix + Config + Ribbon + Spring Cloud Stream + Spring Cloud Bus + Spring Cloud Security + Spring Cloud Sleuth   ](https://weread.qq.com/web/reader/c9932ea07163ff6ac993e0dkc81322c012c81e728d9d180)

* [8 （先看） 重新定义Spring Cloud实战---2018 Spring Cloud中国社区倾力打造，多位行业专家推荐，作者来自原阿里、蚂蚁金服、京东金融等互联网企业, 一本实践与理论并重、广度与深度兼顾的Spring Cloud生产实践开发指南, 有3大特色: 足够广,有深度,重实践](https://weread.qq.com/web/reader/71d32370716443e271df020kc81322c012c81e728d9d180)

* [9 Spring Cloud微服务和分布式系统实践---2020 组件包括服务注册和服务发现（Eureka）、服务调用（Ribbon和OpenFeign）、断路器（Hystrix和Resilience4j）、网关（Zuul和Spring Cloud Gateway）、配置（Config）、全链路追踪（Sleuth）、微服务的监控（Admin）,分布式系统方面主要讲解分布式数据库、分布式缓存、分布式会话和分布式权限以及发号机制,远程过程调用， 微服务设计和高并发等](https://weread.qq.com/web/reader/2fe329c071e041322feb53d)

* [10  （先看）  深入理解Spring Cloud与微服务构建 （第2版）---包括Spring Boot + Eureka、Consul, Ribbon + RestTemplate , Feign、Hystrix、Zuul、Gateway、Consul、Config、Sleuth、Admint等组件,使用JWT, Spring Cloud OAuth2来保护微服务系统, 还有一个综合案例](https://weread.qq.com/web/reader/1223205071ccfab912296c2)

* [11  （先看） Spring Cloud微服务： 入门 实战与进阶--- 2019 从技术原理、工程实践、进阶提升3个维度详解Spring Cloud微服务的架构与开发 （Eureka + Ribbon + Feign + Hystrix + ZUUL + Spring Cloud Gateway + Apollo + Spring Cloud Sleuth + JWT + Spring Boot Admin + Swagger + 微服务缓存 + API 网关 + 微服务存储 + 分布式事务解决方案 + 分布式任务调度 + 分库分表解决方案 + 最佳生产实践经验)](https://weread.qq.com/web/reader/5c832cf07186d8355c87a4f)

* [12 Spring Cloud微服务架构开发实战 ---2018 涵盖了构建微服务架构所需要的服务治理（Eureka）、客户端负载均衡（Ribbon）、微服务容错与降级处理（Hystrix）、微服务API统一网关（Zuul）、分布式配置中心（Config）、微服务调用链追踪（Sleuth）、微服务消息驱动开发（Stream）及微服务安全（OAuth及JWT）等组件](https://weread.qq.com/web/reader/7d332ce0715dbcba7d3a09d)

* [13 Spring Cloud开发实战 ---2021 Eureka + Consul + Apollo + Nacos + Zuul + Feign + Gateway + Kafka + config + Zipkin + Hystrix + Ribbon + ZooKeeper + Swagger + Postman + MongoDB + Redis ](https://weread.qq.com/web/reader/1c632610725a69631c6c26f)

* [17 Spring Cloud 实战--- SpringCloud的各种整合: Spring Data + Eureka整合 + Ribbon整合 + ZUUL +  Hystrix整合 + Config + Zipkin + 微服务健康检查与服务跟踪 + 消息机制与消息驱动框架 ](https://weread.qq.com/web/reader/a1132af07199a64ca1167d3)


 [Spring Cloud Config](https://weread.qq.com/web/reader/c9932ea07163ff6ac993e0dk64232b60230642e92efb54c)|[Spring Cloud Config上篇](https://weread.qq.com/web/reader/71d32370716443e271df020kfc432fb0241fc490ca45614)|[Spring Cloud Config下篇](https://weread.qq.com/web/reader/71d32370716443e271df020k14b3246024514bfa6bb1534)|
 ---|---|---|
 
 # 目录
 * [Spring Cloud Config统一配置管理快速入门例子1](https://mrbird.cc/Spring-Cloud-Config.html)
 * [Spring Cloud Config入门案例2](https://weread.qq.com/web/reader/71d32370716443e271df020k32932b102423295c76ac7d9)
 * [配置中心概念](#Config概念)
   * Spring Cloud Config配置中心
     * [Spring Cloud Config Server配置](https://weread.qq.com/web/reader/71d32370716443e271df020k32932b102423295c76ac7d9)
     * [Spring Cloud Config Client配置](https://weread.qq.com/web/reader/71d32370716443e271df020k32932b102423295c76ac7d9)
     * [手动刷新配置中心信息](https://weread.qq.com/web/reader/71d32370716443e271df020k73532580243735b90b45ac8)
     * [结合Spring Cloud Bus热刷新](https://weread.qq.com/web/reader/71d32370716443e271df020k73532580243735b90b45ac8)
   * 扩展配置中心
   * 第三方配置中心Apollo的接入
 * [配置中心应具备的功能 ](https://weread.qq.com/web/reader/71d32370716443e271df020k32932b102423295c76ac7d9)
 
 ## Config概念
 
Spring Cloud Config是一个解决分布式系统的配置管理方案。微服务意味着要将单体应用中的业务拆分成一个个子服务，每个服务的粒度相对较小，因此系统 中会出现大量的服务。由于每个服务都需要必要的配置信息才能运行，所以一套集中式的、 动态的配置管理设施是必不可少的。Spring Cloud提供了ConfigServer来解决这个问题,我们每一个微服务自 己带着一个application.yml 上百个配置文件的管理。

配置中心被用作集中管理不同环境（Dev、Test、Stage、Prod）和不同集群配置，以及在修改配置后将实时动态推送到应用上进行刷新

其能够很好地将程序中配置日益增多的**各种功能的开关、参数的配置、服务器的地址，配置修改后实时生效，灰度发布，分环境、分集群管理配置**等进行全面的集中化管理，有利于系统的配置管理和维护

Spring Cloud Config是一个集中化外部配置的分布式系统，由服务端和客户端组成。它不依赖于注册中心，是一个独立的配置中心。Spring Cloud config支持多种存储配置信息的形式，目前主要有jdbc、Vault、Native、svn、git，其中默认为git

**应用场景**

不方便维护，多人同时对配置文件进行修改，冲突不断，很难维护，
配置内容安全和权限，主要是针对线上的配置来说，一般不对开发公开，只有运维有权限所以需要将配置文件隔离，不放到项目代码里
更新配置项目需要重启，每次更新配置文件都需要重启项目，很耗时。使用了配置中心后，即可实现配置实时更新congfig Server和Config Client结合Spring Cloud Bus实现配置自动刷新。

**原理**

配置文件存储在远端Git（比如GitHub，Gitee等仓库），config-server从远端Git拉取配置文件，并保存到本地Git。
本地Git和config-server的交互是双向的，因为当远端Git无法访问时，会从本地Git获取配置文件。
config-client（即各个微服务），从config-server拉取配置文件。

**角色**

Config Server：提供配置文件的存储、以接口的形式将配置文件的内容提供出去。
Config Client：通过接口获取数据、并依据此数据初始化自己的应用。






# 参考 
* [springcloud Config 入门，带视频](https://www.cnblogs.com/crazymakercircle/p/12043604.html)
* [Spring Cloud Config采用数据库存储配置内容](http://blog.didispace.com/spring-cloud-starter-edgware-3-1/)
* [Spring Cloud Config采用Git存储时两种常用的配置策略](http://blog.didispace.com/Spring-Cloud-Config-Git-Config-Two-Mode/)
* [Spring Cloud Config采用数据库存储配置内容【Edgware+】](http://blog.didispace.com/spring-cloud-starter-edgware-3-1/)
* [Spring Cloud Config Server迁移节点或容器化带来的问题](http://blog.didispace.com/Spring-Cloud-Config-Server-ip-change-problem/)
* [Spring Cloud Config对特殊字符加密的处理](http://blog.didispace.com/spring-cloud-config-sp-char-encrypt/)
* [Spring Cloud构建微服务架构：分布式配置中心（加密解密）](http://blog.didispace.com/spring-cloud-starter-dalston-3-2/)
* [Spring Cloud Config的配置中心获取不到最新配置信息的问题](http://blog.didispace.com/spring-cloud-tips-config-tmp-clear/)
* [Spring Cloud构建微服务架构（四）分布式配置中心（续）](http://blog.didispace.com/springcloud4-2/)
* [Spring Cloud构建微服务架构（四）分布式配置中心](http://blog.didispace.com/springcloud4/)
* [微服务与API网关（上）: 为什么需要API网关](https://blog.didispace.com/hzf-ms-apigateway-1/)
* [谈谈 API 网关](https://www.jianshu.com/p/b52a2773e75f)
* [谈谈微服务中的 API 网关（API Gateway）](https://www.cnblogs.com/savorboard/p/api-gateway.html)
* [API网关性能比较：NGINX vs. ZUUL vs. Spring Cloud Gateway](http://www.360doc.com/content/18/0208/05/46368139_728502763.shtml)
* [谈 API 网关的背景、架构以及落地方案](https://www.infoq.cn/news/2016/07/API-background-architecture-floo/)
* [zuul和nginx](https://zhuanlan.zhihu.com/p/37385481)
