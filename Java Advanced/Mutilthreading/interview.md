这也是必问的一块了。因为三年工作经验，所以基本上不会再问你怎么实现多线程了，会问得深入一些比如说Thread和Runnable的区别和联系、多次start一个线程会怎么样、线程有哪些状态。当然这只是最基本的，出乎意料地，几次面试几乎都被同时问到了一个问题，问法不尽相同，总结起来是这么一个意思：

假如有Thread1、Thread2、ThreaD3、Thread4四条线程分别统计C、D、E、F四个盘的大小，所有线程都统计完毕交给Thread5线程去做汇总，应当如何实现？

---

<details>
<summary>什么是线程？</summary>
  
进程：每个进程都有独立的代码和数据空间（进程上下文），进程间的切换会有较大的开销，一个进程包含1–n个线程。（进程是资源分配的最小单位）

线程：同一类线程共享代码和数据空间，每个线程有独立的运行栈和程序计数器(PC)，线程切换开销小。（线程是cpu调度的最小单位）

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
<summary>如何在Java中实现线程？</summary>
  
在语言层面有两种方式。java.lang.Thread 类的实例就是一个线程但是它需要调用java.lang.Runnable接口来执行，由于线程类本身就是调用的Runnable接口所以你可以继承java.lang.Thread 类或者直接调用Runnable接口来重写run()方法实现线程。

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
<summary>Thread 类中的start() 和 run() 方法有什么区别？</summary>
  
这个问题经常被问到，但还是能从此区分出面试者对Java线程模型的理解程度。start()方法被用来启动新创建的线程，而且start()内部调用了run()方法，这和直接调用run()方法的效果不一样。当你调用run()方法的时候，只会是在原来的线程中调用，没有新的线程启动，start()方法才会启动新线程。

</details>

<details>
<summary>Java中Runnable和Callable有什么不同？</summary>

Runnable和Callable都代表那些要在不同的线程中执行的任务。Runnable从JDK1.0开始就有了，Callable是在JDK1.5增加的。它们的主要区别是Callable的 call() 方法可以返回值和抛出异常，而Runnable的run()方法没有这些功能。Callable可以返回装载有计算结果的Future对象。

</details>

<details>
<summary>多线程的几种实现方式？</summary>

（1）继承Thread类

（2）实现Runnable接口

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


---
---

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



# 有用的参考

* [JAVA高并发多线程必须懂的50个问题](https://blog.csdn.net/u011163372/article/details/73995897)
* [Java面试：投行的15个多线程和并发面试题](http://www.importnew.com/29562.html)
* [Java多线程看这一篇就足够了](https://blog.csdn.net/c10WTiybQ1Ye3/article/details/78212045)
* [几种Java线程池的实现算法分析](http://www.open-open.com/lib/view/open1406778349171.html)
