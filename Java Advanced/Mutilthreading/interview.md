[2020年Java多线程与并发系列22道高频面试题解析](https://mp.weixin.qq.com/s?__biz=MzIwNjg4MzY4NA==&mid=2247484552&idx=1&sn=6b0acf6d7501997d5a145db92f78a486&chksm=971b9a83a06c1395addfbfda02ed0ef9790f2a730a39d228119ef5abc2bb0fb4bf1fc42a1190&scene=21#wechat_redirect)|
---|

这也是必问的一块了。因为三年工作经验，所以基本上不会再问你怎么实现多线程了，会问得深入一些比如说Thread和Runnable的区别和联系、多次start一个线程会怎么样、线程有哪些状态。当然这只是最基本的，出乎意料地，几次面试几乎都被同时问到了一个问题，问法不尽相同，总结起来是这么一个意思：

假如有Thread1、Thread2、ThreaD3、Thread4四条线程分别统计C、D、E、F四个盘的大小，所有线程都统计完毕交给Thread5线程去做汇总，应当如何实现？

---
<details>
<summary>进程的缺点？为什么要发明多线程？</summary>
  
多进程让多任务能够并行处理任务，但本身还有缺点，单个进程内部只能串行处理，而实际上很多进程内部的子任务并不要求是严格按照时间顺序来执行的，也需要并行处理。

例如，一个餐馆管理进程，排位、点菜、买单、服务员调度等子任务必须能够并行处理，否则就会出现某个客人买单时间比较长（比如说信用卡刷不出来），其他客人都不能点菜的情况。为了解决这个问题，人们又发明了线程，线程是进程内部的子任务，但这些子任务都共享同一份进程数据。为了保证数据的准确性，又发明了互斥锁机制。有了多线程后，**操作系统调度的最小单位就变成了线程，而进程变成了操作系统分配资源的最小单位。**

</details>


<details>
<summary>什么是线程？</summary>
  
进程：每个进程都有独立的代码和数据空间（进程上下文），进程间的切换会有较大的开销，一个进程包含1–n个线程。（进程是资源分配的最小单位）

线程：同一类线程共享代码和数据空间，每个线程有独立的运行栈和程序计数器(PC)，线程切换开销小。（线程是cpu调度的最小单位）

</details>

<details>
<summary>什么是线程优先级？</summary>

 线程的优先级不能小于l 也不能大于10 ，如果指定的线程优先级大于线程所在group 的优先级，那么指定的优先级将会失效，取而代之的是group 的最大优先级
线程默认的优先级和它的父类保持一致，一般情况下都是5 ，因为main 线程的优先级就是5 ，所以它派生出来的线程都是5

</details>

<details>
<summary>为什么要使用多线程？或者说使用多线程的好处</summary>
  
1）发挥多核CPU的优势

随着工业的进步，现在的笔记本、台式机乃至商用的应用服务器至少也都是双核的，4核、8核甚至16核的也都不少见，如果是单线程的程序，那么在双核CPU上就浪费了50%，在4核CPU上就浪费了75%。单核CPU上所谓的”多线程”那是假的多线程，同一时间处理器只会处理一段逻辑，只不过线程之间切换得比较快，看着像多个线程”同时”运行罢了。多核CPU上的多线程才是真正的多线程，它能让你的多段逻辑同时工作，多线程，可以真正发挥出多核CPU的优势来，达到充分利用CPU的目的。

（2）防止阻塞

从程序运行效率的角度来看，单核CPU不但不会发挥出多线程的优势，反而会因为在单核CPU上运行多线程导致线程上下文的切换，而降低程序整体的效率。但是单核CPU我们还是要应用多线程，就是为了防止阻塞。试想，如果单核CPU使用单线程，那么只要这个线程阻塞了，比方说远程读取某个数据吧，对端迟迟未返回又没有设置超时时间，那么你的整个程序在数据返回回来之前就停止运行了。多线程可以防止这个问题，多条线程同时运行，哪怕一条线程的代码执行读取数据阻塞，也不会影响其它任务的执行。

（3）便于建模

这是另外一个没有这么明显的优点了。假设有一个大的任务A，单线程编程，那么就要考虑很多，建立整个程序模型比较麻烦。但是如果把这个大的任务A分解成几个小任务，任务B、任务C、任务D，分别
建立程序模型，并通过多线程分别运行这几个任务，那就简单很多了。

</details>

<details>
<summary>线程和进程有什么区别？</summary>

线程是进程的子集，一个进程可以有很多线程，每条线程并行执行不同的任务。不同的进程使用不同的内存空间，而所有的线程共享一片相同的内存空间。别把它和栈内存搞混，每个线程都拥有单独的栈内存用来存储本地数据。更多详细信息请点击这里。

</details>

<details>
<summary>多进程多线程并不是真正的并行处理？</summary>
  
多进程多线程虽然让多任务并行处理的性能大大提升，但本质上还是分时系统，并不能做到时间上真正的并行。解决这个问题的方式显而易见，就是让多个CPU 能够同时执行计算任务，从而实现真正意义上的多任务并行。目前这样的解决方案有3 种： SMP C 对称多处理器结构，Symmetric Multi -Processor ）、NUMA (Non-Uniform Memory Access ，非一致存储访问结构）、MPP (Massive Parallel Processi吨，海量并行处理结构） 。其中SMP 是我们最常见的，目前流行
的多核处理器就是SMP 方案。

</details>


<details>
<summary>如何在Java中实现线程？</summary>
  
在语言层面有两种方式。java.lang.Thread 类的实例就是一个线程但是它需要调用java.lang.Runnable接口来执行，由于线程类本身就是调用的Runnable接口所以你可以继承java.lang.Thread 类或者直接调用Runnable接口来重写run()方法实现线程。

</details>

<details>
<summary>Context ClassLoader 线程上下文类加载器？</summary>

[Context ClassLoader 线程上下文类加载器](https://blog.csdn.net/dw147258dw/article/details/93847568)  

</details>

<details>
<summary>用Runnable还是Thread？</summary>

显然是用Runnable更好，实现Runnable接口比继承Thread类所具有的优势：

1）：适合多个相同的程序代码的线程去处理同一个资源

2）：可以避免java中的单继承的限制

3）：增加程序的健壮性，代码可以被多个线程共享，代码和数据独立

4）：线程池只能放入实现Runable或callable类线程，不能直接放入继承Thread的类

</details>

<details>
<summary>一个线程的生命周期有哪几种状态？它们之间如何流转的？</summary>

**NEW：**毫无疑问表示的是刚创建的线程，还没有开始启动。

**RUNNABLE:** 表示线程已经触发 start()方式调用，线程正式启动，线程处于运行中

状态。

**BLOCKED：**表示线程阻塞，等待获取锁，如碰到 synchronized、lock 等关键字等占

用临界区的情况，一旦获取到锁就进行 RUNNABLE 状态继续运行。

**WAITING：**表示线程处于无限制等待状态，等待一个特殊的事件来重新唤醒，如通过

wait()方法进行等待的线程等待一个 notify()或者 notifyAll()方法，通过 join()方法进

行等待的线程等待目标线程运行结束而唤醒，一旦通过相关事件唤醒线程，线程就进

入了 RUNNABLE 状态继续运行。

**TIMED_WAITING：**表示线程进入了一个有时限的等待，如 sleep(3000)，等待 3 秒

后线程重新进行 RUNNABLE 状态继续运行。

**TERMINATED：**表示线程执行完毕后，进行终止状态。

需要注意的是，一旦线程通过 start 方法启动后就再也不能回到初始 NEW 状态，线

程终止后也不能再回到 RUNNABLE 状态

</details>

<details>
<summary>Thread 类中的start() 和 run() 方法有什么区别？</summary>
  
这个问题经常被问到，但还是能从此区分出面试者对Java线程模型的理解程度。start()方法被用来启动新创建的线程，而且start()内部调用了run()方法，这和直接调用run()方法的效果不一样。当你调用run()方法的时候，只会是在原来的线程中调用，没有新的线程启动，start()方法才会启动新线程。

</details>

<details>
<summary>线程中的 wait()和 sleep()方法有什么区别？</summary>
  
这个问题常问，sleep 方法和 wait 方法都可以用来放弃 CPU 一定的时间，不同点在于如果

线程持有某个对象的监视器，sleep 方法不会放弃这个对象的监视器，wait 方法会放弃这个

对象的监视器

</details>

<details>
<summary>多线程同步有哪几种方法？</summary>

Synchronized 关键字，Lock 锁实现，分布式锁等。
  
</details>

<details>
<summary>Java中Runnable和Callable有什么不同？</summary>

Runnable和Callable都代表那些要在不同的线程中执行的任务。Runnable从JDK1.0开始就有了，Callable是在JDK1.5增加的。它们的主要区别是Callable的 call() 方法可以返回值和抛出异常，而Runnable的run()方法没有这些功能。Callable可以返回装载有计算结果的Future对象。

</details>

<details>
<summary>多线程的几种实现方式？</summary>

（1）继承Thread类

（2）实现Runnable接口

（3）、使用 ExecutorService、Callable、Future 实现有返回结果的多线程
```java

//继承Thread类
class Thread1 extends Thread{
    private String name;
    public Thread1(String name) {
       this.name=name;
    }
    public void run() {
        for (int i = 0; i < 5; i++) {
            System.out.println(name + "运行  :  " + i);
            try {
                sleep((int) Math.random() * 10);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
public class Main {
    public static void main(String[] args) {
        Thread1 mTh1=new Thread1("A");
        Thread1 mTh2=new Thread1("B");
        mTh1.start();
        mTh2.start();
    }
}

```

说明：
程序启动运行main时候，java虚拟机启动一个进程，主线程main在main()调用时候被创建。随着调用两个对象的start方法，另外两个线程也启动了，这样，整个应用就在多线程下运行。

注意：start()方法的调用后并不是立即执行多线程代码，而是使得该线程变为可运行态（Runnable），什么时候运行是由操作系统决定的。从程序运行的结果可以发现，多线程程序是乱序执行。因此，只有乱序执行的代码才有必要设计为多线程。Thread.sleep()方法调用目的是不让当前线程独自霸占该进程所获取的CPU资源，以留出一定时间给其他线程执行的机会。实际上所有的多线程代码执行顺序都是不确定的，每次执行的结果都是随机的。

实现接口的方式比继承类的方式更灵活，也能减少程序之间的耦合度，面向接口编程也是设计模式6大原则的核心。

```java

//实现Runnable接口
class Thread2 implements Runnable{
    private String name;
    public Thread2(String name) {
        this.name=name;
    }

    @Override
    public void run() {
          for (int i = 0; i < 5; i++) {
                System.out.println(name + "运行  :  " + i);
                try {
                    Thread.sleep((int) Math.random() * 10);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
    }
}

public class Main {
    public static void main(String[] args) {
        new Thread(new Thread2("C")).start();
        new Thread(new Thread2("D")).start();
    }
}

```

说明：
Thread2类通过实现Runnable接口，使得该类有了多线程类的特征。run（）方法是多线程程序的一个约定。所有的多线程代码都在run方法里面。Thread类实际上也是实现了Runnable接口的类。

在启动的多线程的时候，需要先通过Thread类的构造方法Thread(Runnable target)构造出对象，然后调用Thread对象的start()方法来运行多线程代码。
实际上所有的多线程代码都是通过运行Thread的start()方法来运行的。因此，不管是扩展Thread类还是实现Runnable接口来实现多线程，最终还是通过Thread的对象的API来控制线程的，熟悉Thread类的API是进行多线程编程的基础。

</details>

<details>
<summary>Java中CyclicBarrier 和 CountDownLatch有什么不同？</summary>
  
CyclicBarrier 和 CountDownLatch 都可以用来让一组线程等待其它线程。与 CyclicBarrier 不同的是，CountdownLatch 不能重新使用
</details>

<details>
<summary>Java内存模型是什么？</summary>
  
Java内存模型规定和指引Java程序在不同的内存架构、CPU和操作系统间有确定性地行为。它在多线程的情况下尤其重要。Java内存模型对一个线程所做的变动能被其它线程可见提供了保证，它们之间是先行发生关系。这个关系定义了一些规则让程序员在并发编程时思路更清晰。比如，先行发生关系确保了：

线程内的代码能够按先后顺序执行，这被称为程序次序规则。
对于同一个锁，一个解锁操作一定要发生在时间上后发生的另一个锁定操作之前，也叫做管程锁定规则。
前一个对volatile的写操作在后一个volatile的读操作之前，也叫volatile变量规则。
一个线程内的任何操作必需在这个线程的start()调用之后，也叫作线程启动规则。
一个线程的所有操作都会在线程终止之前，线程终止规则。
一个对象的终结操作必需在这个对象构造完成之后，也叫对象终结规则。
可传递性
我强烈建议大家阅读《Java并发编程实践》第十六章来加深对Java内存模型的理解。

</details>

<details>
<summary>Java中的volatile 变量是什么？</summary>
  
volatile是一个特殊的修饰符，只有成员变量才能使用它。在Java并发程序缺少同步类的情况下，多线程对成员变量的操作对其它线程是透明的。volatile变量可以保证下一个读取操作会在前一个写操作之后发生，就是上一题的volatile变量规则

</details>

<details>
<summary>什么是线程安全？Vector是一个线程安全类吗？</summary>
  
线程安全就是多线程访问时，采用了加锁机制，当一个线程访问该类的某个数据时，进行保护，其他线程不能进行访问直到该线程读取完，其他线程才可使用。不会出现数据不一致或者数据污染。线程不安全就是不提供数据访问保护，有可能出现多个线程先后更改数据造成所得到的数据是脏数据
还有一种通俗的解释：如果你的代码在多线程下执行和在单线程下执行永远都能获得一样的结果，那么你的代码就是线程安全的。

这个问题有值得一提的地方，就是线程安全也是有几个级别的：

（1）不可变

像String、Integer、Long这些，都是final类型的类，任何一个线程都改变不了它们的值，要改变除非新创建一个，因此这些不可变对象不需要任何同步手段就可以直接在多线程环境下使用

（2）绝对线程安全

不管运行时环境如何，调用者都不需要额外的同步措施。要做到这一点通常需要付出许多额外的代价，Java中标注自己是线程安全的类，实际上绝大多数都不是线程安全的，不过绝对线程安全的类，Java中也有，比方说CopyOnWriteArrayList、CopyOnWriteArraySet

（3）相对线程安全

相对线程安全也就是我们通常意义上所说的线程安全，像Vector这种，add、remove方法都是原子操作，不会被打断，但也仅限于此，如果有个线程在遍历某个Vector、有个线程同时在add这个Vector，99%的情况下都会出现ConcurrentModificationException，也就是fail-fast机制。

（4）线程非安全

这个就没什么好说的了，ArrayList、LinkedList、HashMap等都是线程非安全的类.

</details>

<details>
<summary>JVector, SimpleDateFormat是线程安全类吗？</summary>
  
SimpleDateFormate不是线程安全的，如果我们把SimpleDateFormat定义成static成员变量，那么多个thread之间会共享这个sdf对象， 所以Calendar对象也会共享。假定线程A和线程B都进入了parse(text, pos)方法， 线程B执行到calendar.clear()后，线程A执行到calendar.getTime(), 那么就会有问题。

Vector 在方法上使用同步这样做本身没有解决多线程问题，反而，在引入了概念的混乱的同时，导致性能问题，因为 synchronized 的开销是巨大的：阻止编译器乱序 。详情请看《Vector是线程安全吗？》。看下面Vector代码就知道了：

```java

if (!vector.contains(element))
    vector.add(element);
    ...
}

```

这是经典的 put-if-absent 情况，尽管contains, add方法都正确地同步了，但作为 vector之外的使用环境，仍然存在 race condition: 因为虽然条件判断if (!vector.contains(element))与方法调用 vector.add(element); 都是原子性的操作 (atomic)，但在 if 条件判断为真后，那个用来访问vector.contains方法的锁已经释放，在即将的 vector.add方法调用 之间有间隙，在多线程环境中，完全有可能被其他线程获得 vector的 lock并改变其状态, 此时当前线程的vector.add(element); 正在等待（只不过我们不知道而已）。只有当其他线程释放了 vector 的 lock 后，vector.add(element); 继续，但此时它已经基于一个错误的假设了。单个的方法synchronized 了并不代表组合（compound）的方法调用具有原子性，使 compound actions成为线程安全的可能解决办法之一还是离不开intrinsic lock(这个锁应该是 vector的，但由 client 维护)：

```java

// Vector v = ...
   public  boolean putIfAbsent(E x) {
       synchronized(v) {
           boolean absent = !contains(x);
           if (absent) {
               add(x);
           }
       }
       return absent;
   }
   
```

所以，正确地回答那个“愚蠢”的问题是：Vector 和 ArrayList 实现了同一接口 List, 但所有的 Vector 的方法都具有 synchronized 关键修饰。但对于复合操作，Vector仍然需要进行同步处理。

</details>

<details>
<summary>Java中什么是竞态条件？ 举个例子说明。</summary>
  
竞态条件会导致程序在并发情况下出现一些bugs。多线程对一些资源的竞争的时候就会产生竞态条件，如果首先要执行的程序竞争失败排到后面执行了，那么整个程序就会出现一些不确定的bugs。这种bugs很难发现而且会重复出现，因为线程间的随机竞争。一个例子就是无序处理，

</details>

<details>
<summary>Java中如何停止一个线程？</summary>
  
ava提供了很丰富的API但没有为停止线程提供API。JDK 1.0本来有一些像stop(), suspend() 和 resume()的控制方法但是由于潜在的死锁威胁因此在后续的JDK版本中他们被弃用了，之后Java API的设计者就没有提供一个兼容且线程安全的方法来停止一个线程。当run() 或者 call() 方法执行完的时候线程会自动结束,如果要手动结束一个线程，你可以用volatile 布尔变量来退出run()方法的循环或者是取消任务来中断线程。

</details>

<details>
<summary> 一个线程运行时发生异常会怎样？</summary>
  
这是我在一次面试中遇到的一个很刁钻的Java面试题, 简单的说，如果异常没有被捕获该线程将会停止执行。Thread.UncaughtExceptionHandler是用于处理未捕获异常造成线程突然中断情况的一个内嵌接口。当一个未捕获异常将造成线程中断的时候JVM会使用Thread.getUncaughtExceptionHandler()来查询线程的UncaughtExceptionHandler并将线程和异常作为参数传递给handler的uncaughtException()方法进行处理。

</details>

<details>
<summary>如何在两个线程间共享数据？</summary>
  
你可以通过共享对象来实现这个目的，或者是使用像阻塞队列这样并发的数据结构。这篇教程《Java线程间通信》(涉及到在两个线程间共享对象)用wait和notify方法实现了生产者消费者模型。

</details>

<details>
<summary>Java中notify 和 notifyAll有什么区别？</summary>
  
这又是一个刁钻的问题，因为多线程可以等待单监控锁，Java API 的设计人员提供了一些方法当等待条件改变的时候通知它们，但是这些方法没有完全实现。notify()方法不能唤醒某个具体的线程，所以只有一个线程在等待的时候它才有用武之地。而notifyAll()唤醒所有线程并允许他们争夺锁确保了至少有一个线程能继续运行。

</details>

<details>
<summary>为什么wait, notify 和 notifyAll这些方法不在thread类里面？</summary>

这是个设计相关的问题，它考察的是面试者对现有系统和一些普遍存在但看起来不合理的事物的看法。回答这些问题的时候，你要说明为什么把这些方法放在Object类里是有意义的，还有不把它放在Thread类里的原因。一个很明显的原因是JAVA提供的锁是对象级的而不是线程级的，每个对象都有锁，通过线程获得。如果线程需要等待某些锁那么调用对象中的wait()方法就有意义了。如果wait()方法定义在Thread类中，线程正在等待的是哪个锁就不明显了。简单的说，由于wait，notify和notifyAll都是锁级别的操作，所以把他们定义在Object类中因为锁属于对象。你也可以查看这篇文章了解更多。

</details>


<details>
<summary>什么是FutureTask？</summary>
  
在Java并发程序中FutureTask表示一个可以取消的异步运算。它有启动和取消运算、查询运算是否完成和取回运算结果等方法。只有当运算完成的时候结果才能取回，如果运算尚未完成get方法将会阻塞。一个FutureTask对象可以对调用了Callable和Runnable的对象进行包装，由于FutureTask也是调用了Runnable接口所以它可以提交给Executor来执行。

</details>

<details>
<summary>Java中interrupted 和 isInterruptedd方法的区别？</summary>
  
interrupted() 和 isInterrupted()的主要区别是前者会将中断状态清除而后者不会。Java多线程的中断机制是用内部标识来实现的，调用Thread.interrupt()来中断一个线程就会设置中断标识为true。当中断线程调用静态方法Thread.interrupted()来检查中断状态时，中断状态会被清零。而非静态方法isInterrupted()用来查询其它线程的中断状态且不会改变中断状态标识。简单的说就是任何抛出InterruptedException异常的方法都会将中断状态清零。无论如何，一个线程的中断状态有有可能被其它线程调用中断来改变。

</details>

<details>
<summary>为什么wait和notify方法要在同步块中调用？</summary>
  
主要是因为Java API强制要求这样做，如果你不这么做，你的代码会抛出IllegalMonitorStateException异常。还有一个原因是为了避免wait和notify之间产生竞态条件。

</details>

<details>
<summary>为什么你应该在循环中检查等待条件?</summary>

处于等待状态的线程可能会收到错误警报和伪唤醒，如果不在循环中检查等待条件，程序就会在没有满足结束条件的情况下退出。因此，当一个等待线程醒来时，不能认为它原来的等待状态仍然是有效的，在notify()方法调用之后和等待线程醒来之前这段时间它可能会改变。这就是在循环中使用wait()方法效果更好的原因，你可以在Eclipse中创建模板调用wait和notify试一试。如果你想了解更多关于这个问题的内容，我推荐你阅读《Effective Java》这本书中的线程和同步章节。

</details>

<details>
<summary>Java中的同步集合与并发集合有什么区别？</summary>
  
同步集合与并发集合都为多线程和并发提供了合适的线程安全的集合，不过并发集合的可扩展性更高。在Java1.5之前程序员们只有同步集合来用且在多线程并发的时候会导致争用，阻碍了系统的扩展性。Java5介绍了并发集合像ConcurrentHashMap，不仅提供线程安全还用锁分离和内部分区等现代技术提高了可扩展性。更多内容详见答案。

</details>

<details>
<summary>Java中堆和栈有什么不同？</summary>
  
为什么把这个问题归类在多线程和并发面试题里？因为栈是一块和线程紧密相关的内存区域。每个线程都有自己的栈内存，用于存储本地变量，方法参数和栈调用，一个线程中存储的变量对其它线程是不可见的。而堆是所有线程共享的一片公用内存区域。对象都在堆里创建，为了提升效率线程会从堆中弄一个缓存到自己的栈，如果多个线程使用该变量就可能引发问题，这时volatile 变量就可以发挥作用了，它要求线程从主存中读取变量的值。

</details>

<details>
<summary>什么是线程池？ 为什么要使用它？</summary>
  
创建线程要花费昂贵的资源和时间，如果任务来了才创建线程那么响应时间会变长，而且一个进程能创建的线程数有限。为了避免这些问题，在程序启动的时候就创建若干线程来响应处理，它们被称为线程池，里面的线程叫工作线程。从JDK1.5开始，Java API提供了Executor框架让你可以创建不同的线程池。比如单线程池，每次处理一个任务；数目固定的线程池或者是缓存线程池（一个适合很多生存期短的任务的程序的可扩展线程池）
</details>

<details>
<summary>如何写代码来解决生产者消费者问题？</summary>
  
在现实中你解决的许多线程问题都属于生产者消费者模型，就是一个线程生产任务供其它线程进行消费，你必须知道怎么进行线程间通信来解决这个问题。比较低级的办法是用wait和notify来解决这个问题，比较赞的办法是用Semaphore 或者 BlockingQueue来实现生产者消费者模型，这篇教程有实现它。

</details>

<details>
<summary>如何避免死锁？</summary>
  
Java多线程中的死锁
死锁是指两个或两个以上的进程在执行过程中，因争夺资源而造成的一种互相等待的现象，若无外力作用，它们都将无法推进下去。这是一个严重的问题，因为死锁会让你的程序挂起无法完成任务，死锁的发生必须满足以下四个条件：

互斥条件：一个资源每次只能被一个进程使用。
请求与保持条件：一个进程因请求资源而阻塞时，对已获得的资源保持不放。
不剥夺条件：进程已获得的资源，在末使用完之前，不能强行剥夺。
循环等待条件：若干进程之间形成一种头尾相接的循环等待资源关系。
避免死锁最简单的方法就是阻止循环等待条件，将系统中所有的资源设置标志位、排序，规定所有的进程申请资源必须以一定的顺序（升序或降序）做操作来避免死锁。这篇教程有代码示例和避免死锁的讨论细节。

</details>

<details>
<summary>Java中活锁和死锁有什么区别？</summary>
  
这是上题的扩展，活锁和死锁类似，不同之处在于处于活锁的线程或进程的状态是不断改变的，活锁可以认为是一种特殊的饥饿。一个现实的活锁例子是两个人在狭小的走廊碰到，两个人都试着避让对方好让彼此通过，但是因为避让的方向都一样导致最后谁都不能通过走廊。简单的说就是，活锁和死锁的主要区别是前者进程的状态可以改变但是却不能继续执行。

</details>

<details>
<summary>怎么检测一个线程是否拥有锁？</summary>
  
我一直不知道我们竟然可以检测一个线程是否拥有锁，直到我参加了一次电话面试。在java.lang.Thread中有一个方法叫holdsLock()，它返回true如果当且仅当当前线程拥有某个具体对象的锁。你可以查看这篇文章了解更多。

</details>

<details>
<summary>怎么控制同一时间只有 3 个线程运行？</summary>
  
用 Semaphore。

</details>

<details>
<summary>新建 T1、T2、T3 三个线程，如何保证它们按顺序执行？</summary>
  
用 join 方法。

</details>

<details>
<summary>线程怎样拿到返回结果</summary>
  
实现Callable 接口。

</details>

<details>
<summary>多线程之间如何进行通信</summary>
  
wait/notify

</details>

<details>
<summary>多线程同步有哪几种方法？</summary>
  
Synchronized 关键字，Lock 锁实现，分布式锁等。

</details>

<details>
<summary>为什么要使用线程池？</summary>
  
我们知道不用线程池的话，每个线程都要通过 new Thread(xxRunnable).start()的方式来创建并运行一个线程，线程少的话这不会是问题，而真实环境可能会开启多个线程让系统和程序达到最佳效率，当线程数达到一定数量就会耗尽系统的 CPU 和内存资源，也会造成 GC 频繁收集和停顿，因为每次创建和销毁一个线程都是要消耗系统资源的，如果为每个任务都创建线程这无疑是一个很大的性能瓶颈。所以，线程池中的线程复用极大节省了系统资源，当线程一段时间不再有任务处理时它也会自动销毁，而不会长驻内存。

</details>

<details>
<summary>常用的几种线程池并讲讲其中的工作原理。</summary>
  
什么是线程池？

很简单，简单看名字就知道是装有线程的池子，我们可以把要执行的多线程交给线程池来处

理，和连接池的概念一样，通过维护一定数量的线程池来达到多个线程的复用。

线程池的好处

我们知道不用线程池的话，每个线程都要通过 new Thread(xxRunnable).start()的方式来

创建并运行一个线程，线程少的话这不会是问题，而真实环境可能会开启多个线程让系统和

程序达到最佳效率，当线程数达到一定数量就会耗尽系统的 CPU 和内存资源，也会造成 GC

频繁收集和停顿，因为每次创建和销毁一个线程都是要消耗系统资源的，如果为每个任务都创建线程这无疑是一个很大的性能瓶颈。所以，线程池中的线程复用极大节省了系统资源，

当线程一段时间不再有任务处理时它也会自动销毁，而不会长驻内存。

线程池核心类

在 java.util.concurrent 包中我们能找到线程池的定义，其中 ThreadPoolExecutor 是我们

线程池核心类，首先看看线程池类的主要参数有哪些。

如何提交线程

如可以先随便定义一个固定大小的线程池

ExecutorService es = Executors.newFixedThreadPool(3);

提交一个线程

es.submit(xxRunnble);

es.execute(xxRunnble);

submit 和 execute 分别有什么区别呢？

execute 没有返回值，如果不需要知道线程的结果就使用 execute 方法，性能会好很多。

submit 返回一个 Future 对象，如果想知道线程结果就使用 submit 提交，而且它能在主线

程中通过 Future 的 get 方法捕获线程中的异常。

如何关闭线程池

es.shutdown();

不再接受新的任务，之前提交的任务等执行结束再关闭线程池。

es.shutdownNow();

不再接受新的任务，试图停止池中的任务再关闭线程池，返回所有未处理的线程 list 列表。

</details>

<details>
<summary>线程池启动线程 submit()和 execute()方法有什么不同？</summary>
  
execute 没有返回值，如果不需要知道线程的结果就使用 execute 方法，性能会好很多。submit 返回一个 Future 对象，如果想知道线程结果就使用 submit 提交，而且它能在主线程中通过 Future 的 get 方法捕获线程中的异常。

</details>

<details>
<summary>什么是活锁、饥饿、无锁、死锁？</summary>
  
死锁、活锁、饥饿是关于多线程是否活跃出现的运行阻塞障碍问题，如果线程出现了这三种情况，即线程不再活跃，不能再正常地执行下去了。

**死锁**

死锁是多线程中最差的一种情况，多个线程相互占用对方的资源的锁，而又相互等对方释放锁，此时若无外力干预，这些线程则一直处理阻塞的假死状态，形成死锁。
举个例子，A 同学抢了 B 同学的钢笔，B 同学抢了 A 同学的书，两个人都相互占用对方的东西，都在让对方先还给自己自己再还，这样一直争执下去等待对方还而又得不到解决，老师知道此事后就让他们相互还给对方，这样在外力的干预下他们才解决，当然这只是个例子没有老师他们也能很好解决，计算机不像人如果发现这种情况没有外力干预还是会一直阻塞下去的。

**活锁**

活锁这个概念大家应该很少有人听说或理解它的概念，而在多线程中这确实存在。活锁恰恰与死锁相反，死锁是大家都拿不到资源都占用着对方的资源，而活锁是拿到资源却又相互释放不执行。当多线程中出现了相互谦让，都主动将资源释放给别的线程使用，这样这个资源在多个线程之间跳动而又得不到执行，这就是活锁。饥饿
我们知道多线程执行中有线程优先级这个东西，优先级高的线程能够插队并优先执行，这样如果优先级高的线程一直抢占优先级低线程的资源，导致低优先级线程无法得到执行，这就是饥饿。当然还有一种饥饿的情况，一个线程一直占着一个资源不放而导致其他线程得不到执行，与死锁不同的是饥饿在以后一段时间内还是能够得到执行的，如那个占用资源的线程结束了并释放了资源。

**无锁**

无锁，即没有对资源进行锁定，即所有的线程都能访问并修改同一个资源，但同时只有一个线程能修改成功。无锁典型的特点就是一个修改操作在一个循环内进行，线程
会不断的尝试修改共享资源，如果没有冲突就修改成功并退出否则就会继续下一次循环尝试。所以，如果有多个线程修改同一个值必定会有一个线程能修改成功，而其他
修改失败的线程会不断重试直到修改成功。之前的文章我介绍过 JDK 的 CAS 原理及应用即是无锁的实现。可以看出，无锁是一种非常良好的设计，它不会出现线程出现的跳跃性问题，锁使用不当肯定会出现系统性能问题，虽然无锁无法全面代替有锁，但无锁在某些场合下是非常高效的。

</details>

<details>
<summary>什么是原子性、可见性、有序性？</summary>

原子性、可见性、有序性是多线程编程中最重要的几个知识点，由于多线程情况复杂，如何让每个线程能看到正确的结果，这是非常重要的。

**原子性**

原子性是指一个线程的操作是不能被其他线程打断，同一时间只有一个线程对一个变量进行操作。在多线程情况下，每个线程的执行结果不受其他线程的干扰，比如说多
个线程同时对同一个共享成员变量 n++100 次，如果 n 初始值为 0，n 最后的值应该是 100，所以说它们是互不干扰的，这就是传说的中的原子性。但 n++并不是原子性的操作，要使用 AtomicInteger 保证原子性。

**可见性**

可见性是指某个线程修改了某一个共享变量的值，而其他线程是否可以看见该共享变量修改后的值。在单线程中肯定不会有这种问题，单线程读到的肯定都是最新的值，
而在多线程编程中就不一定了。每个线程都有自己的工作内存，线程先把共享变量的值从主内存读到工作内存，形成一个副本，当计算完后再把副本的值刷回主内存，从读取到最后刷回主内存这是一个过程，当还没刷回主内存的时候这时候对其他线程是不可见的，所以其他线程从主内存读到的值是修改之前的旧值。像 CPU 的缓存优化、硬件优化、指令重排及对 JVM 编译器的优化，都会出现可见性的问题。

**有序性**

我们都知道程序是按代码顺序执行的，对于单线程来说确实是如此，但在多线程情况下就不是如此了。为了优化程序执行和提高 CPU 的处理性能，JVM 和操作系统都会
对指令进行重排，也就说前面的代码并不一定都会在后面的代码前面执行，即后面的代码可能会插到前面的代码之前执行，只要不影响当前线程的执行结果。所以，指令
重排只会保证当前线程执行结果一致，但指令重排后势必会影响多线程的执行结果。虽然重排序优化了性能，但也是会遵守一些规则的，并不能随便乱排序，只是重排序
会影响多线程执行的结果。

</details>

<details>
<summary>CyclicBarrier 和 CountDownLatch 的区别？</summary>
  
两个看上去有点像的类，都在 java.util.concurrent 下，都可以用来表示代码运行到某个点

上，二者的区别在于：

1、CyclicBarrier 的某个线程运行到某个点上之后，该线程即停止运行，直到所有的线程都到达了这个点，所有线程才重新运行；CountDownLatch 则不是，某线程运行到某个点上之后，只是给某个数值-1 而已，该线程继续运行

2、CyclicBarrier 只能唤起一个任务，CountDownLatch 可以唤起多个任务

3、CyclicBarrier 可重用，CountDownLatch 不可重用，计数值为 0 该 CountDownLatch就不可再用了

</details>

<details>
<summary>你如何在Java中获取线程堆栈？</summary>
  
对于不同的操作系统，有多种方法来获得Java进程的线程堆栈。当你获取线程堆栈时，JVM会把所有线程的状态存到日志文件或者输出到控制台。在Windows你可以使用Ctrl + Break组合键来获取线程堆栈，Linux下用kill -3命令。你也可以用jstack这个工具来获取，它对线程id进行操作，你可以用jps这个工具找到id。

</details>

<details>
<summary>JVM中哪个参数是用来控制线程的栈堆栈小的</summary>
  
这个问题很简单， -Xss参数用来控制线程的堆栈大小。你可以查看JVM配置列表来了解这个参数的更多信息。

</details>

<details>
<summary>Java中synchronized 和 ReentrantLock 有什么不同？</summary>
  
Java在过去很长一段时间只能通过synchronized关键字来实现互斥，它有一些缺点。比如你不能扩展锁之外的方法或者块边界，尝试获取锁时不能中途取消等。Java 5 通过Lock接口提供了更复杂的控制来解决这些问题。 ReentrantLock 类实现了 Lock，它拥有与 synchronized 相同的并发性和内存语义且它还具有可扩展性。你可以查看这篇文章了解更多

</details>

<details>
<summary> 有三个线程T1，T2，T3，怎么确保它们按顺序执行？</summary>
  
在多线程中有多种方法让线程按特定顺序执行，你可以用线程类的join()方法在一个线程中启动另一个线程，另外一个线程完成该线程继续执行。为了确保三个线程的顺序你应该先启动最后一个(T3调用T2，T2调用T1)，这样T1就会先完成而T3最后完成。你可以查看这篇文章了解更多。

</details>

<details>
<summary> Thread类中的yield方法有什么作用？</summary>
  
Yield方法可以暂停当前正在执行的线程对象，让其它有相同优先级的线程执行。它是一个静态方法而且只保证当前线程放弃CPU占用而不能保证使其它线程一定能占用CPU，执行yield()的线程有可能在进入到暂停状态后马上又被执行。点击这里查看更多yield方法的相关内容。

</details>


<details>
<summary>Java中ConcurrentHashMap的并发度是什么？</summary>
  
ConcurrentHashMap把实际map划分成若干部分来实现它的可扩展性和线程安全。这种划分是使用并发度获得的，它是ConcurrentHashMap类构造函数的一个可选参数，默认值为16，这样在多线程情况下就能避免争用。欲了解更多并发度和内部大小调整请阅读我的文章How ConcurrentHashMap works in Java。

</details>

<details>
<summary>Java中Semaphore是什么？</summary>
  
Java中的Semaphore是一种新的同步类，它是一个计数信号。从概念上讲，从概念上讲，信号量维护了一个许可集合。如有必要，在许可可用前会阻塞每一个 acquire()，然后再获取该许可。每个 release()添加一个许可，从而可能释放一个正在阻塞的获取者。但是，不使用实际的许可对象，Semaphore只对可用许可的号码进行计数，并采取相应的行动。信号量常常用于多线程的代码中，比如数据库连接池。更多详细信息请点击这里。

</details>

<details>
<summary>如果你提交任务时，线程池队列已满。会时发会生什么？</summary>

这个问题问得很狡猾，许多程序员会认为该任务会阻塞直到线程池队列有空位。事实上如果一个任务不能被调度执行那么ThreadPoolExecutor’s submit()方法将会抛出一个RejectedExecutionException异常。

</details>

<details>
<summary>Java线程池中submit() 和 execute()方法有什么区别？</summary>

两个方法都可以向线程池提交任务，execute()方法的返回类型是void，它定义在Executor接口中, 而submit()方法可以返回持有计算结果的Future对象，它定义在ExecutorService接口中，它扩展了Executor接口，其它线程池类像ThreadPoolExecutor和ScheduledThreadPoolExecutor都有这些方法。更多详细信息请点击这里。

</details>

<details>
<summary> 什么是阻塞式方法？</summary>

阻塞式方法是指程序会一直等待该方法完成期间不做其他事情，ServerSocket的accept()方法就是一直等待客户端连接。这里的阻塞是指调用结果返回之前，当前线程会被挂起，直到得到结果之后才会返回。此外，还有异步和非阻塞式方法在任务完成前就返回。更多详细信息请点击这里。

</details>

<details>
<summary>Swing是线程安全的吗？ 为什么？</summary>

你可以很肯定的给出回答，Swing不是线程安全的，但是你应该解释这么回答的原因即便面试官没有问你为什么。当我们说swing不是线程安全的常常提到它的组件，这些组件不能在多线程中进行修改，所有对GUI组件的更新都要在AWT线程中完成，而Swing提供了同步和异步两种回调方法来进行更新。点击这里查看更多swing和线程安全的相关内容。

</details>

<details>
<summary>Java中invokeAndWait 和 invokeLater有什么区别？</summary>

这两个方法是Swing API 提供给Java开发者用来从当前线程而不是事件派发线程更新GUI组件用的。InvokeAndWait()同步更新GUI组件，比如一个进度条，一旦进度更新了，进度条也要做出相应改变。如果进度被多个线程跟踪，那么就调用invokeAndWait()方法请求事件派发线程对组件进行相应更新。而invokeLater()方法是异步调用更新组件的。更多详细信息请点击这里。

</details>

<details>
<summary>Swing API中那些方法是线程安全的？</summary>

这个问题又提到了swing和线程安全，虽然组件不是线程安全的但是有一些方法是可以被多线程安全调用的，比如repaint(), revalidate()。 JTextComponent的setText()方法和JTextArea的insert() 和 append() 方法也是线程安全的。

</details>

<details>
<summary>如何在Java中创建Immutable对象？</summary>

这个问题看起来和多线程没什么关系， 但不变性有助于简化已经很复杂的并发程序。Immutable对象可以在没有同步的情况下共享，降低了对该对象进行并发访问时的同步化开销。可是Java没有@Immutable这个注解符，要创建不可变类，要实现下面几个步骤：通过构造方法初始化所有成员、对变量不要提供setter方法、将所有的成员声明为私有的，这样就不允许直接访问这些成员、在getter方法中，不要直接返回对象本身，而是克隆对象，并返回对象的拷贝。我的文章how to make an object Immutable in Java有详细的教程，看完你可以充满自信。

</details>

<details>
<summary>Java中的ReadWriteLock是什么？</summary>

一般而言，读写锁是用来提升并发程序性能的锁分离技术的成果。Java中的ReadWriteLock是Java 5 中新增的一个接口，一个ReadWriteLock维护一对关联的锁，一个用于只读操作一个用于写。在没有写线程的情况下一个读锁可能会同时被多个读线程持有。写锁是独占的，你可以使用JDK中的ReentrantReadWriteLock来实现这个规则，它最多支持65535个写锁和65535个读锁。

</details>

<details>
<summary>多线程中的忙循环是什么?</summary>

忙循环就是程序员用循环让一个线程等待，不像传统方法wait(), sleep() 或 yield() 它们都放弃了CPU控制，而忙循环不会放弃CPU，它就是在运行一个空循环。这么做的目的是为了保留CPU缓存，在多核系统中，一个等待线程醒来的时候可能会在另一个内核运行，这样会重建缓存。为了避免重建缓存和减少等待重建的时间就可以使用它了。你可以查看这篇文章获得更多信息。

</details> 

<details>
<summary>volatile 变量和 atomic 变量有什么不同？</summary>

这是个有趣的问题。首先，volatile 变量和 atomic 变量看起来很像，但功能却不一样。Volatile变量可以确保先行关系，即写操作会发生在后续的读操作之前, 但它并不能保证原子性。例如用volatile修饰count变量那么 count++ 操作就不是原子性的。而AtomicInteger类提供的atomic方法可以让这种操作具有原子性如getAndIncrement()方法会原子性的进行增量操作把当前值加一，其它数据类型和引用变量也可以进行相似操作。

</details>

<details>
<summary>如果同步块内的线程抛出异常会发生什么？</summary>

这个问题坑了很多Java程序员，若你能想到锁是否释放这条线索来回答还有点希望答对。无论你的同步块是正常还是异常退出的，里面的线程都会释放锁，所以对比锁接口我更喜欢同步块，因为它不用我花费精力去释放锁，该功能可以在finally block里释放锁实现。

</details>

<details>
<summary>单例模式的双检锁是什么</summary>


这个问题在Java面试中经常被问到，但是面试官对回答此问题的满意度仅为50%。一半的人写不出双检锁还有一半的人说不出它的隐患和Java1.5是如何对它修正的。它其实是一个用来创建线程安全的单例的老方法，当单例实例第一次被创建时它试图用单个锁进行性能优化，但是由于太过于复杂在JDK1.4中它是失败的，我个人也不喜欢它。无论如何，即便你也不喜欢它但是还是要了解一下，因为它经常被问到。你可以查看how double checked locking on Singleton works这篇文章获得更多信息。

</details>

<details>
<summary>如何在Java中创建线程安全的Singleton？</summary>

这是上面那个问题的后续，如果你不喜欢双检锁而面试官问了创建Singleton类的替代方法，你可以利用JVM的类加载和静态变量初始化特征来创建Singleton实例，或者是利用枚举类型来创建Singleton，我很喜欢用这种方法。

</details>

<details>
<summary>写出3条你遵循的多线程最佳实践</summary>

这种问题我最喜欢了，我相信你在写并发代码来提升性能的时候也会遵循某些最佳实践。以下三条最佳实践我觉得大多数Java程序员都应该遵循：
给你的线程起个有意义的名字。
这样可以方便找bug或追踪。OrderProcessor, QuoteProcessor or TradeProcessor 这种名字比 Thread-1. Thread-2 and Thread-3 好多了，给线程起一个和它要完成的任务相关的名字，所有的主要框架甚至JDK都遵循这个最佳实践。
避免锁定和缩小同步的范围
锁花费的代价高昂且上下文切换更耗费时间空间，试试最低限度的使用同步和锁，缩小临界区。因此相对于同步方法我更喜欢同步块，它给我拥有对锁的绝对控制权。
多用同步类少用wait 和 notify
首先，CountDownLatch, Semaphore, CyclicBarrier 和 Exchanger 这些同步类简化了编码操作，而用wait和notify很难实现对复杂控制流的控制。其次，这些类是由最好的企业编写和维护在后续的JDK中它们还会不断优化和完善，使用这些更高等级的同步工具你的程序可以不费吹灰之力获得优化。
多用并发集合少用同步集合
这是另外一个容易遵循且受益巨大的最佳实践，并发集合比同步集合的可扩展性更好，所以在并发编程时使用并发集合效果更好。如果下一次你需要用到map，你应该首先想到用ConcurrentHashMap。我的文章Java并发集合有更详细的说明。

</details>

<details>
<summary>如何强制启动一个线程？</summary>

这个问题就像是如何强制进行Java垃圾回收，目前还没有觉得方法，虽然你可以使用System.gc()来进行垃圾回收，但是不保证能成功。在Java里面没有办法强制启动一个线程，它是被线程调度器控制着且Java没有公布相关的API。

</details>

<details>
<summary>Java中的fork join框架是什么？</summary>

fork join框架是JDK7中出现的一款高效的工具，Java开发人员可以通过它充分利用现代服务器上的多处理器。它是专门为了那些可以递归划分成许多子模块设计的，目的是将所有可用的处理能力用来提升程序的性能。fork join框架一个巨大的优势是它使用了工作窃取算法，可以完成更多任务的工作线程可以从其它线程中窃取任务来执行。你可以查看这篇文章获得更多信息。

</details>

<details>
<summary>Java多线程中调用wait() 和 sleep()方法有什么不同？</summary>

Java程序中wait 和 sleep都会造成某种形式的暂停，它们可以满足不同的需要。wait()方法用于线程间通信，如果等待条件为真且其它线程被唤醒时它会释放锁，而sleep()方法仅仅释放CPU资源或者让当前线程停止执行一段时间，但不会释放锁

</details>

<details>
<summary>现在有线程 T1、T2 和 T3。你如何确保 T2 线程在 T1 之后执行，并且 T3 线程在 T2 之后执行？</summary>

这个线程面试题通常在第一轮面试或电话面试时被问到，这道多线程问题为了测试面试者是否熟悉 join 方法的概念。答案也非常简单——可以用 Thread 类的 join 方法实现这一效果。

</details>

<details>
<summary> Java 中新的 Lock 接口相对于同步代码块（synchronized block）有什么优势？如果让你实现一个高性能缓存，支持并发读取和单一写入，你如何保证数据完整性</summary>

多线程和并发编程中使用 lock 接口的最大优势是它为读和写提供两个单独的锁，可以让你构建高性能数据结构，比如 ConcurrentHashMap 和条件阻塞。
这道 Java 线程面试题越来越多见，而且随后的面试题都基于面试者对这道题的回答。
我强烈建议在任何 Java 多线程面试前都要多看看有关锁的知识，因为如今电子交易系统的客户端和数据交互中，锁被频繁使用来构建缓存。

</details>

<details>
<summary>Java 中 wait 和 sleep 方法有什么区别？</summary>

我们来看看另一个经常被问到的线程面试题。这道题常出现在电话面试中。两者主要的区别就是等待释放锁和监视器。sleep方法在等待时不会释放任何锁或监视器。wait 方法多用于线程间通信，而 sleep 只是在执行时暂停.

</details>

<details>
<summary>如何在 Java 中实现一个阻塞队列？</summary>

这是一道相对困难的 Java 多线程面试题，考察点很多。它考察了面试者是否真正写过 Java 多线程代码，考察了面试者对并发场景的理解。并且可以根据面试者的代码问很多后续问题，如果他用 wait() 和 notify() 方法成功实现了阻塞队列，可以让他用 Java 5 的并发类重新实现一次。

</details>

<details>
<summary>如何在 Java 中编写代码解决生产者消费者问题？</summary>

和上面有关线程的问题相似，这个问题在工作中很典型，但有时面试官会问这类问题，比如“在 Java 中如何解决生产者消费者问题？”其实，有很多解决方式。我分享过用 Java 中 BlockingQueue 的解决方案。有时他们甚至会让你给出哲学家进餐问题的解决方案。

</details>

<details>
<summary>哪些集合类是线程安全的？</summary>

### 1.在Map类中，提供两种线程安全容器。

(1)java.util.Hashtable

Hashtable和HashMap类似，都是散列表，存储键值对映射。主要区别在于Hashtable是线程安全的。当我们查看Hashtable源码的时候，可以看到Hashtable的方法都是通过synchronized来进行方法层次的同步，以达到线程安全的作用。

(2)java.util.concurrent.ConcurrentHashMap

ConcurrentHashMap是性能更好的散列表。在兼顾线程安全的同时，相对于Hashtable，在效率上有很大的提高。我们可以猜想，Hashtable的线程安全实现是对方法进行synchronized，很明显可以通过其他并发方式，如ReentrantLock进行优化。而ConcurrentHashMap正是采用了ReentrantLock。运用锁分离技术，即在代码块上加锁，而不是方法上加。同时ConcurrentHashMap的一个特色是允许多个修改并发操作。这就有意思了，我们知道一般写都是互斥的，为什么这个还能多个同时写呢？那是因为ConcurrentHashMap采用了内部使用段机制，将ConcurrentHashMap分成了很多小段。只要不在一个小段上写就可以并发写


### 2.Collection部分主要是运用的CopyOnWrite机制，即写时复制机制。从字面上就能理解什么意思，就是当我们往一个容器里添加元素的时候，先对这个容器进行一次复制，对副本进行写操作。写操作结束后，将原容器的引用指向新副本容器，就完成了写的刷新。从它的实现原理，我们可以看出这种机制是存在缺点的。

(1).内存占用：毫无疑问，每次写时需要首先复制一遍原容器，假如复制了很多，或者本身原容器就比较大，那么肯定会占用很多内存。可以采用压缩容器中的元素来防止内存消耗过大。

(2).数据一致性问题：当我们在副本中进行写操组时，只能在最终结束后使数据同步，不能实时同步
可以看到，这种机制适用于读操作多，写操作少的应用场景。

java.util.concurrent.CopyOnWriteArrayList

Collection类的线程安全容器主要都是利用的ReentrantLock实现的线程安全，CopyOnWriteArrayList也不例外。在并发写的时候，需要获取lock。读的时候不需要进行lock

java.util.concurrent.CopyOnWriteArraySet

CopyOnWriteArraySet的实现就是基于CopyOnWriteArrayList实现的，采用的装饰器进行实现。二者的区别和List和Set的区别一样。

Vector

一般我们都不用Vector了，不过它确实也是线程安全的。相对于其他容器，能够提供随机访问功能。


</details>

<details>
<summary>多线程中的忙循环是什么?</summary>

忙循环就是程序员用循环让一个线程等待，不像传统方法wait(), sleep()或 yield()它们都放弃了CPU控制，而忙循环不会放弃CPU，它就是在运行一个空循环。这么做的目的是为了保留CPU缓存，在多核系统中，一个等待线程醒来的时候可能会在另一个内核运行，这样会重建缓存。为了避免重建缓存和减少等待重建的时间就可以使用它了。
</details>

<details>
<summary>什么是线程局部变量?</summary>

ThreadLocal，很多地方叫做线程本地变量，也有些地方叫做线程本地存储。变量值的共享可以使用public static变量的形式，所有的线程都使用同一个public static变量，但是如果每一个线程都有自己的变量该如何共享呢，就是通过ThreadLocal，ThreadLocal为变量在每个线程中都创建了一个副本，那么每个线程可以访问自己内部的副本变量。

最常见的ThreadLocal使用场景为 用来解决 数据库连接、Session管理等。譬如在数据库链接的时候可以这样实现：

```java

class ConnectionManager {
    private  Connection connect = null;
    public Connection openConnection() {
        if(connect == null){
            connect = DriverManager.getConnection();
        }
        return connect;
    }
    public void closeConnection() {
        if(connect!=null)
            connect.close();
    }
}
class Dao{
    public void insert() {
        ConnectionManager connectionManager = new ConnectionManager();
        Connection connection = connectionManager.openConnection();
        //使用connection进行操作
        connectionManager.closeConnection();
    }
}

```

每次都是在方法内部创建的连接，那么线程之间避免了线程安全问题。但是这样会有一个致命的影响：导致服务器压力非常大，并且严重影响程序执行性能。由于在方法中需要频繁地开启和关闭数据库连接，这样不尽严重影响程序执行效率，还可能导致服务器压力巨大。　那么这种情况下使用ThreadLocal是再适合不过的了，因为ThreadLocal在每个线程中对该变量会创建一个副本，即每个线程内部都会有一个该变量，且在线程内部任何地方都可以使用，线程之间互不影响，这样一来就不存在线程安全问题，也不会严重影响程序执行性能。但是要注意，虽然ThreadLocal能够解决上面说的问题，但是由于在每个线程中都创建了副本，所以要考虑它对资源的消耗，比如内存的占用会比不使用ThreadLocal要大。
关于ThreadLocal的API：

//关于ThreadLocal的API：
public T get() { }
public void set(T value) { }
public void remove() { }
protected T initialValue() { }

get()方法是用来获取ThreadLocal在当前线程中保存的变量副本，set()用来设置当前线程中变量的副本，remove()用来移除当前线程中变量的副本，initialValue()是一个protected方法，一般是用来在使用时进行重写的，它是一个延迟加载方法。这部分可以网上参考：[Java并发编程：深入剖析ThreadLocal](https://www.cnblogs.com/dolphin0520/p/3920407.html) ，往上关于此部分知识的有几篇高访问量博文都有争议，切勿照搬，可看评论区。

</details>

<details>
<summary>线程和进程有什么区别？进程间如何通讯，线程间如何通讯？</summary>

进程：每个进程都有独立的代码和数据空间（进程上下文），进程间的切换会有较大的开销，一个进程包含1–n个线程。（进程是资源分配的最小单位）

线程：同一类线程共享代码和数据空间，每个线程有独立的运行栈和程序计数器(PC)，线程切换开销小。（线程是cpu调度的最小单位）

### 一、进程间的通讯

管道( pipe )：管道是一种半双工的通信方式，数据只能单向流动，而且只能在具有亲缘关系的进程间使用。进程的亲缘关系通常是指父子进程关系。

有名管道 (namedpipe)： 有名管道也是半双工的通信方式，但是它允许无亲缘关系进程间的通信。

信号量(semophore )： 信号量是一个计数器，可以用来控制多个进程对共享资源的访问。它常作为一种锁机制，防止某进程正在访问共享资源时，其他进程也访问该资源。因此，主要作为进程间以及同一进程内不同线程之间的同步手段。

消息队列( messagequeue ) ： 消息队列是由消息的链表，存放在内核中并由消息队列标识符标识。消息队列克服了信号传递信息少、管道只能承载无格式字节流以及缓冲区大小受限等缺点。

信号： 信号是一种比较复杂的通信方式，用于通知接收进程某个事件已经发生。

共享内存(shared memory )：共享内存就是映射一段能被其他进程所访问的内存，这段共享内存由一个进程创建，但多个进程都可以访问。共享内存是最快的 IPC 方式，它是针对其他进程间通信方式运行效率低而专门设计的。它往往与其他通信机制，如信号两，配合使用，来实现进程间的同步和通信。

套接字(socket ) ： 套解口也是一种进程间通信机制，与其他通信机制不同的是，它可用于不同及其间的进程通信。

### 二、线程间的通信方式

锁机制：包括互斥锁、条件变量、读写锁

互斥锁提供了以排他方式防止数据结构被并发修改的方法。

读写锁允许多个线程同时读共享数据，而对写操作是互斥的。

条件变量可以以原子的方式阻塞进程，直到某个特定条件为真为止。对条件的测试是在互斥锁的保护下进行的。条件变量始终与互斥锁一起使用。

信号量机制(Semaphore)：包括无名线程信号量和命名线程信号量

信号机制(Signal)：类似进程间的信号处理

线程间的通信目的主要是用于线程同步，所以线程没有像进程通信中的用于数据交换的通信机制。

管道( pipe )：管道是一种半双工的通信方式，数据只能单向流动，而且只能在具有亲缘关系的进程间使用。进程的亲缘关系通常是指父子进程关系。

有名管道 (namedpipe)： 有名管道也是半双工的通信方式，但是它允许无亲缘关系进程间的通信。

信号量(semophore ) ： 信号量是一个计数器，可以用来控制多个进程对共享资源的访问。它常作为一种锁机制，防止某进程正在访问共享资源时，其他进程也访问该资源。因此，主要作为进程间以及同一进程内不同线程之间的同步手段。

消息队列( messagequeue ) ： 消息队列是由消息的链表，存放在内核中并由消息队列标识符标识。消息队列克服了信号传递信息少、管道只能承载无格式字节流以及缓冲区大小受限等缺点。

信号 (sinal ) ： 信号是一种比较复杂的通信方式，用于通知接收进程某个事件已经发生。

共享内存(shared memory ) ：共享内存就是映射一段能被其他进程所访问的内存，这段共享内存由一个进程创建，但多个进程都可以访问。共享内存是最快的 IPC 方式，它是针对其他进程间通信方式运行效率低而专门设计的。它往往与其他通信机制，如信号两，配合使用，来实现进程间的同步和通信。

套接字(socket ) ： 套解口也是一种进程间通信机制，与其他通信机制不同的是，它可用于不同及其间的进程通信。

</details>

<details>
<summary>什么是多线程环境下的伪共享（false sharing）</summary>

共享就是一个内存区域的数据被多个处理器访问，伪共享就是不是真的共享。这里的共享这个概念是基于逻辑层面的。实际上伪共享与共享在cache line 上实际都是共享的。CPU访问的数据都是从cache line中读取的。如果cpu 在cache 中找不到需要的变量，则称缓存未命中。

未命中时，需要通过总线从内存中读取进cache 中。每次读取的内存大小就是一个cache line 的大小。如果多个CPU访问的不同内存变量被装载到了同一个cache line 中，则从程序逻辑层上讲，并没有共享变量，但实际上在cache line 上他们是共享访问的，这个就是典型的伪共享。伪共享与共享 在 cache line 的层面上必须都是共享的。多个CPU对共享内存的访问安全通过缓存一致性来保证。伪共享问题很难被发现，因为线程可能访问完全不同的全局变量，内存中却碰巧在很相近的位置上。如其他诸多的并发问题，避免伪共享的最基本方式是仔细审查代码，根据缓存行来调整你的数据结构。

</details>

<details>
<summary>同步和异步有何异同，在什么情况下分别使用他们？举例说明</summary>

如果数据将在线程间共享。例如正在写的数据以后可能被另一个线程读到，或者正在读的数据可能已经被另一个线程写过了，那么这些数据就是共享数据，必须进行同步存取。当应用程序在对象上调用了一个需要花费很长时间来执行的方法，并且不希望让程序等待方法的返回时，就应该使用异步编程，在很多情况下采用异步途径往往更有效率。

Java中交互方式分为同步和异步两种：

同步交互：指发送一个请求,需要等待返回,然后才能够发送下一个请求，有个等待过程；
异步交互：指发送一个请求,不需要等待返回,随时可以再发送下一个请求，即不需要等待。
区别：一个需要等待，一个不需要等待，在部分情况下，我们的项目开发中都会优先选择不需要等待的异步交互方式。
哪些情况建议使用同步交互呢？比如银行的转账系统，对数据库的保存操作等等，都会使用同步交互操作，其余情况都优先使用异步交互

</details>

<details>
<summary>线程调度的基本方法</summary>

1、调整线程优先级：

Java线程有优先级，优先级高的线程会获得较多的运行机会。Java线程的优先级用整数表示，取值范围是1~10，Thread类有以下三个静态常量：

static int MAX_PRIORITY    //线程可以具有的最高优先级，取值为10。

static int MIN_PRIORITY    //线程可以具有的最低优先级，取值为1。

static int NORM_PRIORITY  //分配给线程的默认优先级，取值为5。

Thread类的setPriority()和getPriority()方法分别用来设置和获取线程的优先级。每个线程都有默认的优先级。主线程的默认优先级为Thread.NORM_PRIORITY。线程的优先级有继承关系，比如A线程中创建了B线程，那么B将和A具有相同的优先级。JVM提供了10个线程优先级，但与常见的操作系统都不能很好的映射。如果希望程序能移植到各个操作系统中，应该仅仅使用Thread类有以下三个静态常量作为优先级，这样能保证同样的优先级采用了同样的调度方式。

2、线程睡眠：

Thread.sleep(long millis)方法，使线程转到阻塞状态。millis参数设定睡眠的时间，以毫秒为单位。当睡眠结束后，就转为就绪（Runnable）状态。sleep()平台移植性好。

3、线程等待：

Object类中的wait()方法，导致当前的线程等待，直到其他线程调用此对象的notify()方法或 notifyAll() 唤醒方法。这个两个唤醒方法也是Object类中的方法，行为等价于调用 wait(0) 一样。

4、线程让步：

Thread.yield()方法，暂停当前正在执行的线程对象，把执行机会让给相同或者更高优先级的线程。

5、线程加入：

join()方法，等待其他线程终止。在当前线程中调用另一个线程的join()方法，则当前线程转入阻塞状态，直到另一个进程运行结束，当前线程再由阻塞转为就绪状态。

6、线程唤醒：

Object类中的notify()方法，唤醒在此对象监视器上等待的单个线程。如果所有线程都在此对象上等待，则会选择唤醒其中一个线程。选择是任意性的，并在对实现做出决定时发生。线程通过调用其中一个 wait方法，在对象的监视器上等待。 直到当前的线程放弃此对象上的锁定，才能继续执行被唤醒的线程。被唤醒的线程将以常规方式与在该对象上主动同步的其他所有线程进行竞争；例如，唤醒的线程在作为锁定此对象的下一个线程方面没有可靠的特权或劣势。类似的方法还有一个notifyAll()，唤醒在此对象监视器上等待的所有线程。

注意：Thread中suspend()和resume()两个方法在JDK1.5中已经废除，不再介绍。因为有死锁倾向。

</details>

<details>
<summary>线程调度常用函数</summary>
  
①sleep(long millis): 在指定的毫秒数内让当前正在执行的线程休眠（暂停执行）

②join():指等待t线程终止。join是Thread类的一个方法，启动线程后直接调用，即join()的作用是：“等待该线程终止”，这里需要理解的就是该线程是指的主线程等待子线程的终止。也就是在子线程调用了join()方法后面的代码，只有等到子线程结束了才能执行。

```java

Thread t = new AThread();
t.start();
t.join();

```

为什么要用join()方法?在很多情况下，主线程生成并起动了子线程，如果子线程里要进行大量的耗时的运算，主线程往往将于子线程之前结束，但是如果主线程处理完其他的事务后，需要用到子线程的处理结果，也就是主线程需要等待子线程执行完成之后再结束，这个时候就要用到join()方法了。

```java

public class Main2 {
    public static void main(String[] args) {
        System.out.println(Thread.currentThread().getName()+"主线程运行开始!");
        Thread1 mTh1=new Thread1("A");
        Thread1 mTh2=new Thread1("B");
        mTh1.start();
        mTh2.start();
        try {
            mTh1.join();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        try {
            mTh2.join();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println(Thread.currentThread().getName()+ "主线程运行结束!");
    }
}
class Thread1 extends Thread{
    private String name;
    public Thread1(String name) {
        super(name);
        this.name=name;
    }
    public void run() {
        System.out.println(Thread.currentThread().getName() + " 线程运行开始!");
        for (int i = 0; i < 5; i++) {
            System.out.println("子线程"+name + "运行 : " + i);
            try {
                sleep((int) Math.random() * 10);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        System.out.println(Thread.currentThread().getName() + " 线程运行结束!");
    }
}

```

main主线程运行开始!
A 线程运行开始!
B 线程运行开始!
子线程A运行 : 0
子线程B运行 : 0
子线程A运行 : 1
子线程A运行 : 2
子线程A运行 : 3
子线程A运行 : 4
A 线程运行结束!
子线程B运行 : 1
子线程B运行 : 2
子线程B运行 : 3
子线程B运行 : 4
B 线程运行结束!
main主线程运行结束!
③yield():暂停当前正在执行的线程对象，并执行其他线程。

Thread.yield()方法作用是：暂停当前正在执行的线程对象，并执行其他线程。yield()应该做的是让当前运行线程回到可运行状态，以允许具有相同优先级的其他线程获得运行机会。因此，使用yield()的目的是让相同优先级的线程之间能适当的轮转执行。但是，实际中无法保证yield()达到让步目的，因为让步的线程还有可能被线程调度程序再次选中。

结论：yield()从未导致线程转到等待/睡眠/阻塞状态。在大多数情况下，yield()将导致线程从运行状态转到可运行状态，但有可能没有效果（注意线程的生命周期）。

```java

public class Main2 {
    public static void main(String[] args) {
        ThreadYield yt1 = new ThreadYield("张三");
        ThreadYield yt2 = new ThreadYield("李四");
        yt1.start();
        yt2.start();
    }
}
class ThreadYield extends Thread{
    public ThreadYield(String name) {
        super(name);
    }
    @Override
    public void run() {
        for (int i = 1; i <= 50; i++) {
            System.out.println("" + this.getName() + "-----" + i);
            // 当i为30时，该线程就会把CPU时间让掉，让其他或者自己的线程执行（也就是谁先抢到谁执行）
            if (i ==30) {
                this.yield();
            }
        }
    }
}

```

运行结果：

第一种情况：李四（线程）当执行到30时会CPU时间让掉，这时张三（线程）抢到CPU时间并执行。

第二种情况：李四（线程）当执行到30时会CPU时间让掉，这时李四（线程）抢到CPU时间并执行。

④setPriority(): 更改线程的优先级。

MIN_PRIORITY = 1

NORM_PRIORITY = 5

MAX_PRIORITY = 10

用法：

```java
Thread4 t1 = new Thread4("t1");
Thread4 t2 = new Thread4("t2");
t1.setPriority(Thread.MAX_PRIORITY);
t2.setPriority(Thread.MIN_PRIORITY);

```

⑤interrupt():不要以为它是中断某个线程！它只是线线程发送一个中断信号，让线程在无限等待时（如死锁时）能抛出抛出，从而结束线程，但是如果你吃掉了这个异常，那么这个线程还是不会中断的！

⑥wait()：Obj.wait()，与Obj.notify()必须要与synchronized(Obj)一起使用，也就是wait,与notify是针对已经获取了Obj锁进行操作，从语法角度来说就是Obj.wait(),Obj.notify必须在synchronized(Obj){...}语句块内。从功能上来说wait就是说线程在获取对象锁后，主动释放对象锁，同时本线程休眠。直到有其它线程调用对象的notify()唤醒该线程，才能继续获取对象锁，并继续执行。相应的notify()就是对对象锁的唤醒操作。但有一点需要注意的是notify()调用后，并不是马上就释放对象锁的，而是在相应的synchronized(){}语句块执行结束，自动释放锁后，JVM会在wait()对象锁的线程中随机选取一线程，赋予其对象锁，唤醒线程，继续执行。这样就提供了在线程间同步、唤醒的操作。Thread.sleep()与Object.wait()二者都可以暂停当前线程，释放CPU控制权，主要的区别在于Object.wait()在释放CPU同时，释放了对象锁的控制。

单单在概念上理解清楚了还不够，需要在实际的例子中进行测试才能更好的理解。对Object.wait()，Object.notify()的应用最经典的例子，应该是三线程打印ABC的问题了吧，这是一道比较经典的面试题，题目要求如下：建立三个线程，A线程打印10次A，B线程打印10次B,C线程打印10次C，要求线程同时运行，交替打印10次ABC。这个问题用Object的wait()，notify()就可以很方便的解决。代码如下：

```java

public class MyThreadPrinter2 implements Runnable {

    private String name;
    private Object prev;
    private Object self;

    private MyThreadPrinter2(String name, Object prev, Object self) {
        this.name = name;
        this.prev = prev;
        this.self = self;
    }

    @Override
    public void run() {
        int count = 10;
        while (count > 0) {
            synchronized (prev) {
                synchronized (self) {
                    System.out.print(name);
                    count--;

                    self.notify();
                }
                try {
                    prev.wait();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
    }

    public static void main(String[] args) throws Exception {
        Object a = new Object();
        Object b = new Object();
        Object c = new Object();
        MyThreadPrinter2 pa = new MyThreadPrinter2("A", c, a);
        MyThreadPrinter2 pb = new MyThreadPrinter2("B", a, b);
        MyThreadPrinter2 pc = new MyThreadPrinter2("C", b, c);

        new Thread(pa).start();
        Thread.sleep(100);  //确保按顺序A、B、C执行
        new Thread(pb).start();
        Thread.sleep(100);
        new Thread(pc).start();
        Thread.sleep(100);
        }
}

```

先来解释一下其整体思路，从大的方向上来讲，该问题为三线程间的同步唤醒操作，主要的目的就是ThreadA->ThreadB->ThreadC->ThreadA循环执行三个线程。为了控制线程执行的顺序，那么就必须要确定唤醒、等待的顺序，所以每一个线程必须同时持有两个对象锁，才能继续执行。一个对象锁是prev，就是前一个线程所持有的对象锁。还有一个就是自身对象锁。主要的思想就是，为了控制执行的顺序，必须要先持有prev锁，也就前一个线程要释放自身对象锁，再去申请自身对象锁，两者兼备时打印，之后首先调用self.notify()释放自身对象锁，唤醒下一个等待线程，再调用prev.wait()释放prev对象锁，终止当前线程，等待循环结束后再次被唤醒。运行上述代码，可以发现三个线程循环打印ABC，共10次。程序运行的主要过程就是A线程最先运行，持有C,A对象锁，后释放A,C锁，唤醒B。线程B等待A锁，再申请B锁，后打印B，再释放B，A锁，唤醒C，线程C等待B锁，再申请C锁，后打印C，再释放C,B锁，唤醒A。看起来似乎没什么问题，但如果你仔细想一下，就会发现有问题，就是初始条件，三个线程按照A,B,C的顺序来启动，按照前面的思考，A唤醒B，B唤醒C，C再唤醒A。但是这种假设依赖于JVM中线程调度、执行的顺序。

</details>

<details>
<summary>启动一个线程是调用 run() 还是 start() 方法？start() 和 run() 方法有什么区别</summary>
  
相当于玩游戏机，只有一个游戏机（cpu），可是有很多人要玩，于是，start是排队！等CPU选中你就是轮到你，你就run（），当CPU的运行的时间片执行完，这个线程就继续排队，等待下一次的run（）。调用start（）后，线程会被放到等待队列，等待CPU调度，并不一定要马上开始执行，只是将这个线程置于可动行状态。然后通过JVM，线程Thread会调用run（）方法，执行本线程的线程体。先调用start后调用run，这么麻烦，为了不直接调用run？就是为了实现多线程的优点，没这个start不行。

（1）.start（）方法来启动线程，真正实现了多线程运行。这时无需等待run方法体代码执行完毕，可以直接继续执行下面的代码；通过调用Thread类的start()方法来启动一个线程， 这时此线程是处于就绪状态， 并没有运行。 然后通过此Thread类调用方法run()来完成其运行操作的， 这里方法run()称为线程体，它包含了要执行的这个线程的内容， Run方法运行结束， 此线程终止。然后CPU再调度其它线程。

（2）.run（）方法当作普通方法的方式调用。程序还是要顺序执行，要等待run方法体执行完毕后，才可继续执行下面的代码； 程序中只有主线程——这一个线程， 其程序执行路径还是只有一条， 这样就没有达到写线程的目的。

记住：多线程就是分时利用CPU，宏观上让所有线程一起执行 ，也叫并发

start() :它的作用是启动一个新线程，新线程处于就绪状态，拿到cpu执行权就会执行相应的run()方法。start()不能被重复调用。
run(): run()就和普通的成员方法一样，可以被重复调用。单独调用run()的话，会在当前线程中执行run()，而并不会启动新线程！（为什么不直接调用run方法的原因也在此。）

</details>

<details>
<summary>sleep() 方法和对象的 wait() 方法都可以让线程暂停执行，它们有什么区别？</summary>

sleep（）方法

sleep()使当前线程进入停滞状态（阻塞当前线程），让出CUP的使用、目的是不让当前线程独自霸占该进程所获的CPU资源，以留一定时间给其他线程执行的机会;sleep()是Thread类的Static(静态)的方法；因此他不能改变对象的机锁，所以当在一个Synchronized块中调用Sleep()方法是，线程虽然休眠了，但是对象的机锁并木有被释放，其他线程无法访问这个对象（即使睡着也持有对象锁）。在sleep()休眠时间期满后，该线程不一定会立即执行，这是因为其它线程可能正在运行而且没有被调度为放弃执行，除非此线程具有更高的优先级。

wait（）方法

wait()方法是Object类里的方法；当一个线程执行到wait()方法时，它就进入到一个和该对象相关的等待池中，同时失去（释放）了对象的机锁（暂时失去机锁，wait(long timeout)超时时间到后还需要返还对象锁）；其他线程可以访问；wait()使用notify或者notifyAlll或者指定睡眠时间来唤醒当前等待池中的线程。wiat()必须放在synchronized block中，否则会在program runtime时扔出”java.lang.IllegalMonitorStateException“异常。
共同点：

他们都是在多线程的环境下，都可以在程序的调用处阻塞指定的毫秒数，并返回。

wait()和sleep()都可以通过interrupt()方法 打断线程的暂停状态，从而使线程立刻抛出InterruptedException。

如果线程A希望立即结束线程B，则可以对线程B对应的Thread实例调用interrupt方法。如果此刻线程B正在wait/sleep /join，则线程B会立刻抛出InterruptedException，在catch() {} 中直接return即可安全地结束线程。 需要注意的是，InterruptedException是线程自己从内部抛出的，并不是interrupt()方法抛出的。对某一线程调用 interrupt()时，如果该线程正在执行普通的代码，那么该线程根本就不会抛出InterruptedException。但是，一旦该线程进入到 wait()/sleep()/join()后，就会立刻抛出InterruptedException 。

不同点：

Thread类的方法：sleep(),yield()等。Object的方法：wait()和notify()等

每个对象都有一个锁来控制同步访问。Synchronized关键字可以和对象的锁交互，来实现线程的同步。sleep方法没有释放锁，而wait方法释放了锁，使得其他线程可以使用同步控制块或者方法。

wait，notify和notifyAll只能在同步控制方法或者同步控制块里面使用，而sleep可以在任何地方使用

sleep必须捕获异常，而wait，notify和notifyAll不需要捕获异常

所以sleep()和wait()方法的最大区别是：sleep()睡眠时，保持对象锁，仍然占有该锁；而wait()睡眠时，释放对象锁。但是wait()和sleep()都可以通过interrupt()方法打断线程的暂停状态，从而使线程立刻抛出InterruptedException（但不建议使用该方法）。

</details>


<details>
<summary>yield方法有什么作用？sleep() 方法和 yield()方法有什么区别?</summary>

sleep()和yield()的区别:sleep()使当前线程进入停滞状态，所以执行sleep()的线程在指定的时间内肯定不会被执行；yield()只是使当前线程重新回到可执行状态，所以执行yield()的线程有可能在进入到可执行状态后马上又被执行。

sleep 方法使当前运行中的线程睡眼一段时间，进入不可运行状态，这段时间的长短是由程序设定的，yield 方法使当前线程让出 CPU 占有权，但让出的时间是不可设定的。实际上，yield()方法对应了如下操作：先检测当前是否有相同优先级的线程处于同可运行状态，如有，则把 CPU 的占有权交给此线程，否则，继续运行原来的线程。所以yield()方法称为“退让”，它把运行机会让给了同等优先级的其他线程

另外，sleep 方法允许较低优先级的线程获得运行机会，但 yield() 方法执行时，当前线程仍处在可运行状态，所以，不可能让出较低优先级的线程些时获得 CPU 占有权。在一个运行系统中，如果较高优先级的线程没有调用 sleep 方法，又没有受到 I\O 阻塞，那么，较低优先级线程只能等待所有较高优先级的线程运行结束，才有机会运行。

</details>

<details>
<summary>Java 中如何停止一个线程？</summary>

停止一个线程意味着在任务处理完任务之前停掉正在做的操作，也就是放弃当前的操作。停止一个线程可以用Thread.stop()方法，但最好不要用它。虽然它确实可以停止一个正在运行的线程，但是这个方法是不安全的，而且是已被废弃的方法。在java中有以下3种方法可以终止正在运行的线程：

1.使用退出标志，使线程正常退出，也就是当run方法完成后线程终止。

2.使用stop方法强行终止，但是不推荐这个方法，因为stop和suspend及resume一样都是过期作废的方法。

3.使用interrupt方法中断线程。具体案例和api可以参考文章[java多线程之——interrupt深入研究](https://www.cnblogs.com/carmanloneliness/p/3516405.html)

</details>

<details>
<summary>stop()和 suspend()方法为何不推荐使用?</summary>

Stop()方法作为一种粗暴的线程终止行为，在线程终止之前没有对其做任何的清除操作，因此具有固有的不安全性。 用Thread.stop()方法来终止线程将会释放该线程对象已经锁定的所有监视器。如果以前受这些监视器保护的任何对象都处于不连贯状态，那么损坏的对象对其他线程可见，这有可能导致不安全的操作。 由于上述原因，因此不应该使用stop()方法，而应该在自己的Thread类中置入一个标志，用于控制目标线程是活动还是停止。如果该标志指示它要停止运行，可使其结束run（）方法。如果目标线程等待很长时间，则应使用interrupt()方法来中断该等待。

suspend()方法 该方法已经遭到反对，因为它具有固有的死锁倾向。调用suspend（）方法的时候，目标线程会停下来。如果目标线程挂起时在保护关键系统资源的监视器上保持有锁，则在目标线程重新开始以前，其他线程都不能访问该资源。除非被挂起的线程恢复运行。对任何其他线程来说，如果想恢复目标线程，同时又试图使用任何一个锁定的资源，就会造成死锁。由于上述原因，因此不应该使用suspend（）方法，而应在自己的thread类中置入一个标志，用于控制线程是活动还是挂起。如果标志指出线程应该挂起，那么用wait（）方法命令其进入等待状态。如果标志指出线程应当恢复，那么用notify()方法重新启动线程。


</details>

<details>
<summary>如何在两个线程间共享数据?</summary>

通过在线程之间共享对象就可以了，然后通过wait/notify/notifyAll、await/signal/signalAll进行唤起和等待，比方说阻塞队列BlockingQueue就是为线程之间共享数据而设计的。

</details>

<details>
<summary>强制启动一个线程？</summary>

实现Runnable接口优势：

1）适合多个相同的程序代码的线程去处理同一个资源。

2）可以避免java中的单继承的限制。

3）增加程序的健壮性，代码可以被多个线程共享，代码和数据独立。

继承Thread类优势：

1）可以将线程类抽象出来，当需要使用抽象工厂模式设计时。

2）多线程同步

在函数体使用

1）无需继承thread或者实现Runnable，缩小作用域。


</details>

<details>
<summary>如何让正在运行的线程暂停一段时间</summary>

1、sleep()方法（休眠）是线程类（Thread）的静态方法，调用此方法会让当前线程暂停执行指定的时间，将执行机会（CPU）让给其他线程，但是对象的锁依然保持，因此休眠时间结束后会自动恢复（线程回到就绪状态，请参考第66题中的线程状态转换图）。

2、wait()是Object类的方法，调用对象的wait()方法导致当前线程放弃对象的锁（线程暂停执行），进入对象的等待池（wait pool），只有调用对象的notify()方法（或notifyAll()方法）时才能唤醒等待池中的线程进入等锁池（lockpool），如果线程重新获得对象的锁就可以进入就绪状态

</details>

<details>
<summary>什么是线程组，为什么在Java中不推荐使用？</summary>

ThreadGroup是一个类, 它的目的是提供关于线程组的信息.

ThreadGroupAPI比较薄弱, 它并没有为Thread提供了更多的功能. 它主要有两个功能: 一是获取线程组中处于活跃状态线程的列表; 二是甚至为线程设置未捕获异常处理器(uncaught exception handler) . 但在java 1.5中Thread类也添加了setUncaughtExceptionHandler(UncaughtExceptionHandler eh)方法, 所以ThreadGroup是已经过时的, 不建议使用.

</details>

<details>
<summary>你是如何调用 wait（方法的）？使用 if 块还是循环？为什么</summary>

wait() 方法应该在循环调用，因为当线程获取到 CPU 开始执行的时候，其他条件可能还没有满足，所以在处理前，循环检测条件是否满足会更好。

</details>

<details>
<summary>线程的生命周期--新建和就绪状态</summary>

当程序使用new关键字创建了一个线程之后，该线程就处于新建状态，此时它和其他的Java对象一样，仅仅由Java虚拟机为其分配内存，并初始化其成员变量的值。此时的线程对象没有表现出任何线程的动态特征，程序也不会执行线程的线程执行体。

当线程对象调用了start()方法之后，该线程处于就绪状态。Java虚拟机会为其创建方法调用栈和程序计数器，处于这个状态中的线程并没有开始运行，只是表示该线程可以运行了。至于该线程何时开始运行，取决于JVM里线程调度器的调度。

注意：启动线程使用start()方法，而不是run()方法。永远不要调用线程对象的run()方法。调用start方法来启动线程，系统会把该run()方法当成线程执行体来处理；但如果直按调用线程对象的run()方法，则run()方法立即就会被执行，而且在run()方法返回之前其他线程无法并发执行。也就是说，系统把线程对象当成一个普通对象，而run()方法也是一个普通方法，而不是线程执行体。需要指出的是，调用了线程的run()方法之后，该线程已经不再处于新建状态，不要再次调用线程对象的start()方法。只能对处于新建状态的线程调用start()方法，否则将引发IllegaIThreadStateExccption异常。
调用线程对象的start()方法之后，该线程立即进入就绪状态——就绪状态相当于”等待执行”，但该线程并未真正进入运行状态。如果希望调用子线程的start()方法后子线程立即开始执行，程序可以使用Thread.sleep(1)来让当前运行的线程（主线程）睡眠1毫秒，1毫秒就够了，因为在这1毫秒内CPU不会空闲，它会去执行另一个处于就绪状态的线程，这样就可以让子线程立即开始执行。

</details>

<details>
<summary>线程的生命周期--运行和阻塞状态</summary>

**线程调度**

如果处于就绪状态的线程获得了CPU，开始执行run()方法的线程执行体，则该线程处于运行状态，如果计算机只有一个CPU。那么在任何时刻只有一个线程处于运行状态，当然在一个多处理器的机器上，将会有多个线程并行执行；当线程数大于处理器数时，依然会存在多个线程在同一个CPU上轮换的现象。

当一个线程开始运行后，它不可能一直处于运行状态（除非它的线程执行体足够短，瞬间就执行结束了）。线程在运行过程中需要被中断，目的是使其他线程获得执行的机会，线程调度的细节取决于底层平台所采用的策略。对于采用抢占式策略的系统而言，系统会给每个可执行的线程一个小时间段来处理任务；当该时间段用完后，系统就会剥夺该线程所占用的资源，让其他线程获得执行的机会。在选择下一个线程时，系统会考虑线程的优先级。

所有现代的桌面和服务器操作系统都采用抢占式调度策略，但一些小型设备如手机则可能采用协作式调度策略，在这样的系统中，只有当一个线程调用了它的sleep()或yield()方法后才会放弃所占用的资源——也就是必须由该线程主动放弃所占用的资源。

**线程阻塞**

当发生如下情况时，线程将会进入阻塞状态

① 线程调用sleep()方法主动放弃所占用的处理器资源

② 线程调用了一个阻塞式IO方法，在该方法返回之前，该线程被阻塞

③ 线程试图获得一个同步监视器，但该同步监视器正被其他线程所持有。关于同步监视器的知识、后面将存更深入的介绍

④ 线程在等待某个通知（notify）

⑤ 程序调用了线程的suspend()方法将该线程挂起。但这个方法容易导致死锁，所以应该尽量避免使用该方法

当前正在执行的线程被阻塞之后，其他线程就可以获得执行的机会。被阻塞的线程会在合适的时候重新进入就绪状态，注意是就绪状态而不是运行状态。也就是说，被阻塞线程的阻塞解除后，必须重新等待线程调度器再次调度它。

**解除阻塞**

针对上面几种情况，当发生如下特定的情况时可以解除上面的阻塞，让该线程重新进入就绪状态：

① 调用sleep()方法的线程经过了指定时间。

② 线程调用的阻塞式IO方法已经返回。

③ 线程成功地获得了试图取得的同步监视器。

④ 线程正在等待某个通知时，其他线程发出了个通知。

⑤ 处于挂起状态的线程被调甩了resume()恢复方法。

线程从阻塞状态只能进入就绪状态，无法直接进入运行状态。而就绪和运行状态之间的转换通常不受程序控制，而是由系统线程调度所决定。当处于就绪状态的线程获得处理器资源时，该线程进入运行状态；当处于运行状态的线程失去处理器资源时，该线程进入就绪状态。但有一个方法例外，调用yield()方法可以让运行状态的线程转入就绪状态。关于yield()方法后面有更详细的介纽。

</details>

<details>
<summary>线程的生命周期--线程死亡</summary>

**死亡状态**

线程会以如下3种方式结束，结束后就处于死亡状态：

① run()或call()方法执行完成，线程正常结束。

② 线程抛出一个未捕获的Exception或Error。

③ 直接调用该线程stop()方法来结束该线程——该方法容易导致死锁，通常不推荐使用。

</details>

<details>
<summary>有哪些不同的线程生命周期？</summary>

线程被创建并启动以后，它既不是一启动就进入了执行状态，也不是一直处于执行状态。在线程的生命周期中，它要经过新建(New)、就绪（Runnable）、运行（Running）、阻塞(Blocked)和死亡(Dead)5种状态。尤其是当线程启动以后，它不可能一直”霸占”着CPU独自运行，所以CPU需要在多条线程之间切换，于是线程状态也会多次在运行、阻塞之间切换

1. 新建状态，当程序使用new关键字创建了一个线程之后，该线程就处于新建状态，此时仅由JVM为其分配内存，并初始化其成员变量的值

2. 就绪状态，当线程对象调用了start()方法之后，该线程处于就绪状态。Java虚拟机会为其创建方法调用栈和程序计数器，等待调度运行

3. 运行状态，如果处于就绪状态的线程获得了CPU，开始执行run()方法的线程执行体，则该线程处于运行状态

4. 阻塞状态，当处于运行状态的线程失去所占用资源之后，便进入阻塞状态.

5. 转换过程, 在线程的生命周期当中，线程的各种状态的转换过程

</details>

<details>
<summary>线程状态，BLOCKED 和 WAITING 有什么区别</summary>

线程可以通过wait,join,LockSupport.park方式进入wating状态，进入wating状态的线程等待唤醒(notify或notifyAll)才有机会获取cpu的时间片段来继续执行。线程的 blocked状态往往是无法进入同步方法/代码块来完成的。这是因为无法获取到与同步方法/代码块相关联的锁。与wating状态相关联的是等待队列，与blocked状态相关的是同步队列，一个线程由等待队列迁移到同步队列时，线程状态将会由wating转化为blocked。可以这样说，blocked状态是处于wating状态的线程重新焕发生命力的必由之路。

不过个人觉得实际上不用可以区分两者, 因为两者都会暂停线程的执行. 两者的区别是: 进入waiting状态是线程主动的, 而进入blocked状态是被动的. 更进一步的说, 进入blocked状态是在同步(synchronized代码之外), 而进入waiting状态是在同步代码之内.

</details>

<details>
<summary>ThreadLocal用途是什么，原理是什么，用的时候要注意什么？</summary>

在多线程程序中，同一个线程在某个时间段只能处理一个任务，我们希望在这个时间段内,任务的某些变量能够和处理它的线程进行绑定，,在任务需要使用这个变量的时候，这个变量能够方便的从线程中取出来。ThreadLocal能很好的满足这个需求，用ThreadLocal变量的程序看起来也会简洁很多，因为减少了变量在程序中的传递，每个运行的线程都会有一个类型为ThreadLocal。ThreadLocalMap的map，这个map就是用来存储与这个线程绑定的变量,map的key就是ThreadLocal对象，value就是线程正在执行的任务中的某个变量的包装类Entry、在使用ThreadLocal对象，尽量使用static，不然会使线程的ThreadLocalMap产生太多Entry，从而造成内存泄露。

</details>

<details>
<summary>什么是多线程中的上下文切换？</summary>

即使是单核CPU也支持多线程执行代码，CPU通过给每个线程分配CPU时间片来实现这个机制。时间片是CPU分配给各个线程的时间，因为时间片非常短，所以CPU通过不停地切换线程执行，让我们感觉多个线程时同时执行的，时间片一般是几十毫秒（ms）。CPU通过时间片分配算法来循环执行任务，当前任务执行一个时间片后会切换到下一个任务。但是，在切换前会保存上一个任务的状态，以便下次切换回这个任务时，可以再次加载这个任务的状态，从任务保存到再加载的过程就是一次上下文切换。

</details>

<details>
<summary>你对线程优先级的理解是什么？</summary>

每一个线程都是有优先级的, 一般来说, 高优先级的线程在运行时会具有优先权, 但这依赖于线程调度的实现, 这个实现是和操作系统相关的. 我们可以定义线程的优先级getPriority() setPriority(), 但是这并不能保证高优先级的线程会在低优先级的线程前执行. 县城优先级是一个int变量, 1代表最低, 10代表最高。

</details>

<details>
<summary>什么是线程调度器 (Thread Scheduler) 和时间分片 (Time Slicing)?</summary>

线程调度器是一个操作系统服务, 它负责为Runnable状态的线程分配CPU时间, 一旦我们创建一个线程并启动它, 它的执行便依赖于线程调度器的实现。时间分片是指将可用的CPU时间分配给可用的Runnable线程的过程。分配的CPU时间可以基于线程的优先级或者线程的等待时间, 线程调度并不受到Java虚拟机控制, 所以由应用程序来控制它是更好的选择(也就是说不要让你的程序依赖于线程优先级)。

</details>

<details>
<summary> 请说出你所知的线程同步的方法？</summary>

**1.同步方法。即有synchronized关键字修饰的方法。 由于java的每个对象都有一个内置锁，当用此关键字修饰方法时， 内置锁会保护整个方法。在调用该方法前，需要获得内置锁，否则就处于阻塞状态。**

代码如：

```java

public synchronized void save(){}

```

//注：synchronized关键字也可以修饰静态方法，此时如果调用该静态方法，将会锁住整个类


**2.同步代码块，即有synchronized关键字修饰的语句块。 被该关键字修饰的语句块会自动被加上内置锁，从而实现同步**

//代码如：

```java

synchronized(object){
}

```

注：同步是一种高开销的操作，因此应该尽量减少同步的内容。 通常没有必要同步整个方法，使用synchronized代码块同步关键代码即可。


```java

/**
     * 线程同步的运用
     */
    public class SynchronizedThread {

        class Bank {

            private int account = 100;

            public int getAccount() {
                return account;
            }

            /**
             * 用同步方法实现
             * @param money
             */
            public synchronized void save(int money) {
                account += money;
            }

            /**
             * 用同步代码块实现
             * @param money
             */
            public void save1(int money) {
                synchronized (this) {
                    account += money;
                }
            }
        }

        class NewThread implements Runnable {
            private Bank bank;

            public NewThread(Bank bank) {
                this.bank = bank;
            }

            @Override
            public void run() {
                for (int i = 0; i < 10; i++) {
                    // bank.save1(10);
                    bank.save(10);
                    System.out.println(i + "账户余额为：" + bank.getAccount());
                }
            }
        }

        /**
         * 建立线程，调用内部类
         */
        public void useThread() {
            Bank bank = new Bank();
            NewThread new_thread = new NewThread(bank);
            System.out.println("线程1");
            Thread thread1 = new Thread(new_thread);
            thread1.start();
            System.out.println("线程2");
            Thread thread2 = new Thread(new_thread);
            thread2.start();
        }

        public static void main(String[] args) {
            SynchronizedThread st = new SynchronizedThread();
            st.useThread();
        }
    }

```

**3.使用特殊域变量(volatile)实现线程同步**

a.volatile关键字为域变量的访问提供了一种免锁机制，

b.使用volatile修饰域相当于告诉虚拟机该域可能会被其他线程更新，

c.因此每次使用该域就要重新计算，而不是使用寄存器中的值

d.volatile不会提供任何原子操作，它也不能用来修饰final类型的变量

//只给出要修改的代码，其余代码与上同

```java

        class Bank {
            //需要同步的变量加上volatile
            private volatile int account = 100;

            public int getAccount() {
                return account;
            }
            //这里不再需要synchronized
            public void save(int money) {
                account += money;
            }

```
        
        
注：多线程中的非同步问题主要出现在对域的读写上，如果让域自身避免这个问题，则就不需要修改操作该域的方法。 用final域，有锁保护的域和volatile域可以避免非同步的问题。

**4.使用重入锁实现线程同步，在JavaSE5.0中新增了一个java.util.concurrent包来支持同步。 ReentrantLock类是可重入、互斥、实现了Lock接口的锁， 它与使用synchronized方法和快具有相同的基本行为和语义，并且扩展了其能力ReenreantLock类的常用方法有：**

ReentrantLock() : 创建一个ReentrantLock实例

lock() : 获得锁

unlock() : 释放锁

注：ReentrantLock()还有一个可以创建公平锁的构造方法，但由于能大幅度降低程序运行效率，不推荐使用

**5.使用局部变量实现线程同步。如果使用ThreadLocal管理变量，则每一个使用该变量的线程都获得该变量的副本， 副本之间相互独立，这样每一个线程都可以随意修改自己的变量副本，而不会对其他线程产生影响。**

ThreadLocal 类的常用方法

ThreadLocal() : 创建一个线程本地变量

get() : 返回此线程局部变量的当前线程副本中的值

initialValue() : 返回此线程局部变量的当前线程的"初始值"

set(T value) : 将此线程局部变量的当前线程副本中的值设置为value

注：ThreadLocal与同步机制：a.ThreadLocal与同步机制都是为了解决多线程中相同变量的访问冲突问题。 b.前者采用以”空间换时间”的方法，后者采用以”时间换空间”的方式

**6.使用阻塞队列实现线程同步，**

**7.使用原子变量实现线程同步**

[关于线程同步的7种方式](https://www.cnblogs.com/XHJT/p/3897440.html)

</details>

<details>
<summary> synchronized 的原理是什么</summary>

Synchronized的语义底层是通过一个monitor（监视器锁）来实现的。Synchronized进过编译，会在同步块的前后分别形成monitorenter和monitorexit这个两个字节码指令。在执行monitorenter指令时，首先要尝试获取对象锁。如果这个对象没被锁定，或者当前线程已经拥有了那个对象锁，把锁的计算器加1，相应的，在执行monitorexit指令时会将锁计算器就减1，当计算器为0时，锁就被释放了。如果获取对象锁失败，那当前线程就要阻塞，直到对象锁被另一个线程释放为止。

monitorenter ：
Each object is associated with a monitor. A monitor is locked if and only if it has an owner. The thread that executes monitorenter attempts to gain ownership of the monitor associated with objectref, as follows:
• If the entry count of the monitor associated with objectref is zero, the thread enters the monitor and sets its entry count to one. The thread is then the owner of the monitor.
• If the thread already owns the monitor associated with objectref, it reenters the monitor, incrementing its entry count.
• If another thread already owns the monitor associated with objectref, the thread blocks until the monitor’s entry count is zero, then tries again to gain ownership.

每个对象有一个监视器锁（monitor）。当monitor被占用时就会处于锁定状态，线程执行monitorenter指令时尝试获取monitor的所有权，过程如下：
1、如果monitor的进入数为0，则该线程进入monitor，然后将进入数设置为1，该线程即为monitor的所有者。
2、如果线程已经占有该monitor，只是重新进入，则进入monitor的进入数加1.
3.如果其他线程已经占用了monitor，则该线程进入阻塞状态，直到monitor的进入数为0，再重新尝试获取monitor的所有权。

monitorexit：　
The thread that executes monitorexit must be the owner of the monitor associated with the instance referenced by objectref.
The thread decrements the entry count of the monitor associated with objectref. If as a result the value of the entry count is zero, the thread exits the monitor and is no longer its owner. Other threads that are blocking to enter the monitor are allowed to attempt to do so.

执行monitorexit的线程必须是objectref所对应的monitor的所有者。指令执行时，monitor的进入数减1，如果减1后进入数为0，那线程退出monitor，不再是这个monitor的所有者。其他被这个monitor阻塞的线程可以尝试去获取这个 monitor 的所有权。

[深入分析Synchronized](https://blog.csdn.net/shandian000/article/details/54927876) [Java并发编程之——Synchronized](https://www.cnblogs.com/paddix/p/5367116.html)


</details>

<details>
<summary>synchronized 和ReentrantLock有什么不同</summary>

相似点：这两种同步方式有很多相似之处，它们都是加锁方式同步，而且都是阻塞式的同步，也就是说当如果一个线程获得了对象锁，进入了同步块，其他访问该同步块的线程都必须阻塞在同步块外面等待，而进行线程阻塞和唤醒的代价是比较高的（操作系统需要在用户态与内核态之间来回切换，代价很高，不过可以通过对锁优化进行改善）。

区别：这两种方式最大区别就是对于Synchronized来说，它是java语言的关键字，是原生语法层面的互斥，需要jvm实现。而ReentrantLock它是JDK 1.5之后提供的API层面的互斥锁，需要lock()和unlock()方法配合try/finally语句块来完成。

总的来说，Lock提供了比synchronized更多的功能。但是要注意以下几点：

1）Lock不是Java语言内置的，synchronized是Java语言的关键字，因此是内置特性。Lock是一个类，通过这个类可以实现同步访问；

2）Lock和synchronized有一点非常大的不同，采用synchronized不需要用户去手动释放锁，当synchronized方法或者synchronized代码块执行完之后，系统会自动让线程释放对锁的占用；而Lock则必须要用户去手动释放锁，如果没有主动释放锁，就有可能导致出现死锁现象。
synchronized在发生异常时，会自动释放线程占有的锁，因此不会导致死锁现象发生；而Lock在发生异常时，如果没有主动通过unLock()去释放锁，则很可能造成死锁现象，因此使用Lock时需要在finally块中释放锁；

3）Lock可以让等待锁的线程响应中断，而synchronized却不行，使用synchronized时，等待的线程会一直等待下去，不能够响应中断；

4）通过Lock可以知道有没有成功获取锁，而synchronized却无法办到。

5）Lock可以提高多个线程进行读操作的效率。

在性能上来说，如果竞争资源不激烈，两者的性能是差不多的，而当竞争资源非常激烈时（即有大量线程同时竞争），此时Lock的性能要远远优于synchronized。所以说，在具体使用时要根据适当情况选择。

[java两种线程同步方式的区别——Synchronized和ReentrantLock](https://blog.csdn.net/chenchaofuck1/article/details/51045134)

</details>

<details>
<summary>什么场景下可以使用 volatile 替换 synchronized</summary>
  
只需要保证共享资源的可见性的时候可以使用volatile替代，synchronized保证可操作的原子性一致性和可见性。volatile适用于新值不依赖于旧值的情形

</details>

<details>
<summary>有T1，T2，T3三个线程，怎么确保它们按顺序执行？怎样保证T2在T1执行完后执行，T3在T2执行完后执行</summary>
  
这里考察的主要知识点就是线程同步机制和锁的问题，[Java 指定线程执行顺序（三种方式）](https://blog.csdn.net/difffate/article/details/63684290)
另外关于执行顺序的问题，可以有很多的考察点，可以刷刷这个博客[java指定线程执行的顺序](https://blog.csdn.net/BeauXie/article/details/53018570)

</details>

<details>
<summary>同步块内的线程抛出异常会发生什么</summary>


这个问题坑了很多Java程序员，若你能想到锁是否释放这条线索来回答还有点希望答对。无论你的同步块是正常还是异常退出的，里面的线程都会释放锁，所以对比锁接口我更喜欢同步块，因为它不用我花费精力去释放锁，该功能可以在finally block里释放锁实现。

</details>

<details>
<summary>当一个线程进入一个对象的synchronized 方法A 之后，其它线程是否可进入此对象的synchronized 方法B</summary>

不能。其它线程只能访问该对象的非同步方法，同步方法则不能进入。因为非静态方法上的synchronized修饰符要求执行方法时要获得对象的锁，如果已经进入A方法说明对象锁已经被取走，那么试图进入B方法的线程就只能在等锁池（注意不是等待池哦 ）中等待对象的锁

</details>

<details>
<summary>使用 synchronized 修饰静态方法和非静态方法有什么区别</summary>

在static方法前加synchronizedstatic：静态方法属于类方法，它属于这个类，获取到的锁，是属于类的锁。 在普通方法前加synchronizedstatic：非static方法获取到的锁，是属于当前对象的锁。 结论：类锁和对象锁不同，他们之间不会产生互斥。

</details>

<details>
<summary>如何从给定集合那里创建一个 synchronized 的集合</summary>

我们可以使用Collections.synchronizedCollection(Collection c)根据指定集合来获取一个synchronized（线程安全的）集合。比如HashMap可以这样来实现线程安全：

</details>

<details>
<summary>Java Concurrency API 中的 Lock接口是什么？对比同步它有什么优势？</summary>

Lock接口比同步方法和同步块（这里的同步就是考察Synchronized关键字）提供了更具扩展性的锁操作。

如果一个代码块被synchronized修饰了，当一个线程获取了对应的锁，并执行该代码块时，其他线程便只能一直等待，等待获取锁的线程释放锁，而这里获取锁的线程释放锁只会有两种情况：

     获取锁的线程执行完了该代码块，然后线程释放对锁的占有；

     线程执行发生异常，此时JVM会让线程自动释放锁。

那么如果这个获取锁的线程由于要等待IO或者其他原因（比如调用sleep方法）被阻塞了，但是又没有释放锁，其他线程便只能阻塞等待，非常影响效率。因此就需要有一种机制可以不让等待的线程一直无期限地等待下去（比如只等待一定的时间或者能够响应中断），通过Lock就可以办到。

再举个例子：当有多个线程读写文件时，读操作和写操作会发生冲突现象，写操作和写操作会发生冲突现象，但是读操作和读操作不会发生冲突现象。

但是采用synchronized关键字来实现同步的话，就会导致一个问题：如果多个线程都只是进行读操作，所以当一个线程在进行读操作时，其他线程只能等待无法进行读操作。因此就需要一种机制来使得多个线程都只是进行读操作时，线程之间不会发生冲突，通过Lock就可以办到。

Lock不是Java语言内置的，synchronized是Java语言的关键字，因此是内置特性，Lock是一个类，通过这个类可以实现同步访问；他们允许更灵活的结构，可以具有完全不同的性质，并且可以支持多个相关类的条件对象。它的优势有：可以使锁更公平；可以使线程在等待锁的时候响应中断；可以让线程尝试获取锁，并在无法获取锁的时候立即返回或者等待一段时间；可以在不同的范围，以不同的顺序获取和释放锁。

关于API及代码的例子请移步：《java并发编程Lock》。常用接口方法如下：

```java

public interface Lock {
    void lock();
    void lockInterruptibly() throws InterruptedException;
    boolean tryLock();
    boolean tryLock(long time, TimeUnit unit) throws InterruptedException;
    void unlock();
    Condition newCondition();
}

```

首先lock()方法是平常使用得最多的一个方法，就是用来获取锁。如果锁已被其他线程获取，则进行等待。由于在前面讲到如果采用Lock，必须主动去释放锁，并且在发生异常时，不会自动释放锁。因此一般来说，使用Lock必须在try{}catch{}块中进行，并且将释放锁的操作放在finally块中进行，以保证锁一定被被释放，防止死锁的发生。通常使用Lock来进行同步的话，是以下面这种形式去使用的：

```java

Lock lock = ...;
lock.lock();
try{
    //处理任务
}catch(Exception ex){

}finally{
    lock.unlock();   //释放锁
}

```

tryLock()方法是有返回值的，它表示用来尝试获取锁，如果获取成功，则返回true，如果获取失败（即锁已被其他线程获取），则返回false，也就说这个方法无论如何都会立即返回。在拿不到锁时不会一直在那等待。tryLock(long time, TimeUnit unit)方法和tryLock()方法是类似的，只不过区别在于这个方法在拿不到锁时会等待一定的时间，在时间期限之内如果还拿不到锁，就返回false。如果如果一开始拿到锁或者在等待期间内拿到了锁，则返回true。

```java

Lock lock = ...;
if(lock.tryLock()) {
     try{
         //处理任务
     }catch(Exception ex){

     }finally{
         lock.unlock();   //释放锁
     }
}else {
    //如果不能获取锁，则直接做其他事情
}

```

lockInterruptibly()方法比较特殊，当通过这个方法去获取锁时，如果线程正在等待获取锁，则这个线程能够响应中断，即中断线程的等待状态。也就使说，当两个线程同时通过lock.lockInterruptibly()想获取某个锁时，假若此时线程A获取到了锁，而线程B只有在等待，那么对线程B调用threadB.interrupt()方法能够中断线程B的等待过程。由于lockInterruptibly()的声明中抛出了异常，所以lock.lockInterruptibly()必须放在try块中或者在调用lockInterruptibly()的方法外声明抛出InterruptedException。

```java

public void method() throws InterruptedException {
    lock.lockInterruptibly();
    try {
     //.....
    }
    finally {
        lock.unlock();
    }
    
```    
    
ReentrantLock，意思是“可重入锁”，ReentrantLock是唯一实现了Lock接口的类，并且ReentrantLock提供了更多的方法。以下给出一个ReentrantLock的运行实例：

```java

public class Test {
    private ArrayList<Integer> arrayList = new ArrayList<Integer>();
    private Lock lock = new ReentrantLock();    //注意这个地方，声明为类的属性
    public static void main(String[] args)  {
        final Test test = new Test();

        new Thread(){
            public void run() {
                test.insert(Thread.currentThread());
            };
        }.start();
         //可以用Java箭头函数特性改写上述冗余代码：
         // new Thread(){()->Thread.currentThread}.start();
        new Thread(){
            public void run() {
                test.insert(Thread.currentThread());
            };
        }.start();
    }

    public void insert(Thread thread) {
        lock.lock();
        try {
            System.out.println(thread.getName()+"得到了锁");
            for(int i=0;i<5;i++) {
                arrayList.add(i);
            }
        } catch (Exception e) {
            // TODO: handle exception
        }finally {
            System.out.println(thread.getName()+"释放了锁");
            lock.unlock();
        }
    }
    
```

上文中提到了Lock接口以及对象，使用它，很优雅的控制了竞争资源的安全访问，但是这种锁不区分读写，称这种锁为普通锁。为了提高性能，Java提供了读写锁，在读的地方使用读锁，在写的地方使用写锁，灵活控制，如果没有写锁的情况下，读是无阻塞的,在一定程度上提高了程序的执行效率。Java中读写锁有个接口java.util.concurrent.locks. ReadWriteLock，也有具体的实现ReentrantReadWriteLock，

[Java并发编程：Lock](https://www.cnblogs.com/dolphin0520/p/3923167.html)

</details>

<details>
<summary>ReadWriteLock是什么？</summary>

当有写线程时，则写线程独占同步状态，当没有写线程时只有读线程时，则多个读线程可以共享同步状态。读写锁就是为了实现这种效果而生。

读写锁：分为读锁和写锁，多个读锁不互斥，读锁与写锁互斥，这是由jvm自己控制的，我们只要上好相应的锁即可。如果你的代码只读数据，可以很多人同时读，但不能同时写，那就上读锁；如果你的代码修改数据，只能有一个人在写，且不能同时读取，那就上写锁。总之，读的时候上读锁，写的时候上写锁！读写锁接口：ReadWriteLock，它的具体实现类为：ReentrantReadWriteLock。

[ReadWriteLock场景应用](https://www.cnblogs.com/liang1101/p/6475555.html?utm_source=itdadao&utm_medium=referral)：在多线程的环境下，对同一份数据进行读写，会涉及到线程安全的问题。比如在一个线程读取数据的时候，另外一个线程在写数据，而导致前后数据的不一致性；一个线程在写数据的时候，另一个线程也在写，同样也会导致线程前后看到的数据的不一致性。这时候可以在读写方法中加入互斥锁，任何时候只能允许一个线程的一个读或写操作，而不允许其他线程的读或写操作，这样是可以解决这样以上的问题，但是效率却大打折扣了。因为在真实的业务场景中，一份数据，读取数据的操作次数通常高于写入数据的操作，而线程与线程间的读读操作是不涉及到线程安全的问题，没有必要加入互斥锁，只要在读-写，写-写期间上锁就行了。API调用请移步

构造了一个线程安全的缓存，先创建一个ReentrantReadWriteLock对象，构造函数 false 代表是非公平的（非公平的含义和ReentrantLock相同）。然后通过readLock、writeLock方法分别获取读锁和写锁。在做读操作的时候，也就是get方法，我们要先获取读锁；在做写操作的时候，即put方法，我们要先获取写锁。

```java

public class ReadWriteCache {
    private static Map<String, Object> data = new HashMap<>();
    private static ReadWriteLock lock = new ReentrantReadWriteLock(false);
    private static Lock rlock = lock.readLock();
    private static Lock wlock = lock.writeLock();

    public static Object get(String key) {
        rlock.lock();
        try {
            return data.get(key);
        } finally {
            rlock.unlock();
        }
    }

    public static Object put(String key, Object value) {
        wlock.lock();
        try {
            return data.put(key, value);
        } finally {
            wlock.unlock();
        }
    }
}

```

</details>

<details>
<summary>锁机制有什么用</summary>

有些业务逻辑在执行过程中要求对数据进行排他性的访问，于是需要通过一些机制保证在此过程中数据被锁住不会被外界修改，这就是所谓的锁机制。

</details>

<details>
<summary>什么是乐观锁（Optimistic Locking）？如何实现乐观锁？如何避免ABA问题</summary>

悲观锁(Pessimistic Lock), 顾名思义，就是很悲观，每次去拿数据的时候都认为别人会修改，所以每次在拿数据的时候都会上锁，这样别人想拿这个数据就会block直到它拿到锁。传统的关系型数据库里边就用到了很多这种锁机制，比如行锁，表锁等，读锁，写锁等，都是在做操作之前先上锁。

乐观锁(Optimistic Lock), 顾名思义，就是很乐观，每次去拿数据的时候都认为别人不会修改，所以不会上锁，但是在更新的时候会判断一下在此期间别人有没有去更新这个数据，可以使用版本号等机制。乐观锁适用于多读的应用类型，这样可以提高吞吐量，像数据库如果提供类似于write_condition机制的其实都是提供的乐观锁。

</details>

<details>
<summary>解释以下名词：重排序，自旋锁，偏向锁，轻量级锁，可重入锁，公平锁，非公平锁，乐观锁，悲观锁</summary>

重入锁（ReentrantLock）是一种递归无阻塞的同步机制。重入锁，也叫做递归锁，指的是同一线程 外层函数获得锁之后 ，内层递归函数仍然有获取该锁的代码，但不受影响。在JAVA环境下 ReentrantLock 和synchronized都是 可重入锁。

自旋锁，由于自旋锁使用者一般保持锁时间非常短，因此选择自旋而不是睡眠是非常必要的，自旋锁的效率远高于互斥锁。如何旋转呢？何为自旋锁，就是如果发现锁定了，不是睡眠等待，而是采用让当前线程不停地的在循环体内执行实现的，当循环的条件被其他线程改变时 才能进入临界区。

偏向锁(Biased Locking)是Java6引入的一项多线程优化，它会偏向于第一个访问锁的线程，如果在运行过程中，同步锁只有一个线程访问，不存在多线程争用的情况，则线程是不需要触发同步的，这种情况下，就会给线程加一个偏向锁。 如果在运行过程中，遇到了其他线程抢占锁，则持有偏向锁的线程会被挂起，JVM会消除它身上的偏向锁，将锁恢复到标准的轻量级锁。

轻量级锁是由偏向所升级来的，偏向锁运行在一个线程进入同步块的情况下，当第二个线程加入锁争用的时候，偏向锁就会升级为轻量级锁。

重入锁（ReentrantLock）是一种递归无阻塞的同步机制，也叫做递归锁，指的是同一线程 外层函数获得锁之后 ，内层递归函数仍然有获取该锁的代码，但不受影响。 在JAVA环境下 ReentrantLock 和synchronized都是 可重入锁。

公平锁，就是很公平，在并发环境中，每个线程在获取锁时会先查看此锁维护的等待队列，如果为空，或者当前线程线程是等待队列的第一个，就占有锁，否则就会加入到等待队列中，以后会按照FIFO的规则从队列中取到自己

非公平锁比较粗鲁，上来就直接尝试占有锁。在公平的锁上，线程按照他们发出请求的顺序获取锁，但在非公平锁上，则允许‘插队’：当一个线程请求非公平锁时，如果在发出请求的同时该锁变成可用状态，那么这个线程会跳过队列中所有的等待线程而获得锁。 非公平的ReentrantLock 并不提倡 插队行为，但是无法防止某个线程在合适的时候进行插队。

</details>

<details>
<summary>什么时候应该使用可重入锁？</summary>

场景1：如果已加锁，则不再重复加锁。a、忽略重复加锁。b、用在界面交互时点击执行较长时间请求操作时，防止多次点击导致后台重复执行（忽略重复触发）。以上两种情况多用于进行非重要任务防止重复执行，（如：清除无用临时文件，检查某些资源的可用性，数据备份操作等）

场景2：如果发现该操作已经在执行，则尝试等待一段时间，等待超时则不执行（尝试等待执行）这种其实属于场景2的改进，等待获得锁的操作有一个时间的限制，如果超时则放弃执行。用来防止由于资源处理不当长时间占用导致死锁情况（大家都在等待资源，导致线程队列溢出）。

场景3：如果发现该操作已经加锁，则等待一个一个加锁（同步执行，类似synchronized）这种比较常见大家也都在用，主要是防止资源使用冲突，保证同一时间内只有一个操作可以使用该资源。但与synchronized的明显区别是性能优势（伴随jvm的优化这个差距在减小）。同时Lock有更灵活的锁定方式，公平锁与不公平锁，而synchronized永远是公平的。这种情况主要用于对资源的争抢（如：文件操作，同步消息发送，有状态的操作等）

场景4：可中断锁。synchronized与Lock在默认情况下是不会响应中断(interrupt)操作，会继续执行完。lockInterruptibly()提供了可中断锁来解决此问题。（场景3的另一种改进，没有超时，只能等待中断或执行完毕）这种情况主要用于取消某些操作对资源的占用。如：（取消正在同步运行的操作，来防止不正常操作长时间占用造成的阻塞）

</details>

<details>
<summary>简述锁的等级方法锁、对象锁、类锁</summary>

方法锁（synchronized修饰方法时）通过在方法声明中加入 synchronized关键字来声明 synchronized方法。synchronized方法控制对类成员变量的访问： 每个类实例对应一把锁，每个synchronized方法都必须获得调用该方法的类实例的锁方能执行，否则所属线程阻塞，方法一旦执行，就独占该锁，直到从该方法返回时才将锁释放，此后被阻塞的线程方能获得该锁，重新进入可执行状态。这种机制确保了同一时刻对于每一个类实例，其所有声明为 synchronized的成员函数中至多只有一个处于可执行状态，从而有效避免了类成员变量的访问冲突。

对象锁（synchronized修饰方法或代码块）当一个对象中有synchronized method或synchronized block的时候调用此对象的同步方法或进入其同步区域时，就必须先获得对象锁。如果此对象的对象锁已被其他调用者占用，则需要等待此锁被释放。（方法锁也是对象锁）。java的所有对象都含有1个互斥锁，这个锁由JVM自动获取和释放。线程进入synchronized方法的时候获取该对象的锁，当然如果已经有线程获取了这个对象的锁，那么当前线程会等待；synchronized方法正常返回或者抛异常而终止，JVM会自动释放对象锁。这里也体现了用synchronized来加锁的1个好处，方法抛异常的时候，锁仍然可以由JVM来自动释放。　

类锁(synchronized修饰静态的方法或代码块)，由于一个class不论被实例化多少次，其中的静态方法和静态变量在内存中都只有一份。所以，一旦一个静态的方法被申明为synchronized。此类所有的实例化对象在调用此方法，共用同一把锁，我们称之为类锁。对象锁是用来控制实例方法之间的同步，类锁是用来控制静态方法（或静态变量互斥体）之间的同步。类锁只是一个概念上的东西，并不是真实存在的，它只是用来帮助我们理解锁定实例方法和静态方法的区别的。java类可能会有很多个对象，但是只有1个Class对象，也就是说类的不同实例之间共享该类的Class对象。Class对象其实也仅仅是1个java对象，只不过有点特殊而已。由于每个java对象都有1个互斥锁，而类的静态方法是需要Class对象。所以所谓的类锁，不过是Class对象的锁而已。获取类的Class对象有好几种，最简单的就是［类名.class］的方式。

</details>

<details>
<summary>Java中活锁和死锁有什么区别？</summary>

死锁：是指两个或两个以上的进程（或线程）在执行过程中，因争夺资源而造成的一种互相等待的现象，若无外力作用，它们都将无法推进下去。此时称系统处于死锁状态或系统产生了死锁，这些永远在互相等待的进程称为死锁进程。死锁发生的四个条件

1、互斥条件：线程对资源的访问是排他性的，如果一个线程对占用了某资源，那么其他线程必须处于等待状态，直到资源被释放。

2、请求和保持条件：线程T1至少已经保持了一个资源R1占用,但又提出对另一个资源R2请求，而此时，资源R2被其他线程T2占用，于是该线程T1也必须等待，但又对自己保持的资源R1不释放。

3、不剥夺条件：线程已获得的资源，在未使用完之前，不能被其他线程剥夺，只能在使用完以后由自己释放。

4、环路等待条件：在死锁发生时，必然存在一个“进程-资源环形链”，即：{p0,p1,p2,...pn},进程p0（或线程）等待p1占用的资源，p1等待p2占用的资源，pn等待p0占用的资源。（最直观的理解是，p0等待p1占用的资源，而p1而在等待p0占用的资源，于是两个进程就相互等待）

活锁：是指线程1可以使用资源，但它很礼貌，让其他线程先使用资源，线程2也可以使用资源，但它很绅士，也让其他线程先使用资源。这样你让我，我让你，最后两个线程都无法使用资源。

</details>

<details>
<summary>如何确保 N 个线程可以访问 N 个资源同时又不导致死锁？</summary>
  
预防死锁，预先破坏产生死锁的四个条件。互斥不可能破坏，所以有如下3种方法：

1.破坏，请求和保持条件1.1）进程等所有要请求的资源都空闲时才能申请资源，这种方法会使资源严重浪费（有些资源可能仅在运行初期或结束时才使用，甚至根本不使用）1.2）允许进程获取初期所需资源后，便开始运行，运行过程中再逐步释放自己占有的资源。比如有一个进程的任务是把数据复制到磁盘中再打印，前期只需要获得磁盘资源而不需要获得打印机资源，待复制完毕后再释放掉磁盘资源。这种方法比上一种好，会使资源利用率上升。

2.破坏，不可抢占条件。这种方法代价大，实现复杂

3.破坏，循坏等待条件。对各进程请求资源的顺序做一个规定，避免相互等待。这种方法对资源的利用率比前两种都高，但是前期要为设备指定序号，新设备加入会有一个问题，其次对用户编程也有限制

</details>

<details>
<summary>死锁与饥饿的区别？</summary>

相同点：二者都是由于竞争资源而引起的。

不同点：

从进程状态考虑，死锁进程都处于等待状态，忙等待(处于运行或就绪状态)的进程并非处于等待状态，但却可能被饿死；
死锁进程等待永远不会被释放的资源，饿死进程等待会被释放但却不会分配给自己的资源，表现为等待时限没有上界(排队等待或忙式等待)；
死锁一定发生了循环等待，而饿死则不然。这也表明通过资源分配图可以检测死锁存在与否，但却不能检测是否有进程饿死；
死锁一定涉及多个进程，而饥饿或被饿死的进程可能只有一个。

在饥饿的情形下，系统中有至少一个进程能正常运行，只是饥饿进程得不到执行机会。而死锁则可能会最终使整个系统陷入死锁并崩溃

</details>

<details>
<summary>怎么检测一个线程是否拥有锁？</summary>
  
java.lang.Thread中有一个方法叫holdsLock()，它返回true如果当且仅当当前线程拥有某个具体对象的锁

```java

Object o = new Object();

@Test
public void test1() throws Exception {

    new Thread(new Runnable() {

        @Override
        public void run() {
            synchronized(o) {
                System.out.println("child thread: holdLock: " +
                    Thread.holdsLock(o));
            }
        }
    }).start();

    System.out.println("main thread: holdLock: " + Thread.holdsLock(o));
    Thread.sleep(2000);

}

```

main thread: holdLock: false
child thread: holdLock: true


</details>

<details>
<summary>如何实现分布式锁？</summary>
  
基于数据库实现分布式锁

基于缓存（redis，memcached，tair）实现分布式锁

基于Zookeeper实现分布式锁

可以参考详情[分布式锁的几种实现方式](http://www.hollischuang.com/archives/1716) 、 [分布式锁的3种方式](https://www.cnblogs.com/rwxwsblog/p/6046034.html)

</details>

<details>
<summary>有哪些无锁数据结构，他们实现的原理是什么？</summary>

java 1.5提供了一种无锁队列（wait-free/lock-free）ConcurrentLinkedQueue，可支持多个生产者多个消费者线程的环境：网上别人自己实现的一种无锁算法队列，原理和jdk官方的ConcurrentLinkedQueue相似：通过volatile关键字来保证数据唯一性（注：java的volatile和c++的volatile关键字是两码事！），但是里面又用到atomic，感觉有点boost::lockfree::queue的风格，估计参考了boost的代码来编写这个java无锁队列。

</details>

<details>
<summary>Executors类是什么？ Executor和Executors的区别</summary>

正如上面所说，这三者均是 Executor 框架中的一部分。Java 开发者很有必要学习和理解他们，以便更高效的使用 Java 提供的不同类型的线程池。总结一下这三者间的区别，以便大家更好的理解：

Executor和 ExecutorService 这两个接口主要的区别是：ExecutorService 接口继承了Executor 接口，是Executor的子接口
Executor和ExecutorService 第二个区别是：Executor接口定义了 execute()方法用来接收一个Runnable接口的对象，而 ExecutorService接口中的 submit()方法可以接受Runnable和Callable接口的对象。

Executor 和 ExecutorService 接口第三个区别是Executor中的 execute()方法不返回任何结果，而 ExecutorService中的 submit()方法可以通过一个 Future对象返回运算结果。

Executor 和 ExecutorService 接口第四个区别是除了允许客户端提交一个任务，ExecutorService 还提供用来控制线程池的方法。比如：调用 shutDown()方法终止线程池。可以通过 《Java Concurrency in Practice》 一书了解更多关于关闭线程池和如何处理 pending 的任务的知识。

Executors 类提供工厂方法用来创建不同类型的线程池。比如: newSingleThreadExecutor() 创建一个只有一个线程的线程池，newFixedThreadPool(int numOfThreads)来创建固定线程数的线程池，newCachedThreadPool()可以根据需要创建新的线程，但如果已有线程是空闲的会重用已有线程。

Executor ExecutorService
Executor 是 Java 线程池的核心接口，用来并发执行提交的任务 ExecutorService 是 Executor 接口的扩展，提供了异步执行和关闭线程池的方法
提供execute()方法用来提交任务 提供submit()方法用来提交任务
execute()方法无返回值 submit()方法返回Future对象，可用来获取任务执行结果
不能取消任务 可以通过Future.cancel()取消pending中的任务
没有提供和关闭线程池有关的方法 提供了关闭线程池的方法


</details>

<details>
<summary>什么是Java线程转储(Thread Dump)，如何得到它？</summary>

线程转储是一个JVM活动线程的列表，它对于分析系统瓶颈和死锁非常有用。
有很多方法可以获取线程转储——使用Profiler，Kill -3命令，jstack工具等等。我更喜欢jstack工具，因为它容易使用并且是JDK自带的。由于它是一个基于终端的工具，所以我们可以编写一些脚本去定时的产生线程转储以待分析。

</details>

<details>
<summary>如何在Java中获取线程堆栈？  </summary>

Java虚拟机提供了线程转储（thread dump）的后门，通过这个后门可以把线程堆栈打印出来。通常我们将堆栈信息重定向到一个文件中，便于我们分析，由于信息量太大，很可能超出控制台缓冲区的最大行数限制造成信息丢失。这里介绍一个jdk自带的打印线程堆栈的工具，jstack用于打印出给定的Java进程ID或core file或远程调试服务的Java堆栈信息。（Java问题定位之Java线程堆栈分析）

示例：$jstack –l 23561 >> xxx.dump
命令 : $jstack [option] pid >> 文件
表示输出到文件尾部，实际运行中，往往一次dump的信息，还不足以确认问题，建议产生三次dump信息，如果每次dump都指向同一个问题，我们才确定问题的典型性。

</details>

<details>
<summary>说出 3 条在 Java 中使用线程的最佳实践</summary>

给你的线程起个有意义的名字。这样可以方便找bug或追踪。OrderProcessor, QuoteProcessor or TradeProcessor这种名字比Thread-1. Thread-2 and Thread-3好多了，给线程起一个和它要完成的任务相关的名字，所有的主要框架甚至JDK都遵循这个最佳实践。

避免锁定和缩小同步的范围锁花费的代价高昂且上下文切换更耗费时间空间，试试最低限度的使用同步和锁，缩小临界区。因此相对于同步方法我更喜欢同步块，它给我拥有对锁的绝对控制权。

多用同步类少用wait和notify首先，CountDownLatch, Semaphore, CyclicBarrier和Exchanger这些同步类简化了编码操作，而用wait和notify很难实现对复杂控制流的控制。其次，这些类是由最好的企业编写和维护在后续的JDK中它们还会不断优化和完善，使用这些更高等级的同步工具你的程序可以不费吹灰之力获得优化。

多用并发集合少用同步集合，这是另外一个容易遵循且受益巨大的最佳实践，并发集合比同步集合的可扩展性更好，所以在并发编程时使用并发集合效果更好。如果下一次你需要用到map，你应该首先想到用ConcurrentHashMap。

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
<summary>Java中的两种锁</summary>
  
目前Java中存在两种锁机制：Synchronized(使用广泛)和Lock。

数据同步需要依赖锁，而Synchronized锁的同步在软件层面需要依赖JVM；Lock锁的同步在硬件层面需要依赖特殊的CPU指令。
  
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

<details>
<summary>守护线程</summary>

1、setDaemon(true)必须在调用线程的start（）方法之前设置，否则会抛出IllegalThreadStateException异常。

2、在守护线程中产生的新线程也是守护线程。

3、 不要认为所有的应用都可以分配给守护线程来进行服务，比如读写操作或者计算逻辑。

</details>


<details>
<summary>volatile和synchronized区别</summary>

1）volatile本质是在告诉jvm当前变量在寄存器中的值是不确定的,需要从主存中读取,synchronized则是锁定当前变量,只有当前线程可以访问该变量,其他线程被阻塞住.

2）volatile仅能使用在变量级别,synchronized则可以使用在变量,方法.

3）volatile仅能实现变量的修改可见性,而synchronized则可以保证变量的修改可见性和原子性.

　　《Java编程思想》上说，定义long或double变量时，如果使用volatile关键字，就会获得（简单的赋值与返回操作）原子性。 
　　 
4）volatile不会造成线程的阻塞,而synchronized可能会造成线程的阻塞.

5、当一个域的值依赖于它之前的值时，volatile就无法工作了，如n=n+1,n++等。如果某个域的值受到其他域的值的限制，那么volatile也无法工作，如Range类的lower和upper边界，必须遵循lower<=upper的限制。

6、使用volatile而不是synchronized的唯一安全的情况是类中只有一个可变的域。

</details>

<details>
<summary>synchronized和lock区别</summary>

   1）Lock是一个接口，而synchronized是Java中的关键字，synchronized是内置的语言实现；

　　2）synchronized在发生异常时，会自动释放线程占有的锁，因此不会导致死锁现象发生；而Lock在发生异常时，如果没有主动通过unLock()去释放锁，则很可能造成死锁现象，因此使用Lock时需要在finally块中释放锁；

　　3）Lock可以让等待锁的线程响应中断，而synchronized却不行，使用synchronized时，等待的线程会一直等待下去，不能够响应中断；

　　4）通过Lock可以知道有没有成功获取锁，而synchronized却无法办到。

　　5）Lock可以提高多个线程进行读操作的效率。

</details>


---
---

<details>
<summary>Java中用到的线程调度算法是什么？</summary>

</details>

<details>
<summary>什么 Java 原型不是线程安全的？</summary>

</details>

<details>
<summary>什麽是Futuretask,future, callable</summary>

</details>

<details>
<summary>什麽是虚假唤醒？</summary>

</details>


<details>
<summary>什么是原子操作？Java 中有哪些原子操作？</summary>

</details>

<details>
<summary>为什么要使用线程池？</summary>


</details>

<details>
<summary>线程池有什么作用？</summary>


</details>

<details>
<summary>说说几种常见的线程池及使用场景。</summary>


</details>

<details>
<summary>线程池都有哪几种工作队列？</summary>


</details>

<details>
<summary>线程池中的几种重要的参数及流程说明</summary>


</details>
Java 中 volatile 关键字是什么？你如何使用它？它和 Java 中的同步方法有什么区别？

什么是竞态条件？你如何发现并解决竞态条件？

在 Java 中你如何转储线程（thread dump）？如何分析它？

既然 start() 方法会调用 run() 方法，为什么我们调用 start() 方法，而不直接调用 run() 方法？

Java 中你如何唤醒阻塞线程？

Java 中 CyclicBarriar 和 CountdownLatch 有什么区别？

什么是不可变类？它对于编写并发应用有何帮助？

你在多线程环境中遇到的最多的问题是什么？你如何解决的？

Java 中绿色线程和本地线程的区别？

线程和进程的区别？

多线程的上下文切换是什么？

死锁和活锁的区别？死锁和饥饿的区别？

Java 中使用什么线程调度算法？

Java 中线程调度是什么？

线程中如何处理某个未处理异常？

什么是线程组？为什么 Java 中不建议使用线程组？

为什么使用 Executor 框架比直接创建线程要好？

Java 中 Executor 和 Executors 的区别

在 windows 和 linux 系统上分别如何找到占用 CPU 最多的线程？
1.Java创建线程之后，直接调用start()方法和run()的区别

2.常用的线程池模式以及不同线程池的使用场景

3.newFixedThreadPool此种线程池如果线程数达到最大值后会怎么办，底层原理。

4.多线程之间通信的同步问题，synchronized锁的是对象，衍伸出和synchronized相关很多的具体问题，例如同一个类不同方法都有synchronized锁，一个对象是否可以同时访问。或者一个类的static构造方法加上synchronized之后的锁的影响。

5.了解可重入锁的含义，以及ReentrantLock和synchronized的区别

6.同步的数据结构，例如concurrentHashMap的源码理解以及内部实现原理，为什么他是同步的且效率高

7.atomicinteger和Volatile等线程安全操作的关键字的理解和使用

8.线程间通信，wait和notify

9.定时线程的使用

10.场景：在一个主线程中，要求有大量(很多很多)子线程执行完之后，主线程才执行完成。多种方式，考虑效率。

11.进程和线程的区别

12.什么叫线程安全？举例说明

13.线程的几种状态

14.并发、同步的接口或方法

15.HashMap是否线程安全，为何不安全。ConcurrentHashMap，线程安全，为何安全。底层实现是怎么样的。

16.J.U.C下的常见类的使用。ThreadPool的深入考察；BlockingQueue的使用。（take，poll的区别，put，offer的区别）；原子类的实现。

17.简单介绍下多线程的情况，从建立一个线程开始。然后怎么控制同步过程，多线程常用的方法和结构

18.volatile的理解

19.实现多线程有几种方式，多线程同步怎么做，说说几个线程里常用的方法

35.并行和并发有什么区别？

36.线程和进程的区别？

37.守护线程是什么？

38.创建线程有哪几种方式？

39.说一下 runnable 和 callable 有什么区别？

40.线程有哪些状态？

41.sleep() 和 wait() 有什么区别？

42.notify()和 notifyAll()有什么区别？

43.线程的 run()和 start()有什么区别？

44.创建线程池有哪几种方式？

45.线程池都有哪些状态？

46.线程池中 submit()和 execute()方法有什么区别？

47.在 java 程序中怎么保证多线程的运行安全？

48.多线程锁的升级原理是什么？

49.什么是死锁？

50.怎么防止死锁？

51.ThreadLocal 是什么？有哪些使用场景？

52.说一下 synchronized 底层实现原理？

53.synchronized 和 volatile 的区别是什么？

54.synchronized 和 Lock 有什么区别？

55.synchronized 和 ReentrantLock 区别是什么？

56.说一下 atomic 的原理？

实际项目中使用多线程举例。你在多线程环境中遇到的常见的问题是什么？你是怎么解决它的？

请说出与线程同步以及线程调度相关的方法

程序中有3个 socket，需要多少个线程来处理

假如有一个第三方接口，有很多个线程去调用获取数据，现在规定每秒钟最多有 10 个线程同时调用它，如何做到

如何在 Windows 和 Linux 上查找哪个线程使用的 CPU 时间最长

如何确保 main() 方法所在的线程是 Java 程序最后结束的线程

非常多个线程（可能是不同机器），相互之间需要等待协调才能完成某种工作，问怎么设计这种协调方案

你需要实现一个高效的缓存，它允许多个用户读，但只允许一个用户写，以此来保持它的完整性，你会怎样去实现它

# 有用的参考

* [JAVA高并发多线程必须懂的50个问题](https://blog.csdn.net/u011163372/article/details/73995897)
* [Java面试：投行的15个多线程和并发面试题](http://www.importnew.com/29562.html)
* [Java多线程看这一篇就足够了](https://blog.csdn.net/c10WTiybQ1Ye3/article/details/78212045)
* [几种Java线程池的实现算法分析](http://www.open-open.com/lib/view/open1406778349171.html)
* [多线程全面详解总结](https://www.cnblogs.com/yjboke/p/8911220.html)
