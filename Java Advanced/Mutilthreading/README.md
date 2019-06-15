
# [多线程面试](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20Advanced/Mutilthreading/interview.md)

# 多线程目录

* 操作系统
  * 进程和线程
  * 存储和寻址
  * 进程间通信
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
* 线程组
* 处理线程未捕获的异常
* ThreadLocal类
* Concurrent并发包
  * 用于线程同步的Lock外部锁
  * 用于线程通信的Condition条件接口
  * 支持异步计算的Callable接口和Future接口
  * 通过线程池来高效管理多个线程


# 内容









# 有用的参考

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
  
  
  
  
