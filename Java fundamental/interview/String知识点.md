
<details>
<summary> Java 中操作字符串都有哪些类？它们之间有什么区别？</summary>
 
操作字符串的类有：String、StringBuffer、StringBuilder。

String 和 StringBuffer、StringBuilder 的区别在于 String 声明的是不可变的对象，每次操作都会生成新的 String 对象，然后将指针指向新的 String 对象，而 StringBuffer、StringBuilder 可以在原有对象的基础上进行操作，所以在经常改变字符串内容的情况下最好不要使用 String。

StringBuffer 和 StringBuilder 最大的区别在于，StringBuffer 是线程安全的，而 StringBuilder 是非线程安全的，但 StringBuilder 的性能却高于 StringBuffer，所以在单线程环境下推荐使用 StringBuilder，多线程环境下推荐使用 StringBuffer。

</details>

<details>
<summary> String 属于基础的数据类型吗？</summary>

String 不属于基础类型，基础类型有 8 种：byte、boolean、char、short、int、float、long、double，而 String 属于对象。

</details>

<details>
<summary> String str="i"与 String str=new String("i")一样吗？</summary>
 
 不一样，因为内存的分配方式不一样。String str="i"的方式，Java 虚拟机会将其分配到常量池中；而 String str=new String("i") 则会被分到堆内存中。
 
</details> 

<details>
<summary>如何将字符串反转？</summary>

使用 StringBuilder 或者 stringBuffer 的 reverse() 方法。

示例代码：

```java

// StringBuffer reverse
StringBuffer stringBuffer = new StringBuffer();
stringBuffer. append("abcdefg");
System. out. println(stringBuffer. reverse()); // gfedcba
// StringBuilder reverse
StringBuilder stringBuilder = new StringBuilder();
stringBuilder. append("abcdefg");
System. out. println(stringBuilder. reverse()); // gfedcba

```

</details>
 
<details>
<summary> String 类的常用方法都有那些？</summary>
 
IndexOf()：返回指定字符的索引。
charAt()：返回指定索引处的字符。
replace()：字符串替换。
trim()：去除字符串两端空白。
split()：分割字符串，返回一个分割后的字符串数组。
getBytes()：返回字符串的 byte 类型数组。
length()：返回字符串长度。
toLowerCase()：将字符串转成小写字母。
toUpperCase()：将字符串转成大写字符。
substring()：截取字符串。
equals()：字符串比较。
 
</details> 

<details>
<summary>String可以被子类继承吗？</summary>

不能。因为 String 类的定义为 final class，被 final 修饰的类不能被继承

String 对象不可变的（immutable）。分析为什么要这么设计，可能有以下3个原因：

String pool：这是方法（method）区域里一个特殊的存储区域，创建一个 String 时，如果已经在 String pool 中存在，那么会返回已存在的 String 引用。
允许 String 缓存 hashcode：String 定义中，有 hash 成员变量 private int hash; // 默认为0，对 hashcode 进行缓存。
安全性：确保不会被恶意篡改。
</details>

<details>
<summary>String str = new String("abc");创建了几个对象，为什么？</summary>
据说这是《java面试宝典》上的一道题，对java不熟悉的朋友可能就被唬住了，我曾经也是其中之一。

而事实上，这是一道本身就经不起推敲的问题，或许这也是容易让很多人疑惑的原因吧。

先说这个问题的槽点：答案将String变量和String对象（实例）混淆、问题没有具体说明是在类加载时还是代码运行时，应该分开讨论、问题没有针对具体的运行环境。



结论1：题目问的是String Object，应认为是问创建了几个String对象，String对象和String变量是两个概念。String变量是String s，这个s就是一个用户声明的String类型的变量，这里没有final，所以s可以指向任意的String对象，这里它指向了new出来的一个String对象。ps：String类的对象一旦创建，内容就不可变更，用户对String变量重新赋值只是让它指向另一个String对象。想具体了解这个，附传送门，看胖胖回答.https://www.zhihu.com/question/31345592



结论2：类加载时，符合规范的JVM实现应该在类加载的过程中创建并驻留一个String实例作为常量来对应"xyz"字面量；具体是在类加载的resolve阶段进行的。这个常量是全局共享的，只在先前尚未有内容相同的字符串驻留过的前提下才需要创建新的String实例。也就是说如果在类加载到这段之前没有对应“xyz”字面量的常量String对象，加载到这里会创建一个String对象。



结论3：在代码执行时，这里new关键字会创建一个String对象。



结论4：结论3前提是符合规范的JVM。实际上有些JVM会有好的优化，不同的JVM和不同的运行参数可能会有不同结果。例如HotSpot在开启逃逸分析和空间分配消除功能后，发现如果这个new String("xyz")没有什么用，那么在执行至此时并不会创建String对象。



总结：问题不靠谱，面试的时候可以直接甩结论，笔试的话可以按原答案回答吧，但是搞不好会有人专门挑书上有毛病的问题来问，所以追求完美的答案应该是这样的：会创建一个String类型的变量s。在类加载到此处之前没有出现“xyz”字面量的话，加载此处会创建一个对应“xyz”的String常量对象。在符合规范的JVM上，执行到此处new关键字会创建一个String对象。
--------------------- 
作者：_古井心 
来源：CSDN 
原文：https://blog.csdn.net/u010234516/article/details/52738254 
版权声明：本文为博主原创文章，转载请附上博文链接！
</details>


---


<details>
<summary>String是可变的话？</summary>

</details>

<details>
<summary>怎么比较两个字符串的值一样，怎么比较两个字符串是否同一对象？</summary>

</details>

<details>
<summary>switch中可以使用String吗？</summary>

</details>



<details>
<summary>String.trim()方法去掉的是哪些字符？</summary>

</details>


<details>
<summary>可以自定义java.lang.String类并使用吗？</summary>

</details>

<details>
<summary>String与byte[]两者相互之间如何转换？</summary>

</details>

1.String类为什么是final的。


14.Stringa=“abc”Stringb=“abc”Stringc=newString(“abc”)Stringd=“ab”+“c”.他们之间用==比较的结果

* [每周10道Java面试题：String, String Pool, StringBuilder](http://www.importnew.com/31022.html)
* [Java String 对 null 对象的容错处理](http://www.importnew.com/27601.html)


