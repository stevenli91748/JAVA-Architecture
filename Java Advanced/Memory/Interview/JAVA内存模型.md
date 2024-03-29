



<details>
<summary> 深拷贝/浅拷贝 </summary>

[一个工作三年的同事，居然还搞不清深拷贝/浅拷贝，被老大批了](https://www.bilibili.com/read/cv7349900)

</details>

<details>
<summary>java 进程内存组成</summary>

**JVM内存占用 = 操作系统自身耗内存 + 堆 + Java永久代/元数据区/方法区/常量池/代码缓存 + 程序计数器(可忽略不计) plus 线程数 + 虚拟机进程本身 + 虚拟机栈(线程栈) plus 线程数 + 本地方法栈(JNI调用) plus 线程数 + 直接内存(Java NIO)**


[java 进程内存组成](https://blog.csdn.net/snoweaglelord/article/details/81094153)

</details>

<details>
<summary>Java内存溢出的原因有哪些？Java进程占用内存构成有哪些？</summary>
   
  [Java内存溢出的原因有哪些？Java进程占用内存构成有哪些？](https://blog.csdn.net/sinlff/article/details/62040358) 
   
</details>   


<details>
<summary>java内存分布</summary>

在JAVA中，有**六个不同的地方可以存储数据**：

### 1. 寄存器（register）。

   这是最快的存储区，因为它位于不同于其他存储区的地方——处理器内部。但是寄存器的数量极其有限，所以寄存器由编译器根据需求进行分配。你不能直接控制 ，也不能在程序中感觉到寄存器存在的任何迹象。
   
### 2. 堆栈（stack）。

   位于通用RAM中，但通过它的“堆栈指针”可以从处理器哪里获得支持。堆栈指针若向下移动，则分配新的内存；若向上移动，则释放那些内存。这是一种快速有效的分配存储方法，仅次于寄存器。创建程序时候，JAVA编译器必须知道存储在堆栈内所有数据的确切大小和生命周期，因为它必须生成相应的代码，以便上下移动堆栈指针 。这一约束限制了程序的灵活性，所以虽然某些JAVA数据存储在堆栈中——特别是对象引用，但是JAVA对象不存储其中。 

### 3. 堆（heap）。

   一种通用性的内存池 （也存在于RAM中），用于存放所以的JAVA对象。堆不同于堆栈的好处是：编译器不需要知道要从堆里分配多少存储区域 ，也不必知道存储的数据在堆里存活多长时间。因此，在堆里分配存储有很大的灵活性。当你需要创建一个对象的时候，只需要new写一行简单的代码，当执行这行代码时，会自动在堆里进行存储分配。当然，为这种灵活性必须要付出相应的代码。用堆进行存储分配比用堆栈进行存储存储需要更多的时间 。

### 4. 静态存储（static storage）。

   这里的“静态”是指“在固定的位置”。静态存储里存放程序运行时一直存在的数据。你可用关键字static来标识一个对象的特定元素是静态的，但JAVA对象本身从来不会存放在静态存储空间里。
   
### 5. 常量存储（constant storage）。
   
   常量值通常直接存放在程序代码内部，这样做是安全的，因为它们永远不会被改变。有时，在嵌入式系统中，常量本身会和其他部分分割离开，所以在这种情况下，可以选择将其放在ROM中

### 6. 非RAM存储。

   如果数据完全存活于程序之外，那么它可以不受程序的任何控制，在程序没有运行时也可以存在。
   
就速度来说，有如下关系：

   **寄存器 < 堆栈 < 堆 < 其他**

在这里，主要要说下堆与堆栈的关系：

      堆：堆是heap，是所谓的动态内存，其中的内存在不需要时可以回收，以分配给新的内存请求，其内存中的数据是无序的，即先分配的和随后分配的内存并没有 什么必然的位置关系，释放时也可以没有先后顺序。一般由使用者自由分配，malloc分配的就是堆，需要手动释放。

      堆栈:就是STACK。实际上是只有一个出入口的队列，即后进先出（First     In     Last     Out），先分配的内存必定后释放。一般由，由系统自动分配，存放存放函数的参数值，局部变量等，自动清除。

还有，堆是全局的，堆栈是每个函数进入的时候分一小块，函数返回的时候就释放了，静态和全局变量，new     得到的变量，都放在堆中，局部变量放在堆栈中，所以函数返回，局部变量就全没了。

其实在实际应用中，堆栈多用来存储方法的调用。而堆则用于对象的存储。

       JAVA中的基本类型，其实需要特殊对待。因为，在JAVA中，通过new创建的对象存储在“堆”中，所以用new 创建一个小的、简单的变量，如基本类型等，往往不是很有效。因此，在JAVA中，对于这些类型，采用了与C、C++相同的方法。也就是说，不用new 来创建，而是创建一个并非是“引用”的“自动”变量。这个变量拥有它的“值”，并置于堆栈中，因此更高效。

</details>

<details>
<summary>Java中对象都是分配在堆上吗？</summary>
  
**HotSpot虚拟机使用了两种技术来加快内存分配。他们分别是是”bump-the-pointer“和“TLABs（Thread-Local Allocation Buffers）”**


## Java对象分配的过程:

所以，如果以后再有人问你：是不是所有的对象和数组都会在堆内存分配空间？

那么你可以告诉他：不一定，随着JIT编译器的发展，在编译期间，如果JIT经过逃逸分析，发现有些对象没有逃逸出方法，那么有可能堆内存分配会被优化成栈内存分配。但是这也并不是绝对的。就像我们前面看到的一样，在开启逃逸分析之后，也并不是所有User对象都没有在堆上分配。
  
**编译器通过逃逸分析，确定对象是在栈上分配还是在堆上分配**。

如果开启栈上分配，JVM会先进行栈上分配，如果没有开启栈上分配或则不符合条件的则会在堆上进行TLAB分配，如果tlab_top + size <= tlab_end，则在在TLAB上直接分配对象并增加tlab_top 的值，如果现有的TLAB不足以存放当前对象则重新申请一个TLAB，发出一次“TLAB refill”，也就是说之前自己的TLAB就“不管了”（所有权交回给共享的Eden），然后重新从Eden里分配一块空间作为新的TLAB。所谓“不管了”并不是说就让旧TLAB里的对象直接死掉，而是把那块空间的控制权归还给普通的Eden，里面的对象该怎样还是怎样  并再次尝试存放当前对象。如果放不下，再尝试在eden区分配，在Eden区加锁（这个区是多线程共享的），如果eden_top + size <= eden_end则将对象存放在Eden区，增加eden_top 的值，如果Eden区不足以存放则执行一次Young GC（minor collection）,经过Young GC之后，如果Eden区任然不足以存放当前对象，则直接分配到老年代。 

对象不在堆上分配主要的原因还是堆是共享的，在堆上分配有锁的开销。无论是TLAB还是栈都是线程私有的，私有即避免了竞争（当然也可能产生额外的问题例如可见性问题），这是典型的用空间换效率的做法。

  [Java中对象都是分配在堆上吗？](https://blog.csdn.net/c526796017/article/details/80816061)
  
</details>  

<details>
<summary>逃逸分析</summary>

**方法逃逸   **
   
   当一个对象在方法中被定义后，它可能被外部方法所引用，例如作为调用参数传递到其他方法中，这种行为就叫做方法逃逸 。
   
**线程逃逸**
   
   我们把指向刚分配出来的Test实例的引用赋值到了一个静态变量或者可以被其他线程访问的实例字段上时，就可能导致别的线程可以感知到这个新对象的存在，所以这种动作也叫做“发布”（publish）或者叫做“线程逃逸”（thread escaping）
  
  逃逸分析，是一种可以有效减少Java 程序中同步负载和内存堆分配压力的跨函数全局数据流分析算法。通过逃逸分析，Java Hotspot编译器能够分析出一个新的对象的引用的使用范围从而决定是否要将这个对象分配到堆上。
  
  逃逸分析的目的是判断对象的作用域是否有可能逃逸出函数体。在运行时分析对象的生命周期，如果发现该对象只会被本线程使用（一般是一些局部对象），那么就将该对象在栈上分配，而不在堆中（heap）分配，以减少对象对堆的压力，减少GC的次数。
  
  [Java中对象都是分配在堆上吗？](https://blog.csdn.net/c526796017/article/details/80816061)
  
</details>   

<details>
<summary>TLAB </summary>
  
JVM在内存新生代Eden Space中开辟了一小块线程私有的区域，称作TLAB（Thread-local allocation buffer）,默认设定为占用Eden Space的1%。在Java程序中很多对象都是小对象且用过即丢，它们不存在线程共享也适合被快速GC，所以对于小对象通常JVM会优先分配在TLAB上，并且TLAB上的分配由于是线程私有所以没有锁开销。因此在实践中分配多个小对象的效率通常比分配一个大对象的效率要高。 

也就是说，**Java中每个线程都会有自己的缓冲区称作TLAB（Thread-local allocation buffer），每个TLAB都只有一个线程可以操作**，TLAB结合bump-the-pointer技术可以实现快速的对象分配，而不需要任何的锁进行同步，也就是说，在对象分配的时候不用锁住整个堆，而只需要在自己的缓冲区分配即可。 

即线程本地分配缓存区，这是一个线程专用的内存分配区域。 
由于对象一般会分配在堆上，而堆是全局共享的。因此在同一时间，可能会有多个线程在堆上申请空间。因此，每次对象分配都必须要进行同步（虚拟机采用CAS配上失败重试的方式保证更新操作的原子性），而在竞争激烈的场合分配的效率又会进一步下降。JVM使用TLAB来避免多线程冲突，在给对象分配内存时，每个线程使用自己的TLAB，这样可以避免线程同步，提高了对象分配的效率。

每个线程会从Eden分配一大块空间，例如说100KB，作为自己的TLAB。这个start是TLAB的起始地址，end是TLAB的末尾，然后top是当前的分配指针。显然start <= top < end。

当一个Java线程在自己的TLAB中分配到尽头之后，再要分配就会出发一次“TLAB refill”，也就是说之前自己的TLAB就“不管了”（所有权交回给共享的Eden），然后重新从Eden里分配一块空间作为新的TLAB。所谓“不管了”并不是说就让旧TLAB里的对象直接死掉，而是把那块空间的控制权归还给普通的Eden，里面的对象该怎样还是怎样。通常情况下，在TLAB中分配多次才会填满TLAB、触发TLAB refill，这样使用TLAB分配就比直接从共享部分的Eden分配要均摊（amortized）了同步开销，于是提高了性能。其实很多关注多线程性能的malloc库实现也会使用类似的做法，例如TCMalloc。

到触发GC的时候，无论是minor GC还是full GC，要收集Eden的时候里面的空间无论是属于某个线程的TLAB还是不属于任何TLAB都一视同仁，把Eden当作一个整体来收集里面的对象——把活的对象拷贝到survivor space（或者直接晋升到Old Gen）。在GC结束之后，每个Java线程又会重新从Eden分配自己的TLAB。周而复始。

[Java中对象都是分配在堆上吗？](https://blog.csdn.net/c526796017/article/details/80816061)
 
</details>   

<details>
<summary>TLAB分配的对象可以共享吗？  </summary>
   
只要是Heap上的对象，所有线程都是可以共享的，就看你有没有本事访问到了。在GC的时候只从root sets来扫描对象，而不管你到底在哪个TLAB中。

</details>   

<details>
<summary>bump-the-pointer技术</summary>

Bump-the-pointer技术跟踪在伊甸园空间创建的最后一个对象。这个对象会被放在伊甸园空间的顶部。如果之后再需要创建对象，只需要检查伊甸园空间是否有足够的剩余空间。如果有足够的空间，对象就会被创建在伊甸园空间，并且被放置在顶部。这样以来，每次创建新的对象时，只需要检查最后被创建的对象。这将极大地加快内存分配速度。但是，如果我们在**多线程的情况下，事情将截然不同。如果想要以线程安全的方式以多线程在伊甸园空间存储对象，不可避免的需要加锁，而这将极大地的影响性能。**
  
</details>   

<details>
<summary>栈上分配技术</summary>
  
  栈上分配是java虚拟机提供的一种优化技术，基本思想是对于那些线程私有的对象（指的是不可能被其他线程访问的对象），可以将它们打散分配在栈上，而不是分配在堆上。分配在栈上的好处是可以在函数调用结束后自行销毁，而不需要垃圾回收器的介入，从而提供系统的性能。

</details>   

<details>
<summary>标量替换优化技术</summary>
  
  若一个对象被证明不会被外部访问，并且这个对象可以被拆解成若干个基本类型的形式，那么当程序真正执行的时候可以不创建这个对象，而是采用直接创建它的若干个被这个方法所使用到的成员变量来代替，将对象拆分后，除了可以让对象的成员变量在栈上分配和读写之外，还可以为后续进一步的优化手段创造条件。
  
</details>   

<details>
<summary>同步消除 </summary>

若一个变量被证明不会逃逸出线程，那么这个变量的读写就肯定不会出现竞争的情况，那么对这个变量实施的同步措施也就可以消除掉。

</details>   

<details>
<summary>Java虚拟机是否有对对象做逃逸分析呢? </summary>

否

</details>   




9.Java内存泄露的问题调查定位：jmap，jstack的使用等等


