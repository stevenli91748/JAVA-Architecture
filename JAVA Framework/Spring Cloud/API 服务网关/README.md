# 参考的书 
* Spring Cloud微服务架构实战派.PDF
* [（Spring Cloud 深入系列 ）Spring Cloud 微服务架构进阶](https://weread.qq.com/web/reader/c9932ea07163ff6ac993e0dkc81322c012c81e728d9d180)
* [8 重新定义Spring Cloud实战---2018 Spring Cloud中国社区倾力打造，多位行业专家推荐，作者来自原阿里、蚂蚁金服、京东金融等互联网企业, 一本实践与理论并重、广度与深度兼顾的Spring Cloud生产实践开发指南, 有3大特色: 足够广,有深度,重实践](https://weread.qq.com/web/reader/71d32370716443e271df020kc81322c012c81e728d9d180)
* [10  （先看）  深入理解Spring Cloud与微服务构建 （第2版）---包括Spring Boot + Eureka、Consul, Ribbon + RestTemplate , Feign、Hystrix、Zuul、Gateway、Consul、Config、Sleuth、Admint等组件,使用Spring Cloud OAuth2来保护微服务系统](https://weread.qq.com/web/reader/1223205071ccfab912296c2)
---


# 目录
  * [百亿流量交易系统API网关设计](https://weread.qq.com/web/reader/d9e327a07188b377d9eb7dakd67323c0227d67d8ab4fb04)
  * Web应用防火墙（WAF）---WAF一般是基于Nginx/OpenResty的ngx_lua模块开发的Web应用防火墙,简单地说就是在Nginx本身的代理能力以外，添加了安全相关功能。用一句话描述其原理，就是解析HTTP请求（协议解析模块），规则检测（规则模块），做不同的防御动作（动作模块），并将防御过程（日志模块）记录下来
    * Web应用防火墙功能
      * 防止SQL注入、部分溢出、fuzzing测试、XSS/SSRF等Web攻击；● 防止Apache Bench之类压力测试工具的攻击
      * 屏蔽常见的扫描黑客工具，比如扫描器
      * 禁止图片附件类目录执行权限、防止webshell上传
      * 支持IP白名单和黑名单功能，直接拒绝黑名单的IP访问
      * 支持URL白名单，定义不需要过滤的URL
      * 支持User-Agent的过滤、支持CC攻击防护、限制单个URL指定时间的访问次数
      * 支持支持Cookie过滤，URL与URL参数过滤
      * 支持日志记录，将所有拒绝的操作记录到日志中  
  * [API 网关的概念](#API-网关的概念)
    * API网关有四大职能
      * 请求接入---作为所有API接口服务请求的接入点，管理所有的接入请求
      * 业务聚合---作为所有后端业务服务的聚合点，所有的业务服务都可以在这里被调用
      * 中介策略---实现安全、验证、路由、过滤、流控、缓存等策略，进行一些必要的中介处理
      * 统一管理---提供配置管理工具，对所有API服务的调用生命周期和相应的中介策略进行统一管理。 
    * API网关海量并发最重要的三个关注点
      * 保持大规模的inbound请求接入能力（长短连接），比如基于Netty实现
      * 最大限度地复用outbound的HTTP连接能力，比如基于HttpClient4的异步HttpClient实现
      * 方便灵活地实现安全、验证、过滤、聚合、限流、监控等各种策略。
    * API网关的分类
      * 流量网关是全局性的，跟具体的后端业务系统和服务完全无关的部分，这样流量网关关注全局流量的稳定与安全, 比如安全策略、全局性流控策略、流量分发策略等
        * 流量网关具体功能
          * 全局性流控
          * 日志统计
          * 防止SQL注入
          * 防止Web攻击
          * 屏蔽工具扫描
          * 黑白名单控 
          * 接入请求到业务系统的负载均衡
      * 业务网关针对具体的后端业务系统，或者是服务和业务有一定关联性的部分，业务网关一般部署在流量网关之后、业务系统之前，比流量网关更靠近业务系统。
        * 业务网关具体功能
          * 针对某个系统、某个服务或某个用户分类的流控策略
          * 针对某一类服务的缓存策略
          * 针对某个具体系统的权限验证方式
          * 针对某些用户条件判断的请求过滤
          * 针对具体几个相关API的数据聚合封装  
  * [服务网关的基本功能](https://github.com/aCoder2013/blog/issues/35)
    * 路由转发
      * 动态路由：能够匹配任何请求属性 
      * 可以对路由指定 Predicate（断言）和 Filter（过滤器）；
      * 易于编写的 Predicate（断言）和 Filter（过滤器）
      * 支持路径重写。
    * 单点入口
    * 熔断
    * [服务限流](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring%20Cloud/%E9%99%90%E6%B5%81/README.md)
    * [服务降级](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring%20Cloud/%E6%9C%8D%E5%8A%A1%E9%99%8D%E7%BA%A7/README.md)
    * 安全认证
    * 日志监控
    * 集成 Spring Cloud 服务发现功能；
    * 集成Hystrix的断路器功能 
  * [API网关异步化改造技术选型](https://github.com/aCoder2013/blog/issues/34)
  * 服务网关的分类
    * [常见的开源网关分类](https://weread.qq.com/web/reader/d9e327a07188b377d9eb7dak34132fc02293416a75f431d) 
      * [Nginx+Lua](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring%20Cloud/API%20%E6%9C%8D%E5%8A%A1%E7%BD%91%E5%85%B3/Nginx%20%2B%20Lua/README.md)---Open Resty、[Kong](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring%20Cloud/API%20%E6%9C%8D%E5%8A%A1%E7%BD%91%E5%85%B3/Kong/README.md)、Orange、Abtesting Gateway等
      * Java---[Zuul/Zuul 2](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring%20Cloud/API%20%E6%9C%8D%E5%8A%A1%E7%BD%91%E5%85%B3/Zuul/README.md)、[Spring Cloud Gateway](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring%20Cloud/API%20%E6%9C%8D%E5%8A%A1%E7%BD%91%E5%85%B3/Spring%20Cloud%20Gateway/README.md)、Kaazing KWG、gravitee、Dromara soul等
      * Go---Janus、fagongzi、Grpc-Gateway
      * .NET---Ocelot
      * Node.js---Express Gateway、MicroGateway。 
  * Route路由信息
  * 断言工厂
  * 
  * Predicate机制
  * Filter机制
  * 整合注册中心和配置中心
  * GatewayControllerEndpoint 
  * API网关扩展
  * 灰度发布


## API 网关的概念

作为后端服务的统一入口，API网关可提供请求路由、协议转换、安全认证、服务鉴权、流量控制与日志监控等服务。使用微服务架构将所有的应用管理起来，那么API网关就起到了微服务网关的作用；如果只是使用REST方式进行服务之间的访问，使用API网关对调用进行管理，那么API网关起到的就是API服务治理的作用。不管哪一种使用方式，都不影响API网关核心功能的实现


服务网关是微服务架构中一个不可或缺的部分。在通过服务网关统一向外系统提供REST API的过程中，除了具备服务路由、均衡负载功能之外，它还具备了权限控制等功能

为微服务架构提供了前门保护的作用，同时将权限控制这些较重的非业务逻辑内容迁移到服务路由层面，使得服务集群主体能够具备更高的可复用性和可测试性

API网关作为分散在各个业务系统微服务的API聚合点和统一接入点，外部请求通过访问这个接入点，即可访问内部所有的REST API服务

Github中的100个项目里，使用Go语言实现的Gateway差不多占一半，从语言分类上来看：Go＞Node.js/JavaScript＞Java＞Lua＞C/C++＞PHP＞ Python/Ruby/Perl

网关的角色是作为一个API架构，用来保护、增强和控制对于API服务的访问，API网关是一个处于应用程序或服务（提供REST API接口服务）之前的系统，用来管理授权、访问控制和流量限制等，这样REST API接口服务就被API网关保护起来，对所有的调用者透明。因此，隐藏在API网关后面的业务系统就可以专注于创建和管理服务，而不用去处理这些策略性的基础设施。这样，网关系统就可以代理业务系统的业务服务API。此时网关接收外部其他系统的服务调用请求，也需要访问后端的实际业务服务。在接收请求的同时，可以实现安全相关的系统保护措施。在访问后端业务服务的时候，可以根据相关的请求信息做出判断，路由到特定的业务服务上，或者调用多个服务后聚合成新的数据返回给调用方。网关系统也可以把请求的数据做一些过滤和预处理，同理也可以把返回给调用者的数据做一些过滤和预处理，即根据需要对请求头/响应头、请求报文/响应报文做一些修改。如果不做这些额外的处理，则简单直接代理服务API功能，我们称之为透传。同时，由于REST API的语言无关性，基于API网关，后端服务可以是任何异构系统，不论Java、.NET、Python，还是PHP、ROR、Node.js等，只要支持RESTAPI，就可以被API网关管理起来


# 参考
* [SpringCloudAlibaba网关Gateway集成Sentinel](https://www.jianshu.com/p/f2f7019b4a3e)
* [SpringCloudAlibaba集成Gateway动态路由Nacos服务](https://www.jianshu.com/p/36373e5be522)
* [Gateway动态路由实现](https://www.jianshu.com/p/7dfc8b67149e)
* [Spring Cloud Gateway原理解析](https://www.jianshu.com/p/daaa38070bf2)
* [Spring Cloud Gateway + Jwt + Oauth2 实现网关的鉴权操作](https://www.jianshu.com/p/c9f0f1c7333c)
* [SpringCloud gateway （史上最全）](https://www.cnblogs.com/crazymakercircle/p/11704077.html)
* [初识Spring WebFlux和Spring Cloud Gateway](https://linz.ink/%E7%BD%91%E5%85%B3/gateway/spring%20cloud/webflux/2019/02/03/spring-webflux-cloud-gateway-overview.html)
