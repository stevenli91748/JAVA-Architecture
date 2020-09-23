
# IOC

* Spring容器类型
  * [BeanFactory](#BeanFactory)
  * [ApplicationContext](#ApplicationContext)
* [深入剖析Spring IOC容器的运作原理](https://www.ctolib.com/topics-109935.html)
* [Spring之IOC原理详解](https://blog.csdn.net/sunpeng_sp/article/details/57419999)
* [《包你懂系列》一文讲清楚 Spring IoC 实现原理和过程](https://juejin.im/post/5eb4c13fe51d454dec72d4bd)
* [面试被问了几百遍的 IoC 和 AOP ，还在傻傻搞不清楚](https://juejin.im/post/5ecf62ee51882542ef4f851f)


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
