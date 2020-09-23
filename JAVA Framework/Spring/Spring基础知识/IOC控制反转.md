
# IOC

* Spring容器类型
  * [BeanFactory](#BeanFactory)
  * [ApplicationContext](#ApplicationContext)
* [深入剖析Spring IOC容器的运作原理](https://www.ctolib.com/topics-109935.html)
* [Spring之IOC原理详解](https://blog.csdn.net/sunpeng_sp/article/details/57419999)
* [《包你懂系列》一文讲清楚 Spring IoC 实现原理和过程](https://juejin.im/post/5eb4c13fe51d454dec72d4bd)
* [面试被问了几百遍的 IoC 和 AOP ，还在傻傻搞不清楚](https://juejin.im/post/5ecf62ee51882542ef4f851f)

* [Spring IOC 容器源码分析系列文章导读](https://www.tianxiaobo.com/2018/05/30/Spring-IOC-%E5%AE%B9%E5%99%A8%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90%E7%B3%BB%E5%88%97%E6%96%87%E7%AB%A0%E5%AF%BC%E8%AF%BB/)
* [Spring IOC 容器源码分析 - 获取单例 bean](https://www.tianxiaobo.com/2018/06/01/Spring-IOC-%E5%AE%B9%E5%99%A8%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90-%E8%8E%B7%E5%8F%96%E5%8D%95%E4%BE%8B-bean/)
* [Spring IOC 容器源码分析 - 创建单例 bean 的过程](https://www.tianxiaobo.com/2018/06/04/Spring-IOC-%E5%AE%B9%E5%99%A8%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90-%E5%88%9B%E5%BB%BA%E5%8D%95%E4%BE%8B-bean/)
* [Spring IOC 容器源码分析 - 创建原始 bean 对象](https://www.tianxiaobo.com/2018/06/06/Spring-IOC-%E5%AE%B9%E5%99%A8%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90-%E5%88%9B%E5%BB%BA%E5%8E%9F%E5%A7%8B-bean-%E5%AF%B9%E8%B1%A1/)
* [Spring IOC 容器源码分析 - 循环依赖的解决办法](https://www.tianxiaobo.com/2018/06/08/Spring-IOC-%E5%AE%B9%E5%99%A8%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90-%E5%BE%AA%E7%8E%AF%E4%BE%9D%E8%B5%96%E7%9A%84%E8%A7%A3%E5%86%B3%E5%8A%9E%E6%B3%95/)
* [Spring IOC 容器源码分析 - 填充属性到 bean 原始对象](https://www.tianxiaobo.com/2018/06/11/Spring-IOC-%E5%AE%B9%E5%99%A8%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90-%E5%A1%AB%E5%85%85%E5%B1%9E%E6%80%A7%E5%88%B0-bean-%E5%8E%9F%E5%A7%8B%E5%AF%B9%E8%B1%A1/)
* [Spring IOC 容器源码分析 - 余下的初始化工作](https://www.tianxiaobo.com/2018/06/11/Spring-IOC-%E5%AE%B9%E5%99%A8%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90-%E4%BD%99%E4%B8%8B%E7%9A%84%E5%88%9D%E5%A7%8B%E5%8C%96%E5%B7%A5%E4%BD%9C/)



IOC就是控制反转，是指创建对象的控制权的转移，以前创建对象的主动权和时机是由自己把控的，而现在这种权力转移到Spring容器中，并由容器根据配置文件去创建实例和管理各个实例之间的依赖关系，对象与对象之间松散耦合，也利于功能的复用

在spring中，对象的属性是由对象自己创建的，就是正向流程；如果属性不是对象创建，而是由spring来自动进行装配，就是控制反转。这里的DI也就是依赖注入，就是实
现控制反转的方式。正向流程导致了对象于对象之间的高耦合，IOC可以解决对象耦合的问题，有利于功能的复用，能够使程序的结构变得非常灵活。

当一个对象创建时, 它所依赖的对象由外部传递给他, 而非自己去创建所依赖的对象(比如通过new操作). 因此,也可以说在对象如何获取他所依赖的对象这件事情上, 控制权反转了

应用程序在运行时依赖IoC容器来动态注入对象需要的外部资源。



Spring的IOC有三种注入方式 ：构造器注入、setter方法注入、根据注解注入

构造器注入|setter方法注入|根据注解注入|
---|---|---|


---
## BeanFactory

    BeanFactory。基础类型IoC容器，提供完整的IoC服务支持。如果没有特殊指定，默认采用延迟初始化策略（lazy-load）。只有当客户端对象需要访问容器中的某个受管对象的时候，才
    对该受管对象进行初始化以及依赖注入操作。所以，相对来说，容器启动初期速度较快，所需要的资源有限。对于资源有限，并且功能要求不是很严格的场景，BeanFactory是比较合适
    的IoC容器选择


## ApplicationContext

    ApplicationContext在BeanFactory的基础上构建，是相对比较高级的容器实现，除了拥有BeanFactory的所有支持，ApplicationContext还提供了其他高级特性，比如事件发布、国际化信息支持等，
    这些会在后面详述。ApplicationContext所管理的对象，在该类型容器启动之后，默认全部初始化并绑定完成。所以，相对于BeanFactory来说，ApplicationContext要求更多的系统资源，同时，因为
    在启动时就完成所有初始化，容器启动时间较之BeanFactory也会长一些。在那些系统资源充足，并且要求更多功能的场景中，ApplicationContext类型的容器是比较合适的选择
