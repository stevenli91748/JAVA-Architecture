

# [Java中50个关键字](https://blog.csdn.net/ThinkWon/article/details/101642385)

* 訪问控制3个
  * private
  * portected
  * public
* 类 方法和变量修饰符14个  
  * [abstract](#abstract)
  * class
  * enum
  * extends
  * final
  * implements
  * interface
  * native
  * new
  * static
  * strictfp
  * synchronized
  * transient
  * volatile
* 程序控制语句12个
  * break
  * case
  * continue
  * default
  * do
  * else
  * for
  * if
  * instanceof
  * return
  * switch
  * while
* 错误处理6个
  * assert
  * catch
  * finally
  * throw
  * throws
  * try
* 包2个
  * import
  * package
* 基本数据类型8个
  * boolean
  * byte
  * char
  * double
  * int
  * float
  * shout
  * long
* 变量引用3个
  * super
  * this
  * void
* default
* exports
* module
* requires
* var


### abstract
* [abstract class和interface区别](https://blog.csdn.net/b_boy_hong10/article/details/80273259)

### Assert关键字
* [调试时的Assert用法](https://blog.csdn.net/jiang111_111shan/article/details/70186013)

### enum关键字
* [枚举enum与多例设计](https://blog.csdn.net/qq_36582604/article/details/81408919)
* [Java 枚举(enum) 详解7种常见的用法](https://blog.csdn.net/qq_27093465/article/details/52180865)
* [java反射获取枚举值](https://blog.csdn.net/Bronze5/article/details/80214011)

### final,finally,finalize
<details>
<summary>使用final关键字修饰一个变量时，是引用不能变，还是引用的对象不能变?</summary>
 是引用不能变（final引用恒定不变），引用的对象内容还是可以变的
</details>

<details>
<summary>什么是finalize()方法？finalize()方法什么时候被调用？</summary>

Java允许在类中定义一个名为finalize()的方法，一旦垃圾回收器准备好释放对象占用的存储空间，将首先调用finalize()方法，并且在下一次垃圾回收动作发生时，才会真正回收对象占用的内存。

</details>

<details>
<summary>析构函数(finalization)的目的是什么</summary>

析构函数目的是撤销对象前、完成一些清理工作，比如释放资源。释放了之后这些资源可以被回收，重新利用

</details>

<details>
<summary>final关键字有哪些用法</summary>

final关键字主要用于修饰类、类成员、方法、以及方法的形参。

final修饰成员属性：说明该成员属性是常量，不能被修改；

final修饰类，该类是最终类，不能被继承。

final修饰方法：该方法是最终方法，不能被重写。

final关键字修饰形参：1：当形参被修饰为final,那么该形参所属的方法中不能被篡改。

</details>

<details>
<summary>final 与 static 关键字可以用于哪里？它们的作用是什么？</summary>

用于修饰成员变量和成员方法，可以理解为“全局常量”，对于变量表示一旦给定值就不可以修改，并且通过类名可以访问；对于方法表示不可覆盖，并且可以通过类名直接访问

</details>


<details>
<summary> final, finally, finalize的区别（或者说final、finalize 和 finally 的不同之处？）</summary>

final关键字可以用于类，方法，变量前，用来表示该关键字修饰的类，方法，变量具有不可变的特性。

final关键字用于基本数据类型前：这时表明该关键字修饰的变量是一个常量，在定义后该变量的值就不能被修改。

final关键字用于方法声明前：这时意味着该方法时最终方法，只能被调用，不能被覆盖，但是可以被重载。

final关键字用于类名前：此时该类被称为最终类，该类不能被其他类继承。

finalize()方法来自于java.lang.Object，用于回收资源。可以为任何一个类添加finalize方法。finalize方法将在垃圾回收器清除对象之前调用。在实际应用中，不要依赖使用该方法回收任何短缺的资源，这是因为很难知道这个方法什么时候被调用。

finally，当代码抛出一个异常时，就会终止方法中剩余代码的处理，并退出这个方法的执行。finally块是程序在正常情况下或异常情况下都会运行的。比较适合用于既要处理异常又有资源释放的代码，保证了资源的合理回收。

</details>

<details>
<summary>能否在运行时向 static final 类型的赋值</summary>

不可以，被static final修饰的变量只能在被定义的时候或者类的静态代码块中初始化，一旦赋值后就不能在改变了。static final相当于类常量，就是在类被加载进内存的时候就要为属性分配内存，static块就是类被加载的时候执行且被执行一次，所以可以在其中进行初始化。

</details>

<details>
<summary>使用final关键字修饰一个变量时，是引用不能变，还是引用的对象不能变?</summary>

是引用不能变（final引用恒定不变），引用的对象内容还是可以变的

</details>

* [深入分析Object.finalize方法的实现原理](http://www.importnew.com/23913.html)
* [java中final,finally,finalize三个关键字的区别](https://blog.csdn.net/NDboy/article/details/45535201)
* [细说final关键字和static关键字](https://blog.csdn.net/m15732622413/article/details/53241231)
* [JAVA 堆栈 堆 方法区 静态区 final static 内存分配 详解](https://blog.csdn.net/peterwin1987/article/details/7571808)
* [final、static和final static之间的差别](https://blog.csdn.net/tracyjack123/article/details/81630997)
* [从Java内存模型理解synchronized、volatile和final关键字](https://blog.csdn.net/fuzhongmin05/article/details/60464835)
* [final关键词在多线程环境中的使用](https://blog.csdn.net/xiaoxiaoxuanao/article/details/52573859)


### instanceof关键字
* [Java instanceof用法详解](https://blog.csdn.net/kuangay/article/details/81563992)

### static关键字
<details>
<summary>能否在运行时向 static final 类型的赋值</summary>
不可以，被static final修饰的变量只能在被定义的时候或者类的静态代码块中初始化，一旦赋值后就不能在改变了。static final相当于类常量，就是在类被加载进内存的时候就要为属性分配内存，static块就是类被加载的时候执行且被执行一次，所以可以在其中进行初始化。

</details>

<details>
<summary>public static void 写成 static public void会怎样?</summary>

一样的

</details>

<details>
<summary>static class 与 non static class的区别</summary>
 
* 内部静态类不需要有指向外部类的引用。但非静态内部类需要持有对外部类的引用。

* 非静态内部类能够访问外部类的静态和非静态成员。静态类不能访问外部类的非静态成员。他只能访问外部类的静态成员。

* 一个非静态内部类不能脱离外部类实体被创建，一个非静态内部类可以访问外部类的数据和方法，因为他就在外部类里面。

#### 生命周期（Lifecycle）：

* 静态方法（Static Method）与静态成员变量一样，属于类本身，在类装载的时候被装载到内存（Memory），不自动进行销毁，会一直存在于内存中，直到JVM关闭。

* 非静态方法（Non-Static Method）又叫实例化方法，属于实例对象，实例化后才会分配内存，必须通过类的实例来引用。不会常驻内存，当实例对象被JVM 回收之后，也跟着消失。

效率：静态方法的使用效率比非静态方法的效率高。

#### 线程安全

* 静态方法是共享代码段，静态变量是共享数据段。既然是“共享”就有并发（Concurrence）的问题。

* 非静态方法是针对确定的一个对象的，所以不会存在线程安全的问题。

静态方法和实例方法是一样的，在类型第一次被使用时加载。调用的速度基本上没有差别。
 
</details>

<details>
<summary>static 关键字是什么意思？Java中是否可以覆盖(override)一个private或者是static的方法，静态类型有什么特点？</summary>

static表示静态的意思，可用于修饰成员变量和成员函数，被静态修饰的成员函数只能访问静态成员，不可以访问非静态成员。静态是随着类的加载而加载的，因此可以直接用类进行访问。 重写是子类中的方法和子类继承的父类中的方法一样（函数名，参数，参数类型，反回值类型），但是子类中的访问权限要不低于父类中的访问权限。重写的前提是必须要继承，private修饰不支持继承，因此被私有的方法不可以被重写。在Java中，如果父类中含有一个静态方法，且在子类中也含有一个返回类型、方法名、参数列表均与之相同的静态方法，那么该子类实际上只是将父类中的该同名方法进行了隐藏，而非重写。换句话说，父类和子类中含有的其实是两个没有关系的方法，它们的行为也并不具有多态性。

</details> 

<details>
<summary>main() 方法为什么必须是静态的？能不能声明 main() 方法为非静态？</summary>

用static修饰的就是静态方法。静态方法不依靠对象而存在。其直接与类有关，只要包含在类中，就可以得到执行，而不一定依附于对象的存在而执行。因此，main方法作为程序的入口方法，在这之前是不可能有任何对象被建立的，也就在main之前包括main自身不可能是非静态方法。所以main方法一定是静态的，有类就行——从而得到执行，进而有更多静态或非静态方法得到执行。

</details> 

<details>
<summary>是否可以从一个静态（static）方法内部发出对非静态（non-static）方法的调用</summary>
 
 不可以，静态函数中不能访问非静态成员变量，只能访问静态变量。因为静态优先于对象存在.静态方法中更不可以出现this
 
</details>  

<details>
<summary>静态变量在什么时候加载？编译期还是运行期？静态代码块加载的时机呢？</summary>
 
 静态是随着类的加载而加载的，JVM的代码编译运行顺序是编译、类的加载到执行，属于二者的过渡期。静态代码块也是如此
 
</details>  


<details>
<summary>成员方法是否可以访问静态变量？为什么静态方法不能访问成员变量？</summary>
 
 成员方法中可以访问静态成员变量。

请看下面代码来确定程序的打印先后顺序：

```java

public class test {

    public static void main(String[] args) {
        new test();
    }

    static int num = 4;

    {
        num += 3;
        System.out.println(b);
    }

    int a = 5;

    {
        System.out.println(c);
    }

    test() {System.out.println(d);}

    static {System.out.println(a);}

    static void run() {System.out.println(e);}
}

```

执行顺序如下：

```java

public class test {        //1.第一步，准备加载类

    public static void main(String[] args) {
        new test();        //4.第四步，new一个类，但在new之前要处理匿名代码块
    }

    static int num = 4;    //2.第二步，静态变量和静态代码块的加载顺序由编写先后决定

    {
        num += 3;
        System.out.println(b);//5.第五步，按照顺序加载匿名代码块，代码块中有打印
    }

    int a = 5;                //6.第六步，按照顺序加载变量

    {  成员变量第三个
        System.out.println(c);    //7.第七步，按照顺序打印c
    }
              //如果将构造函数和构造代码块互换，依旧还是先执行构造代码块。
    test() {  //类的构造函数，第四个加载
        System.out.println(d);     //8.第八步，最后加载构造函数，完成对象的建立
    }

    static {             //3.第三步，静态块，然后执行静态代码块，因为有输出，故打印a
        System.out.println(a);
    }

    static void run()              //静态方法，调用的时候才加载 注意看，e没有加载
    {
        System.out.println(e);
    }
}

```

静态块（静态变量）——成员变量——构造方法——静态方法

1、静态代码块（只加载一次） 2、构造方法（创建一个实例就加载一次）3、静态方法需要调用才会执行.

如果类还没有被加载：

1、先执行父类的静态代码块和静态变量初始化，并且静态代码块和静态变量的执行顺序只跟代码中出现的顺序有关。

2、执行子类的静态代码块和静态变量初始化。

3、执行父类的实例变量初始化

4、执行父类的构造函数（有构造代码块则先执行构造代码块）

5、执行子类的实例变量初始化

6、执行子类的构造函数

如果类已经被加载：

则静态代码块和静态变量就不用重复执行，再创建类对象时，只执行与实例相关的变量初始化和构造方法。

补充构造代码块：给对象进行初始化。对象一建立就运行并且优先于构造函数。

构造代码块和构造函数的区别，**构造代码块是给所有对象进行统一初始化， 构造函数给对应的对象初始化。**
 
</details>  


* [Java static关键字详解](https://blog.csdn.net/kuangay/article/details/81485324)
* [static关键字的四种用法](https://blog.csdn.net/shuyizhi/article/details/79700054)
* [细说final关键字和static关键字](https://blog.csdn.net/m15732622413/article/details/53241231)
* [JAVA 堆栈 堆 方法区 静态区 final static 内存分配 详解](https://blog.csdn.net/peterwin1987/article/details/7571808)
* [静态方法中new对象](https://blog.csdn.net/weixin_41929877/article/details/80422009)
* [静态方法中不可直接new内部类实例对象问题](https://blog.csdn.net/aizhihua19900214/article/details/79714235)
* [Java里面 创建对象放在main方法外面为什么要用static修饰](https://www.zhihu.com/question/322912459/answer/674332458?utm_source=wechat_session&utm_medium=social&utm_oi=991812777480134656)
* [static方法能否被重写](https://blog.csdn.net/xiangwanpeng/article/details/52504274?locationNum=12&fps=1)

### strictfp关键字
* [Java语言中关键字strictfp的用途](https://blog.csdn.net/redv/article/details/326444)

### synchronized关键字
* [synchronized](https://blog.csdn.net/mingyundezuoan/article/details/79264644)
* [synchronized(.class) synchronized(Object ) synchronized(this) 区别](https://blog.csdn.net/u011996917/article/details/80783471)
* [Java语言中synchronized 修饰在 static方法和 非static方法的区别](https://blog.csdn.net/virgoboy2004/article/details/7585182)
* [Synchronized和volatile 的区别，两者使用的场景](https://blog.csdn.net/yinni11/article/details/81514082)
* [JVM - synchronized 关键字原理](https://blog.csdn.net/dao_wolf/article/details/85627828)
* [非常好   Synchronized的JVM底层实现](https://blog.csdn.net/Winston_Limf/article/details/80915304)
* [深入JVM锁机制1-synchronized](https://blog.csdn.net/chen77716/article/details/6618779)
* [深入JVM锁机制2-Lock](https://blog.csdn.net/chen77716/article/details/6641477)
* [JVM 中synchronized的底层实现原理解析](https://blog.csdn.net/HinstenyHisoka/article/details/80864378)
* [Java中的ReentrantLock和synchronized两种锁定机制的对比](https://blog.csdn.net/fw0124/article/details/6672522)
* [Java 中 synchronized的用法详解(四种用法)](https://www.jb51.net/article/74566.htm)
* [透彻理解Java中Synchronized（对象锁）和Static Synchronized（类锁）的区别](https://www.jb51.net/article/140175.htm)
* [java中synchronized（同步代码块和同步方法）详解及区别](https://www.jb51.net/article/106941.htm)
* [Java类锁、对象锁、私有锁冲突测试](https://www.jb51.net/article/56440.htm)
* [详解Java中synchronized关键字的死锁和内存占用问题](https://www.jb51.net/article/86666.htm)
* [详解synchronized与Lock的区别与使用](https://blog.csdn.net/u012403290/article/details/64910926)
* [java中volatile、synchronized和lock解析](https://blog.csdn.net/ztchun/article/details/60778950)
### this关键字
* [Java this关键字详解](https://blog.csdn.net/kuangay/article/details/81535701)

### transient关键字

<details>
<summary>transient变量有什么特点</summary>
 
 一旦变量被transient修饰，变量将不再是对象持久化的一部分，该变量内容在序列化后无法获得访问。
 
transient关键字只能修饰变量，而不能修饰方法和类。注意，本地变量是不能被transient关键字修饰的。变量如果是用户自定义类变量，则该类需要实现Serializable接口。

被transient关键字修饰的变量不能被序列化，一个静态变量不管是否被transient修饰，均不能被序列化。

</details> 

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

volatile是一个特殊的修饰符，只有成员变量才能使用它。在Java并发程序缺少同步类的情况下，多线程对成员变量的操作对其它线程是透明的。volatile变量可以保证下一个读取操作会在前一个写操作之后发生

<details>
<summary>volatile 变量是什么？volatile 变量和 atomic 变量有什么不同值</summary>
 
 Java语言提供了一种稍弱的同步机制，即volatile变量，用来确保将变量的更新操作通知到其他线程。当把变量声明为volatile类型后，编译器与运行时都会注意到这个变量是共享的，因此不会将该变量上的操作与其他内存操作一起重排序。volatile变量不会被缓存在寄存器或者对其他处理器不可见的地方，因此在读取volatile类型的变量时总会返回最新写入的值。
 
在访问volatile变量时不会执行加锁操作，因此也就不会使执行线程阻塞，因此volatile变量是一种比sychronized关键字更轻量级的同步机制。
 
</details> 

<details>
<summary>volatile 类型变量提供什么保证？能使得一个非原子操作变成原子操作吗?</summary>

volatile只提供了保证访问该变量时，每次都是从内存中读取最新值，并不会使用寄存器缓存该值——每次都会从内存中读取。而对该变量的修改，volatile并不提供原子性的保证。那么编译器究竟是直接修改内存的值，还是使用寄存器修改都符合volatile的定义。所以，一句话，volatile并不提供原子性的保证。

</details>

<details>
<summary>能创建 volatile 数组吗？</summary>

可以，volatile修饰的变量如果是对象或数组之类的，其含义是对象获数组的地址具有可见性，但是数组或对象内部的成员改变不具备可见性

</details>


<details>
<summary>能创建 volatile 数组吗？</summary>

可以，volatile修饰的变量如果是对象或数组之类的，其含义是对象获数组的地址具有可见性，但是数组或对象内部的成员改变不具备可见性

</details>

<details>
<summary> Java并发这块了解的怎么样？说说你对volatile关键字的理解</summary>


就我理解的而言，被volatile修饰的共享变量，就具有了以下两点特性：

1.保证了不同线程对该变量操作的内存可见性;

2.禁止指令重排序

</details>

<details>
<summary>能不能详细说下什么是内存可见性，什么又是重排序呢？</summary>

这个聊起来可就多了，我还是从Java内存模型说起吧。 Java虚拟机规范试图定义一种Java内存模型（JMM）,来屏蔽掉各种硬件和操作系统的内存访问差异，让Java程序在各种平台上都能达到一致的内存访问效果。简单来说，由于CPU执行指令的速度是很快的，但是内存访问的速度就慢了很多，相差的不是一个数量级，所以搞处理器的那群大佬们又在CPU里加了好几层高速缓存。 在Java内存模型里，对上述的优化又进行了一波抽象。JMM规定所有变量都是存在主存中的，类似于上面提到的普通内存，每个线程又包含自己的工作内存，方便理解就可以看成CPU上的寄存器或者高速缓存。所以线程的操作都是以工作内存为主，它们只能访问自己的工作内存，且工作前后都要把值在同步回主内存。 这么说得我自己都有些不清楚了，拿张纸画一下：



在线程执行时，首先会从主存中read变量值，再load到工作内存中的副本中，然后再传给处理器执行，执行完毕后再给工作内存中的副本赋值，随后工作内存再把值传回给主存，主存中的值才更新。 使用工作内存和主存，虽然加快的速度，但是也带来了一些问题。比如看下面一个例子：

i = i + 1;

假设i初值为0，当只有一个线程执行它时，结果肯定得到1，当两个线程执行时，会得到结果2吗？这倒不一定了。可能存在这种情况：

<a href="https://ibb.co/51ymY8T"><img src="https://i.ibb.co/9pL6wTn/2018120165342367.jpg" alt="2018120165342367" border="0"></a>
 
线程1： load i from 主存  // i = 0
    i + 1 // i = 1
线程2： load i from主存 // 因为线程1还没将i的值写回主存，所以i还是0
    i + 1 //i = 1
线程1: save i to 主存
线程2： save i to 主存
 

如果两个线程按照上面的执行流程，那么i最后的值居然是1了。如果最后的写回生效的慢，你再读取i的值，都可能是0，这就是缓存不一致问题。 下面就要提到你刚才问到的问题了，JMM主要就是围绕着如何在并发过程中如何处理原子性、可见性和有序性这3个特征来建立的，通过解决这三个问题，可以解除缓存不一致的问题。而volatile跟可见性和有序性都有关。


</details>

<details>
<summary>那你具体说说这并发三个特性呢？</summary>

1.原子性(Atomicity)： Java中，对基本数据类型的读取和赋值操作是原子性操作，所谓原子性操作就是指这些操作是不可中断的，要做一定做完，要么就没有执行。 比如：
 
i = 2;
j = i;
i++;
i = i + 1;
 

上面4个操作中，i=2是读取操作，必定是原子性操作，j=i你以为是原子性操作，其实吧，分为两步，一是读取i的值，然后再赋值给j,这就是2步操作了，称不上原子操作，i++和i = i + 1其实是等效的，读取i的值，加1，再写回主存，那就是3步操作了。所以上面的举例中，最后的值可能出现多种情况，就是因为满足不了原子性。 这么说来，只有简单的读取，赋值是原子操作，还只能是用数字赋值，用变量的话还多了一步读取变量值的操作。有个例外是，虚拟机规范中允许对64位数据类型(long和double)，分为2次32为的操作来处理，但是最新JDK实现还是实现了原子操作的。 JMM只实现了基本的原子性，像上面i++那样的操作，必须借助于synchronized和Lock来保证整块代码的原子性了。线程在释放锁之前，必然会把i的值刷回到主存的

2. 可见性(Visibility)： 说到可见性，Java就是利用volatile来提供可见性的。 当一个变量被volatile修饰时，那么对它的修改会立刻刷新到主存，当其它线程需要读取该变量时，会去内存中读取新值。而普通变量则不能保证这一点。 其实通过synchronized和Lock也能够保证可见性，线程在释放锁之前，会把共享变量值都刷回主存，但是synchronized和Lock的开销都更大

3. 有序性（Ordering） JMM是允许编译器和处理器对指令重排序的，但是规定了as-if-serial语义，即不管怎么重排序，程序的执行结果不能改变。比如下面的程序段：

```java

double pi = 3.14;  //A
double r = 1;    //B
double s= pi * r * r;//C
 
```

上面的语句，可以按照A->B->C执行，结果为3.14,但是也可以按照B->A->C的顺序执行，因为A、B是两句独立的语句，而C则依赖于A、B，所以A、B可以重排序，但是C却不能排到A、B的前面。JMM保证了重排序不会影响到单线程的执行，但是在多线程中却容易出问题。 比如这样的代码:

```java

int a = 0;
bool flag = false;
 
public void write() {
  a = 2;       //1
  flag = true;    //2
}
 
public void multiply() {
  if (flag) {     //3
    int ret = a * a;//4
  }
}

```

假如有两个线程执行上述代码段，线程1先执行write，随后线程2再执行multiply，最后ret的值一定是4吗？结果不一定：

<a href="https://ibb.co/w71LYMm"><img src="https://i.ibb.co/8bVBX6Z/2018120165556633.jpg" alt="2018120165556633" border="0"></a>

如图所示，write方法里的1和2做了重排序，线程1先对flag赋值为true，随后执行到线程2，ret直接计算出结果，再到线程1，这时候a才赋值为2,很明显迟了一步。 这时候可以为flag加上volatile关键字，禁止重排序，可以确保程序的有序性，也可以上重量级的synchronized和Lock来保证有序性,它们能保证那一块区域里的代码都是一次性执行完毕的。 另外，JMM具备一些先天的有序性,即不需要通过任何手段就可以保证的有序性，通常称为happens-before原则。<<JSR-133：Java Memory Model and Thread Specification>>定义了如下happens-before规则： 1.程序顺序规则： 一个线程中的每个操作，happens-before于该线程中的任意后续操作 2.监视器锁规则：对一个线程的解锁，happens-before于随后对这个线程的加锁 3.volatile变量规则： 对一个volatile域的写，happens-before于后续对这个volatile域的读 4.传递性：如果A happens-before B ,且 B happens-before C, 那么 A happens-before C 5.start()规则： 如果线程A执行操作ThreadB_start()(启动线程B) , 那么A线程的ThreadB_start()happens-before 于B中的任意操作 6.join()原则： 如果A执行ThreadB.join()并且成功返回，那么线程B中的任意操作happens-before于线程A从ThreadB.join()操作成功返回。 7.interrupt()原则： 对线程interrupt()方法的调用先行发生于被中断线程代码检测到中断事件的发生，可以通过Thread.interrupted()方法检测是否有中断发生 8.finalize()原则：一个对象的初始化完成先行发生于它的finalize()方法的开始 第1条规则程序顺序规则是说在一个线程里，所有的操作都是按顺序的，但是在JMM里其实只要执行结果一样，是允许重排序的，这边的happens-before强调的重点也是单线程执行结果的正确性，但是无法保证多线程也是如此。 第2条规则监视器规则其实也好理解，就是在加锁之前，确定这个锁之前已经被释放了，才能继续加锁。 第3条规则，就适用到所讨论的volatile，如果一个线程先去写一个变量，另外一个线程再去读，那么写入操作一定在读操作之前。 第4条规则，就是happens-before的传递性。 后面几条就不再一一赘述了。

</details>

<details>
<summary>volatile关键字如何满足并发编程的三大特性的？</summary>

那就要重提volatile变量规则： 对一个volatile域的写，happens-before于后续对这个volatile域的读。 这条再拎出来说，其实就是如果一个变量声明成是volatile的，那么当我读变量时，总是能读到它的最新值，这里最新值是指不管其它哪个线程对该变量做了写操作，都会立刻被更新到主存里，我也能从主存里读到这个刚写入的值。也就是说volatile关键字可以保证可见性以及有序性。 继续拿上面的一段代码举例：

```java
 
int a = 0;
bool flag = false;
 
public void write() {
  a = 2;       //1
  flag = true;    //2
}
 
public void multiply() {
  if (flag) {     //3
    int ret = a * a;//4
  }
}
 
```

这段代码不仅仅受到重排序的困扰，即使1、2没有重排序。3也不会那么顺利的执行的。假设还是线程1先执行write操作，线程2再执行multiply操作，由于线程1是在工作内存里把flag赋值为1，不一定立刻写回主存，所以线程2执行时，multiply再从主存读flag值，仍然可能为false，那么括号里的语句将不会执行。 如果改成下面这样：

```java

int a = 0;
volatile bool flag = false;
 
public void write() {
  a = 2;       //1
  flag = true;    //2
}
 
public void multiply() {
  if (flag) {     //3
    int ret = a * a;//4
  }
}
 
```

那么线程1先执行write,线程2再执行multiply。根据happens-before原则，这个过程会满足以下3类规则： 程序顺序规则：1 happens-before 2; 3 happens-before 4; (volatile限制了指令重排序，所以1 在2 之前执行) volatile规则：2 happens-before 3 传递性规则：1 happens-before 4 当写一个volatile变量时，JMM会把该线程对应的本地内存中的共享变量刷新到主内存 当读一个volatile变量时，JMM会把该线程对应的本地内存置为无效，线程接下来将从主内存中读取共享变量。

</details>

<details>
<summary>volatile的两点内存语义能保证可见性和有序性，但是能保证原子性吗？</summary>

首先我回答是不能保证原子性，要是说能保证，也只是对单个volatile变量的读/写具有原子性，但是对于类似volatile++这样的复合操作就无能为力了，比如下面的例子：

```java
 
public class Test {
  public volatile int inc = 0;
  
  public void increase() {
    inc++;
  }
  
  public static void main(String[] args) {
    final Test test = new Test();
    for(int i=0;i<10;i++){
      new Thread(){
        public void run() {
          for(int j=0;j<1000;j++)
            test.increase();
        };
      }.start();
    }
  
    while(Thread.activeCount()>1) //保证前面的线程都执行完
      Thread.yield();
    System.out.println(test.inc);
  }
 
```

按道理来说结果是10000，但是运行下很可能是个小于10000的值。有人可能会说volatile不是保证了可见性啊，一个线程对inc的修改，另外一个线程应该立刻看到啊！可是这里的操作inc++是个复合操作啊，包括读取inc的值，对其自增，然后再写回主存。 假设线程A，读取了inc的值为10，这时候被阻塞了，因为没有对变量进行修改，触发不了volatile规则。 线程B此时也读读inc的值，主存里inc的值依旧为10，做自增，然后立刻就被写回主存了，为11。 此时又轮到线程A执行，由于工作内存里保存的是10，所以继续做自增，再写回主存，11又被写了一遍。所以虽然两个线程执行了两次increase()，结果却只加了一次。 有人说，volatile不是会使缓存行无效的吗？但是这里线程A读取到线程B也进行操作之前，并没有修改inc值，所以线程B读取的时候，还是读的10。 又有人说，线程B将11写回主存，不会把线程A的缓存行设为无效吗？但是线程A的读取操作已经做过了啊，只有在做读取操作时，发现自己缓存行无效，才会去读主存的值，所以这里线程A只能继续做自增了。 综上所述，在这种复合操作的情景下，原子性的功能是维持不了了。但是volatile在上面那种设置flag值的例子里，由于对flag的读/写操作都是单步的，所以还是能保证原子性的。 要想保证原子性，只能借助于synchronized,Lock以及并发包下的atomic的原子操作类了，即对基本数据类型的 自增（加1操作），自减（减1操作）、以及加法操作（加一个数），减法操作（减一个数）进行了封装，保证这些操作是原子性操作。

</details>

<details>
<summary>那你知道volatile底层的实现机制？</summary>

果把加入volatile关键字的代码和未加入volatile关键字的代码都生成汇编代码，会发现加入volatile关键字的代码会多出一个lock前缀指令。 lock前缀指令实际相当于一个内存屏障，内存屏障提供了以下功能： 

1.重排序时不能把后面的指令重排序到内存屏障之前的位置 

2.使得本CPU的Cache写入内存 

3.写入动作也会引起别的CPU或者别的内核无效化其Cache，相当于让新写入的值对别的线程可见。

</details>

<details>
<summary>你在哪里会使用到volatile，举两个例子呢？</summary>

1.状态量标记，就如上面对flag的标记，我重新提一下：

```java

int a = 0;
volatile bool flag = false;
 
public void write() {
  a = 2;       //1
  flag = true;    //2
}
 
public void multiply() {
  if (flag) {     //3
    int ret = a * a;//4
  }
}
 
```

这种对变量的读写操作，标记为volatile可以保证修改对线程立刻可见。比synchronized,Lock有一定的效率提升。 2.单例模式的实现，典型的双重检查锁定（DCL）

```java

class Singleton{
  private volatile static Singleton instance = null;
  
  private Singleton() {
  
  }
  
  public static Singleton getInstance() {
    if(instance==null) {
      synchronized (Singleton.class) {
        if(instance==null)
          instance = new Singleton();
      }
    }
    return instance;
  }
}
 
```

这是一种懒汉的单例模式，使用时才创建对象，而且为了避免初始化操作的指令重排序，给instance加上了volatile

</details>

* [volatile 关键字实现原理》汇编层面的讲解，推荐](http://www.importnew.com/27002.html)
* [全面理解Java内存模型(JMM)及volatile关键字](https://blog.csdn.net/javazejian/article/details/72772461)
* [Java中volatile关键字的最全总结](https://blog.csdn.net/u012723673/article/details/80682208)
* [绝对深度解析volatile关键字的实现原理（大部分内容整理于其他博客](https://blog.csdn.net/yanshuanche3765/article/details/78405872)
* [volatile与synchronized 同步原理基础讲解](https://blog.csdn.net/lxlmycsdnfree/article/details/70256031)
* [从volatile和synchronized的底层实现原理看Java虚拟机对锁优化所做的努力](https://blog.csdn.net/lxlmycsdnfree/article/details/70256031)
* [Java中volatile关键字的最全总结](https://blog.csdn.net/u012723673/article/details/80682208)
* [Synchronized和volatile 的区别，两者使用的场景](https://blog.csdn.net/yinni11/article/details/81514082)
* [内存屏障和 volatile 语义](http://www.importnew.com/29860.html)
* [面试必问的 volatile，你了解多少？](http://www.importnew.com/27863.html)
* [就是要你懂 Java 中 volatile 关键字实现原理](http://www.importnew.com/27002.html)
* [详解Java面试官最爱问的volatile关键字](https://www.jb51.net/article/133313.htm) 
* [java中volatile、synchronized和lock解析](https://blog.csdn.net/ztchun/article/details/60778950)

### throw and throws关键字

throw是指的语句抛出一个异常，throws指的是声明方法可能抛出的异常类型

### Java 有几种修饰符？分别用来修饰什么

<details>
<summary>类的修饰符</summary>

public可以在其他任何类中使用，默认为统一包下的任意类。

abstract抽象类，不能被实例化，可以包含抽象方法，抽象方法没有被实现，无具体功能，只能衍生子类。

final不能被继承。

</details>

<details>
<summary>成员变量</summary>

static类变量：一个类所拥有的变量，不是类的每个实例有的变量。类变量是指不管类创建了多少对象，系统仅在第一次调用类的时候为类变量分配内存，所有对象共享该类的类变量，因此可以通过类本身或者某个对象来访问类变量。

final：常量。

volatile：声明一个可能同时被并存运行的几个线程所控制和修改的变量。

abstract：只有声明部分，方法体为空，具体在子类中完成。

transient：（过度修饰符）指定该变量是系统保留，暂无特别作用的临时性变量。
</details>

<details>
<summary>方法修饰符</summary>

public（公共控制符）

private（私有控制符）指定此方法只能有自己类等方法访问，其他的类不能访问（包括子类）

protected（保护访问控制符）指定该方法可以被它的类和子类进行访问。

final，指定该方法不能被重载。

static，指定不需要实例化就可以激活的一个方法。

synchronize，同步修饰符，在多个线程中，该修饰符用于在运行前，对他所属的方法加锁，以防止其他线程的访问，运行结束后解锁。

native，本地修饰符。指定此方法的方法体是用其他语言在程序外部编写的。

</details>

### switch关键字

<details>
<summary>1、switch 语句中的表达式可以是什么类型数据？</summary>
 
 switch(A),括号中A的取值可以是byte、short、int、char、String，还有枚举类型。
 
</details>


<details>
<summary>2、switch 是否能作用在byte 上，是否能作用在long 上，是否能作用在String上？</summary>
 
Java 7之前，switch后面的括号里面只能放int类型的值，注意是只能放int类型，但是放byte，short，char类型的也可以，是因为byte，short，shar可以自动提升（自动类型转换）为int，不是说就可以放它们，说白了，你放的byte，short，shar类型，然后他们会自动转换为int类型（宽化，自动转换并且安全），其实最后放的还是int类型。String可以了，但是long仍然不行。

1.小的往大的转换(宽化)，自动转换，有些时候就会自动提升为大的类型，比如switch中
2.大的往小的转换(窄化)必须强制类型转换所以long不行，要想行就得强转如（int）long。同理，float、double也是不行的，要想行就强转。
 
</details>

### Try-with-resources
[Java 7中的Try-with-resources](http://ifeve.com/java-7中的try-with-resources/)
[揭秘try-catch-finally在JVM底层都干了些啥？](https://mp.weixin.qq.com/s/Cmw-8ej1Ze5UlSaFSumn2A)

### Serializable
[Java中序列化接口Serializable接口的作用](https://blog.csdn.net/xilihong816/article/details/78893032)
