<details>
<summary> String的intern()方法详解</summary>
 
 判断这个常量是否存在于常量池。
  如果存在
   判断存在内容是引用还是常量，
    如果是引用，
     返回引用地址指向堆空间对象，
    如果是常量，
     直接返回常量池常量
  如果不存在，
   将当前对象引用复制到常量池,并且返回的是当前对象的引用
 
 先说说常量池里的字符串怎么来。

JDK1.6及以前，调用String.intern()，如果常量池中没有，则拷贝一份对象，放到常量池中。

JDK1.7及以后，调用String.intern()，如果常量池中没有，则拷贝一份引用，放到常量池中。

这会导致intern()方法返回的字符串的含义有微小的区别，考虑常量池中现在没有字符串"test"，现在有一个字符串s，内容为"test"，JDK1.6以前，s.intern() 返回的地址是拷贝后的对象的地址，JDK1.7以后，返回的是s的地址，因此用 s == s.intern()这句判断JDK1.6为false，JDK1.7为true。

 
* [String的intern()方法详解](https://blog.csdn.net/wjzhang5514/article/details/70209403)
* [String.intern()详细分析，JDK1.8](https://blog.csdn.net/u013366617/article/details/83618361)
* [几张图轻松理解String.intern()](https://blog.csdn.net/tyyking/article/details/82496901)
* [Java面试——从JVM角度比较equals和==的区别](https://blog.csdn.net/seu_calvin/article/details/52089040)
* [Java技术——你真的了解String类的intern()方法吗](https://blog.csdn.net/seu_calvin/article/details/52291082)

 
</details>

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
String s=new String("sdd")

这个产生了2个对象，一个是new关键字创建的new Sring（）；另一个是“sdd”对象，abc在一个字符串池中s这个对象指向这个串池 

这个题的考点知识很多：

引用变量与对象的区别；
字符串文字"abc"是一个String对象； 
文字池(pool of literal strings)和堆(heap)中的字符串对象。
一、引用变量与对象：除了一些早期的Java书籍和现在的垃圾书籍，人们都可以从中比较清楚地学习到两者的区别。
A aa;
这个语句声明一个类A的引用变量aa[我们常常称之为句柄]，而对象一般通过new创建。所以题目中s仅仅是一个引用变量，它不是对象。

二、Java中所有的字符串文字[字符串常量]都是一个String的对象。有人[特别是C程序员]在一些场合喜欢把字符串"当作/看成"字符数组，这也没有办法，因为字符串与字符数组存在一些内在的联系。事实上，它与字符数组是两种完全不同的对象。

System.out.println("Hello".length());
char[] cc={'H','i'};
System.out.println(cc.length);

三、字符串对象的创建:
由于字符串对象的大量使用(它是一个对象，一般而言对象总是在heap分配内存)，Java中为了节省内存空间和运行时间(如比较字符串时，==比equals()快)，在编译阶段就把所有的字符串文字放到一个文字池(pool of literal strings)中，而运行时文字池成为常量池的一部分。文字池的好处，就是该池中所有相同的字符串常量被合并，只占用一个空间。
我们知道，对两个引用变量，使用==判断它们的值(引用)是否相等，即指向同一个对象：
String s1 = "abc" ;
String s2 = "abc" ;
if( s1 == s2 ) System.out.println("s1,s2 refer to the same object");
else System.out.println("trouble");

这里的输出显示，两个字符串文字保存为一个对象。就是说，上面的代码只在pool中创建了一个String对象。

现在看String s = new String("abc");语句，这里"abc"本身就是pool中的一个对象，而在运行时执行new String()时，
将pool中的对象复制一份放到heap中，并且把heap中的这个对象的引用交给s持有。ok，这条语句就创建了2个String对象。
String s1 = new String("abc") ;
String s2 = new String("abc") ;
if( s1 == s2 ){ //不会执行的语句}

这时用==判断就可知，虽然两个对象的"内容"相同(equals()判断)，但两个引用变量所持有的引用不同

补充一个面试题：String str = "aaa" + new String("bbb")创建了几个String对象？

 "aa"一个对象   new Sring()一个对象  "bbb"一个对象 "aa" + new String("bbb");一个对象

注意下面的区别：

如果

String str = "aa";
   String s = "aa";
那么if(str == s) 为true 即引用同一个对象
继续
   String str = new String();
String s = new String();
   那么if(str == s) 为false 既不同对象
再来 
   String str = "aa" + new String();
   String s = "aa" + new String();
那么if(sre == s) 为false 既不同对象



举例一

package com.jit.java.test;

public class TestStringObject {
 public static void main(String[] args) {
  String str = new String("sdd");

 }

}

查看下面的结果 运行时javap -c可以查看

E:\XiaoCheng\2012workspace\Test2Array\src\com\jit\java\test>javac TestStringObje
ct.java

E:\XiaoCheng\2012workspace\Test2Array\src\com\jit\java\test>javap -c TestStringO
bject
Compiled from "TestStringObject.java"
public class com.jit.java.test.TestStringObject extends java.lang.Object{
public com.jit.java.test.TestStringObject();
  Code:
   0:   aload_0
   1:   invokespecial   #1; //Method java/lang/Object."<init>":()V
   4:   return

public static void main(java.lang.String[]);
  Code:
   0:   new     #2; //class java/lang/String
   3:   dup
   4:   ldc     #3; //String sdd
   6:   invokespecial   #4; //Method java/lang/String."<init>":(Ljava/lang/Strin
g;)V
   9:   astore_1
   10:  return

}

举例二

package com.jit.java.test;

public class TestStringObject {
 public static void main(String[] args) {
  String str = new String("sdd");
  String st = new String("sdd"); //如果不看前面的，光看这一行本身也是创建了2个对象，但sdd在字符串池里已经有了，所以结合前面的就创建了1个对象new Sring（）;

解释下字符串池：他是一个由字符组成的一个数组就相当于char str1 =new char[]{a,b,c};
如果不是遇到新的字符串，则直接指向原来的串池，如果遇到新的字符串则创建一个新的串池

}

}


E:\XiaoCheng\2012workspace\Test2Array\src\com\jit\java\test>javap -c TestStringO
bject
Compiled from "TestStringObject.java"
public class com.jit.java.test.TestStringObject extends java.lang.Object{
public com.jit.java.test.TestStringObject();
  Code:
   0:   aload_0
   1:   invokespecial   #1; //Method java/lang/Object."<init>":()V
   4:   return

public static void main(java.lang.String[]);
  Code:
   0:   new     #2; //class java/lang/String
   3:   dup
   4:   ldc     #3; //Stringsdd
   6:   invokespecial   #4; //Method java/lang/String."<init>":(Ljava/lang/Strin
g;)V
   9:   astore_1
   10:  new     #2; //class java/lang/String
   13:  dup
   14:  ldc     #3; //Stringsdd
   16:  invokespecial   #4; //Method java/lang/String."<init>":(Ljava/lang/Strin
g;)V
   19:  astore_2
   20:  return

}

举例三

package com.jit.java.test;

public class TestStringObject {
 public static void main(String[] args) {
  String str = new String("sdd");
  String st = new String("sdd");
         String st1 = "sdd";

此处没有创建新的对象，故运行到此还是3个对象，首先没new，另外它会去看字符串池中有没有相同的字符，如果有则st1直接指向sdd,如果不是abc,而是xyz的话则又创建了一个对象 
         String st2= "xyz";
 }

}
E:\XiaoCheng\2012workspace\Test2Array\src\com\jit\java\test>javap -c TestStringO
bject
Compiled from "TestStringObject.java"
public class com.jit.java.test.TestStringObject extends java.lang.Object{
public com.jit.java.test.TestStringObject();
  Code:
   0:   aload_0
   1:   invokespecial   #1; //Method java/lang/Object."<init>":()V
   4:   return

public static void main(java.lang.String[]);
  Code:
   0:   new     #2; //class java/lang/String
   3:   dup
   4:   ldc    #3; //Stringsdd
   6:   invokespecial   #4; //Method java/lang/String."<init>":(Ljava/lang/Strin
g;)V
   9:   astore_1
   10:  new     #2; //class java/lang/String
   13:  dup
   14:  ldc   #3; //Stringsdd
   16:  invokespecial   #4; //Method java/lang/String."<init>":(Ljava/lang/Strin
g;)V
   19:  astore_2
   20:  ldc   #3; //Stringsdd
   22:  astore_3
   23:  ldc     #5; //Stringxyz
   25:  astore  4
   27:  return

}
--------------------- 
作者：边缘元素 
来源：CSDN 
原文：https://blog.csdn.net/john2522/article/details/7437120 
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


