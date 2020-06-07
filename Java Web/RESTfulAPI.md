# [API网关面试知识点](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20Web/Interview/apigate.md)

[Vert.x](https://www.bilibili.com/video/BV174411E7Vr?from=search&seid=3412424618058651269)|
---|

[kong](https://www.bilibili.com/video/BV16E411n7Bu?from=search&seid=13204508987684806757)|[Spring Cloud Gateway](https://www.bilibili.com/video/BV11b411V7MA?from=search&seid=10918068436806250854)|
---|---|



| **第一部分 RestFulAPI 篇** | [无代码] | 
| :--- | :---: | 
| 1-1 数据结构和算法的关系 | [无代码] |
| [1-2 RESTfull API简单项目的快速搭建](https://www.bilibili.com/video/av13224378?from=search&seid=10786600269014143134) | [无代码] |
| [REST API 接口测试](https://www.imooc.com/learn/1048)||
| **第二部分 API网关 篇** | [无代码] | 
| :--- | :---: | 
| [零、API网关的分类](#API网关的分类)| [无代码]|
| [一、API网关的用处](#API网关的用处)| [无代码]|
| [二、API网关在企业架构中的地位](#API网关在企业架构中的地位)||
| [三、企业中如何应用API网关](#企业中如何应用API网关)||
| [四、API网关有哪些竞争方案](#API网关有哪些竞争方案)||
| [五、API网关解决方案](#API网关解决方案)||
| [六、企业怎么选择API网关](#企业怎么选择API网关)||
| 参考： [百亿流量微服务网关的设计与实现](https://www.infoq.cn/article/EeE1xZeic4UdpbmR*03t)||
| **第三部分 前后端分离 篇** | [无代码] | 
| :--- | :---: | 
| [1-1 前后端分离是如何做的](#前后端分离是如何做的) | [无代码] |
| [1-2  软件开发中前端和后端如何协同开发和集成](#软件开发中前端和后端如何协同开发和集成) | [无代码] |
| [包工头都能听明白的前后端分离](https://www.bilibili.com/video/BV1f7411L7By)||
| [前后端分离架构，Node 中间层](https://www.crs811.com/archives/1542)||
| [前后端联调](https://www.jianshu.com/p/91486e9c0e34)||
| [后端工程师快速上手前后端分离开发](https://juejin.im/post/5edb44adf265da76f75be68b)||
| [快速构建标准RESTful API完成前后端分离开发](https://www.bilibili.com/video/BV12W411b7oT?from=search&seid=2011606573791409942)||
| [4小时学会Spring Boot+Vue前后端分离开发](https://www.bilibili.com/video/BV137411B7vB/?spm_id_from=333.788.videocard.9)|[无代码]|
| [React+springboot权限管理系统·前后端分离](https://www.jianshu.com/p/b34674cd328d)|[无代码]|


# API网关的分类

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

API网关我的分析中会用到以下三种场景。

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
