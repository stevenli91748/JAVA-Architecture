
# 在线书籍
* [java异步编程实战](https://weread.qq.com/web/reader/44332cc071a486a7443c539k8f132430178f14e45fce0f7)
* [并发编程网](http://ifeve.com/)

---

* Spring Boot 并发编程
  * [厉害了！我们部门主管半小时把我的springboot项目并发提升几倍](https://www.jianshu.com/p/669edac4ae8f) 
  * [并发编程系列之如何正确使用线程池？](https://www.jianshu.com/p/6a2ed7180ff8)
  * [从“设计思想”到“源码解读”，一步一步深入解析Java线程池](https://www.jianshu.com/p/a5e71ae77c53)
  * [SpringBoot —— 多线程定时任务的实现（注解配置、task:annotation-driven配置）](https://www.jianshu.com/p/cec00ad3be2e)
  * [Spring Boot中使用@Async实现异步调用，加速任务的执行](https://www.jianshu.com/p/a57e3aa8418a)
  * [Spring Boot中有多个@Async异步任务时，记得做好线程池的隔离](https://www.jianshu.com/p/9441a048696c)
  * [Spring Boot中使用@Async的时候，千万别忘了线程池的配置！](https://www.jianshu.com/p/0db4d91a6d22)
  * [Spring Boot中如何配置线程池拒绝策略，妥善处理好溢出的任务](https://www.jianshu.com/p/7f519a8267ee)
  * [调用线程不可捕捉异步线程的异常,如何处理?](https://www.jianshu.com/p/ad32d98a624f)


## 异步任务
* 异步编程场景
  * 1 单JVM内的异步编程 
    * [需要异步地处理一些事情，而不需要知道异步任务的结果  ](https://weread.qq.com/web/reader/44332cc071a486a7443c539k16732dc0161679091c5aeb1)
      * [java中使用线程和线程池实现异步编程 ](https://weread.qq.com/web/reader/44332cc071a486a7443c539k45c322601945c48cce2e120)
        * 显式使用Thread创建异步任务的两种方式
          * 第一种方式是实现java.lang.Runnable接口的run方法，然后传递Runnable接口的实现类作为创建Thread时的参数，启动线程
          * 第二种开启线程进行异步执行的方式是实现Thread类，并重写run方法
    * [有时候我们还需要在主线程等待异步任务的执行结果](https://weread.qq.com/web/reader/44332cc071a486a7443c539k16732dc0161679091c5aeb1)
      * Future
      * CompletableFuture
      * Stream + CompletableFuture
      * Reactor、RxJava等反应式API
  * 2 [跨网络的异步编程 ](https://weread.qq.com/web/reader/44332cc071a486a7443c539k16732dc0161679091c5aeb1)
    * 网络请求中RPC框架的异步编程
      * NIO
      * Netty + CompletableFuture
    * [Web请求的异步编程](https://weread.qq.com/web/reader/44332cc071a486a7443c539k16732dc0161679091c5aeb1)
      * Servlet容器对Servlet的处理都是每个请求对应一个线程这种1 : 1的模式进行处理的 
      * WebFlux---新的使用少量线程和较少的硬件资源来处理并发的非阻塞Web技术栈,其是与Servlet技术栈并行存在的一种新技术，基于JDK8函数式编程与Netty实现天然的异步、非阻塞处理
  * 3 异步编程框架
    * Disruptor---高性能线程间消息传递库,其通过为事件（event）预先分配内存、无锁CAS算法、缓冲行填充、两阶段协议提交来实现多线程并发地处理不同的元素，从而实现高性能的异步处理
    * Akka---基于Actor模式实现了天然支持分布式的使用消息进行异步处理的服务
    * Apache RocketMetaQ  
* [java异步编程实战](https://weread.qq.com/web/reader/44332cc071a486a7443c539k16732dc0161679091c5aeb1)
* [Spring Boot 中的异步调用](https://mrbird.cc/Spring-Boot-Async.html)
* [使用@Async实现异步调用](http://blog.didispace.com/springbootasync/)
* [使用@Async实现异步调用：自定义线程池](http://blog.didispace.com/springbootasync-2/)
* [使用@Async实现异步调用：资源优雅关闭](http://blog.didispace.com/springbootasync-3/)
* [使用@Async实现异步调用：使用Future以及定义超时](http://blog.didispace.com/springbootasync-4/)


## 定时任务

* [使用@Scheduled创建定时任务](http://blog.didispace.com/springbootscheduled/)
* [SpringBoot中@Scheduled实现多线程并发定时任务](https://www.jianshu.com/p/ff876c2ab24a)


# SpringBoot线程
* [Spring Boot线程安全指南](https://zhuanlan.zhihu.com/p/60346760)
* [Spring Boot启用异步线程](https://cloud.tencent.com/developer/article/1609499?from=article.detail.1632359)
* [Spring Boot 多线程](https://www.jianshu.com/p/769573bd362c)
* [spring-boot | 多线程并发定时任务](https://www.jianshu.com/p/0db083bf4d39)
* [SpringBoot中如何优雅的使用多线程](https://juejin.cn/post/6844904173977206797)
* [SpringBoot 使用线程池](https://crossoverjie.top/2018/07/29/java-senior/ThreadPool/)
* [SpringBoot结合线程池解决多线程问题实录](https://www.cxyzjd.com/article/GFJ0814/92422245)
* [Spring-Boot中如何使用多线程处理任务](https://zhuanlan.zhihu.com/p/49416067)
* [Spring Boot 对多线程支持-提高程序执行效率](https://www.cnblogs.com/felixzh/p/12753365.html#:~:text=spring%20boot%20%E5%A6%82%E4%BD%95%E4%BD%BF%E7%94%A8%E5%A4%9A,%E5%B0%B1%E5%8F%AF%E4%BB%A5%E4%BD%BF%E7%94%A8%E7%BA%BF%E7%A8%8B%E6%B1%A0%E3%80%82)


# 并发

* [Spring Boot的并发编程](https://cloud.tencent.com/developer/article/1347245?from=information.detail.spring%E9%AB%98%E5%B9%B6%E5%8F%91)
* [Spring 异步调用，一行代码实现！舒服，不接受任何反驳](https://cloud.tencent.com/developer/article/1632359?from=article.detail.1347245)
* [Spring Boot中的并发处理](https://blog.happyhack.io/2019/08/30/springboot-concurrency/)
* [springboot配置线程池-高并发场景](https://zhuanlan.zhihu.com/p/149274738)
* [springboot利用@Async提升API接口并发能力](http://www.danyuanblog.com/blog/app/blog/blogDetail.html?id=5e6d13094c636312f4b7091a)
* [我是如何把SpringBoot项目的并发提升十倍量级的](https://blog.csdn.net/emprere/article/details/104404256?utm_medium=distribute.pc_relevant.none-task-blog-baidujs_baidulandingword-0&spm=1001.2101.3001.4242)
* [通过Tomcat配置改善SpringBoot项目并发线程数上不去的问题](https://blog.csdn.net/weixin_41657493/article/details/90819987?utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromMachineLearnPai2%7Edefault-3.control&dist_request_id=1619637687737_21438&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromMachineLearnPai2%7Edefault-3.control)
* [设置springboot自带tomcat的最大连接数和最大并发数](https://blog.csdn.net/kouwoo/article/details/83898788?utm_medium=distribute.pc_relevant.none-task-blog-baidujs_baidulandingword-5&spm=1001.2101.3001.4242)
* [tomcat优化——并发和Tomcat线程数](https://blog.csdn.net/hunhun1122/article/details/78818337)
* [Spring boot中最大连接数、最大线程数与最大等待数在生产中的异常场景](https://blog.csdn.net/qq_36956015/article/details/100929993?utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromMachineLearnPai2%7Edefault-1.control&dist_request_id=1619638919485_32258&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromMachineLearnPai2%7Edefault-1.control)
* [springboot能抗住多少并发_并发测试神器，看看你的能不能抗住超过 5 万的并发用户](https://blog.csdn.net/weixin_39989688/article/details/111394127?utm_medium=distribute.pc_relevant.none-task-blog-baidujs_baidulandingword-0&spm=1001.2101.3001.4242)
