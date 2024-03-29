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

Hystrix是一个用于处理分布式系统的延迟和容错的开源库,在分布式系统里,许多依赖不可避兔的会调用失败，比如超时、异常等，Hystrix能够保证在一个依赖出问题的情况下，不会导致整体服务失败，避免级联故障，以提高分布式系统的弹性。Hystrix的出现就是为了解决雪崩效应。断路器很好理解，当Hystrix Command请求后端服务失败数量超过一定比例（默认为50%），断路器会切换到开路状态（Open）。这时所有请求会直接失败而不会发送到后端服务。断路器保持在开路状态一段时间后（默认为5秒），自动切换到半开路状态（HALF-OPEN）。这时会判断下一次请求的返回情况，如果请求成功，断路器切回闭路状态（CLOSED），否则重新切换到开路状态（OPEN）。Hystrix的断路器就像我们家庭电路中的保险丝，一旦后端服务不可用，断路器就会直接切断请求链，避免发送大量无效请求，从而影响系统吞吐量，并且断路器有自我检测并恢复的能力

在Hystrix中，主要通过线程池来实现资源隔离。通常在使用的时候我们会根据调用的远程服务划分出多个线程池。例如，调用产品服务的Command放入A线程池，调用账户服务的Command放入B线程池。这样做的主要优点是运行环境被隔离开了。这样就算调用服务的代码存在bug或者由于其他原因导致自己所在线程池被耗尽，也不会对系统的其他服务造成影响，但是带来的代价就是维护多个线程池会对系统带来额外的性能开销。如果是对性能有严格要求而且确信自己调用服务的客户端代码不会出问题，就可以使用Hystrix的信号模式（Semaphores）来隔离资源。

断路器的含义是，当服务调用过载或不可用时，通过降级调用或故障转移的方法，减轻服务的负载。这里我们使用了回退方法设计，以快速响应来自客户端的访问，并保障客户端对微服务的访问不会因为出现故障而崩溃。断路器的设计就像电路的保护开关一样，对系统服务起到一定的保护作用。**与保护开关不同的是，当系统恢复正常时，断路器会自动失效，不用人为干预。**


**服务雪崩**

多个微服务之间调用的时候，假设微服务A调用微服务B和微服务C,微服务B和微服务C又调用其它的微服务，这就是所谓的“扇出”。如果扇出的链路上某个微服务的调用响应时间过长或者不可用，对微服务A的调用就会占用越来越多的系统资源，进而引起系统崩溃，所谓的”雪崩效应”。

**服务熔断**

熔断机制是应对雪崩效应的一种微服务链路保护机制。

当删除链路的某个微服务不可用或者响应时间太长时，会进行服务的降级，进而熔断该节点微服务的调用，快速返回”错误的响应信息。当检测到该节点微服务调用响应正常后恢复调用链路。在SpringCloud框架里熔断机制通过Hystrix实现。Hystrix会监控微服务间调用的状况，当失败的调用到一定阈值,缺省是5秒内20次调用失败就会启动熔断机制。熔断机制的注解是@HystrixCommand。

**服务降级**

整体资源快不够了，忍痛将某些服务先关掉，待渡过难关，再开启回来。

**Hystrix监控和断路器**

我们只需要在服务接口上添加Hystrix标签，就可以实现对这个接口的监控和断路器功能。Hystrix Dashboard监控面板，提供了一个界面，可以监控各个服务上的服务调用所消耗的时间等。

Hystrix Dashboard共支持以下3种不同的监控方式：

（1）[单体Hystrix消费者：通过URL http://hystrix-app:port/hystrix.stream开启，实现对具体某个服务实例的监控](https://weread.qq.com/web/reader/9593218071950312959b681kf8932dd0264f899139df0ae)

（2）默认集群监控：通过URL http://turbine-hostname:port/turbine.stream开启，实现对默认集群的监控。

（3）自定集群监控：通过URL http://turbine-hostname:port/turbine.stream?cluster=[clusterName]开启，实现对clusterName集群的监控

**Hystrix Turbine监控聚合**

使用Hystrix监控，我们需要打开每一个服务实例的监控信息来查看。而Turbine可以帮助我们把所有的服务实例的监控信息聚合到一个地方统查看。这样就不需要挨个打开一个个的页面一个个查看。

[**Hystrix Turbine默认使用注册中心是Eureka, 如果使用别的注册中心，例如Consul，需要排除默认的euraka包，不然会出现冲突，导致启动过程出错**](https://weread.qq.com/web/reader/9593218071950312959b681k38b3252026538b3eff8b041)



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
