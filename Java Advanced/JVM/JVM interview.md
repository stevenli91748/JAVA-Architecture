
[JVM面试题（2020最新版）](https://thinkwon.blog.csdn.net/article/details/104390752)|[77道JVM系列面试题总结（2万字解析）](https://mp.weixin.qq.com/s?__biz=MzIwNjg4MzY4NA==&mid=2247485319&idx=1&sn=63cb1fdf3b56e4a84d7768e80f71e027&chksm=971b998ca06c109aa7de32d4f254e51a516647430e3934192dbf799fda12a82024396adbf40d&scene=21#wechat_redirect)|
---|---|

# 虚拟机知识点目录
---
* [虚拟机体系结构](#虚拟机体系结构)
* [JVM 的垃圾收集机制](#JVM-的垃圾收集机制)
* [JVM运行时内存](#JVM运行时内存)
  * [更高效地使用内存](#更高效地使用内存)
  * [定位和排查内存性能问题](#定位和排查内存性能问题)
  * [在面试中如何展示虚拟机和内存调优技能](#在面试中如何展示虚拟机和内存调优技能)
---
# 虚拟机体系结构

<details>
<summary>什么是Monitor?</summary>
 
[什么是Monitor](https://blog.csdn.net/dao_wolf/article/details/85627361)

</details>


<details>
<summary>字节码、虚拟机、JRE 和跨平台特性</summary>

</details>

<details>
<summary>虚拟机体系结构</summary>

</details>

<details>
<summary>归纳静态数据、基本数据类型和引用等数据的存储位置</summary>

</details>

# JVM 的垃圾收集机制

<details>
<summary>分代管理与垃圾回收流程</summary>

</details>

<details>
<summary>不重视内存性能可能会导致的后果</summary>

</details>

<details>
<summary>判断对象可回收的依据</summary>

</details>

<details>
<summary>深入了解finalize方法</summary>

</details>

<details>
<summary>Java 垃圾回收机制</summary>

</details>

<details>
<summary>通过强、弱、软、虚4 种引用进一步了解垃圾回收机制</summary>

</details>

<details>
<summary>软引用和弱引用的用法</summary>

</details>

<details>
<summary>软引用的使用场景</summary>

</details>

<details>
<summary>通过WeakHashMap 来了解弱引用的使用场景</summary>

</details>

<details>
<summary>虚引用及其使用场景</summary>

</details>

# JVM运行时内存

### 更高效地使用内存

<details>
<summary>StoptheWorld 、找溢出错误和内存溢出错误</summary>

</details>

<details>
<summary>内存泄漏的示例</summary>

</details>

<details>
<summary>在代码中优化内存性能的具体做法</summary>

</details>

<details>
<summary>调整运行参数，优化堆内存性能…</summary>

</details>

### 定位和排查内存性能问题

<details>
<summary>什么情况下该排查内存问题</summary>

</details>

<details>
<summary>通过 JConsole 监控内存使用量</summary>

</details>

<details>
<summary>通过GC 日志来观察内存使用情况</summary>

</details>

<details>
<summary>通过打印内存使用量定位问题点</summary>

</details>

<details>
<summary>出现OOM 后如何获取和分析Dump 文件</summary>

</details>

<details>
<summary>出现内存问题该怎样排查</summary>

</details>

### 在面试中如何展示虚拟机和内存调优技能

<details>
<summary>从虚拟机体系结构引出内存管理的话题</summary>

</details>

<details>
<summary>如何自然地引出内存话题</summary>

</details>

<details>
<summary>根据堆区结构，阐述垃圾回收的流程</summary>

</details>

<details>
<summary>进一步说明如何写出高性能的代码．</summary>

</details>

<details>
<summary>展示监控、定位和调优方面的综合能力</summary>

</details>




---
---
<details>
<summary>Java虚拟机中，数据类型可以分为哪几类？</summary>



</details>

<details>
<summary>怎么理解栈、堆？堆中存什么？栈中存什么？</summary>



</details>

<details>
<summary>为什么要把堆和栈区分出来呢？栈中不是也可以存储数据吗？</summary>



</details>

<details>
<summary>在Java中，什么是是栈的起始点，同是也是程序的起始点？</summary>



</details>

<details>
<summary>为什么不把基本类型放堆中呢？</summary>



</details>

<details>
<summary>Java中，栈的大小通过什么参数来设置？</summary>



</details>

<details>
<summary>一个空Object对象的占多大空间？</summary>



</details>

<details>
<summary>对象引用类型分为哪几类？</summary>



</details>

<details>
<summary>讲一讲垃圾回收算法</summary>



</details>

<details>
<summary>如何解决内存碎片的问题？</summary>



</details>

<details>
<summary>如何解决同时存在的对象创建和对象回收问题？</summary>



</details>

<details>
<summary>如何解决同时存在的对象创建和对象回收问题？</summary>



</details>

<details>
<summary> 如何解决同时存在的对象创建和对象回收问题？</summary>



</details>

<details>
<summary>讲一讲内存分代及生命周期。</summary>



</details>

<details>
<summary>什么情况下触发垃圾回收？</summary>



</details>

<details>
<summary>如何选择合适的垃圾收集算法？</summary>



</details>

<details>
<summary>JVM中最大堆大小有没有限制？</summary>



</details>

<details>
<summary>堆大小通过什么参数设置？？</summary>



</details>

<details>
<summary>JVM有哪三种垃圾回收器？</summary>



</details>

<details>
<summary>吞吐量优先选择什么垃圾回收器？响应时间优先呢？</summary>



</details>

<details>
<summary>如何进行JVM调优？有哪些方法？</summary>



</details>

<details>
<summary> 如何理解内存泄漏问题？有哪些情况会导致内存泄露？如何解决？</summary>



</details>

<details>
<summary>什么是Java虚拟机，为什么要使用？</summary>



</details>

<details>
<summary> 说说Java虚拟机的生命周期及体系结构。</summary>


</details>

<details>
<summary> 说一说Java内存区域。</summary>

</details>

<details>
 <summary>26. 一个实例变量在对象初始化的过程中会被赋值几次？</summary>

我们知道，JVM在为一个对象分配完内存之后，会给每一个实例变量赋予默认值，这个时候实例变量被第一次赋值，这个赋值过程是没有办法避免的。如果我们在声明实例变量x的同时对其进行了赋值操作，那么这个时候，这个实例变量就被第二次赋值了。如果我们在实例代码块中，又对变量x做了初始化操作，那么这个时候，这个实例变量就被第三次赋值了。如果我们在构造函数中，也对变量x做了初始化操作，那么这个时候，变量x就被第四次赋值。也就是说，在Java的对象初始化过程中，一个实例变量最多可以被初始化4次。

</details>

<details>
<summary> 27. 类的初始化过程与类的实例化过程的异同？</summary>
 
特别需要指出的是，类的实例化与类的初始化是两个完全不同的概念：

类的实例化是指在类完全加载到内存中后创建对象的过程，类的实例化是在类的使用阶段，
类的初始化是指为类加载过程中各个类成员(被static修饰的成员变量)赋初始值的过程，

</details>

<details>
<summary> 28. 假如一个类还未加载到内存中，那么在创建一个该类的实例时，具体过程是怎样的？</summary>

我们知道，要想创建一个类的实例，必须先将该类加载到内存并进行初始化，也就是说，类初始化操作是在类实例化操作之前进行的，但并不意味着：只有类初始化操作结束后才能进行类实例化操作

```java

public class StaticTest {
    public static void main(String[] args) {
        staticFunction();
    }

    static StaticTest st = new StaticTest();

    static {   //静态代码块
        System.out.println("1");
    }

    {       // 实例代码块
        System.out.println("2");
    }

    StaticTest() {    // 实例构造器
        System.out.println("3");
        System.out.println("a=" + a + ",b=" + b);
    }

    public static void staticFunction() {   // 静态方法
        System.out.println("4");
    }

    int a = 110;    // 实例变量
    static int b = 112;     // 静态变量
}/* Output: 
        2
        3
        a=110,b=0
        1
        4
 *///:~

```

</details>

Java GC机制（可达性分析法，引用计数法）

JVM类加载机制

1.Java的内存模型以及GC算法

2.jvm性能调优都做了什么

3.介绍JVM中7个区域，然后把每个区域可能造成内存的溢出的情况说明

4.介绍GC和GCRoot不正常引用。

5.自己从classload加载方式，加载机制说开去，从程序运行时数据区，讲到内存分配，讲到String常量池，讲到JVM垃圾回收机制，算法，hotspot。反正就是各种扩展

6.jvm如何分配直接内存，new对象如何不分配在堆而是栈上，常量池解析

7.数组多大放在JVM老年代（不只是设置PretenureSizeThreshold，问通常多大，没做过一问便知）

8.老年代中数组的访问方式

9.GC算法，永久代对象如何GC，GC有环怎么处理

10.谁会被GC，什么时候GC

11.如果想不被GC怎么办

12.如果想在GC中生存1次怎么办

3.1 JVM参数主要有⼏种分类 
3.2 3.Java中会存在内存泄漏吗，简述一下。
3.3 Java虚拟机是如何判定两个Java类是相同的？ 
3.4 Java 中都有哪些引用类型 
3.5 在 Java 中，对象什么时候可以被垃圾回收？
3.6 19.StackOverflow异常有没有遇到过？一般你猜测会在什么情况下被触发？
3.7 堆空间分哪些部分？以及如何设置各个部分？ 
3.8 什么是栈帧？栈帧存储了什么？ 
3.9 如何设置参数生成GC日志？ 
3.10 GC 是什么？为什么要有 GC？
3.12 使用过哪些jdk命令，并说明各个的作用是什么 
3.13 JVM运行时数据区区域分为哪⼏部分？
3.14 是否了解类加载器双亲委派模型机制和破坏双亲委派模型？ 
3.15 逃逸分析有几种类型？
3.16 -Xms这些参数的含义是什么？
3.17 你知道哪几种垃圾收集器,各自的优缺点，重点讲下cms和G1，包括原理，流程，优缺点。
3.18 JVM的内存结构，Eden和Survivor比例是多少？




# 有用的参考

  * Java核心技术及面试指南.pdf
  * 百战程序员Java1573题(1.0版).pdf

