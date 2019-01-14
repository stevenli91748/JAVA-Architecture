
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
<summary>22. 如何理解内存泄漏问题？有哪些情况会导致内存泄露？如何解决？</summary>



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



