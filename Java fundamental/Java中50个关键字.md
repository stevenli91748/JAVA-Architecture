

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
 * [《volatile 关键字实现原理》汇编层面的讲解，推荐！](http://www.importnew.com/27002.html)
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
