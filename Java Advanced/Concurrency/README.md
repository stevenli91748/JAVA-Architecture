# [并发知识点](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20Advanced/Concurrency/并发面试.md)

[java并发成神之路---JUC全方位解读脑图](https://naotu.baidu.com/file/89fb28b05e3395800f9dc2d332d2b198?token=9b45e08e55281667)|[Java多任务并行处理框架 Fork Join](https://www.bilibili.com/video/BV1Gp411R7YG)|
---|---|

[高并发系统中三大利器绝学-亿级高并发削峰](https://www.bilibili.com/video/BV1uW411P7HR)|[天猫高并发场景的订单号、ID生成策略](https://www.bilibili.com/video/BV1xs411571g)|
---|---|

[架构师带你深入研究，亿级高并发削峰利器](https://www.bilibili.com/video/BV1Ut411277z)|[Java必备技能，亿级高并发削峰利器](https://www.bilibili.com/video/BV1S4411X7qp)|
---|---|

[深入了解Java高并发通信模型NIO](https://www.bilibili.com/video/BV1x4411Z7ek)|
---|

# 目录
* [并发学习路线](#并发学习路线)
* [七大并发模型](#七大并发模型)
  * [线程与锁](#线程与锁)
  * [函数式编程](#函数式编程)
  * [Clojure之道](#Clojure之道)
  * [Actor](#Actor)
  * [通信顺序进程](#通信顺序进程)
  * [数据并行](#数据并行)
  * [Lambda架构](#Lambda架构)
* 并发基础知识
* 并发理论
* 并发关键字
* Lock体系
  * [Java注解如何基于Redission实现分布式锁](https://www.jb51.net/article/178525.htm)
  * [Java基于注解实现的锁实例解析](https://www.jb51.net/article/181492.htm)
* 并发容器
* 线程池
* 原子操作类
* 并发工具
* 并发实践 

# 并发学习路线

学习并发技术，我建议分为三个步骤

1. 首先熟悉Java大的多线程基础知识，比如Thread的使用， 线程的状态转换，以及一些API的使用方式
2. 了解一些理论基础，比如JMM， 操作系统的多线程实现，这部份内容推荐通过看书和看博客来学习
3. 了解并使用JUC的一些API，然后开始看JUC的一些代码，这个阶段是最难的

* [Java并发学习路线-不懂原理多吃亏](http://ifeve.com/java-concureent/)

# 七大并发模型
## 线程与锁
## 函数式编程
## Clojure之道
## Actor
## 通信顺序进程
## 数据并行
## Lambda架构


# 视频
* [Java高并发之魂：synchronized深度解析](https://www.imooc.com/learn/1086)
 * [玩转Java并发工具，精通JUC，成为并发多面手](https://drive.google.com/drive/folders/1OSoTu7hUjJ3my2cxLGy2VGeqyBsqEs8n)
 * [Java并发编程原理与实战](https://www.youtube.com/watch?v=10Xrkmqa9d0&list=PLStUqVcO94bhOvk5msq7gxkcxoP4Tc8mv)
 * [阿里巴巴大牛教你快速了解并发编程](https://www.bilibili.com/video/av19131750/?spm_id_from=333.788.videocard.17)
 * [程序员一线互联网面试必杀技Java并发编程之Volatile源码分析](https://www.bilibili.com/video/av68881701)
 * [JUC](https://www.bilibili.com/video/av68272550/?spm_id_from=333.788.videocard.2)
 * [juc 与 jvm - Java 必学 2019版本-阳哥- 尚硅谷](https://www.bilibili.com/video/av70166821?from=search&seid=4915820349885388522)
 * [2019最新最全Java并发编程全套源码解析原理与实战](https://www.bilibili.com/video/av65172968/?spm_id_from=333.788.videocard.8)
 * [并发编程专题-java内存模型jmm与volatile关键字详解](https://www.bilibili.com/video/av61474893?from=search&seid=808943440095103708)
 * [JAVA开发工程师/并发编程利器aqs源码深度解读](https://www.bilibili.com/video/av61176596?from=search&seid=9591105149997618327)
 
 # 高并发
 * [1. Java高并发秒杀API之业务分析与DAO层](https://www.imooc.com/learn/587)
 * [2. Java高并发秒杀API之Service层](https://www.imooc.com/learn/631)
 * [3. Java高并发秒杀API之web层](https://www.imooc.com/learn/630)
 * [4. Java高并发秒杀API之高并发优化](https://www.imooc.com/learn/632)
 
 
# 有用的参考
* [并发编程面试题（2020最新版）](https://blog.csdn.net/ThinkWon/article/details/104863992)
* [并发网系列文章集](http://ifeve.com/paper-set/)
* [看动画学并发编程](https://sourceforge.net/projects/javaconcurrenta/)


* [聊聊高并发](https://blog.csdn.net/ITer_ZC/column/info/loveconcurrency)
* [我们来谈下高并发和分布式中的幂等处理](https://juejin.im/post/5c05f233e51d4524860fc51a)
* [关于java高并发设计整合-分布式事务(转发)](https://blog.csdn.net/rainyear/article/details/81294959)
* [Java系统的高并发解决方法详解](https://www.jb51.net/article/124145.htm)
* [Java使用代码模拟高并发操作的示例](https://www.jb51.net/article/139429.htm)
* [Java构建高并发高可用的电商平台架构实践](https://blog.csdn.net/lsm135/article/details/50920483)
* [【Java并发编程】并发编程大合集](https://blog.csdn.net/ns_code/article/details/17539599)
* [java系统高并发解决方案（转载）](https://blog.csdn.net/jimmy609/article/details/37810591)
* [Java并发-不懂原理多吃亏](http://ifeve.com/java-concureent/)
石杉的架构笔记
* [大白话聊聊Java并发面试问题之volatile到底是什么？](https://mp.weixin.qq.com/s/MIsQv8ZWWBzfB-Qhg3l4lA)
* [大白话聊聊Java并发面试问题之Java 8如何优化CAS性能？](https://mp.weixin.qq.com/s/KFsqsCVgyiiTDXMR-Hu1-Q)
* [大白话聊聊Java并发面试问题之谈谈你对AQS的理解？](https://mp.weixin.qq.com/s/PAn5oTlvVmjMepmCRdBnkQ)
* [大白话聊聊Java并发面试问题之公平锁与非公平锁是啥？](https://mp.weixin.qq.com/s/Os8tT1kBqsq0vteB6KD4hg)
* [大白话聊聊Java并发面试问题之微服务注册中心的读写锁优化](https://mp.weixin.qq.com/s/9SKmHeMu_1lNVEJPlEcpsA)
* [Java并发面试系列文章总结](https://mp.weixin.qq.com/s/vTCrilBQfeycbsFKVFQ-cA)
* [高并发场景下，如何保证生产者投递到消息中间件的消息不丢失？](https://mp.weixin.qq.com/s/r2_o5wa6Gn94NY4ViRnjpA)
* [从团队自研的百万并发中间件系统的内核设计看Java并发性能优化](https://juejin.im/post/5c3f708151882524b333b67f)
* [10倍请求压力来袭，你的系统会被击垮吗？](https://juejin.im/post/5c1baab7e51d455e4b55ff72)
* [大白话聊聊Java并发面试问题之微服务注册中心的读写锁优化](https://juejin.im/post/5c0c8540e51d451dbe4fdd83)
* [从团队自研的百万并发中间件系统的内核设计看Java并发性能优化](https://mp.weixin.qq.com/s/d4qfu2MxESc1YJV4Ud5mnA)

#### 2020汇总Java并发核心篇

[Java从网络编程到高并发学习路线](https://coding.imooc.com/learningpath/route?pathId=11)

[Java如何获取当前线程](https://mp.weixin.qq.com/s?__biz=MjM5MzA1Mzc3Nw==&mid=2247484573&idx=1&sn=6502f30deee7da855ff489e506867fbf&chksm=a69da9a391ea20b50f9e34870b6bf5e01f485a2e5bbc02eb52c0791fbd41a904551ba18f8bd3&scene=21#wechat_redirect)

[从Java到JVM到OS线程睡眠](https://mp.weixin.qq.com/s?__biz=MjM5MzA1Mzc3Nw==&mid=2247484652&idx=1&sn=41833d8efde1ce3511171db8179409c4&chksm=a69da9d291ea20c458496ceddd7d482d3bc79a72f6221daaae671922b6523d91168a84310531&scene=21#wechat_redirect)

[从Java到JVM到OS线程的优先级](https://mp.weixin.qq.com/s?__biz=MjM5MzA1Mzc3Nw==&mid=2247484542&idx=1&sn=3f0321d211c9739337f35f88daaf2c91&chksm=a69da94091ea2056a317aaca1f5d69888baccbe9cb210544a8c9d5f42a39eea0245bcf478a1e&scene=21#wechat_redirect)

[Java线程的CPU时间片](https://mp.weixin.qq.com/s?__biz=MjM5MzA1Mzc3Nw==&mid=2247484453&idx=1&sn=08775b75b5011f5b3f08d9d9e83bf3fe&chksm=a69da91b91ea200dac3149709a27240f6eb510f6b085b044d3e5c0f11cb1a8b8b5d73244d776&scene=21#wechat_redirect)

[ava线程的调度](https://mp.weixin.qq.com/s?__biz=MjM5MzA1Mzc3Nw==&mid=2247484460&idx=1&sn=f6c2dd1dc71e459c849556c7a32f06d2&chksm=a69da91291ea2004c88d2844c4f2d31e27afde39dd594e8d5bd1762a1a5d7eb6c3d5a09c7308&scene=21#wechat_redirect)

[Java线程的状态](https://mp.weixin.qq.com/s?__biz=MjM5MzA1Mzc3Nw==&mid=2247484515&idx=1&sn=bfaac161978ccd9339d1b69f01a1bc0e&chksm=a69da95d91ea204b6419df5ac1be7655587995c14fc0f680aa7159fb33dcac16255a9360263d&scene=21#wechat_redirect)

[乐观的并发策略——基于CAS的自旋](https://mp.weixin.qq.com/s?__biz=MjM5MzA1Mzc3Nw==&mid=2247484445&idx=1&sn=74812d16ffad546e0f376bb9ea8589e8&chksm=a69da92391ea20350e24b83fee91c70122e21b36100000bb74be5d3e455b8920fddc9efcbd82&scene=21#wechat_redirect)

[悲观的并发策略——synchronized互斥锁](https://mp.weixin.qq.com/s?__biz=MjM5MzA1Mzc3Nw==&mid=2247484423&idx=1&sn=91bb67ff4b410f069c8390e5155f3950&chksm=a69da93991ea202f2da7541faf650b686676c96382ecd12827d4c50c184ca9f1eceaccc0c598&scene=21#wechat_redirect)

[从JDK源码角度看并发的原子性如何保证](https://mp.weixin.qq.com/s?__biz=MjM5MzA1Mzc3Nw==&mid=2247483931&idx=1&sn=454f72dd2bda04baf363af702e795834&chksm=a69daf2591ea26330b8060fa3a86e2c28e46ac9f875d2f6e76696330dbebc1e83586859586cb&scene=21#wechat_redirect)

[从 JDK 源码角度看 java 并发线程的中断](https://mp.weixin.qq.com/s?__biz=MjM5MzA1Mzc3Nw==&mid=2247483827&idx=1&sn=49da65b4f48f31418d1d8698764dbba3&chksm=a69dac8d91ea259b2de74d3a99ded3c1952abac4dc0dc3e58da98e3b835fde6054327c1ed694&scene=21#wechat_redirect)

[JDK源码角度看并发锁的优化](https://mp.weixin.qq.com/s?__biz=MjM5MzA1Mzc3Nw==&mid=2247483782&idx=1&sn=88df28cc2eb5a2308ed6a780442a7533&chksm=a69dacb891ea25ae1cd304dbb4725ddda1a577adba8ce7331cf8fec061575d7107ce9d26e4dc&scene=21#wechat_redirect)

[从JDK源码角度看线程的阻塞和唤醒](https://mp.weixin.qq.com/s?__biz=MjM5MzA1Mzc3Nw==&mid=2247483772&idx=1&sn=22a4bc28d71393ac539486f71d492b71&chksm=a69dac4291ea2554eb4f7a45b6a89a11cb8e43c3e10c3c7c757565a51802dd31dfbc64bb1698&scene=21#wechat_redirect)

[从JDK源码角度看并发竞争的超时](https://mp.weixin.qq.com/s?__biz=MjM5MzA1Mzc3Nw==&mid=2247483751&idx=1&sn=0f6b64e617ee652f3975f19fd18d521a&chksm=a69dac5991ea254ff23ab06b3d23ee7141dde8f484595dfde5bbf780d6fa810b0ee1d57b6a49&scene=21#wechat_redirect)

[从 JDK 源码角度看 Java 并发的公平性](https://mp.weixin.qq.com/s?__biz=MjM5MzA1Mzc3Nw==&mid=2247483743&idx=1&sn=4dda60569cf2e58e38bef39ae9741d2d&chksm=a69dac6191ea25774851339a6d36443a67c1f610f33ce8513fa65b5f11dbfa66a6734ced5dd7&scene=21#wechat_redirect)

[volatile足以保证数据同步吗](https://mp.weixin.qq.com/s?__biz=MjM5MzA1Mzc3Nw==&mid=2247483960&idx=1&sn=bd1fa1639ac7da0f45667db7cbfb12d8&chksm=a69daf0691ea2610c7c7d85694ed046928de7f7e98ae6d54fa272b235b44c0e953be180cef5a&scene=21#wechat_redirect)

[Java 内存模型](https://mp.weixin.qq.com/s?__biz=MjM5MzA1Mzc3Nw==&mid=2247484074&idx=1&sn=8a98892077ff90582865f5542aba4253&chksm=a69daf9491ea268205b816a4ea5ef5e4d925a1564fbcb8894d10c086daf61534f641ad489979&scene=21#wechat_redirect)

[JDK并发AQS系列(一)](https://mp.weixin.qq.com/s?__biz=MjM5MzA1Mzc3Nw==&mid=2247484695&idx=1&sn=06a69ff81ddd44b8051a6771bf471a57&chksm=a69da82991ea213fab0c84ddfbd7d9ae49c84c867ee4b13ac74105c69a83461d91bb463d7191&scene=21#wechat_redirect)

[JDK并发AQS系列(二)](https://mp.weixin.qq.com/s?__biz=MjM5MzA1Mzc3Nw==&mid=2247484703&idx=1&sn=b45477953fcd67e1bd4a91e3e9e21a77&chksm=a69da82191ea2137b215892608638a68efd40dbe47acd2a60882ab45085b75255c67e651d48a&scene=21#wechat_redirect)

[JDK并发AQS系列(三)](https://mp.weixin.qq.com/s?__biz=MjM5MzA1Mzc3Nw==&mid=2247484715&idx=1&sn=05f593d428946d6a514e0ddaf447d48f&chksm=a69da81591ea2103eb9792cedde0458ee94c4c229ee592536608bfafbae5f14639ec7643df52&scene=21#wechat_redirect)

[JDK并发AQS系列(四)](https://mp.weixin.qq.com/s?__biz=MjM5MzA1Mzc3Nw==&mid=2247484723&idx=1&sn=c8eb8fad43662f010b599b23c1701874&chksm=a69da80d91ea211b2865beb79d6d0671e6850589dd5b500aada656a1d8c64c5708eeab73e5bc&scene=21#wechat_redirect)

[JDK并发AQS系列(五)](https://mp.weixin.qq.com/s?__biz=MjM5MzA1Mzc3Nw==&mid=2247484731&idx=1&sn=ab2953011ff87bb863d1aec8fdd176dc&chksm=a69da80591ea21133cc8a3057b73c0bda4cc833c8a7cb16ee6a93cd2db0fcad167e546244aba&scene=21#wechat_redirect)

[Java并发编程：深入剖析ThreadLocal](https://www.cnblogs.com/dolphin0520/p/3920407.html)
[聊聊并发系列文章](http://ifeve.com/talk-concurrency/)

[从Java视角理解系统结构(一)CPU上下文切换](http://ifeve.com/java-context-switch/)

[从Java视角理解系统结构（二）CPU缓存](http://ifeve.com/from-javaeye-cpu-cache/)

[从Java视角理解系统结构（三）伪共享](http://ifeve.com/from-javaeye-false-sharing/)

[Java 7 并发编程指南中文版](http://ifeve.com/java-7-concurrency-cookbook/)

[Java并发性和多线程介绍目录](http://ifeve.com/java-concurrency-thread-directory/)

[CPU缓存刷新的误解](http://ifeve.com/cpu-cache-flushing-fallacy-cn/)

[伪共享(False Sharing)](http://ifeve.com/falsesharing/)

[Java 7与伪共享的新仇旧恨](http://ifeve.com/false-shareing-java-7-cn/)

[内存访问模型的重要性](http://ifeve.com/memory-access-patterns-are-important-2/)

[Memory Barriers/Fences](http://ifeve.com/mechanical-sympathy/)

[合并写(write combining)](http://ifeve.com/writecombining/)

[内存屏障](http://ifeve.com/memory-barriers-or-fences/)

[Java并发结构](http://ifeve.com/java-concurrency-constructs/)

[任务取消(Cancellation)](http://ifeve.com/cancellation/)

[JUC同步框架 ](http://ifeve.com/aqs/)

[并发数据结构](http://ifeve.com/concurrent-data-structures/)

[J.U.C并发框架](http://ifeve.com/j-u-c-framework/)

[并发框架Disruptor](http://ifeve.com/disruptor/)
[]()

[Oracle官方并发教程](http://ifeve.com/oracle-java-concurrency-tutorial/)

[聊聊并发（一）深入分析Volatile的实现原理](http://ifeve.com/volatile/)

[聊聊并发（二）Java SE1.6中的Synchronized](http://ifeve.com/java-synchronized/)

[聊聊并发（三）Java线程池的分析和使用](http://ifeve.com/java-threadpool/)

[聊聊并发（四）深入分析ConcurrentHashMap](http://ifeve.com/concurrenthashmap/)

[聊聊并发（五）原子操作的实现原理](http://ifeve.com/atomic-operation/)

[聊聊并发（六）ConcurrentLinkedQueue的实现原理分析](http://ifeve.com/concurrentlinkedqueue/)

[聊聊并发（七）——Java中的阻塞队列](http://ifeve.com/java-blocking-queue/)

[聊聊并发（八）——Fork/Join框架介绍](http://ifeve.com/talk-concurrency-forkjoin/)

[聊聊并发（九）Java中的CopyOnWrite容器](http://ifeve.com/java-copy-on-write/)

[聊聊并发（十）生产者消费者模式](http://ifeve.com/producers-and-consumers-mode/)

[非阻塞同步算法实战（一）](http://ifeve.com/no-blicking-do-1/)

[非阻塞同步算法实战（二）－BoundlessCyclicBarrier](http://ifeve.com/boundlesscyclicbarrier/)

[非阻塞同步算法实战（三）－LatestResultsProvider](http://ifeve.com/latestresultsprovider/)

[TimeUnit是java.util.concurrent包下面的一个类，表示给定单元粒度的时间段](https://www.cnblogs.com/zhaoyanjun/p/5486726.html)

[阿里一道Java并发面试题 (详细分析篇)](https://blog.csdn.net/lirenzuo/article/details/90050894)
