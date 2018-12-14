
#  [JVM面试](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20Advanced/JVM/JVM%20interview.md)



目录
---

# 0. 
<p align="center">
  <img src="https://flic.kr/s/aHskNRcu9d">
  <br/>
</p>

# 1. 程序编译与代码优化

 *  早期优化（编译期）
    * 虚拟机编译器
    
 *  晚期优化（运行期）

# 2. 虚拟机执行子系统

 *  类文件结构(ClassFile)
    *  
 *  虚拟机类的加载机制（ClassLoader）
    * 加载，链接与初始化
    
    
    
# 3. 自动内存管理机制


 *  3.1  虚拟机运行时内存数据区
  * 3.1.1  内存公有区域
       *  Java堆 (heap)
          *  新生代
          *  老年代
          *  
      *  方法区
          *  内存中的常量池
          *  代码
          *  方法
          *  字段
  * 3.1.2  线程私有区域
      *  程序计数器 (PC)
      *  Java虚拟机栈 （Stack）
          *  Java 栈帧
          *  局部变量
          *  方法参数
       
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
# 4. 虚拟机字节码执行引擎

    *  虚拟机指令集
    
    *  操作码助记符
 

# 6. 高效并发

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

# 7. JVM参数分析和调优
 
  *  常用Java虚拟机参数
     *  垃圾回收跟踪参数
     *  内存配置参数

# 8. 虚拟机监控工具
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
 
# 9. 对虚拟机的各个组成部份的分析
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
