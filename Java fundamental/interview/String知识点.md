
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
<summary>String str = new String("abc");创建了几个对象，为什么？</summary>

</details>


<details>
<summary>String.trim()方法去掉的是哪些字符？</summary>

</details>

<details>
<summary>String可以被子类继承吗？</summary>

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


