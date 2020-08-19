
# [多线程面试](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20Advanced/Mutilthreading/interview.md)

# 多线程目录

# [还搞不定Java多线程和并发编程面试题？你可能需要这一份书单！](https://mp.weixin.qq.com/s?__biz=MzUyOTk5NDQwOA==&mid=2247490401&idx=2&sn=8a2a896e11c0eba74e13a5781fd75fa8&chksm=fa59dca6cd2e55b0c4d93e23365150475b9ab17c3e70fd01aae59d86fca00a86896e5df70cc0&scene=21#wechat_redirect)


<a href="https://ibb.co/8ss6Hc3"><img src="https://i.ibb.co/pwwJSyD/thread2019050823383080.png" alt="thread2019050823383080" border="0"></a>


[完美解决工作中遇到的Java多线程问题，以后遇到也不慌](https://www.bilibili.com/video/BV1is411j7n4)|[分布式场景中确保线程安全的解决方案，Redis实现分布式锁](https://www.bilibili.com/video/BV1Ks411j7sa)|
---|---|


* 操作系统
  * 进程和线程
  * 存储和寻址
  * 进程间通信
    * 管道
    * 消息队列
    * 信号量
    * 共享存储
  
* Java线程的运行机制
* 线程的创建和启动
  * 展Java.lang.Thread类
  * 实现Runnable接口
* 线程的状态转换
  * 新建状态
  * 就绪状态
  * 运行状态
  * 阻塞状态
  * 死亡状态
* 线程调度
  * 调整各个线程的优先级
  * 线程睡眠 Thread.sleep()方法
  * 线程让步 Thread.yield()方法
  * 等待其他线程结束 join()方法
* 获得当前线程对象的引用
* 后台线程
* 定时器
* 线程的同步
  * 同步代码块
  * 线程同步的特征
  * 同步与并发
  * 线程安全的类
  * 释放对象的锁
  * 死锁
* 线程通信
* 中断阻塞
* 线程控制
  * 被废弃的suspend()和resume()方法
  * 被废弃的stop()方法
  * 以编程的方式控制线程
* [线程池---如何优雅的使用和理解线程池](https://crossoverjie.top/2018/07/29/java-senior/ThreadPool/)
* 处理线程未捕获的异常
* ThreadLocal类
* Concurrent并发包
  * 用于线程同步的Lock外部锁
  * 用于线程通信的Condition条件接口
  * 支持异步计算的Callable接口和Future接口
  * 通过线程池来高效管理多个线程


# 视频
 * [Java工程师 高并发与多线程网络编程 （完）](https://www.bilibili.com/video/BV1L4411i7eW/?spm_id_from=333.788.videocard.19)
 * [阿里P8面试Java多线程底层原理精讲](https://www.bilibili.com/video/BV1gJ411G7ex?p=2)
 * [2019年6月最新Java多线程高级编程——电商项目实战](https://www.bilibili.com/video/av56465131/?spm_id_from=333.788.videocard.19)
 * [Java工程师 高并发与多线程网络编程 （完）](https://www.bilibili.com/video/av51503134/?spm_id_from=333.788.videocard.9)
 * [Java线程池原理讲解全集 Java高级面试必备](https://www.bilibili.com/video/av66055587/?spm_id_from=333.788.videocard.13)
 * [B站超牛老师带你找到挑战年薪50W,解密高性能线程池原理](https://www.bilibili.com/video/av66798714/?spm_id_from=333.788.videocard.11)
 * [Java主流锁全部详解面试必备](https://www.bilibili.com/video/av70757405/?spm_id_from=333.788.videocard.7)
 * [马士兵老师java多线程高并发编程](https://www.bilibili.com/video/av33688545/?spm_id_from=333.788.videocard.10) 
 * [百万年薪架构师带你了解--Java内存模型JMM与AQS同步器深入剖析](https://www.bilibili.com/video/av69537976/?spm_id_from=333.788.videocard.5)
 * [BATJ面试必问之单机锁到分布式锁设计构思与实战](https://www.bilibili.com/video/av59014838?from=search&seid=8708809625625523554)
 * [《多线程与高并发》，帮助你理解多线程在CPU层级的实现，以及这些实现如何一层一层的映射到那些上亿用户](https://www.bilibili.com/video/BV1R7411C7rf/?spm_id_from=333.788.videocard.3)
# 参考书

《Java高并发编程详解-多线程与架构设计》_汪文君



# 有用的参考
  * [背熟这99道Java多线程面试题,和面试官扯皮就没问题了](https://www.jianshu.com/p/3e88a5fe75f0)
  * [Java Concurrency and Multithreading Tutorial](http://tutorials.jenkov.com/java-concurrency/index.html)
  * [java.util.concurrent - Java Concurrency Utilities](http://tutorials.jenkov.com/java-util-concurrent/index.html)
  * [多线程通信的三大法器，你真的会用吗？](https://mp.weixin.qq.com/s?__biz=MzI3ODcxMzQzMw==&mid=2247487757&idx=1&sn=3b5cd693431458064612edef399491f7&chksm=eb53943bdc241d2d1520213a0bac31e457b4f888f3fe4f3034c019b758a179202d8a602f2e5c&scene=21#wechat_redirect)
  * [多线程内容聚合---非常强大](https://mp.weixin.qq.com/s?__biz=MzI4Njc5NjM1NQ==&mid=2247488811&idx=6&sn=a769526cf5c4dfc5b0f8e5a40f984cff&chksm=ebd62a07dca1a3116cc6c45ceb7a81d47293b9811c8df97b5ca363c6370ffccfd913bab3599f&scene=21)
  * [详解Java面试官最爱问的volatile关键字](https://www.jb51.net/article/133313.htm)
  * [volatile足以保证数据同步吗](https://mp.weixin.qq.com/s?__biz=MjM5MzA1Mzc3Nw==&mid=2247483960&idx=1&sn=bd1fa1639ac7da0f45667db7cbfb12d8&chksm=a69daf0691ea2610c7c7d85694ed046928de7f7e98ae6d54fa272b235b44c0e953be180cef5a&scene=21#wechat_redirect)
  * [java中volatile不能保证线程安全(实例讲解)](https://www.jb51.net/article/122843.htm)
  * [Java线程之线程同步synchronized和volatile详解](https://www.jb51.net/article/127973.htm)
  * [深入分析java并发编程中volatile的实现原理](https://www.jb51.net/article/128403.htm)
  * [Java中线程池，你真的会用吗？](https://blog.csdn.net/hollis_chuang/article/details/83743723)
  * [深入源码分析Java线程池的实现原理](https://mp.weixin.qq.com/s/-89-CcDnSLBYy3THmcLEdQ)
  * [简看线程池原理](https://mp.weixin.qq.com/s?__biz=MjM5MzA1Mzc3Nw==&mid=2247483851&idx=1&sn=d4da59dfda97ffaa2915ea87c4748e87&chksm=a69dacf591ea25e32781923cc3a41238ffb3a74dc773ac97e105e78bd231f9630650c3c4c446&scene=21#wechat_redirect)
  * [谈谈Serializable、transient、volatile、final在多线程编程中的应用](https://blog.csdn.net/shen_gang/article/details/18942431)
  * [Java 200+ 面试题补充 ThreadLocal 模块](https://juejin.im/post/5c805cb9f265da2d9e177f6d)
  * [BAT一线互联网公司多线程面试59道题和答案](https://zhuanlan.zhihu.com/p/59376173?utm_source=wechat_session&utm_medium=social&utm_oi=991812777480134656)
  
  * [分门别类总结Java中的各种锁，让你彻底记住](https://zhuanlan.zhihu.com/p/61778635?utm_source=wechat_session&utm_medium=social&utm_oi=991812777480134656)
  * [java多线程之——interrupt深入研究](https://www.cnblogs.com/carmanloneliness/p/3516405.html)
  * [关于线程同步的7种方式](https://www.cnblogs.com/XHJT/p/3897440.html)
  
  * [深入分析Synchronized](https://blog.csdn.net/shandian000/article/details/54927876) 
  * [Java并发编程之——Synchronized](https://www.cnblogs.com/paddix/p/5367116.html)
  * [java两种线程同步方式的区别——Synchronized和ReentrantLock](https://blog.csdn.net/chenchaofuck1/article/details/51045134)
  * [Java 指定线程执行顺序（三种方式）](https://blog.csdn.net/difffate/article/details/63684290)
  * [java指定线程执行的顺序](https://blog.csdn.net/BeauXie/article/details/53018570)
  * [Java常用锁机制简介](https://www.cnblogs.com/hanganglin/p/3577096.html)
  * [Java并发编程：Lock](https://www.cnblogs.com/dolphin0520/p/3923167.html)
  * [ReadWriteLock场景应用](https://www.cnblogs.com/liang1101/p/6475555.html?utm_source=itdadao&utm_medium=referral)  
  * [分布式锁的几种实现方式](http://www.hollischuang.com/archives/1716)
  * [分布式锁的3种方式](https://www.cnblogs.com/rwxwsblog/p/6046034.html)
  * [深入理解线程通信](http://ifeve.com/深入理解线程通信/)
  * [线程池运用实例——一次错误的多线程程序设计以及修复过程](http://ifeve.com/threadpool-example/)
  * [Java Fork Join 框架](http://ifeve.com/a-java-fork-join-framework/)
  * [同步和Java内存模型](http://ifeve.com/syn-jmm/)
  * [实例详解Java中ThreadLocal内存泄露](https://www.jb51.net/article/90450.htm)
  * [15个顶级Java多线程面试题及回答 / Java程序员面试中的多线程问题](https://blog.csdn.net/liangxw1/article/details/51187162)
  * [Java并发：创建线程的两种方法：继承Thread类和实现Runnable接口（一）](https://blog.csdn.net/jingzi123456789/article/details/69830907)
  * [守护线程与非守护线程](https://blog.csdn.net/lc1010078424/article/details/79613348)
  * [深入JVM锁机制1-synchronized](https://blog.csdn.net/chen77716/article/details/6618779)
  * [深入JVM锁机制2-Lock](https://blog.csdn.net/chen77716/article/details/6641477)
  * [Java中的ReentrantLock和synchronized两种锁定机制的对比](https://blog.csdn.net/fw0124/article/details/6672522)
  * [JVM 中synchronized的底层实现原理解析](https://blog.csdn.net/HinstenyHisoka/article/details/80864378)
  * [Java 多线程全局锁与对象锁](https://blog.csdn.net/weixin_40739833/article/details/80293480)
  * [Synchronize实现原理（很难）](https://blog.csdn.net/zbuger/article/details/51030772)
  * [Java synchronized锁对象和锁非静态成员变量的实验](https://blog.csdn.net/wbybyb/article/details/83989121)
  * [Java 中 synchronized的用法详解(四种用法)](https://www.jb51.net/article/74566.htm)
  * [透彻理解Java中Synchronized（对象锁）和Static Synchronized（类锁）的区别](https://www.jb51.net/article/140175.htm)
  * [java中synchronized（同步代码块和同步方法）详解及区别](https://www.jb51.net/article/106941.htm)
  * [Java类锁、对象锁、私有锁冲突测试](https://www.jb51.net/article/56440.htm)
  * [详解Java中synchronized关键字的死锁和内存占用问题](https://www.jb51.net/article/86666.htm)
  * [java中volatile、synchronized和lock解析](https://blog.csdn.net/ztchun/article/details/60778950)
  * [Java多线程干货](https://blog.csdn.net/qq_35808136/article/details/93847655)
  * [java并发编程-Executor框架](https://www.iteye.com/topic/366591)
  * [线程池的内部原理](https://blog.csdn.net/heihaozi/article/details/102882698)
  * [Java中常用的四种线程池](https://blog.csdn.net/heihaozi/article/details/102752638)
  * [java向多线程中传递参数的三种方法详细介绍](https://www.jb51.net/article/31981.htm)
