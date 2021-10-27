
 [客户端负载均衡器：Spring Cloud Netflix Ribbon](https://weread.qq.com/web/reader/c9932ea07163ff6ac993e0dk19c3222022419ca14e7eef7)|
 ---|

# 目录
* [Spring Cloud Ribbon客户端负载均衡快速入门例子1](https://mrbird.cc/Spring-Cloud-Ribbon-LoadBalance.html)
* [Spring Cloud Ribbon客户端负载均衡快速入门例子2](https://weread.qq.com/web/reader/71d32370716443e271df020k182326e0221182be0c5ca23)
* Ribbon概念
  * [Ribbon核心工作原理](https://weread.qq.com/web/reader/71d32370716443e271df020k1c3321802231c383cd30bb3)
    * [Ribbon核心接口](https://weread.qq.com/web/reader/71d32370716443e271df020k1c3321802231c383cd30bb3) 
    * [Ribbon是如何做到使用RestTemplate达到负载均衡的原理](https://weread.qq.com/web/reader/71d32370716443e271df020k1c3321802231c383cd30bb3)
  * [Ribbon 与 Feign 和 Nginx 区别与使用场景,性能介绍](https://blog.csdn.net/lchq1995/article/details/83340753)
  * [Ribbon和Feign的区别](#Ribbon和Feign的区别)
  * [Ribbon和Nginx的区别](#Ribbon和Nginx的区别)
* [Spring Cloud Ribbon实战](https://weread.qq.com/web/reader/71d32370716443e271df020ke3632bd0222e369853df322)
  * [Ribbon负载均衡策略与自定义配置](https://weread.qq.com/web/reader/71d32370716443e271df020ke3632bd0222e369853df322)
    * 7种Ribbon负载均衡策略
    * [负载均衡策略实现原理解析](https://weread.qq.com/web/reader/71d32370716443e271df020k1c3321802231c383cd30bb3) 
    * 全局策略设置---全局更改负载均衡策略
    * 基于注解的策略设置---如果我们想要针对某一个源服务设置其特有的策略，可以通过使用@RibbonClient注解，在使用它之前，我们需要对全局设置的代码进行改变
    * 基于配置文件的策略设置---如果不喜欢用注解，也可以使用配置文件来对源服务负载策略进行配置，其基本语法是<client name>.ribbon.*，使用它几乎可以不用注解形式的任何配置代码，推荐使用
    * Ribbon超时与重试---使用HTTP发起请求，免不了要经历极端环境，此时对调用进行时限控制以及时限之后的重试尤为重要。注意，F版中Ribbon的重试机制是默认开启的，需要添加对于超时时间与重试策略的配置
    * Ribbon的饥饿加载---Ribbon在进行客户端负载均衡的时候并不是在启动时就加载上下文，而是在实际请求的时候才去创建，因此这个特性往往会让我们的第一次调用显得颇为疲软乏力，严重的时候会引起调用超时。所以我们可以通过指定Ribbon具体的客户端的名称来开启饥饿加载，即在启动的时候便加载所有配置项的应用程序上下文
    * 利用配置文件自定义Ribbon客户端---就可以使用配置文件来定制Ribbon客户端了，其实质也就是使用配置文件来指定一些默认加载类，从而更改Ribbon客户端的行为方式，并且使用这种方式优先级最高，优先级高于使用注解@RibbonClient指定的配置和源码中加载的相关Bean
    * Ribbon脱离Eureka的使用---在默认情况下，Ribbon客户端会从Eureka注册中心读取服务注册信息列表，来达到一种动态负载均衡的功能。但是有一种情况下则不会推荐这种方式，即如果Eureka是一个供很多人使用的公共注册中心（比如社区公益Eureka，地址：http://eureka.springcloud.cn），此时极易产生服务侵入性问题，所以就不要从Eureka中读取服务列表了，而应该在Ribbon客户端自行指定源服务地址，让Ribbon脱离Eureka来使用

## Ribbon和Feign的区别

Feign目标使编写Java Http客户端变得更容易

在使用Ribbon+ RestTemplate时，Ribbon需要自己构建http请求，模拟http请求然后使用RestTemplate发送给其他服务，步骤相当繁琐。利用RestTemplate对http请求的封装处理，形成了-套模版化的调用方法。但是在实际开发中，由于对服务依赖的调用可能不止一处,往往一个接口会被多处调用，所以通常都会针对每个微服务自行封装一些客户端类来包装这些依赖服务的调用。所以，Feign在此基础上做了进一步封装，由他来帮助我们定义和实现依赖服务接口的定义。

在Feign的实现下，我们只需创建一个接口并使用注解的方式来配置它（以前是Dao接口上面标注Mapper注解，现在是一个微服务接口上面标注一个Feign注解即可）， 即可完成对服务提供方的接口绑定，简化了使用Spring Cloud Ribbon时，自动封装服务调用客户端的开发量。

Ribbon是一个客户端负载均衡器，它赋予了应用一些支配HTTP与TCP行为的能力，可以得知，这里的客户端负载均衡（许多人称之为后端负载均衡）也是进程内负载均衡的一种

Ribbon默认使用轮询的方式访问源服务，此外Ribbon对服务实例节点的增减也能动态感知

**Feign集成了Ribbon**

Ribbon通过轮询实现了客户端的负载均衡，而与Ribbon不同的是，Feign是一个声明式的Web服务客户端， 使得编写Web服务客户端变得非常容易，只需要创建一个接口， 然后在上面添加注解，像调用本地方法一样调用它就可以，而感觉不到是调用远程方法。SpringCloud中Feign默认集成了Ribbon，并和Eureka结合，默认实现了负载均衡的效果。


## Ribbon和Nginx的区别

**服务器端负载均衡Nginx**

Nginx是客户端所有请求统一交给Nginx，由Nginx进行实现负载均衡请求转发，属于服务器端负载均衡。既请求由Nginx服务器端进行转发。客户端负载均衡Ribbon，Ribbon是从Eureka注册中心服务器端上获取服务注册信息列表，缓存到本地，然后在本地实现轮询负载均衡策略。既在客户端实现负载均衡。

应用场景的区别

Nginx适合于服务器端实现负载均衡，比如：Tomcat，Ribbon适合与在微服务中RPC远程调用实现本地服务负载均衡，比如：Dubbo、Spring Cloud中都是采用本地负载均衡。


# 参考
* [Spring Cloud Ribbon踩坑记录及原理解析](https://github.com/aCoder2013/blog/issues/29)
* [撸一撸Spring Cloud Ribbon的原理-负载均衡策略](https://www.cnblogs.com/kongxianghai/p/8477781.html)
* [Feign Ribbon Hystrix 三者关系 | 史上最全, 深度解析](https://www.cnblogs.com/crazymakercircle/p/11664812.html)
* [为Spring Cloud Ribbon配置请求重试（Camden.SR2+）](http://blog.didispace.com/spring-cloud-ribbon-failed-retry/)
* [Spring Cloud实战小贴士：Zuul的饥饿加载（eager-load）使用](http://blog.didispace.com/spring-cloud-tips-zuul-eager/)
* [为Spring Cloud Ribbon配置请求重试（Camden.SR2+](http://blog.didispace.com/spring-cloud-ribbon-failed-retry/)
* [Spring Cloud实战小贴士：Ribbon的饥饿加载(eager-load)模式](http://blog.didispace.com/spring-cloud-tips-ribbon-eager/)
* [Spring Cloud源码分析（二）Ribbon](http://blog.didispace.com/springcloud-sourcecode-ribbon/)
* [Spring Cloud构建微服务架构（二）服务消费者](http://blog.didispace.com/springcloud2/)
