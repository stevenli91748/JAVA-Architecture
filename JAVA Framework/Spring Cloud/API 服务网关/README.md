# 参考的书 
* Spring Cloud微服务架构实战派.PDF
* [（Spring Cloud 深入系列 ）Spring Cloud 微服务架构进阶](https://weread.qq.com/web/reader/c9932ea07163ff6ac993e0dkc81322c012c81e728d9d180)
* [8 重新定义Spring Cloud实战---2018 Spring Cloud中国社区倾力打造，多位行业专家推荐，作者来自原阿里、蚂蚁金服、京东金融等互联网企业, 一本实践与理论并重、广度与深度兼顾的Spring Cloud生产实践开发指南, 有3大特色: 足够广,有深度,重实践](https://weread.qq.com/web/reader/71d32370716443e271df020kc81322c012c81e728d9d180)
* [10  （先看）  深入理解Spring Cloud与微服务构建 （第2版）---包括Spring Boot + Eureka、Consul, Ribbon + RestTemplate , Feign、Hystrix、Zuul、Gateway、Consul、Config、Sleuth、Admint等组件,使用Spring Cloud OAuth2来保护微服务系统](https://weread.qq.com/web/reader/1223205071ccfab912296c2)
---


# 目录
  * [百亿流量交易系统API网关设计](https://weread.qq.com/web/reader/d9e327a07188b377d9eb7dakd67323c0227d67d8ab4fb04)
  * [API 网关的概念](#API-网关的概念) 
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
    * [Zuul](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring%20Cloud/API%20%E6%9C%8D%E5%8A%A1%E7%BD%91%E5%85%B3/Zuul/README.md)
    * [Consul](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring%20Cloud/API%20%E6%9C%8D%E5%8A%A1%E7%BD%91%E5%85%B3/Consul/README.md)
    * [Nginx + Lua](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring%20Cloud/API%20%E6%9C%8D%E5%8A%A1%E7%BD%91%E5%85%B3/Nginx%20%2B%20Lua/README.md)
    * [Kong](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring%20Cloud/API%20%E6%9C%8D%E5%8A%A1%E7%BD%91%E5%85%B3/Kong/README.md)
    * [非阻塞式的Spring Cloud Gateway](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring%20Cloud/API%20%E6%9C%8D%E5%8A%A1%E7%BD%91%E5%85%B3/Spring%20Cloud%20Gateway/README.md)
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

# 参考
* [SpringCloudAlibaba网关Gateway集成Sentinel](https://www.jianshu.com/p/f2f7019b4a3e)
* [SpringCloudAlibaba集成Gateway动态路由Nacos服务](https://www.jianshu.com/p/36373e5be522)
* [Gateway动态路由实现](https://www.jianshu.com/p/7dfc8b67149e)
* [Spring Cloud Gateway原理解析](https://www.jianshu.com/p/daaa38070bf2)
* [Spring Cloud Gateway + Jwt + Oauth2 实现网关的鉴权操作](https://www.jianshu.com/p/c9f0f1c7333c)
* [SpringCloud gateway （史上最全）](https://www.cnblogs.com/crazymakercircle/p/11704077.html)
* [初识Spring WebFlux和Spring Cloud Gateway](https://linz.ink/%E7%BD%91%E5%85%B3/gateway/spring%20cloud/webflux/2019/02/03/spring-webflux-cloud-gateway-overview.html)
