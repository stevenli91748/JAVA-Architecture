
 [Spring Cloud Config](https://weread.qq.com/web/reader/c9932ea07163ff6ac993e0dk64232b60230642e92efb54c)|[Spring Cloud Config上篇](https://weread.qq.com/web/reader/71d32370716443e271df020kfc432fb0241fc490ca45614)|[Spring Cloud Config下篇](https://weread.qq.com/web/reader/71d32370716443e271df020k14b3246024514bfa6bb1534)|
 ---|---|---|
 
 # 目录
 * [Spring Cloud Config统一配置管理快速入门例子](https://mrbird.cc/Spring-Cloud-Config.html)
 * [Config概念](#Config概念)
 
 
 ## Config概念
 
Spring Cloud Config是一个解决分布式系统的配置管理方案。微服务意味着要将单体应用中的业务拆分成一个个子服务，每个服务的粒度相对较小，因此系统 中会出现大量的服务。由于每个服务都需要必要的配置信息才能运行，所以一套集中式的、 动态的配置管理设施是必不可少的。Spring Cloud提供了ConfigServer来解决这个问题,我们每一个微服务自 己带着一个application.yml 上百个配置文件的管理。

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
