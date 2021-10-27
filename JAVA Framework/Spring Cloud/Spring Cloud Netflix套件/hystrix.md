[Spring Cloud Hystrix实战运用](https://weread.qq.com/web/reader/71d32370716443e271df020ka5b325d0225a5bfc9e0772d)|
---|

# 目录
* [Spring Cloud Hystrix实战运用](https://weread.qq.com/web/reader/71d32370716443e271df020kd67323c0227d67d8ab4fb04)
* [Spring Cloud Hystrix服务容错快速入门例子1](https://mrbird.cc/Spring-Cloud-Hystrix-Circuit-Breaker.html)
* [Spring Cloud Hystrix Dashboard仪表盘快速入门例子2](https://mrbird.cc/Spring-Cloud-Hystrix-Dashboard.html)
* [Hystrix概念](#Hystrix概念)
  * [Hystrix配置说明](https://weread.qq.com/web/reader/71d32370716443e271df020kd67323c0227d67d8ab4fb04) 
  * [Turbine聚合Hystrix](https://weread.qq.com/web/reader/71d32370716443e271df020kd67323c0227d67d8ab4fb04)



## Hystrix概念

Hystrix是一个用于处理分布式系统的延迟和容错的开源库,在分布式系统里,许多依赖不可避兔的会调用失败，比如超时、异常等，Hystrix能够保证在一个依赖出问题的情况下，不会导致整体服务失败，避免级联故障，以提高分布式系统的弹性。Hystrix的出现就是为了解决雪崩效应。

**服务雪崩**

多个微服务之间调用的时候，假设微服务A调用微服务B和微服务C,微服务B和微服务C又调用其它的微服务，这就是所谓的“扇出”。如果扇出的链路上某个微服务的调用响应时间过长或者不可用，对微服务A的调用就会占用越来越多的系统资源，进而引起系统崩溃，所谓的”雪崩效应”。

**服务熔断**

熔断机制是应对雪崩效应的一种微服务链路保护机制。

当删除链路的某个微服务不可用或者响应时间太长时，会进行服务的降级，进而熔断该节点微服务的调用，快速返回”错误的响应信息。当检测到该节点微服务调用响应正常后恢复调用链路。在SpringCloud框架里熔断机制通过Hystrix实现。Hystrix会监控微服务间调用的状况，当失败的调用到一定阈值,缺省是5秒内20次调用失败就会启动熔断机制。熔断机制的注解是@HystrixCommand。

**服务降级**

整体资源快不够了，忍痛将某些服务先关掉，待渡过难关，再开启回来。

**Hystrix监控和断路器**

我们只需要在服务接口上添加Hystrix标签，就可以实现对这个接口的监控和断路器功能。Hystrix Dashboard监控面板，提供了一个界面，可以监控各个服务上的服务调用所消耗的时间等。

**Hystrix Turbine监控聚合**

使用Hystrix监控，我们需要打开每一个服务实例的监控信息来查看。而Turbine可以帮助我们把所有的服务实例的监控信息聚合到一个地方统查看。这样就不需要挨个打开一个个的页面一个个查看。

**Zuul的安全机制**

签名机制，为防止接口数据篡改和重复调用，增加接口参数校验机制，sig签名算法为MD5（appKey+appSecret+timestamp），appKey是分配给客户端的ID，appSecret是分配给客户端的密钥，timestamp为unix时间戳，请求的URL有效时间为15分钟。

Token机制，用户在登录之后会返回一个access_ token，客户端在访问需要登录之后才能访问的资源，需要在在Authorization头部使用Bearer模式新增token，如head(“Authorization”,” Bearer token”)。

**Hystrix的设计原则**

资源隔离（线程池隔离和信号量隔离）机制：限制调用分布式服务的资源使用，某一个调用的服务出现问题不会影响其它服务调用。

限流机制：限流机制主要是提前对各个类型的请求设置最高的QPS阈值，若高于设置的阈值则对该请求直接返回，不再调用后续资源。

熔断机制：当失败率达到阀值自动触发降级（如因网络故障、超时造成的失败率真高），熔断器触发的快速失败会进行快速恢复。

降级机制：超时降级、资源不足时（线程或信号量）降级、运行异常降级等，降级后可以配合降级接口返回托底数据。

缓存支持：提供了请求缓存、请求合并实现。

通过近实时的统计/监控/报警功能，来提高故障发现的速度。

通过近实时的属性和配置热修改功能，来提高故障处理和恢复的速度。



# 参考
* [Hystrix ，为什么说它是每个系统不可或缺的开源框架？](https://zhuanlan.zhihu.com/p/34304136)
* [深入理解Hystrix之文档翻译](https://zhuanlan.zhihu.com/p/28523060)
* [谈谈我对服务熔断、服务降级的理解](https://blog.csdn.net/guwei9111986/article/details/51649240)
* [Hystrix几篇文章](https://segmentfault.com/u/yedge/articles)

* [Hystrix降级逻辑中如何获取触发的异常](http://blog.didispace.com/hystrix-fallback-cause-exception/)
* [Spring Cloud中Hystrix 线程隔离导致ThreadLocal数据丢失（续）](http://blog.didispace.com/Spring-Cloud%E4%B8%ADHystrix-%E7%BA%BF%E7%A8%8B%E9%9A%94%E7%A6%BB%E5%AF%BC%E8%87%B4ThreadLocal%E6%95%B0%E6%8D%AE%E4%B8%A2%E5%A4%B1%EF%BC%88%E7%BB%AD%EF%BC%89/)
* [Spring Cloud中Hystrix 线程隔离导致ThreadLocal数据丢失](http://blog.didispace.com/Spring-Cloud%E4%B8%ADHystrix-%E7%BA%BF%E7%A8%8B%E9%9A%94%E7%A6%BB%E5%AF%BC%E8%87%B4ThreadLocal%E6%95%B0%E6%8D%AE%E4%B8%A2%E5%A4%B1/)
* [Spring Cloud Hystrix的请求合并](http://blog.didispace.com/spring-cloud-hystrix-request-collapse/)
* [探讨通过Feign配合Hystrix进行调用时异常的处理](http://blog.didispace.com/rencong-1/)
* [Spring Cloud构建微服务架构（三）断路器](http://blog.didispace.com/springcloud3/)
* []()
