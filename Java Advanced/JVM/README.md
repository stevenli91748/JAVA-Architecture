
#  [JVM面试](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20Advanced/JVM/JVM%20interview.md)



目录
---

# [0. 初识JVM](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20Advanced/JVM/%E5%88%9D%E8%AF%86JVM.md)

  *  虚拟机的启动



# [1. 程序编译与代码优化](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20Advanced/JVM/%E7%A8%8B%E5%BA%8F%E7%BC%96%E8%AF%91%E4%B8%8E%E4%BB%A3%E7%A0%81%E4%BC%98%E5%8C%96.md)

 *  早期优化（编译期）
    * 虚拟机编译器
    
 *  晚期优化（运行期）

# [2. 虚拟机执行子系统](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20Advanced/JVM/%E8%99%9A%E6%8B%9F%E6%9C%BA%E6%89%A7%E8%A1%8C%E5%AD%90%E7%B3%BB%E7%BB%9F.md)

 *  类文件结构(ClassFile)
    *  
 *  虚拟机类的加载机制（ClassLoader）
    * 加载，链接与初始化
 
# [3. 自动内存管理机制](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20Advanced/JVM/%E8%87%AA%E5%8A%A8%E5%86%85%E5%AD%98%E7%AE%A1%E7%90%86%E6%9C%BA%E5%88%B6.md)


 *  3.1  虚拟机运行时内存数据区
  * 3.1.1  内存公有区域
       *  Java堆 (heap)
          *  新生代
          *  老年代
          *  永久区（也就是方法区）
      *  方法区
          *  类型的常量池
          *  代码
          *  方法
          *  字段
  * 3.1.2  线程私有区域
      *  程序计数器 (PC)
      *  Java虚拟机栈 (Stack)
          *  Java 栈帧
             *  局部变量
             *  方法参数
             *  操作数栈
             *  常量池指针
             *  方法索引(Method Index)
             *  Class File(类)
             *  父帧(Return Frame)
             *  子帧(next Frame)
       
 *  3.2  Hotspot虚拟机对象探秘 
 *  3.3  内存
 *  3.4  垃圾收集器与内存分配策略
     *  垃圾回收的算法
        *  引用计数
        *  标记清除
        *  标记压缩
        *  复制算法
     *  垃圾收集器
        *  串行回收器
        *  并行回收器
        *  CMS
        *  G1
# [4. 虚拟机字节码执行引擎](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20Advanced/JVM/%E8%99%9A%E6%8B%9F%E6%9C%BA%E5%AD%97%E8%8A%82%E7%A0%81%E6%89%A7%E8%A1%8C%E5%BC%95%E6%93%8E.md)
   *  虚拟机指令集
   *  操作码助记符
 

# 6. [高效并发](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20Advanced/JVM/%E9%AB%98%E6%95%88%E5%B9%B6%E5%8F%91.md)

 *  Java 内存模型
 *  现代Java/JDK 并发模型
    *  基于角色的并发模型
 *  软件事务内存
 *  Java与线程
 *  线程安全
 *  锁
    *  内部锁
    *  无锁的并行控制方法
 *  线程优化
 *  同步

# 7. [JVM参数分析和调优](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20Advanced/JVM/JVM%E5%8F%82%E6%95%B0%E5%88%86%E6%9E%90%E5%92%8C%E8%B0%83%E4%BC%98.md)
 
  *  常用Java虚拟机参数
     *  垃圾回收跟踪参数
     *  内存配置参数

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
 *   Java虚拟机性能监控工具
     *  JDK性能监控工具
        *  jps---查看JAVA进程
        *  jstat---查看虚拟机运行时信息
        *  jinfo---查看虚拟机参数
        *  jmap---导出堆到文件
        *  jhat---JDK自带的堆分析工具
        *  jstack---查看线程堆栈
        *  jstatd---查看远程主机信息
        *  jcmd---多功能命令行
        *  hprof---性能统计工具
        *  加强JPS命令
     *  图形化虚拟机监控工具JConsole
     *  可视化性能监控工具Visual VM
     *  虚拟机诊断工具Mission Control
 
# [9. 对虚拟机的各个组成部份的分析](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20Advanced/JVM/%E5%AF%B9%E8%99%9A%E6%8B%9F%E6%9C%BA%E7%9A%84%E5%90%84%E4%B8%AA%E7%BB%84%E6%88%90%E9%83%A8%E4%BB%BD%E7%9A%84%E5%88%86%E6%9E%90.md)
 *   对虚拟机里的堆进行分析
 
# 参考书籍
 
  *  深入理解Java虚拟机_JVM高级特性与最佳实践
  *   HotSpot 实战
  *  揭秘Java虚拟机-JVM设计原理与实现
  *  深入解析Java虚拟机器开发：权衡优化、高校和安全的最优方案
  *  深入理解JVM ＆ G1 GC
  *  实战JAVA虚拟机  JVM故障诊断与性能优化
  *  自己动手写Java虚拟机
  *  Java虚拟机规范.Java SE 8版
  *  Java虚拟机并发编程
