**对于外部AP I 调用或者客户端对后端API 的访问，可以使用HTTP 协议或者RSTful（当然也可以直接通过最原始的Socket 来调用） 。但对于内部服务间的调用， 一般都是通过RPC 机制来进行的**

**前后端分离已成为互联网项目开发的业界标准使用方式，通过nginx+tomcat的方式（也可以中间加一个nodejs）有效的进行解耦，核心思想是前端HTML页面通过AJAX调用后端的RESTFUL API接口并使用JSON数据进行交互,Web服务器：一般指像Nginx，Apache这类的服务器，他们一般只能解析静态资源；应用服务器：一般指像Tomcat，Jetty，Resin这类的服务器可以解析动态资源也可以解析静态资源，但解析静态资源的能力没有web服务器好；一般都是只有web服务器才能被外网访问，应用服务器只能内网访问**


<a href="https://ibb.co/5jFx6Fg"><img src="https://i.ibb.co/gWVF3VL/23600667-7082dd4cefd14184.webp" alt="23600667-7082dd4cefd14184" border="0"></a>

# [API网关面试知识点](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20Web/Interview/apigate.md)


# 在线书籍
* [前端技术架构与工程](https://weread.qq.com/web/reader/14d32a407274b49c14d597dkc81322c012c81e728d9d180)
* [现代API: 通往架构师之门---2018  作者20年为北美18个行业50多家大型企业进行系统集成及API设计的实践经验](https://weread.qq.com/web/reader/b3f321407170d865b3f0e45)
* [API安全技术与实战---2021](https://weread.qq.com/web/reader/54d325707255164554dc831kc81322c012c81e728d9d180)
* [GraphQL 学习指南](https://weread.qq.com/web/reader/2f832c007187fc842f8c219)
* [RESTful Web API Design with Node.js (Second Edition)](https://weread.qq.com/web/reader/e18328807230082ee18b465)
* [hand-on RESTFul API Design patterns and best practices](https://weread.qq.com/web/reader/21f32c20722ffb4121fa251)

---


[REST API Tutorial with example](https://www.javaguides.net/p/rest-api-tutorial.html)|[RESTful Basics Tutorial with Examples](https://www.javaguides.net/p/restful-tutorial.html)|[Guide to Standard JAX-RS RESTful Api Annotations](https://www.javaguides.net/2018/06/guide-to-standard-jax-rs-annotations.html)|
---|---|---|



[Vert.x](https://www.bilibili.com/video/BV174411E7Vr?from=search&seid=3412424618058651269)|
---|

[kong](https://www.bilibili.com/video/BV16E411n7Bu?from=search&seid=13204508987684806757)|[Spring Cloud Gateway](https://www.bilibili.com/video/BV11b411V7MA?from=search&seid=10918068436806250854)|[Postman Beginner's Course - API Testing](https://www.youtube.com/watch?v=VywxIQ2ZXw4)|
---|---|---|

[API网关介绍及选型(kong)](https://blog.csdn.net/pushiqiang/article/details/95726137?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2.channel_param)|
---|



[黑客攻击你的接口服务，如何给你的开放API穿上“金甲神盾”](https://www.bilibili.com/video/BV1qs411J788)|[90分钟教你实现，接口优雅限流降级](https://www.bilibili.com/video/BV1YW41197mr)|
---|---|

[APIs for Beginners - How to use an API ](https://www.youtube.com/watch?v=GZvSYJDk-us)|[别在简历写秒杀系统了！我告诉你消息管理平台实现原理吧---怎样设计接口](https://zhuanlan.zhihu.com/p/258732579)|[RESTfull API简单项目的快速搭建](https://www.bilibili.com/video/av13224378?from=search&seid=10786600269014143134)|
---|---|---|

[Cookie、Session、Token与JWT解析](https://www.jianshu.com/p/cab856c32222)
---|

# 例子

[RESTful Basics Tutorial with Examples](https://www.javaguides.net/p/restful-tutorial.html)|[Jersey Rest Api Examples](https://www.javaguides.net/p/jersey-rest-examples.html)
---|---|

[Spring Boot API Project Seed 是一个基于Spring Boot & MyBatis的种子项目，用于快速构建中小型API、RESTful API项目](https://github.com/lihengming/spring-boot-api-project-seed)|
---|

[Build REST API using Spring Boot的多种例子](https://www.javaguides.net/p/rest-api-tutorial.html)|
---|

[开源商城系统-API文档（单商户）例子，有参考价值](https://www.kancloud.cn/yi_xiang_42/likeshop_api_v2_x)|
---|


[Spring Boot构建 CRUD服务 RESTful Web API接口的完整例子1](https://www.yiibai.com/spring-boot/spring_boot_building_restful_web_services.html)|[Spring Boot 项目实践之 RESTful API 设计与实现](https://github.com/ZHENFENG13/spring-boot-projects/tree/main/SpringBoot%E5%85%A5%E9%97%A8%E6%A1%88%E4%BE%8B%E6%BA%90%E7%A0%81/spring-boot-RESTful-api)|
---|---|

# 目录

* [WebHook---反向 API](https://www.jianshu.com/p/9829986b4363)
  * [使用webhook完成自动化部署](https://zhuanlan.zhihu.com/p/93223263) 

## 第一部分 RestFulAPI 篇

## RestFulAPI 设计思路
* [REST API 教程：REST 客户端，REST 服务及 API 调用（含代码示例）](https://chinese.freecodecamp.org/news/rest-api-tutorial-rest-client-rest-service-and-api-calls-explained-with-code-examples/)
* [微服务手册：API接口9个生命节点，构建全生命周期管理](https://www.toutiao.com/i6900147166866702851/?group_id=6900147166866702851)
* [HTTP 接口设计指北](https://www.kancloud.cn/kancloud/http-api-guide)
* API的意义---我们必须拥抱 API，并使用 API 先行（API-first）的集成思维来开发我们的集成用例，以 API 驱动的方式开展项目开发和集成
  * [企业中的 API 设计思路---以API 驱动的方式设计系统  ](https://www.infoq.cn/article/9aWfgWP7cWqGW44vMmeZ?utm_source=related_read_bottom&utm_medium=article)
    * [Mock API Server Online Testing & API Mocking Guide](https://stoplight.io/mock-api-guide/basics/) 
* [如何设计一个安全的对外接口，老司机总结了这几点.](https://mp.weixin.qq.com/s/i4N5-ftqNdgottfcxigHxQ)
* [接口调用幂等性问题+解决方案](https://mp.weixin.qq.com/s/R9N1L57s7amp80Gxn-ycHg)
* [Api 接口优化的几个技巧](https://mp.weixin.qq.com/s/W_u55Spcm46mnYiujwyPHg)
* [初探APP架构之后端接口设计方案](https://www.jianshu.com/p/f3b747abdf2a)
* [如何设计一个牛逼的API接---主要从几个方面来设计一个安全的API接口](https://segmentfault.com/a/1190000037503942)
  * 安全性---强烈建议 API 部署 SSL 证书，这样接口传递的数据的安全性才能都得一定的保障
    * [REST API 安全设计指南](https://www.kancloud.cn/kancloud/rest-api-design-safety) 
    * [接口身份验证解决方案](https://www.kancloud.cn/kancloud/http-api-guide/56272)
      * [HTTP 基本认证](https://zh.wikipedia.org/wiki/HTTP%E5%9F%BA%E6%9C%AC%E8%AE%A4%E8%AF%81)---只有在部署了 SSL 证书的情况下才可以使用，否则用户密码会有暴露的风险，当然，最好不要使用
      * [JSON Web Token](https://datatracker.ietf.org/doc/html/draft-ietf-oauth-json-web-token-25)---支持通过登录接口使用账号密码获取，在请求接口时使用 Authorization: Bearer #{token} 头标或者 token 参数的值的方式进行验证  
        * [Json Web Tokens: Introduction](http://angular-tips.com/blog/2014/05/json-web-tokens-introduction/)
        * [son Web Tokens: Examples](http://angular-tips.com/blog/2014/05/json-web-tokens-examples/)
        * Cookies vs Tokens. Getting auth right with Angular.JS 
      * OAuth 2.0
        * [官网 ](https://oauth.net/2/)
        * [理解OAuth 2.0 ](http://www.ruanyifeng.com/blog/2014/05/oauth_2_0.html)
  * 幂等性
  * [接口设计有三个层面的复用](https://juejin.cn/post/6844904065852260366)
    * 跨项目复用
    * 跨页面复用
    * 跨端复用 
  * 数据规范
  * 接口文档管理    
* [4 种主流的 API 架构风格对比](https://mp.weixin.qq.com/s/g4ZYYpXoSBNroPNHqciixA)
  * 1 RPC：调用另一个系统的函数
  * 2 SOAP：使数据作为服务可用
  * 3 REST：使数据作为资源可用
  * 4 GraphQL：仅请求所需要的数据
    * [Spring Boot + GraphQL 才是 API 的未来！](https://mp.weixin.qq.com/s/0PnHhG_AcPxUJIClvx1fdQ)
    * [GraphQL 入门看这篇就够了](https://chinese.freecodecamp.org/news/a-detailed-guide-to-graphql/)  
* API 可分三类
  * 1 边缘（edge）API---边缘 API 担当企业系统的网关，边缘 API 是前端团队需要与之交互的。首先设计边缘 API，其中大多数用于访问特定的系统功能。避免将后端的复杂性泄漏给前端应用，一旦设计好边缘 API，后端团队就可以匹配边缘 API 的需求，着手设计工具 API 和领域 AP。如果碰上一些未考虑到的问题，可以按需回滚，更新边缘 API
    * [以API 先行方法 驱动的方式开展边缘（edge）API项目的开发步骤](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20Web/RESTful%20API/%E4%BB%A5%20API%20%E9%A9%B1%E5%8A%A8%E7%9A%84%E6%96%B9%E5%BC%8F%E5%BC%80%E5%B1%95%E9%A1%B9%E7%9B%AE%E5%BC%80%E5%8F%91%E7%9A%84%E6%AD%A5%E9%AA%A4.md)
  * 2 工具类（utility）API---工具类 API 中包含集成逻辑，实现边际 API 和内部系统的相互黏合
  * 3 领域（domain）API--- 领域 API 向集成层开放企业内部系统

## RestFulAPI 工具
* [一个基于 Spring 极度简单的 Restful API 工具，非常好用](https://mp.weixin.qq.com/s/Mnz26kCVEyQWckkOKPkJ-A)
* [你还在用 Postman？IDEA REST Client 好用到爆，Postman 可以扔了](https://mp.weixin.qq.com/s/QwoZkMxOTgCHhKWOv2rEag)
* [微服务如何聚合Swagger实现接口文档管理](https://developer.51cto.com/article/701318.html)



## RestFulAPI 项目实践  

* [ RESTfull API简单项目的快速搭建--视频](https://www.bilibili.com/video/av13224378?from=search&seid=10786600269014143134)
* [【项目实践】SpringBoot三招组合拳，手把手教你打出优雅的后端接口](https://zhuanlan.zhihu.com/p/134209666?utm_source=wechat_session&utm_medium=social&utm_oi=991812777480134656)
* [【项目实践】后端接口统一规范的同时，如何优雅得扩展规范](https://juejin.im/post/6844904143912435719?)
* [【项目实践】SpringBoot-如何设计优秀的后端接口](https://juejin.im/post/6860404263143604232)
* [Spring Boot 如何优雅的进行字段校验？写得太好了！](https://mp.weixin.qq.com/s/EfIh_izIeUv4zNqcDxf-hg)
* [SpringBoot中处理校验逻辑的两种方式，真的很机智！](http://www.macrozheng.com/#/technology/springboot_validator)
* [springboot2.2.X手册：构建多元化的API接口，我们这样子设计](https://www.toutiao.com/i6824397028622074379/?group_id=6824397028622074379&wid=1627684804783)
* [看看人家那后端API接口写得，那叫一个优雅！---着重介绍一下后端服务器如何实现把数据返回给前端](https://www.jianshu.com/p/3c1d3ba5f56d)
* [如何优雅设计 API 接口，实现统一格式返回？](https://www.jianshu.com/p/90bac4eda34a)
* [前端业务开发的通用经验 - 接口篇](https://juejin.cn/post/6844904065852260366)
* [REST API Design Documenttion tools](https://www.kancloud.cn/thinkphp/api-design-and-documentation/43384) 
* [REST API 接口测试](https://www.imooc.com/learn/1048)
* [RESTful API 最佳实践](https://www.ruanyifeng.com/blog/2018/10/restful-api-best-practices.html)
* [基于token身份认证的完整实例](https://www.jianshu.com/p/75f6d7426118)

* [设计模式---接口设计原则之分离原则详解](https://www.jianshu.com/p/fe1e778b4f52)
* [【从零入门系列-4】Spring Boot 之 WEB接口设计实现](https://segmentfault.com/a/1190000019191020)
* [谁要是再敢用Map传参，我过去就是一JIO](https://www.bilibili.com/read/cv5441884)
* [啥？听说你还在手写复杂的API 参数校验？](https://www.bilibili.com/read/cv4308221)
* [【项目实践】SpringBoot三招组合拳，手把手教你打出优雅的后端接口,一步一步演示如何构建起一个优秀的后端接口体系](https://www.jianshu.com/p/b5b8613769db)

* [SpringBoot自定义控制层参数解析](https://www.jianshu.com/p/41beefc40905)
* [SpringBoot实现API接口的完整代码](https://www.jb51.net/article/198167.htm)
* [Spring Boot项目的接口防刷](https://www.javaweb.shop/article/204.html)
* [使用WireMock快速伪造RESTful服务](https://mrbird.cc/WireMock-mock-RESTful.html)
* [Spring Boot整合Swagger2构建RESTful API](https://mrbird.cc/Spring-Boot-Swagger2-RESTful-API.html)


## 第二部分 API网关 篇
* [这样讲 API 网关，你应该能明白了吧](https://zhuanlan.zhihu.com/p/90433496)
* [零、API网关的分类](#API网关的分类)
  * [【云框架项目实战】KONG API Gateway](https://github.com/cloudframeworks-apigateway/user-guide-apigateway) 
* [一、API网关的用处](#API网关的用处)
* [二、API网关在企业架构中的地位](#API网关在企业架构中的地位)
* [三、企业中如何应用API网关](#企业中如何应用API网关)
* [四、API网关有哪些竞争方案](#API网关有哪些竞争方案)
* [五、API网关解决方案](#API网关解决方案)
* [六、企业怎么选择API网关](#企业怎么选择API网关)
* 参考： [百亿流量微服务网关的设计与实现](https://www.infoq.cn/article/EeE1xZeic4UdpbmR*03t)
* [Spring Cloud Gateway + Jwt + Oauth2 实现网关的鉴权操作](https://www.jianshu.com/p/c9f0f1c7333c)

## 第三部分 前后端分离篇



* [前后端分离的思考与实践](https://www.kancloud.cn/kancloud/midway)
* [1-1 前后端分离是如何做的](#前后端分离是如何做的)
* [1-2  软件开发中前端和后端如何协同开发和集成](#软件开发中前端和后端如何协同开发和集成)
* [包工头都能听明白的前后端分离 ---视频](https://www.bilibili.com/video/BV1f7411L7By)
* [前后端分离架构，Node 中间层](https://www.crs811.com/archives/1542)
* [前后端联调](https://www.jianshu.com/p/91486e9c0e34)

* [快速构建标准RESTful API完成前后端分离开发---视频](https://www.bilibili.com/video/BV12W411b7oT?from=search&seid=2011606573791409942)
* [4小时学会Spring Boot+Vue前后端分离开发---视频](https://www.bilibili.com/video/BV137411B7vB/?spm_id_from=333.788.videocard.9)
* [React+springboot权限管理系统·前后端分离](https://www.jianshu.com/p/b34674cd328d)
* [公司项目里如何进行前后端接口联调系列](https://www.jianshu.com/search?q=%E5%90%8E%E7%AB%AF%E6%8E%A5%E5%8F%A3&page=1&type=note)
* [Mockjs,再也不用追着后端小伙伴要接口了](https://www.jianshu.com/p/dd23a6547114)
* [spring boot +themyleaf+vue前后端分离](https://www.zybuluo.com/EggGump/note/1207305)
* [这次要讲不清前后端分离，我都怎么地！](https://mp.weixin.qq.com/s/Y0z-3r_Mdm-etCwa0GmQmQ)
* [前后端分离的接口规范，我们是这样做的](https://mp.weixin.qq.com/s/zAOYAcR-6DEJU_s0qXe91g)
* [前后端分离式开发：高效协作10板斧](https://mp.weixin.qq.com/s/niYHlrCDIkA1NqPVI_VJ8w)
* [前后端都分离了，该搞个好用的API管理系统了！](https://mp.weixin.qq.com/s/Ahs6fnIfFVVPOn3NZpIsNA)
* [前后端分离项目，如何优雅实现文件存储！](http://www.macrozheng.com/#/technology/minio_use)
* [前后端分离项目，引入Spring Cloud Gateway遇到的一个问题](http://www.macrozheng.com/#/technology/gateway_cors)

* [前端架构之路（1） - 前后端分离、web与static服务器分离](https://www.jianshu.com/p/2f651321fb08)
* [前端架构之路（2） - 本地化接口模拟、前后端并行开发](https://www.jianshu.com/p/efb29098a685)
* [前端架构之路（3） - 前端开发规范](https://www.jianshu.com/p/db4d72ad0530)
* [前端架构之路（4） - 前端开发文档](https://www.jianshu.com/p/c99232d9dda7)
* [前端架构之路（5） - 构建工具 for teamwork](https://www.jianshu.com/p/491d496917c8)
* [前端架构之路（6） - 组件化](https://www.jianshu.com/p/7827b4f6b0a9)
* [前端架构之路（7） - 私有 npm 仓库](https://www.jianshu.com/p/5e6129a20d33)
* [前端架构之路（8） - 单页面应用（SPA）、按需加载](https://www.jianshu.com/p/6722940919ca)
* [前端架构之路（9） - 服务器端渲染（SSR）与 node 中间层](https://www.jianshu.com/p/941a7a6eedf7)



# API网关的分类
  
  API 网关拆分成为 3 个系统：

     Gateway-Core（核心）
     Gateway-Admin（管理）
     Gateway-Monitor（监控）
    

 ## 流量网关：
    一类是全局性的，跟具体的后端业务系统和服务完全无关的部分，比如安全策略、全局性流控策略、流量分发策略等。通常有如下通用性的具体功能：
    
    1.全局性流控；
    2.日志统计；
    3.防止 SQL 注入；
    4.防止 Web 攻击；
    5.屏蔽工具扫描；
    6.黑白名单控制。
    
    Kong 的性能非常不错，非常适合做流量网关
    
 ## 业务网关：
    一类是业务网关,一般部署在流量网关之后、业务系统之前，比流量网关更靠近业务系统

    简单使用或轻度定制业务网关系统，目前建议使用 Spring CloudGateway 作为基础骨架。

    ---
 
目前常见的开源网关大致上按照语言分类有如下几类。

    Nginx+Lua：Open Resty、Kong、Orange、Abtesting Gateway 等；

    Java：Zuul/Zuul 2、Spring Cloud Gateway、Kaazing KWG、gravitee、Dromara soul 等；

    Go：Janus、fagongzi、Grpc-Gateway；

    .NET：Ocelot；

    Node.js：Express Gateway、MicroGateway。

按照使用范围、成熟度等来划分，主流的有 4 个：OpenResty、Kong、Zuul/Zuul 2、Spring Cloud Gateway，此外 fagongzi API 网关最近也获得不少关注。

* [微服务网关实战01-网关介绍](https://www.toutiao.com/a6808446597215552003/?log_from=f53faf1695dc8_1627707593158)
* [微服务网关实战02-网关搭建](https://www.toutiao.com/a6808731146826809870/?log_from=e9c137766c14b_1627708717793)
* [微服务网关实战03-网关请求日志及全局异常](https://www.toutiao.com/a6808833585475224076/?log_from=513da4df59178_1627708740697)
* [微服务网关实战04-网关限流及断路](https://www.toutiao.com/a6808834000853926404/?log_from=221a8ad575ac9_1627708761297)
* [微服务网关实战05-网关鉴权](https://www.toutiao.com/a6808842467312402955/?log_from=9d02be2f80d96_1627708784230)
* [微服务网关实战06-网关请求客户端](https://www.toutiao.com/a6808846956186567172/?log_from=d30316fb90f93_1627708805341)
* [微服务网关实战07-聚合服务](https://www.toutiao.com/a6808854890283008523/?log_from=ba807c27a6dc7_1627708844379)
* [微服务网关实战08-聚合服务请求日志及断路](https://www.toutiao.com/a6808861304242045453/?log_from=5df6fd223b401_1627708870534)
* [微服务网关实战09-聚合服务鉴权及限流](https://www.toutiao.com/a6808865734366593547/?log_from=268261052b5c7_1627708895489)





# 前后端分离是如何做的

    在前后端分离架构中，后端只需要负责按照约定的数据格式向前端提供可调用的 API 服务即可。前后端之间通过 HTTP 请求进行交互，前端获取到数据后，进行
    页面的组装和渲染，最终返回给浏览器
    
    前后端分离已成为互联网项目开发的业界标准使用方式，通过nginx+tomcat的方式（也可以中间加一个nodejs）有效的进行解耦，核心思想是前端HTML页面
    通过AJAX调用后端的RESTFUL API接口并使用JSON数据进行交互。
    
    在前后端彻底分离这一时期，前端的范围被扩展，controller层也被认为属于前端的一部分。在这一时期：
    前端：负责View和Controller层。
    后端：只负责Model层，业务/数据处理等。
    
    
    
    
   
    如何解决跨域：
    跨域，指的是浏览器不能执行其他网站的脚本。它是由浏览器的同源策略造成的，是浏览器对 JavaScript 施加的安全限制

    什么是同源？
    所谓同源是指，域名，协议，端口均相同

	http://www.baidu.com --> http://admin.baidu.com    跨域
	http://www.baidu.com --> http://www.baidu.com      非跨域
	http://www.baidu.com --> http://www.baidu.com:8080 跨域
	http://www.baidu.com --> https://www.baidu.com     跨域

    使用 CORS（跨资源共享）解决跨域问题：
    CORS 是一个 W3C 标准，全称是"跨域资源共享"（Cross-origin resource sharing）。它允许浏览器向跨源服务器，发出 XMLHttpRequest 请求，从而克
    服了 AJAX 只能同源使用的限制。

    CORS 需要浏览器和服务器同时支持。目前，所有浏览器都支持该功能，IE 浏览器不能低于 IE10

    整个 CORS 通信过程，都是浏览器自动完成，不需要用户参与。对于开发者来说，CORS 通信与同源的 AJAX 通信没有差别，代码完全一样。浏览器一旦发现 
    AJAX 请求跨源，就会自动添加一些附加的头信息，有时还会多出一次附加的请求，但用户不会有感觉

    因此，实现 CORS 通信的关键是服务器。只要服务器实现了 CORS 接口，就可以跨源通信

    CORS 与 JSONP 的比较
    CORS 与 JSONP 的使用目的相同，但是比 JSONP 更强大。

    JSONP 只支持 GET 请求，CORS 支持所有类型的 HTTP 请求。JSONP 的优势在于支持老式浏览器，以及可以向不支持 CORS 的网站请求数据


# 软件开发中前端和后端如何协同开发和集成
   
     现在的前后端开发一般都是这么一个流程
     
     前端开发流程：
          前端和后端都会遵照一套数据模型定义。前段在开发的时候，会利用模拟数据进行开发。前端在开发的时候就不需要依赖后端的API。
	  这些模拟数据一般来自于内存数据库,就是使用真正的API接口来获取数据，只不过这个数据不是从远程服务器上获取，而是通过本地的内存数据库来获取。
          这样子开发出来的前端，几乎可以无缝的与后端API进行集成。当然需要一个参数变量，或者环境变量来设置是开发环境还是生产环境。如果是开发环境，
	  就直接获取本地内存数据库。如果是生产环境，则调用远程数据接口。在实际环境中，前后端首先在一起喝喝茶，讨论下数据模型和接口地址。说白点就是
	  我后端应该给你输出json啊 还是xml啊 还是静态页面啊之类的。商量好这些后 前端可以根据数据结构伪造数据(推荐一个mock.js,伪造数据和接口地
	  址的)，就可以开始撸码了。后端就可以去干后端的事情了。

     后端开发流程：
     
         后端开发要与前端完全分开，最好要使用rest API。这样子可以使每个接口不存在状态的记录，属于无状态API。这样的API可以让任何的终端调用。
	 一般过程是这样子的，装好插件或者程序以后,如果需要的话，设定输入参数, 然后进行相关的HTTP操作。再查看返回结果。根据返回结果进行调试。
 
     前后端集成：
     
        假设前端的开发任务已经完成，后端的开发任务也已经完成。两者需要真正的集成测试一下。那么前端这一部分只需要打开生产环境变量就可以了。
 
# API网关的用处

# API 网关实现功能

1. 负载均衡
2. 路由选择---根据上下文或消息内容将请求发送到不同的目标
3. 流量控制---入站和出站流量监控
4. 统一鉴权
5. 熔断降级
6. 发布测试
7. 缓存数据
8. 日志记录

# API网关我的分析中会用到以下三种场景。

**1、Open API**

企业需要将自身数据、能力等作为开发平台向外开放，通常会以rest的方式向外提供。最好的例子就是淘宝开放平台、腾讯公司的QQ开发平台、微信开放平台。
Open API开放平台必然涉及到客户应用的接入、API权限的管理、调用次数管理等，必然会有一个统一的入口进行管理，这正是API网关可以发挥作用的时候。

**2、微服务网关**

微服务的概念最早在2012年提出，在Martin Fowler的大力推广下，微服务在2014年后得到了大力发展。**在微服务架构中，有一个组件可以说是必不可少的，那就是微服务网关，微服务网关处理了负载均衡，缓存，路由，访问控制，服务代理，监控，日志等。API 网关在微服务架构中正是以微服务网关的身份存在。**

**3、API服务管理平台**

上述的微服务架构对企业来说有可能实施上是困难的，企业有很多遗留系统，要全部抽取为微服务改动太大，对企业来说成本太高。**但是由于不同系统间存在大量的API服务互相调用，因此需要对系统间服务调用进行管理，清晰地看到各系统调用关系，对系统间调用进行监控等。API网关可以解决这些问题，我们可以认为如果没有大规模的实施微服务架构，那么对企业来说微服务网关就是企业的API服务管理平台。**

# API网关在企业架构中的地位

一个企业随着信息系统复杂度的提高，必然出现外部合作伙伴应用、企业自身的公网应用、企业内网应用等。在架构上应该将这三种应用区别开，三种应用的安排级别、访问方式也不一样。因此在我的设计中将这三种应用分别用不同的网关进行API管理，分别是：**API网关（OpenAPI合伙伙伴应用）、API网关（内部应用）、API网关（内部公网应用）。**

<a href="https://ibb.co/T1Dc7fv"><img src="https://i.ibb.co/HCL7bSF/api-gateway-1.jpg" alt="api-gateway-1" border="0"></a>

# 企业中如何应用API网关

1、对于OpenAPI使用的API网关来说，一般合作伙伴要以应用的形式接入到OpenAPI平台，合作伙伴需要到 OpenAPI平台申请应用。因此在OpenAPI网关之外，需要有一个面向合作伙伴的使用的平台用于合作伙伴，这就要求OpenAPI网关需要提供API给这个用户平台进行访问。当然如果是在简单的场景下，可能并不需要提供一个面向合作伙伴的门户，只需要由公司的运营人员直接添加合作伙伴应用id/密钥等，这种情况下也就不需要合作伙伴门户子系统。

<a href="https://imgbb.com/"><img src="https://i.ibb.co/qkfBfs0/api-gateway-2.jpg" alt="api-gateway-2" border="0"></a>

2、对于内网的API网关，在起到的作用上来说可以认为是微服务网关，也可以认为是内网的API服务治理平台。
**当企业将所有的应用使用微服务的架构管理起来，那么API网关就起到了微服务网关的作用。**
而当企业只是**将系统与系统之间的调用使用rest api的方式进行访问时使用API网关对调用进行管理，那么API网关起到的就是API服务治理的作用。**

<a href="https://imgbb.com/"><img src="https://i.ibb.co/LdwTxXZ/api-gateway-3.jpg" alt="api-gateway-3" border="0"></a>

3、对于公司内部公网应用（如APP、公司的网站），如果管理上比较细致，在架构上可能由独立的API网关来处理这部分内部公网应用如果想比较简单的处理，也可以是使用面向合作伙伴的API网关。如果使用独立的API网关，有以下的好处：

	面向合作伙伴和面向公司主体业务的优先级不一样，不同的API网关可以做到业务影响的隔离。

	内部API使用的管理流程和面向合作伙伴的管理流程可能不一样。

	内部的API在功能扩展等方面的需求一般会大于OpenAPI对于功能的要求。

基于以上的分析，如果公司有能力，那么还是建议**分开使用合作伙伴OPEN API网关和内部公网应用网关。**

# API网关有哪些竞争方案

1、对于Open API平台的API网关，我分析只能选择API网关作为解决方案.业界没有发现比较好的可以用来作为Open API平台的入口的其他方案。

2、对于作为微服务网关的API网关，业界的选择可以选择的解决方案比较多，也取决于微服务器的实现方案，有一些微服务架构的实现方案是不需要微服务网关的。

  **(1)Service Mesh**
     
     这是新兴的基于无API网关的架构，通过在客户端上的代理完成屏蔽网络层的访问，这样达到对应用层最小的改动当前Service Mesh的产品还正在开发中，并没有
     非常成熟可直接应用的产品。发展最迅速的产品是Istio。建议大家密切关注相关产品的研发、业务使用进展。

   <a href="https://ibb.co/NKZ8WFH"><img src="https://i.ibb.co/jDgc381/api-gateway-4.jpg" alt="api-gateway-4" border="0"></a>

  **(2)基于duboo架构**
  
     在这个架构中通常是不需要网关的，是由客户端直接访问服务提供方，由注册中心向客户端返回服务方的地址。

  <a href="https://imgbb.com/"><img src="https://i.ibb.co/jTYbfBm/api-gateway-5.jpg" alt="api-gateway-5" border="0"></a>

# API网关解决方案

私有云开源解决方案：
Netflix Zuul，zuul是spring cloud的一个推荐组件，https://github.com/Netflix/zuul
Kong kong是基于Nginx+Lua进行二次开发的方案， https://konghq.com/
Tyk是2014年创建的开源API网关，甚至比AWS的API网关即服务功能还要早。Tyk用Golang编写并使用Golang自己的HTTP服务器。
————————————————
版权声明：本文为CSDN博主「pushiqiang」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/pushiqiang/article/details/95726137

**公有云解决方案：**

   1. Amazon API Gateway：
      https://aws.amazon.com/cn/api-gateway/
	
   2. 阿里云API网关：
      https://www.aliyun.com/product/apigateway/
	
   3. 腾讯云API网关：
      https://cloud.tencent.com/product/apigateway

**自开发解决方案**

	1. 基于Nginx+Lua+ OpenResty的方案，可以看到Kong,orange都是基于这个方案

	2. 基于Netty、非阻塞IO模型。通过网上搜索可以看到国内的宜人贷等一些公司是基于这种方案，是一种成熟的方案。

	3. 基于Node.js的方案。这种方案是应用了Node.js天生的非阻塞的特性。

	4. 基于java Servlet的方案。zuul基于的就是这种方案，这种方案的效率不高，这也是zuul总是被诟病的原因。

# 企业怎么选择API网关

如果要选择一款已有的API网关，需要从以下几个方面去考虑。

**1、性能与可用性**

如果一旦采用了API网关，那么API网关就会作为企业应用核心，因此性能和可用性是必须要求的。从性能上来说，需要让网关增加的时间消耗越短越好，个人觉得需要10ms以下。系统需要采用非阻塞的IO，如epoll，NIO等，网关和各种依赖的交互也需要是非阻塞的，这样才能保证整体系统的高可用性，如：Node.js的响应式编程和基于java体现的RxJava和Future。网关必须支持集群部署，任务一台服务器的crash都应该不影响整体系统的可用性。多套网关应该支持同一管理平台和同一监控中心。如：一个企业的OpenAPI网关和内部应用的多个系统群的不同的微服务网关可以在同一监控中心进行监控。

**2、可扩展性、可维护性**

一款产品总有不能满足生产需求的地方，因此需求思考产品在如何进行二次开发和维护，是否方便公司团队接手维护产品。

**3、需求匹配度**

需要评估各API网关在需求上是否能满足。比如：如果是OpenAPI平台需要使用API网关，那么需要看API网关在合作伙伴应用接入、合作伙伴门户集成、访问次数限额等OpenAPI核心需求上去思考产品是否能满足要求。如果是微服务网关，那么要从微服务的运维、监控、管理等方面去思考产品是否足够强大。

**4、是否开源？公司是否有自开发的能力？**

现有的开源产品如kong，zuul，orange都有基础的API网关的核心功能，这些开源产品大多离很好的使用有一定的距离，比如：没有提供管理功能的UI界面、监控功能弱小，不支持OpenAPI平台，没有公司运营与运维的功能等。当然开源产品能获取源代码，如果公司有比较强的研发能力，能hold住这些开源产品，经过二次开发kong、zuul应该还是适应一些公司，不过需求注意以下一些点：

    kong是基于ngnix+lua的，从公司的角度比较难于找到能去维护这种架构产品的人。需求评估当前公司是否有这个能力去维护这个产品。

	  zuul因为架构的原因在高并发的情况下性能不高，同时需要去基于研究整合开源的适配zuul的监控和管理系统。

	  orange由于没有被大量使用，同时是国内个人在开源，在可持续性和社区资源上不够丰富，出了问题后可能不容易找到人问。
    
另外kong提供企业版本的API网关，当然也是基于ngnix+lua的，企业版本可以购买他们的技术支持、培训等服务、以及拥有界面的管理、监控等功能。

**5、公有云还是私有云**

现在的亚马逊、阿里、腾讯云都在提供基础公有云的API网关，当然这些网关的基础功能肯定是没有问题，但是二次开发，扩展功能、监控功能可能就不能满足部分用户的定制需求了。另外很多企业因为自身信息安全的原因，不能使用外网公有网的API网关服务，这样就只有选择私有云的方案了。在需求上如果基于公有云的API网关只能做到由内部人员为外网人员申请应用，无法做到定制的合作伙伴门户，这也不适合于部分企业的需求。如果作为微服务网关，大多数情况下是希望网关服务器和服务提供方服务器是要在内网的，在这里情况下也只有私有云的API网关才能满足需求。综合上面的分析，基础公有云的API网关只有满足一部分简单客户的需求，对于很多企业来说私有云的API网关才是正确的选择。





# API网关视频
  * [网关 架构演进及实践](https://mp.weixin.qq.com/s/nb3_c3q8xD6jbR7AjNUQEg)
  * [Top 25 API Testing Interview Questions & Answers](https://www.youtube.com/watch?v=8cyMZ87J7zo)
  * [APIs for Beginners - How to use an API (Full Course / Tutorial)](https://www.youtube.com/watch?v=GZvSYJDk-us)
  * [什么是RESTful API](https://www.bilibili.com/video/BV1nb411c71r/?spm_id_from=333.788.videocard.5)
  * [全网高质量接口设计API网关组件实现分布式合集](https://www.bilibili.com/video/BV1mt411571k/?spm_id_from=333.788.videocard.8)
  * [Restful API实战](https://www.imooc.com/learn/811)
  * [使用java构建和维护接口自动化测试框架](https://www.imooc.com/learn/847)
  * [快速构建标准RESTful API完成前后端分离开发](https://www.bilibili.com/video/av17305978/?spm_id_from=333.788.videocard.0)
  * [接口自动化测试体系构建](https://www.bilibili.com/video/av75749911/?spm_id_from=333.788.videocard.1)
  * [手把手教你做接口文档设计](https://www.bilibili.com/video/av47570402/?spm_id_from=333.788.videocard.4)
  * [微服务架构需要解决的问题---API网关](https://www.bilibili.com/video/av65833021?p=39)
  * [美团架构师-高质量api网关接口设计（实战精品合集）](https://www.bilibili.com/video/av59601665/?spm_id_from=333.788.videocard.2)
  * [java架构知识——大型互联网公司必备之高质量API网关组件实现](https://www.bilibili.com/video/av53757089/?spm_id_from=333.788.videocard.7)
  * [接口自动化测试环境搭建（Java+HttpClient）](https://www.bilibili.com/video/av35129286/?spm_id_from=333.788.videocard.0)
  * [web自动化测试环境搭建(Java+selenium+主流浏览器](https://www.bilibili.com/video/av35129526/?spm_id_from=333.788.videocard.2)
  * [Java接口自动化系列（高级部分）](https://www.bilibili.com/video/av35944252/?spm_id_from=333.788.videocard.7)
  * [微服务和网关API理解](https://www.bilibili.com/video/av29226224/?spm_id_from=333.788.videocard.0)
  * [京东等大厂必备的api网关构建实战](https://www.bilibili.com/video/av71341301?from=search&seid=12766503076032577278)
  * [2019年互联网企业必备高质量API网关接口设计（系统实战全集](https://www.bilibili.com/video/av60753511?from=search&seid=12766503076032577278)
 * [2019千锋软件测试-接口测试全套视频教程](https://www.bilibili.com/video/av63249355/?spm_id_from=333.788.videocard.6)
 * [SpringBoot2.0+Shiro+Jwt+SpringDataJpa框架的Saas多租户基于前后端分离进行开发](https://www.bilibili.com/video/av75572951?from=search&seid=553876227072387657)
  * [极速入门SpringCloud之API网关与服务发现](https://www.imooc.com/learn/1134)
  * [api设计第三版（node.js)](https://www.bilibili.com/video/av74605570?from=search&seid=5739552451698653385)
  * [REST API 接口测试](https://www.imooc.com/learn/1048)
  * [高质量API接口设计最佳实践](https://www.bilibili.com/video/BV1g4411V7R9/?spm_id_from=333.788.videocard.7)




# 有用的参考
  * [对内 DDD 对外 API 之—对外 API 的设计理念](http://dockone.io/article/1397261)
  * [接口请求合并的3种技巧，性能直接爆表！](https://mp.weixin.qq.com/s/QfKOBHXvwvDod664zbG8Zg)
  * [Api 接口优化的几个技巧](https://mp.weixin.qq.com/s/W_u55Spcm46mnYiujwyPHg)
  * [4 种主流的 API 架构风格对比](https://mp.weixin.qq.com/s/g4ZYYpXoSBNroPNHqciixA)
  * [前后端分离的接口规范](https://mp.weixin.qq.com/s/D3_OeK29N1bU__RWqSh6-g)
  * [保证接口数据安全的10种方案](https://mp.weixin.qq.com/s/p_jL2N_8julhRfJQj-Eatw)
  * [微服务如何聚合Swagger实现接口文档管理](https://developer.51cto.com/article/701318.html)
  * [最好用的七大顶级 API 接口测试工具](https://zhuanlan.zhihu.com/p/445755310)
  * [IDEA HTTP Client（史上最全） ](https://www.cnblogs.com/crazymakercircle/p/14317222.html)
  * [老板叫我设计一个亿级API网关](https://network.51cto.com/art/202104/656469.htm)
  * [Spring Boot构建RESTful Web服务](https://www.yiibai.com/spring-boot/spring_boot_building_restful_web_services.html)
  * [分布式Restful SpringBoot骨架搭建](https://blog.csdn.net/songxinjianqwe/article/details/77478385)
  * [SpringBoot实现API接口的完整代码](https://www.jb51.net/article/198167.htm)
  * [正确甄别API & REST API & RESTful API & Web Service之间的差异与联系](https://juejin.cn/post/6844903869625942029)
  * [看看人家那后端API接口写得，那叫一个优雅！](https://www.jianshu.com/p/fa75acba5b07)
  * [微服务五种开源API网关实现组件对比](https://zhuanlan.zhihu.com/p/61014955)
  * [微服务的API网关演化之路](https://zhuanlan.zhihu.com/p/86053141)
  * [【spring 指南系列】如何更好的设计RESTful API](https://zhuanlan.zhihu.com/p/24592119?utm_source=wechat_session&utm_medium=social&utm_oi=991812777480134656&utm_content=first)
  * [前后端分离后，RESTful API 最佳实践](https://zhuanlan.zhihu.com/p/66323805?utm_source=wechat_session&utm_medium=social&utm_oi=991812777480134656&utm_content=first)
  * [避免自己写的 url 被diss！建议看看这篇RestFul API简明教程！](https://juejin.im/post/5e82b1f55188257391463536)
  * [【项目实践】SpringBoot三招组合拳，一步一步演示如何构建起一个优秀的后端接口体系](https://www.jianshu.com/p/b5b8613769db)
  * [如何优雅设计 API 接口，实现统一格式返回？](https://www.jianshu.com/p/90bac4eda34a)
  * [啥？听说你还在手写复杂的参数校验？](https://mp.weixin.qq.com/s/WQ-2fPOxk0GvSHiRR5-kQw)
  * [SpringBoot集成Swagger2实现Restful(类型转换错误解决办法)](https://www.jb51.net/article/117925.htm)
  * [一文弄懂 CORS 跨域(前端+后端代码实例讲解)](https://www.jianshu.com/p/f18388d6b931)
  * [面试官：你们前后端分离的接口规范是什么？](https://juejin.im/post/5dd7e0cd6fb9a07ad665b19d)
  * [前后端角度看接口（什么是json）](https://blog.csdn.net/u011679785/article/details/102621144?depth_1-utm_source=distribute.pc_relevant.none-task&utm_source=distribute.pc_relevant.none-task)
  * [正确甄别API & REST API & RESTful API & Web Service之间的差异与联系](https://juejin.im/post/5d07317e6fb9a07eac05d33c)
  * [API 接口设计规范](https://juejin.im/post/5e50764be51d45271c2fed6f)
  * [软件开发中前端和后端如何协同开发和集成](https://www.jianshu.com/p/90754ac7857e)
  * [Spring Boot中使用Swagger2构建强大的RESTful API文档](http://blog.didispace.com/springbootswagger2/)
  * [在 Spring Boot 项目中使用 Swagger 文档](https://www.ibm.com/developerworks/cn/java/j-using-swagger-in-a-spring-boot-project/index.html)
  * [一篇文章带你搞懂 SpringBoot与Swagger整合](https://blog.csdn.net/itguangit/article/details/78978296)
  * [面试官：你连RESTful都不知道我怎么敢要你？](https://blog.csdn.net/kebi007/article/details/102927209)

 * [API 接口测试的全过程解析](https://gitchat.csdn.net/activity/5c2c65d22061ae3944327faa)
 * [新一代API管理 & 自动化测试平台]()

 * [某小公司RESTful、共用接口、前后端分离、接口约定的实践](https://www.javazhiyin.com/43686.html)
 * [淘宝前后端分离实践](https://wenku.baidu.com/view/1a9f08aa33d4b14e85246895.html?sxts=1562937835448)
 * [异步 API 的设计](http://www.ruanyifeng.com/blog/2018/12/async-api-design.html)
 * [springboot综合项目练习三定义微服务接口返回规范](https://blog.csdn.net/chenhaotao/article/details/86500548)
 * [微服务RESTful 接口设计规范](https://blog.csdn.net/zl1zl2zl3/article/details/73867113)
 * [restful完整实例](https://blog.csdn.net/qq_38869854/article/details/80664677)
 * [RESTful API实战笔记(接口设计及Java后端实现)](https://blog.csdn.net/ZHENFENGSHISAN/article/details/77101527)
 * [设计一套好的RESTful API](https://blog.csdn.net/zhenfengshisan/article/details/77101516)
  * [REST与RESTFul API最佳实践](https://www.itjc8.com/thread-628-1-1.html)
 * [一个简单可参考的 API 网关架构设计](https://www.infoq.cn/article/api-gateway-architecture-design)
 * [有赞API网关实践](https://tech.youzan.com/api-gateway-in-practice/)
 * [使用API网关构建微服务--实际例子](https://cloud.tencent.com/developer/article/1032069?fromSource=waitui)
 * [八步部署NGINX Plus API网关](https://zhuanlan.zhihu.com/p/38359208?utm_source=wechat_session&utm_medium=social&utm_oi=991812777480134656)
 * [Building Microservices: Using an API Gateway](https://www.nginx.com/blog/building-microservices-using-an-api-gateway/)
 * [一个非常适合IT团队的在线API文档、技术文档工具](https://yun.gw.com.cn/doc/#/)
 * [异步 API 的设计](http://www.ruanyifeng.com/blog/2018/12/async-api-design.html)
 * [springboot综合项目练习三定义微服务接口返回规范](https://blog.csdn.net/chenhaotao/article/details/86500548)
 * [微服务RESTful 接口设计规范](https://blog.csdn.net/zl1zl2zl3/article/details/73867113)
 * [前后端分离购物网站—技术选型](https://blog.csdn.net/qq_39206806/article/details/81320826)
 * [JavaWeb项目前后端分离](https://blog.csdn.net/xiaoyangsavvy/article/details/80476703) 
 * [使用RAP2和Mock.JS实现Web API接口的数据模拟和测试](https://www.cnblogs.com/wuhuacong/p/10213540.html)
 * [Web API应用架构设计分析](http://www.uml.org.cn/net/201507222.asp?artid=16667)
 * [从接入层入手，设计高并发的微服务架构？](http://developer.51cto.com/art/201803/567754.htm)
 * [在基于MVC的Web项目中使用Web API和直接连接两种方式混合式接入](https://www.cnblogs.com/wuhuacong/p/5850482.html)
 * [架构设计（4）--API网关](https://blog.csdn.net/hguisu/article/details/79143584)
 * [统一接入层方案](https://blog.csdn.net/JeamKing/article/details/73695200)
 * [一个简单可参考的 API 网关架构设计](https://www.infoq.cn/article/api-gateway-architecture-design)
 * [一切皆API的大环境下，如何打造API Everything](https://dbaplus.cn/news-141-1815-1.html)
 * [基于 Spring Boot 的 Web API 项目的安全设计](https://www.ctolib.com/topics-132196.html)
 * [一个完善的 API 流程标准（草图）](https://segmentfault.com/a/1190000008243832)
 * [设计微服务应用层和 Web API](https://docs.microsoft.com/zh-cn/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/microservice-application-layer-web-api-design)
 * [使用 Web API 实现微服务应用层](https://docs.microsoft.com/zh-cn/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/microservice-application-layer-implementation-web-api)
 * [企业级API网关的设计](https://www.jianshu.com/p/84dcce30f7c9)
 * [微服务架构为何需要搭配API网关](https://blog.csdn.net/belalds/article/details/80913504)
 * [微服务与API网关（上）: 为什么需要API网关](http://blog.didispace.com/hzf-ms-apigateway-1)
 * [微服务与API 网关（下）- Kong能为我们做什么？](http://blog.didispace.com/hzf-ms-apigateway-2)
 * [百亿流量 API 网关设计与实践](https://gitbook.cn/books/5bbb3d2a61d11c2d996be26b/index.html)
 * [为什么需要API网关，如何设计API网关](https://www.eefocus.com/communication/423672)
 * [浅谈三种API设计风格RPC、REST、GraphQL](https://zhuanlan.zhihu.com/p/56955812)
 * [spring 指南系列 如何更好的设计RESTful API](https://zhuanlan.zhihu.com/p/24592119)
 * [Google Cloud 的 API 设计](https://zhuanlan.zhihu.com/p/27458970)
 * [web项目前后端开发过程和体会](https://blog.csdn.net/gu_ude_god/article/details/81570544)
 * [构建SpringMVCRestful前后端分离项目实例](https://blog.csdn.net/songxinjianqwe/article/details/54976251)
 * [Docker 对前后端分离项目的部署和运维（详述）](https://blog.csdn.net/qq_35394891/article/details/83027804)
 * [接口测试讲解](https://wenku.baidu.com/view/b1e8e09f0342a8956bec0975f46527d3250ca67d.html?from=search)
 * [Restful API接口测试四种方法](https://blog.csdn.net/u014677702/article/details/86373519)
 * [接口测试 | 接口测试入门](https://blog.csdn.net/lintyleo/article/details/79360730)
 * [Jmeter接口测试实例讲解](https://wenku.baidu.com/view/47046ca0a98271fe910ef9d2.html?rec_flag=default&sxts=1562938585342)
 * [API接口平台上一键化快速生成接口文档技巧](https://wenku.baidu.com/view/fedcfc226d175f0e7cd184254b35eefdc8d315d5.html?from=search)
 * [前后端分界及项目流程解析](https://wenku.baidu.com/view/e677d1ecfc0a79563c1ec5da50e2524de418d05a.html?from=search)
 * [SpringMVC集成springfox-swagger2构建restful API](https://blog.csdn.net/u014231523/article/details/54411026)
 * [从 MVC 到前后端分离 ](https://my.oschina.net/huangyong/blog/521891)
 * [百亿流量 API 网关设计与实践](https://gitbook.cn/books/5bbb3d2a61d11c2d996be26b/index.html)
 * [API网关的作用、方案以及如何选择](http://blog.didispace.com/API网关的作用、方案以及如何选择/)
 * [自建API网关-架构设计篇](http://blog.didispace.com/自建API网关-架构设计篇/)
 * [浅谈微服务中的 API 网关（API Gateway）](https://zhuanlan.zhihu.com/p/31786017)
 * [一个简单可参考的 API 网关架构设计](https://www.infoq.cn/article/api-gateway-architecture-design)
 * [有赞API网关实践](https://tech.youzan.com/api-gateway-in-practice/)
 * [使用API网关构建微服务--实际例子](https://cloud.tencent.com/developer/article/1032069?fromSource=waitui)
 * [八步部署NGINX Plus API网关](https://zhuanlan.zhihu.com/p/38359208?utm_source=wechat_session&utm_medium=social&utm_oi=991812777480134656)
 * [Building Microservices: Using an API Gateway](https://www.nginx.com/blog/building-microservices-using-an-api-gateway/)
 * [一个非常适合IT团队的在线API文档、技术文档工具](https://yun.gw.com.cn/doc/#/)
 * [异步 API 的设计](http://www.ruanyifeng.com/blog/2018/12/async-api-design.html)
 * [springboot综合项目练习三定义微服务接口返回规范](https://blog.csdn.net/chenhaotao/article/details/86500548)
 * [微服务RESTful 接口设计规范](https://blog.csdn.net/zl1zl2zl3/article/details/73867113)
 * [前后端分离购物网站—技术选型](https://blog.csdn.net/qq_39206806/article/details/81320826)
 * [JavaWeb项目前后端分离](https://blog.csdn.net/xiaoyangsavvy/article/details/80476703) 
 * [使用RAP2和Mock.JS实现Web API接口的数据模拟和测试](https://www.cnblogs.com/wuhuacong/p/10213540.html)
 * [Web API应用架构设计分析](http://www.uml.org.cn/net/201507222.asp?artid=16667)
 * [从接入层入手，设计高并发的微服务架构？](http://developer.51cto.com/art/201803/567754.htm)
 * [在基于MVC的Web项目中使用Web API和直接连接两种方式混合式接入](https://www.cnblogs.com/wuhuacong/p/5850482.html)
 * [架构设计（4）--API网关](https://blog.csdn.net/hguisu/article/details/79143584)
 * [统一接入层方案](https://blog.csdn.net/JeamKing/article/details/73695200)
 * [一个简单可参考的 API 网关架构设计](https://www.infoq.cn/article/api-gateway-architecture-design)
 * [一切皆API的大环境下，如何打造API Everything](https://dbaplus.cn/news-141-1815-1.html)
 * [基于 Spring Boot 的 Web API 项目的安全设计](https://www.ctolib.com/topics-132196.html)
 * [一个完善的 API 流程标准（草图）](https://segmentfault.com/a/1190000008243832)
 * [设计微服务应用层和 Web API](https://docs.microsoft.com/zh-cn/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/microservice-application-layer-web-api-design)
 * [使用 Web API 实现微服务应用层](https://docs.microsoft.com/zh-cn/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/microservice-application-layer-implementation-web-api)
 * [企业级API网关的设计](https://www.jianshu.com/p/84dcce30f7c9)
 * [微服务架构为何需要搭配API网关](https://blog.csdn.net/belalds/article/details/80913504)
 * [微服务与API网关（上）: 为什么需要API网关](http://blog.didispace.com/hzf-ms-apigateway-1)
 * [微服务与API 网关（下）- Kong能为我们做什么？](http://blog.didispace.com/hzf-ms-apigateway-2)
 * [百亿流量 API 网关设计与实践](https://gitbook.cn/books/5bbb3d2a61d11c2d996be26b/index.html)
 * [为什么需要API网关，如何设计API网关](https://www.eefocus.com/communication/423672)
 * [浅谈三种API设计风格RPC、REST、GraphQL](https://zhuanlan.zhihu.com/p/56955812)
 * [spring 指南系列 如何更好的设计RESTful API](https://zhuanlan.zhihu.com/p/24592119)
 * [Google Cloud 的 API 设计](https://zhuanlan.zhihu.com/p/27458970)
 * [web项目前后端开发过程和体会](https://blog.csdn.net/gu_ude_god/article/details/81570544)
 * [构建SpringMVCRestful前后端分离项目实例](https://blog.csdn.net/songxinjianqwe/article/details/54976251)
 * [Docker 对前后端分离项目的部署和运维（详述）](https://blog.csdn.net/qq_35394891/article/details/83027804)
 * [API网关 Zuul1.0 和 2.0 我们该如何选择？](https://mp.weixin.qq.com/s/WlDCpdh3_ygCCd63Gsth6Q)
 * [微网关与服务啮合](http://blog.didispace.com/microgateway-and-service-mesh/)
 * [聊聊 API Gateway 和 Netflix Zuul](http://www.scienjus.com/api-gateway-and-netflix-zuul/)
 
* [SpringBoot如何优雅的校验参数](https://zhuanlan.zhihu.com/p/97555913?utm_source=wechat_session&utm_medium=social&utm_oi=991812777480134656)
* [SpringBoot：如何优雅地进行参数传递、响应数据封装、异常处理？](https://www.jianshu.com/p/b7f881bd940c)
* [构建一个较为复杂的RESTful API以及单元测试](http://blog.didispace.com/springbootrestfulapi/)
* [使用Swagger2构建RESTful API](http://blog.didispace.com/springbootswagger2/)
* [JSR-303实现请求参数校验](http://blog.didispace.com/spring-boot-learning-21-2-3/)
* [Swagger接口分类与各元素排序问题详解](http://blog.didispace.com/spring-boot-learning-21-2-4/)
* [Swagger静态文档的生成](http://blog.didispace.com/spring-boot-learning-21-2-5/)
* [为什么启动时候API路径都没了？](http://blog.didispace.com/spring-boot-learning-21-2-6/)
* [使用SpringFox 3生成Swagger文档](http://blog.didispace.com/spring-boot-learning-21-2-7/)
* [Spring Boot中使用Swagger2构建强大的RESTful API文档](http://blog.didispace.com/springbootswagger2/)
* [自制的spring-boot-starter-swagger](https://github.com/SpringForAll/spring-boot-starter-swagger)
