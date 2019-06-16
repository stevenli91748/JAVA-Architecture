
# 目录

* 栈内存：用于存储局部变量，当数据使用完，所占空间会自动释放。 
* 堆内存：数组和对象，通过new建立的实例都存放在堆内存中。 
* 方法区：静态成员、构造函数、常量池、线程池 
* 本地方法区：window系统占用 
* 寄存器：


# 有用的参考
  * [JAVA的内存模型及结构](http://ifeve.com/under-the-hood-runtime-data-areas-javas-memory-model/)
  * [高级面试:JAVA内存模型](https://mp.weixin.qq.com/s?__biz=MzA3MDY0NTMxOQ==&mid=2247485737&idx=1&sn=14212fb6edcdb88446dea6737b864196&chksm=9f38ea01a84f63176f61e11ea99650d26233bc776b5f1f5ff6f7b70ddd763291b6b7d5af9d17&scene=21#wechat_redirect)
  * [JVM内存结构 VS Java内存模型 VS Java对象模型](http://www.hollischuang.com/archives/2509)
  * [再有人问你Java内存模型是什么，就把这篇文章发给他](http://www.hollischuang.com/archives/2550)
  * [内存模型是怎么解决缓存一致性问题的？](https://blog.csdn.net/hollis_chuang/article/details/82991962)
  * [JVM内存结构 VS Java内存模型 VS Java对象模型](https://blog.csdn.net/hollis_chuang/article/details/80839410)
  * [异常、堆内存溢出、OOM的几种情况](https://blog.csdn.net/sinat_29912455/article/details/51125748)
  * [如何用弱引用实现内存泄漏检测](https://mp.weixin.qq.com/s?__biz=MjM5MzA1Mzc3Nw==&mid=2247483863&idx=1&sn=65d2df0ff4a250f7b5360fe31cb1c78f&chksm=a69dace991ea25ff33b3dc6d0156c2eea3ff1a4cb7935ad796fe72a04adcf1d341f25e1b612f&scene=21#wechat_redirect)
  * [全面理解Java内存模型(JMM)及Java内存区域](https://blog.csdn.net/qq_32625839/article/details/81510800)
  * [java中JVM和JMM之间的区别](https://blog.csdn.net/zhaomengszu/article/details/80270696)
  * [全面理解Java内存模型(JMM)及volatile关键字](https://blog.csdn.net/javazejian/article/details/72772461)
  * [深入理解 Java 内存模型](https://blog.csdn.net/weixin_36795183/article/details/79420771)
  * [从Java内存模型理解synchronized、volatile和final关键字](https://blog.csdn.net/fuzhongmin05/article/details/60464835)
  * [全面理解Java内存模型(JMM)及volatile关键字](https://blog.csdn.net/javazejian/article/details/72772461)
  * [Java对象内存布局](https://juejin.im/post/5c7bc9aa6fb9a049af6dfc45)
  * [深入理解 Java 内存模型](https://zhuanlan.zhihu.com/p/34147141?utm_source=wechat_session&utm_medium=social&utm_oi=991812777480134656)
  * [终于有人把Java内存模型说清楚了](https://zhuanlan.zhihu.com/p/61424782)
  * [案例：隐秘而低调的内存泄露（OOM）](https://blog.51cto.com/xqtesting/2402372)
  * [内存屏障和 volatile 语义](http://www.importnew.com/29860.html)
  * [内存屏障与 JVM 并发](https://www.infoq.cn/article/memory_barriers_jvm_concurrency/)
  * [如何保存/恢复Java应用程序核心内存数据现场](http://ifeve.com/如何保存-恢复java应用程序核心内存数据现场？/)
  
  * [深入理解Java内存模型（一）——基础](http://ifeve.com/java-memory-model-1/)
  * [深入理解Java内存模型（二）——重排序](http://ifeve.com/java-memory-model-2/)
  * [深入理解Java内存模型（三）——顺序一致性](http://ifeve.com/java-memory-model-3/)
  * [深入理解Java内存模型（四）——volatile](http://ifeve.com/java-memory-model-4/)
  * [深入理解Java内存模型（五）——锁](http://ifeve.com/java-memory-model-5/)
  * [深入理解Java内存模型（六）——final](http://ifeve.com/java-memory-model/)
  * [深入理解Java内存模型（七）——总结](http://ifeve.com/java-memory-model-7/)
  * [Java内存模型FAQ系列](http://ifeve.com/jmm-faq/)
  
  * [Java内存模型Cookbook-前言](http://ifeve.com/jmm-cookbook/)
  * [JMM Cookbook(一)指令重排](http://ifeve.com/jmm-cookbook-reorderings/)
  * [Java内存模型Cookbook（二）内存屏障](http://ifeve.com/jmm-cookbook-mb/)
  * [Java内存模型Cookbook(三)多处理器](http://ifeve.com/jmm-cookbook-mps/)
  * [Java内存模型Cookbook（四）指南(Recipes)](http://ifeve.com/cookbook-recipes/)
  
  * [同步与Java内存模型(一)序言](http://ifeve.com/syn-jmm-pre/)
  * [同步和Java内存模型 (二)原子性](http://ifeve.com/syn-jmm-atomicity/)
  * [同步和Java内存模型 (三)可见性](http://ifeve.com/syn-jmm-visibility/)
  * [同步和Java内存模型(四)有序性](http://ifeve.com/syn-jmm-ordering/)
  * [同步和Java内存模型（五）Volatile](http://ifeve.com/syn-jmm-volatile/)
  
  * [CPU缓存刷新的误解](http://ifeve.com/cpu-cache-flushing-fallacy-cn/)
  * [伪共享(False Sharing)](http://ifeve.com/falsesharing/)
  * [Java 7与伪共享的新仇旧恨](http://ifeve.com/false-shareing-java-7-cn/)
  * [内存访问模型的重要性](http://ifeve.com/memory-access-patterns-are-important-2/)
  * [Memory Barriers/Fences](http://ifeve.com/mechanical-sympathy/)
  * [合并写(write combining)](http://ifeve.com/writecombining/)
  * [内存屏障](http://ifeve.com/memory-barriers-or-fences/)
  
