
[Spring Cloud Sleuth](https://weread.qq.com/web/reader/c9932ea07163ff6ac993e0dk7cb321502467cbbc409e62d)|
---|

# 目录

* [使用Spring Cloud Sleuth跟踪微服务快速入门例子1 ](https://mrbird.cc/Spring-Cloud-sleuth.html)
* [使用Spring Cloud Sleuth跟踪微服务快速入门例子2](https://weread.qq.com/web/reader/71d32370716443e271df020k81232fb025f812b4ba28a23)
* [TraceFilter和Baggage实战例子](https://weread.qq.com/web/reader/71d32370716443e271df020k2663284026026657d5ffeed)
* [SpringCloud Sleuth概念](#SpringCloud-Sleuth概念)
  * [Spring Cloud Sleuth全链路追踪源理](https://weread.qq.com/web/reader/71d32370716443e271df020kf4b32ef025ef4b9ec30acd6) 
    * Span---
    * Brave---Brave[插图]是一个用于捕捉分布式系统之间调用信息的工具库，然后将这些信息以Span的形式发送给Zipkin
    * [Zipkin---是一个分布式跟踪系统，它收集系统的延时数据并提供展示界面，以便用户排查问题](https://weread.qq.com/web/reader/9593218071950312959b681ka973204026ba97da629bd12)
* [Sleuth基本用法](https://weread.qq.com/web/reader/71d32370716443e271df020k81232fb025f812b4ba28a23)
  *  Sleuth对Feign的支持 
  *  Sleuth对RestTemplate的支持
  *  Sleuth对多线程的支持---如果你想在多线程场景下使用Sleuth，必须使用它提供的多线程实现，而不是自己去初始化线程池
* [Sleuth深入用法](https://weread.qq.com/web/reader/71d32370716443e271df020k2663284026026657d5ffeed) 
  * TraceFilter
  * Baggage 


# SpringCloud Sleuth概念

Spring Cloud Sleuth为服务之间的调用提供链路追踪，通过Sleuth可以很清楚地了解到一个服务请求经过了哪些服务，每个服务处理花费了多长时间，从而让我们可以很方便地理清各微服务间的调用关系。此外，Sleuth还可以帮助我们：

耗时分析：通过Sleuth可以很方便地了解到每个采样请求的耗时，从而分析出哪些服务调用比较耗时。

可视化错误：对于程序未捕捉的异常，可以通过集成ZipKin服务在界面上看到。

链路优化：对于调用比较频繁的服务，可以针对这些服务实施一些优化措施。

**Spring Cloud Sleuth可以结合ZipKin，将信息发送到ZipKin，利用ZipKin的存储来存储信息，利用ZipKin UI来展示数据**


# 参考
* [Spring Cloud构建微服务架构：分布式服务跟踪（抽样收集）](http://blog.didispace.com/spring-cloud-starter-dalston-8-6/)
* [Spring Cloud构建微服务架构：分布式服务跟踪（收集原理](http://blog.didispace.com/spring-cloud-starter-dalston-8-5/)
* [Spring Cloud构建微服务架构：分布式服务跟踪（整合zipkin）](http://blog.didispace.com/spring-cloud-starter-dalston-8-4/)
* [Spring Cloud构建微服务架构：分布式服务跟踪（整合logstash）](http://blog.didispace.com/spring-cloud-starter-dalston-8-3/)
* [Spring Cloud构建微服务架构：分布式服务跟踪（跟踪原理）](http://blog.didispace.com/spring-cloud-starter-dalston-8-2/)
* [Spring Cloud构建微服务架构：分布式服务跟踪（入门）](http://blog.didispace.com/spring-cloud-starter-dalston-8-1/)
* []()
