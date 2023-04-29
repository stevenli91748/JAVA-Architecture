# 1.栈空间（stack）

**连续的存储空间**，遵循后进先出的原则，存放基本类型的变量数据和对象的引用，但对象本身不存放在栈中，而是存放在堆（new 出来的对象）或者常量池中（字符串常量对象存放在常量池中。）; 当在一段代码块定义一个变量时，Java在栈中为这个变量分配内存空间，当该变量退出其作用域Ⅰ后，Java会自动释放掉为该变量所分配的内存空间，该内存空间可以立即被另作他用。

注:Ⅰ:变量的作用域:从变量定义的位置开始，到该变量所在的那对大括号结束

Ⅱ:变量周期性: 从变量定义的位置开始就在内存中活了；到达它所在的作用域的时候就在内存中消失了；

# 2.堆空间（heap）

**不连续的空间**，用于存放new出的对象，或者说是类的实例;当引用变量是普通的变量，定义时在栈中分配，引用变量在程序运行到其作用域之外后被释放。而数组和对象本身在堆中分配，即使程序运行到使用 new 产生数组或者对象的语句所在的代码块之外，数组和对象本身占据的内存不会被释放，数组和对象在没有引用变量指向它的时候，才变为垃圾，不能在被使用，但仍然占据内存空间不放，在随后的一个不确定的时间被垃圾回收器收走（释放掉）。这也是 Java 比较占内存的原因。实际上，**栈中的变量指向堆内存中的变量，这就是Java中的指针！**

# 3.堆与栈

堆是由垃圾回收来负责的，堆的优势是可以动态地分配内存 大小，生存期也不必事先告诉编译器，因为它是在运行时动态分配内存的，Java的垃圾收集器会自动收走这些不再使用的数据。但缺点是，由于要在运行时动态 分配内存，存取速度较慢。

栈的优势是，存取速度比堆要快，仅次于寄存器，栈数据可以共享(int a = 3再int b = 3此时内存中值存在一个3,a,b两个引用同时指向同一个3)。但缺点是，存在栈中的数据大小与生存期必须是确定的，缺乏灵活性。对于栈和常量池中的对象Ⅰ可以共享，对于堆中的对象不可以共享。栈中的数据大小和生命周期是可以确定的。堆中的对象的由垃圾回收器负责回收，因此大小和生命周期不需要确定 ，具有很大的灵活性。

注:Ⅰ:用new来生成的对象都是放在堆中的，直接定义的局部变量都是放在栈中的，全局和静态的对象是放在数据段的静态存储区，例如： Class People；People p;//栈上分配内存People* pPeople；pPeople = new People;//堆上分配内存

对于字符串：其对象的引用都是存储在栈中的，如果是 编译期已经创建好(直接用双引号定义的)的就存储在常量池中，如果是运行期（new出来的）才确定的就存储在堆中 。对于equals相等的字符串，在常量池中永远只有一份，在堆中有多份。

# 4.方法区（method）

方法区在堆空间内，用于存放 ①类的代码信息；②静态变量和方法；③常量池（字符串常量和基本类型常量（public static final），具有共享机制）;常量池指的是在编译期被确定，并被保存在已编译的.class文件中的一些数据。除了包含代码中所定义的各种基本类型（如int、long等等）和对象型（如String及数组）的常量值(final)还包含一些以文本形式出现的符号引用，比如：类和接口的全限定名；字段的名称和描述符；方法和名称和描述符。
Java中除了基本数据类型，其他的均是引用类型，包括类、数组等等。

#  [JVM面试](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20Advanced/JVM/JVM%20interview.md)

# 在线书籍

