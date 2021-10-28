
**在Spring Cloud构建的微服务系统中，大多数开发者使用官方提供的服务调用组件Feign来进行内部服务通信，这种声明式的HTTP客户端使用起来极为简单、优雅、方便。然而，在使用Feign消费服务时，相对于Dubbo、gRPC等RPC框架来说，性能显得十分低**

# 目录
* [Spring Cloud Feign 声明式服务调用快速入门例子1](https://mrbird.cc/Spring-Cloud-Feign.html)
* [Spring Cloud Feign 声明式服务调用快速入门例子2](https://weread.qq.com/web/reader/71d32370716443e271df020k02e32f0021b02e74f10ece8)
* Feign
  * [Feign原理 （图解）---疯狂创客圈](https://www.cnblogs.com/crazymakercircle/p/11965726.html) 
  * [Feign原理解析](https://weread.qq.com/web/reader/71d32370716443e271df020k02e32f0021b02e74f10ece8)
  * [Feign概念](#Feign概念)
  * [Feign特性](https://weread.qq.com/web/reader/71d32370716443e271df020k02e32f0021b02e74f10ece8)
    * 可插拔的注解支持，包括Feign注解和JAX-RS注解
    * 支持可插拔的HTTP编码器和解码器
    * 支持Hystrix和它的Fallback
    * 支持Ribbon的负载均衡
    * 支持HTTP请求和响应的压缩  
  * Ribbon 与 Feign or OpenFeign 和 Nginx的区别
    * [Ribbon和Feign的区别](#Ribbon和Feign的区别)
    * [Ribbon和Nginx的区别](#Ribbon和Nginx的区别)
    * [springcloud feign 与 openFeign区别](https://www.jianshu.com/p/fc5de8218384)
    * [Ribbon 与 Feign 和 Nginx 区别与使用场景,性能介绍](https://blog.csdn.net/lchq1995/article/details/83340753)
  * Spring Cloud Feign的基础功能使用
    * [FeignClient注解剖析](https://weread.qq.com/web/reader/71d32370716443e271df020k33e3289021c33e75ff09694)
    * [Feign开启GZIP压缩](https://weread.qq.com/web/reader/71d32370716443e271df020k33e3289021c33e75ff09694)---在Spring Cloud Finchley.M9版本中开启Feign GZIP压缩时，应用会启动报错
    * [Feign Client开启日志](https://weread.qq.com/web/reader/71d32370716443e271df020k33e3289021c33e75ff09694)
    * [ Feign的超时设置](https://weread.qq.com/web/reader/71d32370716443e271df020k33e3289021c33e75ff09694)
    * [Feign支持属性文件配置](https://weread.qq.com/web/reader/71d32370716443e271df020k33e3289021c33e75ff09694)
      * 对单个指定特定名称的Feign进行配置
      * 作用于所有Feign的配置方式---注意，如果通过Java代码的方式配置过Feign，然后又通过属性文件的方式配置Feign，属性文件中Feign的配置会覆盖Java代码的配置。但是可以配置feign.client.default-to-properties=false来改变Feign配置生效的优先级
    * Feign的实战运用
      * [替换Feign默认的HTTP客户端---Feign在默认情况下使用的是JDK原生的URLConnection发送HTTP请求，没有连接池，但是对每个地址会保持一个长连接，即利用HTTP的persistenceconnection, 我们可以用Apache的HTTP Client替换Feign原始的HTTP Client，通过设置连接池、超时时间等对服务之间的调用调优](https://weread.qq.com/web/reader/71d32370716443e271df020k6ea321b021d6ea9ab1ba605) 
        * [使用HTTP Client替换Feign默认Client](https://weread.qq.com/web/reader/71d32370716443e271df020k6ea321b021d6ea9ab1ba605)
        * [使用okhttp替换Feign默认的Client](https://weread.qq.com/web/reader/71d32370716443e271df020k6ea321b021d6ea9ab1ba605) 
      * [Feign的Post和Get的多参数传递](https://weread.qq.com/web/reader/71d32370716443e271df020k6ea321b021d6ea9ab1ba605)
      * [Feign的文件上传](https://weread.qq.com/web/reader/71d32370716443e271df020k6ea321b021d6ea9ab1ba605)
      * [解决Feign首次请求失败问题](https://weread.qq.com/web/reader/71d32370716443e271df020k6ea321b021d6ea9ab1ba605)
      * [Feign返回图片流处理方式](https://weread.qq.com/web/reader/71d32370716443e271df020k6ea321b021d6ea9ab1ba605)
      * [Feign调用传递Token---当使用Feign时就会发现外部请求到A服务的时候，A服务是可以拿到Token的，然而当服务使用Feign调用B服务时，Token就会丢失，从而认证失败。解决方法相对比较简单，需要做的就是在Feign调用的时候，向请求头里面添加需要传递的Token](https://weread.qq.com/web/reader/71d32370716443e271df020k6ea321b021d6ea9ab1ba605)

---

## Feign概念

在使用Spring Cloud开发微服务应用时，**各个服务提供者都是以HTTP接口的形式对外提供服务**，因此在服务消费者调用服务提供者时，底层通过HTTP Client的方式访问。当然我们可以使用JDK原生的**URLConnection、Apache的HTTPClient、Netty的异步HTTP Client, Spring的RestTemplate**去实现服务间的调用。但是最方便、最优雅的方式是通过**Spring Cloud Open Feign**进行服务间的调用。Spring Cloud对Feign进行了增强，使Feign支持Spring MVC的注解，并整合了Ribbon等，从而让Feign的使用更加方便

在微服务架构中，业务都会被拆分成一个独立的服务，服务与服务的通讯是基于HTTP RESTful的。Spring Cloud有两种服务调用方式，一种是Ribbon+RestTemplate，另一种是Feign

客户端负载均衡：负载均衡Zuul网关将一个请求发送给某一个服务的应用的时候，如果一个服务启动了多个实例，就会通过Ribbon来通过一定的负载均衡策略来发送给某一一个服务实例。Spring Cloud中的Ribbon，客户端会有一个服务器地址列表，在发送请求前通过负载均衡算法（如简单轮询，随机连接等）选择一个服务器，然后进行访问。

**负载均衡**

负载均衡：用于将工作负载分布到多个服务器来提高网站、应用、数据库或其他服务的性能和可靠性。
使用负载均衡带来的好处很明显：当集群里的1台或者多台服务器down的时候，剩余的没有down的服务器可以保证服务的继续使用；将访问压力分配到各个服务器，不会由于某一高峰时刻导致系统cpu急剧上升。
负载均衡有好几种实现策略，常见的有：随机（Random），轮询（RoundRobin），一致性哈希（ConsistentHash），哈希（Hash），加权（Weighted），Ribbon的默认策略是轮询

**RestTemplate**

传统情况下在Java代码里访问RESTful服务，一般使用Apache的HttpClient，不过此种方法使用起来太过繁琐。Spring提供了一种简单便捷的模板类来进行操作，这就是RestTemplate。
Feign是一个声明式http客户端。使用Feign能让编写http客户端更加简单，它的使用方法是定义一个接口，然后在上面添加注解，避免了调用目标微服务时，需要不断的解析/封装json数据的繁琐。Spring Cloud中Feign默认集成了Ribbon，并和Eureka结合，默认实现了负载均衡的效果。


## Ribbon和Feign的区别

Feign目标使编写Java Http客户端变得更容易

在使用Ribbon+ RestTemplate时，Ribbon需要自己构建http请求，模拟http请求然后使用RestTemplate发送给其他服务，步骤相当繁琐。利用RestTemplate对http请求的封装处理，形成了-套模版化的调用方法。但是在实际开发中，由于对服务依赖的调用可能不止一处,往往一个接口会被多处调用，所以通常都会针对每个微服务自行封装一些客户端类来包装这些依赖服务的调用。所以，Feign在此基础上做了进一步封装，由他来帮助我们定义和实现依赖服务接口的定义。

在Feign的实现下，我们只需创建一个接口并使用注解的方式来配置它（以前是Dao接口上面标注Mapper注解，现在是一个微服务接口上面标注一个Feign注解即可）， 即可完成对服务提供方的接口绑定，简化了使用Spring Cloud Ribbon时，自动封装服务调用客户端的开发量。

**Feign集成了Ribbon**

Ribbon通过轮询实现了客户端的负载均衡，而与Ribbon不同的是，Feign是一个声明式的Web服务客户端， 使得编写Web服务客户端变得非常容易，只需要创建一个接口， 然后在上面添加注解，像调用本地方法一样调用它就可以，而感觉不到是调用远程方法。SpringCloud中Feign默认集成了Ribbon，并和Eureka结合，默认实现了负载均衡的效果

## Ribbon和Nginx的区别

服务器端负载均衡Nginx

Nginx是客户端所有请求统一交给Nginx，由Nginx进行实现负载均衡请求转发，属于服务器端负载均衡。既请求由Nginx服务器端进行转发。客户端负载均衡Ribbon，Ribbon是从Eureka注册中心服务器端上获取服务注册信息列表，缓存到本地，然后在本地实现轮询负载均衡策略。既在客户端实现负载均衡。

应用场景的区别:

Nginx适合于服务器端实现负载均衡，比如：Tomcat，Ribbon适合与在微服务中RPC远程调用实现本地服务负载均衡，比如：Dubbo、Spring Cloud中都是采用本地负载均衡。

# 参考
* [Feign](https://blog.csdn.net/u012734441/article/details/77662617)
* [Spring Cloud Alibaba基础教程：支持的几种服务消费方式（RestTemplate、WebClient、Feign](http://blog.didispace.com/spring-cloud-alibaba-2/)
* [Spring Cloud Feign的文件上传实现](http://blog.didispace.com/spring-cloud-starter-dalston-2-4/)
* [Spring Boot和Feign中使用Java 8时间日期API（LocalDate等）的序列化问题](http://blog.didispace.com/Spring-Boot-And-Feign-Use-localdate/)
* [@FeignClient中的@RequestMapping也被SpringMVC加载的问题解决](http://blog.didispace.com/spring-cloud-feignclient-problem/)
* [Spring Cloud实战小贴士：Feign的继承特性(伪RPC模式)](http://blog.didispace.com/spring-cloud-tips-feign-rpc/)
* [探讨通过Feign配合Hystrix进行调用时异常的处理](http://blog.didispace.com/rencong-1/)
* [Spring Cloud构建微服务架构（二）服务消费者](http://blog.didispace.com/springcloud2/)
