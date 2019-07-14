# [API网关面试知识点](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20Web/Interview/apigate.md)

# 一、API网关的用处

API网关我的分析中会用到以下三种场景。

**1、Open API**

企业需要将自身数据、能力等作为开发平台向外开放，通常会以rest的方式向外提供。最好的例子就是淘宝开放平台、腾讯公司的QQ开发平台、微信开放平台。
Open API开放平台必然涉及到客户应用的接入、API权限的管理、调用次数管理等，必然会有一个统一的入口进行管理，这正是API网关可以发挥作用的时候。

**2、微服务网关**

微服务的概念最早在2012年提出，在Martin Fowler的大力推广下，微服务在2014年后得到了大力发展。
在微服务架构中，有一个组件可以说是必不可少的，那就是微服务网关，微服务网关处理了负载均衡，缓存，路由，访问控制，服务代理，监控，日志等。
API 网关在微服务架构中正是以微服务网关的身份存在。

**3、API服务管理平台**

上述的微服务架构对企业来说有可能实施上是困难的，企业有很多遗留系统，要全部抽取为微服务改动太大，对企业来说成本太高。
但是由于不同系统间存在大量的API服务互相调用，因此需要对系统间服务调用进行管理，清晰地看到各系统调用关系，对系统间调用进行监控等。
API网关可以解决这些问题，我们可以认为如果没有大规模的实施微服务架构，那么对企业来说微服务网关就是企业的API服务管理平台。

# 二、API网关在企业架构中的地位

一个企业随着信息系统复杂度的提高，必然出现外部合作伙伴应用、企业自身的公网应用、企业内网应用等。在架构上应该将这三种应用区别开，三种应用的安排级别、访问方式也不一样。因此在我的设计中将这三种应用分别用不同的网关进行API管理，分别是：**API网关（OpenAPI合伙伙伴应用）、API网关（内部应用）、API网关（内部公网应用）。**


# 三、企业中如何应用API网关
# 四、API网关有哪些竞争方案
# 五、API网关解决方案
# 六、企业怎么选择API网关


# 有用的参考
 * [新一代API管理 & 自动化测试平台]()

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