* [深入理解Java虚拟机：JVM高级特性与最佳实践（第2版） 周志明](https://weread.qq.com/web/bookReview/list?bookId=9b832f305933f09b86bd2a9)
* [深入理解Java虚拟机：JVM高级特性与最佳实践（第3版） 周志明](https://weread.qq.com/web/bookDetail/cf1320d071a1a78ecf19254) 
* [Java虚拟机字节码： 从入门到实战](https://weread.qq.com/web/reader/8d7327107217731e8d7ed1e)
* [垃圾回收的算法与实现](https://weread.qq.com/web/reader/f0232240723982d3f02c0fd)
* [深入解析Java编译器：源码解析和实例讲解](https://weread.qq.com/web/reader/85832b5071a486bc858ab8a)
* [新一代垃圾回收器ZGC设计与实现](https://weread.qq.com/web/reader/7e5327d071916d647e51559)
---

#  [搞定JVM基本原理和面试题，看看这几本书妥妥够了！](https://mp.weixin.qq.com/s?__biz=MzUyOTk5NDQwOA==&mid=2247490561&idx=2&sn=1cb850b51b74531eb058368f78f91f42&chksm=fa59dbc6cd2e52d0da78caa5302204ada8662c2a8796818d177a5adb4aca408f30b913a03818&scene=21#wechat_redirect)


# JVM 视频
* [2022年 java虚拟机jvm性能调优实战+面试](https://www.bilibili.com/video/BV1854y1H7nR?spm_id_from=333.999.0.0&vd_source=be3ac4894f8bf7883ae92030f6f7be46)
* [程序员必备：30个JVM调优实战详细案例，可以直接写在简历上，附22年美团JVM高频面试题。](https://www.bilibili.com/video/BV1fi4y1U76z/?spm_id_from=333.788.recommend_more_video.2&vd_source=be3ac4894f8bf7883ae92030f6f7be46)
* [尚硅谷宋红康JVM全套教程（详解java虚拟机）](https://www.bilibili.com/video/BV1PJ411n7xZ?spm_id_from=333.337.search-card.all.click)
* [JVM上篇：内存与垃圾回收篇概述](https://www.youtube.com/watch?v=KbmZRzl16D4&list=PLmOn9nNkQxJHNSznBikUX5sMv_XwDUYz4)
* [JVM中篇：字节码与类的加载篇](https://www.youtube.com/watch?v=ECMCAe8OJB4&list=PLmOn9nNkQxJFuWhUYNllO-T871GYgKOLU)
* [JVM下篇：性能监控与调优篇](https://www.youtube.com/watch?v=VnRh057KVyk&list=PLmOn9nNkQxJFESqNAbyTDTHwra97BfZwL)
* []()



目录
---


<a href="https://ibb.co/PtLXKtV"><img src="https://i.ibb.co/CWcCgWS/jvm-overview.png" alt="jvm-overview" border="0"></a>

<a href="https://ibb.co/xXQv3ps"><img src="https://i.ibb.co/pbpDntP/java-jvm-overview.png" alt="java-jvm-overview" border="0"></a>

[精尽 Java【虚拟机】学习指南](http://svip.iocoder.cn/Java/VirtualMachine/tutorials/)|[Java 全栈知识体系---JVM相关知识体系详解](https://www.pdai.tech/md/java/jvm/java-jvm-x-overview.html)|
---|---|

[Arthas 是Alibaba开源的Java诊断工具，动态跟踪Java代码；实时监控JVM状态，可以在不中断程序执行的情况下轻松完成JVM相关问题排查工作](https://mp.weixin.qq.com/s/JFa3jc9LIn9ax6eggM34iQ)|
---|

[GraalVM下一代的JVM](https://cloud.tencent.com/developer/article/1838811)|[基本功 | Java即时编译器原理解析及实践 ](https://tech.meituan.com/2020/10/22/java-jit-practice-in-meituan.html)|
---|---|


JVM实验例子|[常见JVM内存错误及解决方案](https://www.kancloud.cn/haijun0314/fengxue/2056242)|[Java 虚拟机底层原理知识总结](https://github.com/doocs/jvm)|
---|---|---|

[用Java实现一个JVM虚拟机](https://bugstack.cn/itstack-demo-jvm/itstack-demo-jvm.html)|
---|

---

# 如何啃下JVM这座大山

* [第一篇： 三天刷完《深入JVM虚拟机 第三版》---JVM的开门篇](https://mp.weixin.qq.com/s/Rfn-utucyeuny1zldz3dzQ)
* [第二篇:  还在学JVM？我都帮你总结好了（附脑图）---理论的重点部分，关于JVM的运行时数据区的各个部分是干嘛的，以及重点JVM堆的分代理论和GC的基本回收算法](https://mp.weixin.qq.com/s/eYrP1dReLxx8YeDAUX09zQ)
* [第三篇:  你是不是垃圾，心里没点数吗？---调优的重中之重，主要聊的就是GC， 如何判断一个对象是否存活、以及常见的GC的种类，常见GC年轻代和老年代的搭配，各种GC的原理和特点、以及适用的场景](https://mp.weixin.qq.com/s/U7Dc5oLCq_rFKwBf5nAKqQ)
* [第四篇: JVM性能调优实战篇（一）---JVM调优实战讲解的工具篇，包括线上的Arthas工具、GUI工具（Visual VM）、内存分析工具（mat）以及linux的原始命令jps、jstack、jstat、jmap、jhat等命令的讲解和使用](https://mp.weixin.qq.com/s/0DanB3AeZBIiIQNGEYyTvQ)
* [第五篇: JVM性能调优实战篇（二）---实战调优的场景、案例、经典排查OOM、磁盘不足排查、CPU飙高的排查、死锁的排查、调优的目的和理论已经调优案例场景解析](https://mp.weixin.qq.com/s/fvLNaTFCDGuW16W__xQ9Tw)
* [第六篇: 类加载子系统---](https://mp.weixin.qq.com/s/FQhMAeMxSKUGah6onDpXww)

* [JVM 内存布局，写得太好了 ](https://mp.weixin.qq.com/s/R7z-4TqmpLfi1Nx6KErZxA)

---

# [0. 初识JVM](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20Advanced/JVM/%E5%88%9D%E8%AF%86JVM.md)

  * java虚拟机的规范
  * 虚拟机的启动
  
# [1. 程序编译与代码优化](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20Advanced/JVM/%E7%A8%8B%E5%BA%8F%E7%BC%96%E8%AF%91%E4%B8%8E%E4%BB%A3%E7%A0%81%E4%BC%98%E5%8C%96.md)
  
  * 编译器
   * 编译器原理
   * 编译器的基本结构
   * 编译器的分类---Java 中即时编译器(JIT)在运行期的优化过程对于程序运行来说更重要， 而前端编译器(ECJ)在编译期的优化过程对于程序编码来说关系更加密切.
     * 前端编译器(ECJ)---早期优化（编译期），把.java 文件转变成.class 文件的过程
     * 后端运行期编译器(JIT)---晚期优化（运行期），把字节码转变成机器码的过程
     * 静态提前编译器(AOT)---直接把.java文件编译成本地机器代码的过程
  * 解析器 

# [2. 虚拟机执行子系统](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20Advanced/JVM/%E8%99%9A%E6%8B%9F%E6%9C%BA%E6%89%A7%E8%A1%8C%E5%AD%90%E7%B3%BB%E7%BB%9F.md)

##  [虚拟机字节码执行引擎](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20Advanced/JVM/%E8%99%9A%E6%8B%9F%E6%9C%BA%E5%AD%97%E8%8A%82%E7%A0%81%E6%89%A7%E8%A1%8C%E5%BC%95%E6%93%8E.md)

 * [2.1 类文件结构(ClassFile)](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20Advanced/JVM/%E8%99%9A%E6%8B%9F%E6%9C%BA%E6%89%A7%E8%A1%8C%E5%AD%90%E7%B3%BB%E7%BB%9F.md#类文件结构(ClassFile))
   * [数据类型](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20Advanced/JVM/%E8%99%9A%E6%8B%9F%E6%9C%BA%E6%89%A7%E8%A1%8C%E5%AD%90%E7%B3%BB%E7%BB%9F.md#数据类型)
   * [类文件格式](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20Advanced/JVM/%E8%99%9A%E6%8B%9F%E6%9C%BA%E6%89%A7%E8%A1%8C%E5%AD%90%E7%B3%BB%E7%BB%9F.md#类文件格式)
   * [ASM字节码编程](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20Advanced/JVM/ASM%E5%AD%97%E8%8A%82%E7%A0%81%E7%BC%96%E7%A8%8B/README.md) 
 * [2.2 虚拟机类的加载机制(ClassLoader)](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20Advanced/JVM/%E8%99%9A%E6%8B%9F%E6%9C%BA%E6%89%A7%E8%A1%8C%E5%AD%90%E7%B3%BB%E7%BB%9F.md#虚拟机类的加载机制(ClassLoader))
   * [类的加载时机](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20Advanced/JVM/%E8%99%9A%E6%8B%9F%E6%9C%BA%E6%89%A7%E8%A1%8C%E5%AD%90%E7%B3%BB%E7%BB%9F.md#类的加载时机)
   * [加载，链接与初始化](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20Advanced/JVM/%E8%99%9A%E6%8B%9F%E6%9C%BA%E6%89%A7%E8%A1%8C%E5%AD%90%E7%B3%BB%E7%BB%9F.md#虚拟机类的加载机制(ClassLoader))
     * [加载](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20Advanced/JVM/%E8%99%9A%E6%8B%9F%E6%9C%BA%E6%89%A7%E8%A1%8C%E5%AD%90%E7%B3%BB%E7%BB%9F.md#加载)
     * [链接](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20Advanced/JVM/%E8%99%9A%E6%8B%9F%E6%9C%BA%E6%89%A7%E8%A1%8C%E5%AD%90%E7%B3%BB%E7%BB%9F.md#链接)
     * [初始化](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20Advanced/JVM/%E8%99%9A%E6%8B%9F%E6%9C%BA%E6%89%A7%E8%A1%8C%E5%AD%90%E7%B3%BB%E7%BB%9F.md#初始化)
   * [类加载器](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20Advanced/JVM/%E8%99%9A%E6%8B%9F%E6%9C%BA%E6%89%A7%E8%A1%8C%E5%AD%90%E7%B3%BB%E7%BB%9F.md#类加载器)
   * [类在内存中存放的地方---方法区](https://www.cnblogs.com/cac2020/p/5046880.html)
   * [对象探秘](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20Advanced/JVM/%E8%99%9A%E6%8B%9F%E6%9C%BA%E6%89%A7%E8%A1%8C%E5%AD%90%E7%B3%BB%E7%BB%9F.md#对象探秘)
     * [对象的创建](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20Advanced/JVM/%E8%99%9A%E6%8B%9F%E6%9C%BA%E6%89%A7%E8%A1%8C%E5%AD%90%E7%B3%BB%E7%BB%9F.md#对象的创建)
     * [对象的内存布局](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20Advanced/JVM/%E8%99%9A%E6%8B%9F%E6%9C%BA%E6%89%A7%E8%A1%8C%E5%AD%90%E7%B3%BB%E7%BB%9F.md#对象的内存布局)
     * [对象的访问定位](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20Advanced/JVM/%E8%99%9A%E6%8B%9F%E6%9C%BA%E6%89%A7%E8%A1%8C%E5%AD%90%E7%B3%BB%E7%BB%9F.md#对象的访问定位)
     * [JAVA对象头](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20Advanced/JVM/JAVA%E5%AF%B9%E8%B1%A1%E5%A4%B4.md)
     * [Java对象初始化](https://www.shuzhiduo.com/A/RnJW7akyJq/)
     * [Java对象序列化](https://www.shuzhiduo.com/A/GBJrbrBRJ0/)
     * [Java对象克隆](https://www.shuzhiduo.com/A/q4zVD7j75K/)
     * [Java 对象排序](https://www.shuzhiduo.com/A/WpdK0vNMzV/)
   * 函数如何调用
     * 局部变量表
     * 操作数栈
     * 帧数据区
     * 栈上分配
 * 2.3 虚拟机字节码执行引擎   
   * 字节码如何执行
     * 字节码插桩技术
   * 虚拟机指令集
     * 数据传送
       * 局部变量 常量池和操作数栈之间的数据传送
       * 数据传送指令
       * 常量入栈
       * 局部变量压栈指令
       * 出栈装入局部变量表
     * 通用型操作
     * 类型转换
     * 对象的创建和操作
     * 程序流程控制
       * 控制转移指令
       * 条件转移指令
       * 无条件转移指令
       * 复合条件转移指令
     * 运算
       * 加法
       * 取负
     * 函数调用与返回
       * 函数分发机制
       * invoke
       * 动态分发：覆盖
       * 静态分发：重载
     * 异常
       * 异常表
       * 创建异常
       * try-catch
       * finally
     * 同步控制
     * Class的方法结构
   * 操作码助记符
   * Java执行引擎工作原理 ---方法调用
   * java Agent运行时修改类
   * 动态函数调用
   * 静态编译优化
# [3. 自动内存管理机制](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20Advanced/JVM/%E8%87%AA%E5%8A%A8%E5%86%85%E5%AD%98%E7%AE%A1%E7%90%86%E6%9C%BA%E5%88%B6.md)

 * [JAVA堆外内存排查小结](https://juejin.im/post/5ca095ea6fb9a05e42555a3e)
 * 3.1 虚拟机运行时内存数据区
   * [Java内存模型](https://www.cnblogs.com/cac2020/p/12030710.html) 
   * 3.1.1 内存公有区域
       * Java堆 (heap)
         *  新生代
         *  老年代
         *  永久区（也就是方法区）
         *  堆中的类与对象
         *  分析Java堆
            * 内存溢出的原因
              * 堆溢出
              * 直接内存溢出
              * 过多线程导致OOM
              * 永久区溢出
              * GC效率低下引起的OOM
            * String在JVM中的实现
              * String的内存泄漏
              * String常量池的位置
            * JVM的内窥镜：使用MAT分析JAVA堆
              * 选堆对象：MAT对OQL的支持
              * Visual VM 对OQL的支持
       * 方法区
          * 运行时的常量池
            * [Java中常量以及常量池](https://www.cnblogs.com/cac2020/p/5046880.html)
          * 代码
          * 方法数据
          * 字段
          * 方法和构造函数的字节码
          * 类 实例 接口初始化时用到的特殊方法
          * code cache
       * 性能监控数据区      
          * 查看perdata
   * 3.1.2 线程私有区域
       * 程序计数器 (PC)
       * Java虚拟机栈 (Stack)
         * Java 栈帧
         * 局部变量表
             * 方法参数
         * 操作数栈
         * 常量池指针
         * 方法索引(Method Index)
         * Class File(类)
         * 父帧(Return Frame)
         * 子帧(next Frame)
         * 动态连接
         * 方法调用正常完成
         * 方法调用异常完成             
         * 栈顶缓存
   * 3.1.3 直接内存     
   * 3.1.4 Hotspot虚拟机对象探秘 
 * 3.2 垃圾收集器与内存分配策略
      * 垃圾回收的算法
        * 引用计数(Reference Counting)
        * 标记清除(Mark-Sweep)
        * 标记压缩(Mark-Compact)
        * 复制算法(Copying)
        * 分代算法(Generational Collecting)
        * 分区算法(Region)
      * 垃圾收集器
        * [Java Garbage Collection with example](https://www.logicbig.com/tutorials/core-java-tutorial/gc.html) 
        * 串行回收器
          * Serial收集器
          * Serial Old收集器
        * 并行回收器
          * ParNew收集器
          * Parallel Scavenge收集器
          * Parallel Old收集器
        * CMS回收器
        * G1回收器
        * 理接GC日志
      * 内存分配与回收策略 
 * 3.4 转存
      * 核心转储
      * 堆转储
      * 线程转储
      * 用VisualVM 进行转储分析
      * JVM Crash 日志的分析
        * 头部信息
        * 线程信息
        * 进程信息
        * 系统信息
# 6. [高效并发](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20Advanced/JVM/%E9%AB%98%E6%95%88%E5%B9%B6%E5%8F%91.md)
 * [Java 内存模型](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20Advanced/JVM/Java%20%E5%86%85%E5%AD%98%E6%A8%A1%E5%9E%8B.md)
 * 现代Java/JDK 并发模型
   *  基于角色的并发模型
 * 软件事务内存
 * Java与线程
 * 线程安全
 * 锁
   * 偏向锁
   * 轻量级锁
   * 锁膨胀
   * 自旋锁
   * 锁消除
 * 线程优化---锁在应用层的优化思路
   * 减少锁持有时间
   * 减少锁粒度
   * 锁分离
   * 锁粗化
   * 无锁的并行控制方法
 * 同步
   * 同步指令
# 7. [JVM参数分析和调优](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20Advanced/JVM/JVM%E5%8F%82%E6%95%B0%E5%88%86%E6%9E%90%E5%92%8C%E8%B0%83%E4%BC%98.md)
  *  [JVM各类参数官方文档](https://docs.oracle.com/javase/8/docs/technotes/tools/windows/java.html#BABCBGHF)
  *  [JVM实用参数教程系列---重要](http://ifeve.com/useful-jvm-flags/)
  *  [JVM性能调优](https://www.kancloud.cn/zlt2000/microservices-platform/989551)
  *  [46张PPT弄懂JVM、GC算法和性能调优](https://blog.csdn.net/youanyyou/article/details/89913387)
  *  常用Java虚拟机参数
     * [JVM的最大线程并发量](https://zhuanlan.zhihu.com/p/70371794)
     * 系统参数查看
     * 垃圾回收跟踪参数
     * 内存配置参数
       * 常用的GC参数
     * 类加载/卸载跟踪参数
     * JIT相关参数---提高JVM的执行效率
       * 开启JIT编译
       * JIT编译阀值
       * 多级编译器
       * OSR栈上替换
       * 方法内联
       * 设置代码缓存大小
     * 堆的跟踪参数
       * 最大堆和初始推的设置参数
       * 新生代堆的设置参数
       * 堆溢出的设置参数
     * 非堆的设置参数
       * 方法区的设置参数
       * 栈的设置参数
       * 直接内存的设置参数
# 8. [虚拟机监控工具](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20Advanced/JVM/%E8%99%9A%E6%8B%9F%E6%9C%BA%E7%9B%91%E6%8E%A7%E5%B7%A5%E5%85%B7.md)
 *   系统级性能监控工具
     *  Linux下的性能监控工具
        *  top---显示系统整体资源使用情况
        *  vmstat---监控内存和CPU
        *  iostat---监控IO使用
        *  pidstat---多攻能断器
     *  Windows下的性能监控工具
        *  任务管理器
        *  Perfmon---性能监控工具
        *  Process Explorer---进程管理工具
        *  Pslist---windows下的命令行工具
     * JDK官方性能监控工具
       * [JMH---专门用于代码微基准测试的工具集（toolkit）。JMH是由实现Java虚拟机的团队开发的](https://openjdk.org/projects/code-tools/jmh/)
         * [为什么要用JMH？何时应该用](https://www.51cto.com/article/713319.html)
         * [官方提供的 jmh 示例 demo](http://hg.openjdk.java.net/code-tools/jmh/file/tip/jmh-samples/src/main/java/org/openjdk/jmh/samples/)
       * [JDK Tools and Utilities---官方文档](https://docs.oracle.com/javase/7/docs/technotes/tools/#general)
       * jps命令---查看Java进程
       * 展JPS命令
       * jstat命令---查看JVM运行时信息
       * jinfo命令---查看JVM参数
       * jmap---导出堆到文件
       * jhat---JDK自带的堆分析工具
       * jstack---查看线程堆栈
       * jstatd---远程主机信息收集
       * jcmd---多功能命
       * hprof---性能统计工具
     *  图形化JVM监控工具JConsole
     *  JVM的内窥镜：使用MAT分析JAVA堆
        * JVM的内窥镜：使用MAT分析JAVA堆
        * 选堆对象：MAT对OQL的支持
     *  可视化性能监控工具Visual VM
        * Visual VM 对OQL的支持
        * [使用VisualVM监控远程服务器JVM](https://blog.csdn.net/autfish/article/details/51326340)
     *  虚拟机诊断工具Mission Control
     *  [HSDB(Hotspot Debugger)](https://liuzhengyang.github.io/2017/05/27/serviceabilityagent/ "HSDB可以查看Java对象的oops、查看类信息、线程栈信息、堆信息、方法字节码和JIT编译后的汇编代码等")
# 9 JVM生产环境中的实战优化
  * troubleshooting
    * 线上用Dubbo开发的一个系统突然卡死了，JVM FullGC太频繁
    * 生产环境部署的一个系统隔几天就莫名其妙的进程崩溃，看了眼异常信息，说是OOM内存溢出，如何解决
    * 每日百万交易的支付系统的JVM生产参数如何优化
    * 每日上亿请求量的电商系统的GC参数如何优化
    * 百万级用户平台的G1垃圾回收如何优化
    * 每秒10万并发的BI系统、每日百亿数据量的处理系统、视频系统、大促系统等各类系统中的JVM GC问题和JVM OOM问题的生产优化



# 用Java实现JVM

* [用Java实现JVM第一章《命令行工具》](https://bugstack.cn/itstack-demo-jvm/2019/05/01/%E7%94%A8Java%E5%AE%9E%E7%8E%B0JVM%E7%AC%AC%E4%B8%80%E7%AB%A0-%E5%91%BD%E4%BB%A4%E8%A1%8C%E5%B7%A5%E5%85%B7.html)
* [用Java实现JVM第二章《搜索class文件》](https://bugstack.cn/itstack-demo-jvm/2019/05/02/%E7%94%A8Java%E5%AE%9E%E7%8E%B0JVM%E7%AC%AC%E4%BA%8C%E7%AB%A0-%E6%90%9C%E7%B4%A2class%E6%96%87%E4%BB%B6.html)
* [用Java实现JVM第三章《解析class文件》](https://bugstack.cn/itstack-demo-jvm/2019/05/03/%E7%94%A8Java%E5%AE%9E%E7%8E%B0JVM%E7%AC%AC%E4%B8%89%E7%AB%A0-%E8%A7%A3%E6%9E%90class%E6%96%87%E4%BB%B6.html)
* [用Java实现JVM第三章《解析class文件》附[classReader拆解]](https://bugstack.cn/itstack-demo-jvm/2019/05/04/%E7%94%A8Java%E5%AE%9E%E7%8E%B0JVM%E7%AC%AC%E4%B8%89%E7%AB%A0-%E8%A7%A3%E6%9E%90class%E6%96%87%E4%BB%B6-%E9%99%84-classReader%E6%8B%86%E8%A7%A3.html)
* [用Java实现JVM第四章《运行时数据区》](https://bugstack.cn/itstack-demo-jvm/2019/05/05/%E7%94%A8Java%E5%AE%9E%E7%8E%B0JVM%E7%AC%AC%E5%9B%9B%E7%AB%A0-%E8%BF%90%E8%A1%8C%E6%97%B6%E6%95%B0%E6%8D%AE%E5%8C%BA.html)
* [用Java实现JVM第五章《指令集和解释器》](https://bugstack.cn/itstack-demo-jvm/2019/05/06/%E7%94%A8Java%E5%AE%9E%E7%8E%B0JVM%E7%AC%AC%E4%BA%94%E7%AB%A0-%E6%8C%87%E4%BB%A4%E9%9B%86%E5%92%8C%E8%A7%A3%E9%87%8A%E5%99%A8.html)
* [用Java实现JVM第六章《类和对象》](https://bugstack.cn/itstack-demo-jvm/2019/05/07/%E7%94%A8Java%E5%AE%9E%E7%8E%B0JVM%E7%AC%AC%E5%85%AD%E7%AB%A0-%E7%B1%BB%E5%92%8C%E5%AF%B9%E8%B1%A1.html)
* [用Java实现JVM第七章《方法调用和返回》](https://bugstack.cn/itstack-demo-jvm/2019/05/08/%E7%94%A8Java%E5%AE%9E%E7%8E%B0JVM%E7%AC%AC%E4%B8%83%E7%AB%A0-%E6%96%B9%E6%B3%95%E8%B0%83%E7%94%A8%E5%92%8C%E8%BF%94%E5%9B%9E.html)
* [用Java实现JVM第八章《数组和字符串》](https://bugstack.cn/itstack-demo-jvm/2019/05/09/%E7%94%A8Java%E5%AE%9E%E7%8E%B0JVM%E7%AC%AC%E5%85%AB%E7%AB%A0-%E6%95%B0%E7%BB%84%E5%92%8C%E5%AD%97%E7%AC%A6%E4%B8%B2.html)
* [用Java实现JVM第九章《本地方法调用》](https://bugstack.cn/itstack-demo-jvm/2019/05/10/%E7%94%A8Java%E5%AE%9E%E7%8E%B0JVM%E7%AC%AC%E4%B9%9D%E7%AB%A0-%E6%9C%AC%E5%9C%B0%E6%96%B9%E6%B3%95%E8%B0%83%E7%94%A8.html)
* [用Java实现JVM第十章《异常处理》](https://bugstack.cn/itstack-demo-jvm/2019/05/11/%E7%94%A8Java%E5%AE%9E%E7%8E%B0JVM%E7%AC%AC%E5%8D%81%E7%AB%A0-%E5%BC%82%E5%B8%B8%E5%A4%84%E7%90%86.html)


# JVM视频
  * [How the HotSpot and Graal JVMs Execute Java Code](https://www.youtube.com/watch?v=CCMDzPYWsAk)
  * [这可能是B站唯一可以讲JVM内存模型的底层源码教程](https://www.bilibili.com/video/BV1BT4y1G73q?p=1)
  * [JVM成神之路-Java系列课](https://www.bilibili.com/video/BV1Cb411t7oP/?spm_id_from=333.788.videocard.9)
  * [尚硅谷_JVM从入门到精通](https://www.bilibili.com/video/BV1BJ41177cp/?spm_id_from=333.788.videocard.7)
  * [深入理解Java虚拟机（JVM性能调优+内存模型+虚拟机原理）](https://www.bilibili.com/video/av29502877/?spm_id_from=333.788.videocard.0)---按照 《深入理解Java虚拟机：JVM高级特性与最佳实践(第2版) 》 来讲的
  * [Java高并发编程精髓Java内存模型JMM详解全集](https://www.bilibili.com/video/av59871562/?spm_id_from=333.788.videocard.3)
  * [百万年薪架构师带你了解--Java内存模型JMM与AQS同步器深入剖析](https://www.bilibili.com/video/av69537976/?spm_id_from=333.788.videocard.5)
  * [吊打面试官系列-史上最详细JVM虚拟机精讲](https://www.bilibili.com/video/av58325052)
  * [Juc 与 jvm - Java 必学 2019版本-阳哥- 尚硅谷](https://www.bilibili.com/video/av70166821/?spm_id_from=333.788.videocard.1)
  * [性能调优专题-java虚拟机性能调优与底层原理分析](https://www.bilibili.com/video/av63875355?from=search&seid=808943440095103708)
  * [美团面试七连问：没有深厚的JVM功底，你一定答不出来！](https://www.bilibili.com/video/BV1eE411V7Lm/?spm_id_from=333.788.videocard.13)
  
  
  
  
  
# 有用的参考书籍
  * [ JAVA JVM](https://www.cnblogs.com/Courage129/category/1716534.html)
  * [一图解千愁，jvm内存从来没有这么简单过！---了解了这张图，会让你对JVM内存的划分有更深入的理解，而不仅限于什么虚拟机栈、程序计数器等比较浅显的认知](https://juejin.im/post/5ed49e7c51882543012f9e6c)
  * [Java虚拟机(JVM)面试题（2020最新版）](https://blog.csdn.net/ThinkWon/article/details/104390752)
  * [Java虚拟机内存分配机制与启动参数说明](https://blog.csdn.net/chengyun19830206/article/details/53244278)
  *  深入理解Java虚拟机_JVM高级特性与最佳实践.PDF
  *   HotSpot 实战.PDF
  *  揭秘Java虚拟机-JVM设计原理与实现.PDF
  *  深入解析Java虚拟机器开发：权衡优化、高校和安全的最优方案.PDF
  *  深入理解JVM ＆ G1 GC.PDF
  *  实战JAVA虚拟机  JVM故障诊断与性能优化.PDF
  *  自己动手写Java虚拟机.PDF
  *  Java虚拟机规范.Java SE 8版.PDF
  *  Java虚拟机并发编程.PDF
  *  [Java对象实例化过程](https://mrbird.cc/Java%E5%AF%B9%E8%B1%A1%E5%AE%9E%E4%BE%8B%E5%8C%96%E8%BF%87%E7%A8%8B.html)
  * [JVM学习笔记](https://mrbird.cc/JVM-Learn.html)
  * [Java 垃圾回收](https://mrbird.cc/Java-%E5%9E%83%E5%9C%BE%E5%9B%9E%E6%94%B6%E2%99%BB%EF%B8%8F.html)
  * [jvm troubleshooting 基础](https://mrbird.cc/Java%20Troubleshooting%E5%9F%BA%E7%A1%80.html)
  * [JVM常用配置参数：Trace跟踪参数](https://edu.51cto.com/study/9314)
  * [github JVM Information ](https://github.com/deephacks/awesome-jvm)
  * [JVM基础面试题及原理讲解](http://www.importnew.com/31126.html)
  * [关于 JVM 内存的 N 个问题](http://www.importnew.com/29920.html)
  * [JVM 堆内存和非堆内存](http://www.importnew.com/27645.html)
  * [一点一滴探究 JVM 之内存结构](http://www.importnew.com/27454.html)
  * [Java 内存溢出排查](http://www.importnew.com/29883.html)
  * [Java直接（堆外）内存使用详解](http://www.importnew.com/21998.html)
  * [堆外内存之 DirectByteBuffer 详解](http://www.importnew.com/26334.html)
  * [又一次线上OOM排查经过](http://www.importnew.com/24393.html)
  * [JVM OOM & JAVA finalizer 引发的 OOM & Thread.stop](http://www.importnew.com/25522.html)
  * [从JVM heap dump里查找没有关闭文件的引用](http://www.importnew.com/29099.html)
  * [JVM之ParNew和CMS日志分析](http://www.importnew.com/30237.html)
  * [从JVM heap dump里查找没有关闭文件的引用](http://www.importnew.com/29099.html)
  * [使用 JITWatch 查看 JVM 的 JIT 编译代码](http://www.importnew.com/28957.html)
  * [深入理解JVM之JVM内存区域与内存分配](https://blog.csdn.net/USTC_Zn/article/details/54912367)
  * [好好说说Java中的常量池之Class常量池](https://blog.csdn.net/hollis_chuang/article/details/83501326)
  * [深入理解Java中的逃逸分析](https://blog.csdn.net/hollis_chuang/article/details/80922794)
  * [异常、堆内存溢出、OOM的几种情况](https://blog.csdn.net/sinat_29912455/article/details/51125748)
  * [JVM的ServerSocket是怎么实现的（上）](https://mp.weixin.qq.com/s?__biz=MjM5MzA1Mzc3Nw==&mid=2247484390&idx=1&sn=718cfa6c0b1634762137f0f02e976ce8&chksm=a69daed891ea27ceee9f4ba8f61e2e689590c7f1648b98b8eea58048c5d70235bab48629ced1&scene=21#wechat_redirect)

* [JVM的ServerSocket是怎么实现的（下）](https://mp.weixin.qq.com/s?__biz=MjM5MzA1Mzc3Nw==&mid=2247484393&idx=1&sn=de4945c7233e950bdfbb62a52826ca92&chksm=a69daed791ea27c1f5d29ab163618a9ca3603f9fd11e4f74946f5683039432dc083832dcd8cd&scene=21#wechat_redirect)

* [JVM知识问答集锦](https://blog.csdn.net/WYpersist/article/details/86547891)
* [java存储机制（栈、堆、方法区详解）](https://blog.csdn.net/qq_26805137/article/details/52996910)
* [【深入Java虚拟机】之五：多态性实现机制——静态分派与动态分派](https://blog.csdn.net/ns_code/article/details/17965867)
* [一次JVM FullGC的背后，竟隐藏着惊心动魄的线上生产事故！](https://juejin.im/post/5c1a448a6fb9a04a0f652134)
* [95%的技术面试必考的JVM知识点都在这](https://zhuanlan.zhihu.com/p/58724558?utm_source=wechat_session&utm_medium=social&utm_oi=991812777480134656)
* [一篇年薪60万的JVM性能调优文章](https://zhuanlan.zhihu.com/p/62762455?utm_source=wechat_session&utm_medium=social&utm_oi=991812777480134656)
* [笔者带你剖析大规模分布式Java平台跟JVM性能调优](https://zhuanlan.zhihu.com/p/35605715?utm_source=wechat_session&utm_medium=social&utm_oi=991812777480134656)

* [关于Java虚拟机性能调优的清单](https://zhuanlan.zhihu.com/p/29043288?utm_source=wechat_session&utm_medium=social&utm_oi=991812777480134656)

* [作为程序员必须掌握的Java虚拟机中的22个重难点](https://zhuanlan.zhihu.com/p/60185162?utm_source=wechat_session&utm_medium=social&utm_oi=991812777480134656)

* [看完你还敢说你懂JVM吗？](https://zhuanlan.zhihu.com/p/61049063?utm_source=wechat_session&utm_medium=social&utm_oi=991812777480134656)
* [扒一扒 JVM 的垃圾回收机制，拿大厂offer少不了它！](https://mp.weixin.qq.com/s/-npEviJ8m1TcO84i7-HVYg)
* [聊聊JVM](https://blog.csdn.net/ITer_ZC/column/info/talk-about-jvm)
* [从字节码和 JVM 的角度解析 Java 核心类 String 的不可变特性](http://www.importnew.com/26595.html)
* [通过JVM日志来进行安全点分析](http://ifeve.com/logging-stop-the-world-pauses-in-jvm/)
* [Java字节码浅析（—）](http://ifeve.com/javacode2bytecode/)
* [Java字节码浅析（二）](http://ifeve.com/javacode2bytecode2/)
* [Java字节码浅析（三）](http://ifeve.com/java-code-to-byte-code-3/)
* [JVM的内部结构以及字节码运行时用到的各个内存区域](http://blog.jamesdbloom.com/JVMInternals.html)
* [JVM实用参数系列](http://ifeve.com/useful-jvm-flags/)
* [JVM性能优化（一）JVM技术入门](http://ifeve.com/jvm-optimize-1/)
* [JVM优化系列-(二)-编译器](http://ifeve.com/jvm-optimize-2/)
* [JVM性能优化（三）：垃圾收集](http://ifeve.com/jvm-3-gc/)
* []()
* [JVM性能优化 （五）Java的伸缩性](http://ifeve.com/jvm-performance-optimization-java-scalability-5/)
* [JVM 内部运行线程介绍](http://ifeve.com/jvm-thread/)
* [JVM Attach机制实现](http://ifeve.com/jvm-attach/)
* [JVM 心得 OOM时的堆信息获取方法与分析](https://www.jb51.net/article/125968.htm)
* [如何限制一个类对象只在堆上分配或者只在栈上分配？](https://blog.csdn.net/wonengxing/article/details/6862971)
* [简单通俗了解一个完整的Java程序运行过程（内存分配情况）](https://blog.csdn.net/m0_37499059/article/details/80451799)
* [你需要知道的那些 Java 字节码知识](https://blog.csdn.net/lpd_tech/article/details/88948020)
* [记一次服务内存不断飙升的JVM调试过程](https://blog.csdn.net/hanchao5272/article/details/93130951)
* [非常好   Synchronized的JVM底层实现](https://blog.csdn.net/Winston_Limf/article/details/80915304)
* [超详细java中的ClassLoader详解](https://blog.csdn.net/dw147258dw/article/details/93847568)
* [java classloader使用，热部署原理](https://blog.csdn.net/cyduo/article/details/80334038)
* [从程序上java 利用managementFactory获取jvm信息](https://blog.csdn.net/dw147258dw/article/details/93164661)
* [SkyWalking jvm指标采集与存储](https://blog.csdn.net/dw147258dw/article/details/93711710)
* [性能分析之-- JAVA Thread Dump 分析综述](https://blog.csdn.net/rachel_luo/article/details/8920596)
* [看完这篇垃圾回收，和面试官扯皮没问题了](https://blog.csdn.net/hollis_chuang/article/details/104140327?depth_1-utm_source=distribute.pc_relevant.none-task&utm_source=distribute.pc_relevant.none-task)
* [可能是把Java内存区域讲的最清楚的一篇文章](https://mp.weixin.qq.com/s?__biz=Mzg2OTA0Njk0OA==&mid=2247484884&amp;idx=1&amp;sn=0d9b841ce0fc300c78ade2a87ffbfb46&source=41#wechat_redirect)
* [搞定 JVM 垃圾回收就是这么简单](https://blog.csdn.net/varyall/article/details/82119947)
