
# AOP

* [AOP术语](https://mrbird.cc/AOP%E6%9C%AF%E8%AF%AD.html)
* [使用注解配置AOP切面](https://mrbird.cc/%E4%BD%BF%E7%94%A8%E6%B3%A8%E8%A7%A3%E9%85%8D%E7%BD%AEAOP%E5%88%87%E9%9D%A2.html)
* [在Spring XML中声明切面](https://mrbird.cc/%E5%9C%A8Spring-XML%E4%B8%AD%E5%A3%B0%E6%98%8E%E5%88%87%E9%9D%A2.html)
* 

# 博客

* [Spring AOP教程](https://www.yiibai.com/spring_aop/) | [Spring AOP Tutorial for Beginners - Step by Step with Example](https://www.javaguides.net/2019/05/understanding-spring-aop-concepts-and-terminology-with-example.html) | [Spring AOP——Spring 中面向切面编程](https://www.cnblogs.com/joy99/p/10941543.html)|
---|---|---|


# AOP例子程序
* [Spring AOP 详解 1(注解方式)  例子说明](https://juejin.im/post/5d390f705188252c784a4f74)
* [Spring AOP 详解 2(xml方式)  例子说明 先看](https://www.cnblogs.com/hongwz/p/5764917.html)
* [深入理解Spring AOP原理](https://mrbird.cc/%E6%B7%B1%E5%85%A5%E7%90%86%E8%A7%A3Spring-AOP%E5%8E%9F%E7%90%86.html)
* [Spring AOP Example](https://howtodoinjava.com/spring-aop-tutorial/)

AOP动态代理|AOP静态代理|
---|--|


[Spring AOP教程](https://www.yiibai.com/spring_aop/)|
---|

[一文读懂 AOP --- 你想要的最全面 AOP 方法探讨](https://www.jianshu.com/p/0799aa19ada1)|[spring专题系列之AOP的理解和分析](https://www.jianshu.com/p/41e9d1c494e4)|[Spring-AOP-基于注解的AOP通知执行顺序](https://www.cnblogs.com/orzjiangxiaoyu/p/13869747.html)|
---|---|---|

[一文应用 AOP  最全选型考量 + 边剖析经典开源库边实践](https://www.jianshu.com/p/42ce95450adb)|[springboot + aop的最佳实践](https://juejin.cn/post/6844904105819783175)|
---|---|

[Spring AOP 编写流程整理](https://www.huaweicloud.com/articles/13636519.html)|[使用spring AOP管理权限时拦截位置的困扰](https://www.jdon.com/27858)|
---|---|

[《包你懂系列》让我们来揭开 AOP 和动态代理技术的神秘面纱](https://juejin.im/post/5ed723606fb9a047a07f2ec2)|[面试被问了几百遍的 IoC 和 AOP ，还在傻傻搞不清楚](https://juejin.im/post/5ecf62ee51882542ef4f851f)|
---|--|


[关于 Spring AOP 的 5 种通知类型，很少人知道它的优先级](https://mp.weixin.qq.com/s/XTCQtKGIEdaXGC5J2FCobg)|
---|


# 视频

* [小马哥讲Spring核心编程思想---在硬盘](https://time.geekbang.org/course/intro/100042601)
* [小马哥讲Spring AOP编程思想---在硬盘](https://time.geekbang.org/course/intro/100066301)
* [自己动手实现的 Spring IOC 和 AOP - 上篇](https://www.tianxiaobo.com/2018/01/18/%E8%87%AA%E5%B7%B1%E5%8A%A8%E6%89%8B%E5%AE%9E%E7%8E%B0%E7%9A%84-Spring-IOC-%E5%92%8C-AOP-%E4%B8%8A%E7%AF%87/)
* [自己动手实现的 Spring IOC 和 AOP - 下篇](https://www.tianxiaobo.com/2018/01/18/%E8%87%AA%E5%B7%B1%E5%8A%A8%E6%89%8B%E5%AE%9E%E7%8E%B0%E7%9A%84-Spring-IOC-%E5%92%8C-AOP-%E4%B8%8B%E7%AF%87/)
* [Spring AOP 源码分析系列文章导读](https://www.tianxiaobo.com/2018/06/17/Spring-AOP-%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90%E7%B3%BB%E5%88%97%E6%96%87%E7%AB%A0%E5%AF%BC%E8%AF%BB/)
* [Spring AOP 源码分析 - 筛选合适的通知器](https://www.tianxiaobo.com/2018/06/20/Spring-AOP-%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90-%E7%AD%9B%E9%80%89%E5%90%88%E9%80%82%E7%9A%84%E9%80%9A%E7%9F%A5%E5%99%A8/)
* [Spring AOP 源码分析 - 创建代理对象](https://www.tianxiaobo.com/2018/06/20/Spring-AOP-%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90-%E5%88%9B%E5%BB%BA%E4%BB%A3%E7%90%86%E5%AF%B9%E8%B1%A1/)
* [Spring AOP 源码分析 - 拦截器链的执行过程](https://www.tianxiaobo.com/2018/06/22/Spring-AOP-%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90-%E6%8B%A6%E6%88%AA%E5%99%A8%E9%93%BE%E7%9A%84%E6%89%A7%E8%A1%8C%E8%BF%87%E7%A8%8B/)






AOP就是面向切面编程。如右面的图，一般程序执行流程是从controller层调用service层、然后service层调用DAO层访问数据，最后在逐层返回结果。

这个是图中向下箭头所示的按程序执行顺序的纵向处理。但是，一个系统中会有多个不同的服务，例如用户服务、商品信息服务等等，每个服务的controller层都需要验
证参数，都需要处理异常，如果按照图中红色的部分，对不同服务的纵向处理流程进行横切，在每个切面上完成通用的功能，例如身份认证、验证参数、处理异常等等、这
样就不用在每个服务中都写相同的逻辑了，这就是AOP思想解决的问题。

AOP以功能进行划分，对服务顺序执行流程中的不同位置进行横切，完成各服务共同需要实现的功能。

AOP的实现是通过代理模式，在调用对象的某个方法时，执行插入的切面逻辑。实现的方式有动态代理也叫运行时增强，比如jdk代理、CGLIB；静态代理是在编译时进行织入或类加载时进行织入，比如AspectJ。

关于AOP还需要了解一下对应的Aspect、pointcut、advice等注解和具体使用方式。



# 有用的参考
* [Spring AOP介绍与使用](https://www.cnblogs.com/Courage129/p/14071482.html)
* [Spring面向切面编程](http://objcoding.com/2017/08/25/Spring-AOP/)
* [SpringAOP ApectJ 动态代理](https://www.cnblogs.com/heapStark/p/8358217.html)
* [AOP的两种实现-Spring AOP以及AspectJ](https://www.cnblogs.com/john8169/p/9780502.html)
* [spring aop原理和实现](https://www.cnblogs.com/JavaZhangXu/p/10109642.html)
* [SpringAOP面向切面编程](https://www.cnblogs.com/SimpleWu/p/9652700.html)
* [Spring AOP无法拦截内部方法调用](https://www.jianshu.com/p/6534945eb3b5)
* [Spring---AOP](https://www.jianshu.com/p/a0101e7cc649)

* [一个Bug，让我发现了 Java 界的.AJ(锥)](https://bugstack.cn/itstack-code-life/2021/04/21/%E4%B8%80%E4%B8%AABug-%E8%AE%A9%E6%88%91%E5%8F%91%E7%8E%B0%E4%BA%86-Java-%E7%95%8C%E7%9A%84.AJ(%E9%94%A5).html)
* [循序渐进之Spring AOP(1) - 原理](https://blog.csdn.net/autfish/article/details/51068062)
* [循序渐进之Spring AOP(2) - 基本概念](https://blog.csdn.net/autfish/article/details/51100425)
* [循序渐进之Spring AOP(3) - 配置代理](https://blog.csdn.net/autfish/article/details/51115420)
* [循序渐进之Spring AOP(4) - Introduction](https://blog.csdn.net/autfish/article/details/51124919)
* [循序渐进之Spring AOP(5) - 创建切面](https://blog.csdn.net/autfish/article/details/51170654)
* [循序渐进之Spring AOP(6) - 使用@Aspect注解](https://blog.csdn.net/autfish/article/details/51184405)

