

# [Java中50个关键字](https://blog.csdn.net/candy_day/article/details/79942153)

### abstract关键字
* [abstract class和interface区别](https://blog.csdn.net/b_boy_hong10/article/details/80273259)

### Assert关键字
* [调试时的Assert用法](https://blog.csdn.net/jiang111_111shan/article/details/70186013)

### enum关键字
* [枚举enum与多例设计](https://blog.csdn.net/qq_36582604/article/details/81408919)
* [Java 枚举(enum) 详解7种常见的用法](https://blog.csdn.net/qq_27093465/article/details/52180865)
* [java反射获取枚举值](https://blog.csdn.net/Bronze5/article/details/80214011)

### final,finally,finalize

什么是finalize()方法？finalize()方法什么时候被调用？
答： Java允许在类中定义一个名为finalize()的方法，一旦垃圾回收器准备好释放对象占用的存储空间，将首先调用finalize()方法，并且在下一次垃圾回收动作发生时，才会真正回收对象占用的内存。
析构函数(finalization)的目的是什么
析构函数目的是撤销对象前、完成一些清理工作，比如释放资源。释放了之后这些资源可以被回收，重新利用。
final关键字有哪些用法
final关键字主要用于修饰类、类成员、方法、以及方法的形参。

final修饰成员属性：说明该成员属性是常量，不能被修改；

final修饰类，该类是最终类，不能被继承。

final修饰方法：该方法是最终方法，不能被重写。

final关键字修饰形参：1：当形参被修饰为final,那么该形参所属的方法中不能被篡改。

补充: try / catch / finally 中return的关系？
1、不管有木有出现异常，finally块中代码都会执行；
2、当try和catch中有return时，finally仍然会执行；
3、finally是在return后面的表达式运算后执行的（此时并没有返回运算后的值，而是先把要返回的值保存起来，管finally中的代码怎么样，返回的值都不会改变，仍然是之前保存的值），所以函数返回值是在finally执行前确定的；
4、finally中最好不要包含return，否则程序会提前退出，返回值不是try或catch中保存的返回值。


* [java中final,finally,finalize三个关键字的区别](https://blog.csdn.net/NDboy/article/details/45535201)
* [细说final关键字和static关键字](https://blog.csdn.net/m15732622413/article/details/53241231)
* [JAVA 堆栈 堆 方法区 静态区 final static 内存分配 详解](https://blog.csdn.net/peterwin1987/article/details/7571808)
* [final、static和final static之间的差别](https://blog.csdn.net/tracyjack123/article/details/81630997)
* [从Java内存模型理解synchronized、volatile和final关键字](https://blog.csdn.net/fuzhongmin05/article/details/60464835)
* [final关键词在多线程环境中的使用](https://blog.csdn.net/xiaoxiaoxuanao/article/details/52573859)


### instanceof关键字
* [Java instanceof用法详解](https://blog.csdn.net/kuangay/article/details/81563992)

### static关键字
* [Java static关键字详解](https://blog.csdn.net/kuangay/article/details/81485324)
* [static关键字的四种用法](https://blog.csdn.net/shuyizhi/article/details/79700054)
* [细说final关键字和static关键字](https://blog.csdn.net/m15732622413/article/details/53241231)
* [JAVA 堆栈 堆 方法区 静态区 final static 内存分配 详解](https://blog.csdn.net/peterwin1987/article/details/7571808)
* [静态方法中new对象](https://blog.csdn.net/weixin_41929877/article/details/80422009)
* [静态方法中不可直接new内部类实例对象问题](https://blog.csdn.net/aizhihua19900214/article/details/79714235)
* [Java里面 创建对象放在main方法外面为什么要用static修饰](https://www.zhihu.com/question/322912459/answer/674332458?utm_source=wechat_session&utm_medium=social&utm_oi=991812777480134656)

### strictfp关键字
* [Java语言中关键字strictfp的用途](https://blog.csdn.net/redv/article/details/326444)

### synchronized关键字
* [synchronized](https://blog.csdn.net/mingyundezuoan/article/details/79264644)
* [synchronized(.class) synchronized(Object ) synchronized(this) 区别](https://blog.csdn.net/u011996917/article/details/80783471)
* [Java语言中synchronized 修饰在 static方法和 非static方法的区别](https://blog.csdn.net/virgoboy2004/article/details/7585182)
* [Synchronized和volatile 的区别，两者使用的场景](https://blog.csdn.net/yinni11/article/details/81514082)

### this关键字
* [Java this关键字详解](https://blog.csdn.net/kuangay/article/details/81535701)

### transient关键字
* [transient关键字有何作用](https://blog.csdn.net/duyiwuerluozhixiang/article/details/86633179)
* [简述serializable和transient关键字作用](https://blog.csdn.net/java__project/article/details/54960525)

### new关键字
* [java new 关键字到底做了什么？](https://blog.csdn.net/u010523770/article/details/68969482)
* [Java中new和不new的区别](https://blog.csdn.net/xujiangdong1992/article/details/73910088)
* [new和java反射的区别](https://blog.csdn.net/brucehome/article/details/17098971)
* [Java new关键字和newInstance()方法的区别](https://blog.csdn.net/shipfei_csdn/article/details/81939616)
* [Class.forName()用法及与new的区别](https://blog.csdn.net/zhangxichao100/article/details/51105205)
* [java中代理模式以及new和newInatance（）的区别](https://blog.csdn.net/lnjphjh/article/details/48752983)


### native关键字
* [java中native关键字的详解](https://blog.csdn.net/u013531824/article/details/21012655)

### volatile关键字
 * [全面理解Java内存模型(JMM)及volatile关键字](https://blog.csdn.net/javazejian/article/details/72772461)
 * [Java中volatile关键字的最全总结](https://blog.csdn.net/u012723673/article/details/80682208)
 * [绝对深度解析volatile关键字的实现原理（大部分内容整理于其他博客](https://blog.csdn.net/yanshuanche3765/article/details/78405872)
 * [volatile与synchronized 同步原理基础讲解](https://blog.csdn.net/lxlmycsdnfree/article/details/70256031)
 * [从volatile和synchronized的底层实现原理看Java虚拟机对锁优化所做的努力](https://blog.csdn.net/lxlmycsdnfree/article/details/70256031)
 * [Java中volatile关键字的最全总结](https://blog.csdn.net/u012723673/article/details/80682208)
 * [Synchronized和volatile 的区别，两者使用的场景](https://blog.csdn.net/yinni11/article/details/81514082)
 
 
