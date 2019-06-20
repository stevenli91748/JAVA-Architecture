

<details>
<summary>Java中对象都是分配在堆上吗？</summary>
  
**HotSpot虚拟机使用了两种技术来加快内存分配。他们分别是是”bump-the-pointer“和“TLABs（Thread-Local Allocation Buffers）”**


## Java对象分配的过程:

所以，如果以后再有人问你：是不是所有的对象和数组都会在堆内存分配空间？

那么你可以告诉他：不一定，随着JIT编译器的发展，在编译期间，如果JIT经过逃逸分析，发现有些对象没有逃逸出方法，那么有可能堆内存分配会被优化成栈内存分配。但是这也并不是绝对的。就像我们前面看到的一样，在开启逃逸分析之后，也并不是所有User对象都没有在堆上分配。
  
**编译器通过逃逸分析，确定对象是在栈上分配还是在堆上分配**。

如果开启栈上分配，JVM会先进行栈上分配，如果没有开启栈上分配或则不符合条件的则会在堆上进行TLAB分配，如果tlab_top + size <= tlab_end，则在在TLAB上直接分配对象并增加tlab_top 的值，如果现有的TLAB不足以存放当前对象则重新申请一个TLAB，并再次尝试存放当前对象。如果放不下，再尝试在eden区分配，在Eden区加锁（这个区是多线程共享的），如果eden_top + size <= eden_end则将对象存放在Eden区，增加eden_top 的值，如果Eden区不足以存放则执行一次Young GC（minor collection）,经过Young GC之后，如果Eden区任然不足以存放当前对象，则直接分配到老年代。 

对象不在堆上分配主要的原因还是堆是共享的，在堆上分配有锁的开销。无论是TLAB还是栈都是线程私有的，私有即避免了竞争（当然也可能产生额外的问题例如可见性问题），这是典型的用空间换效率的做法。

  [Java中对象都是分配在堆上吗？](https://blog.csdn.net/c526796017/article/details/80816061)
  
</details>  

<details>
<summary>逃逸分析</summary>
  
  逃逸分析，是一种可以有效减少Java 程序中同步负载和内存堆分配压力的跨函数全局数据流分析算法。通过逃逸分析，Java Hotspot编译器能够分析出一个新的对象的引用的使用范围从而决定是否要将这个对象分配到堆上。
  
  [Java中对象都是分配在堆上吗？](https://blog.csdn.net/c526796017/article/details/80816061)
  
</details>   

<details>
<summary>TLAB </summary>
  
JVM在内存新生代Eden Space中开辟了一小块线程私有的区域，称作TLAB（Thread-local allocation buffer）,默认设定为占用Eden Space的1%。在Java程序中很多对象都是小对象且用过即丢，它们不存在线程共享也适合被快速GC，所以对于小对象通常JVM会优先分配在TLAB上，并且TLAB上的分配由于是线程私有所以没有锁开销。因此在实践中分配多个小对象的效率通常比分配一个大对象的效率要高。 

也就是说，**Java中每个线程都会有自己的缓冲区称作TLAB（Thread-local allocation buffer），每个TLAB都只有一个线程可以操作**，TLAB结合bump-the-pointer技术可以实现快速的对象分配，而不需要任何的锁进行同步，也就是说，在对象分配的时候不用锁住整个堆，而只需要在自己的缓冲区分配即可。 

 [Java中对象都是分配在堆上吗？](https://blog.csdn.net/c526796017/article/details/80816061)
 
</details>   

---

<details>
<summary>bump-the-pointer技术</summary>

Bump-the-pointer技术跟踪在伊甸园空间创建的最后一个对象。这个对象会被放在伊甸园空间的顶部。如果之后再需要创建对象，只需要检查伊甸园空间是否有足够的剩余空间。如果有足够的空间，对象就会被创建在伊甸园空间，并且被放置在顶部。这样以来，每次创建新的对象时，只需要检查最后被创建的对象。这将极大地加快内存分配速度。但是，如果我们在**多线程的情况下，事情将截然不同。如果想要以线程安全的方式以多线程在伊甸园空间存储对象，不可避免的需要加锁，而这将极大地的影响性能。**
  
</details>   


<details>
<summary>栈上分配技术</summary>
  
  
</details>   

<details>
<summary>标量替换优化技术</summary>
  
  
</details>   

9.Java内存泄露的问题调查定位：jmap，jstack的使用等等


