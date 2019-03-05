
# Java基础：面试知识点目录

* [基本程序设计结构知识点](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20fundamental/interview/%E5%9F%BA%E6%9C%AC%E7%A8%8B%E5%BA%8F%E8%AE%BE%E8%AE%A1%E7%BB%93%E6%9E%84%E7%9F%A5%E8%AF%86%E7%82%B9.md)
  * [静态块、静态变量](#静态块-静态变量)
* [面向对象-对象与类知识点](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Object%20oriented/interview/README.md)
* [继承知识点](#3-继承)
  * [继承设计技巧](#继承设计技巧)
* [接口知识点](#4-接口)
* [Lambda标达式知识点](#5-Lambda标达式)
* [内部类知识点](#6-内部类)
* [ 异常 断言和日志知识点](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20fundamental/interview/异常%20断言和日志知识点.md)
* [泛型知识点](#8-泛型)
* [集合知识点](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20fundamental/interview/容器.md)
* [JAVA 8的流库知识点](#10-JAVA-8的流库)
* [Java常用类知识点](#11-Java常用类)
* [输入与输出知识点](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20fundamental/interview/javaIO.md)
* [XML知识点](#13-XML)
* [JAVA网络操作知识点](#14-JAVA网络操作)
* [JAVA数据库知识点](#15-JAVA数据库)
  *  [JDBC](#JDBC)
* [日期和时间API知识点](#16-日期和时间API)
* [ 国际化知识点](#17-国际化)
* [本地方法知识点](#18-本地方法)
* [脚本 编译与注解知识点](#19-本-编译与注解)
* [反射知识点](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20fundamental/interview/反射.md)
  * [利用反射分析类的能力](#利用反射分析类的能力)
  * [利用反射在运行时分析对象的能力](#利用反射在运行时分析对象的能力)
  * [调用任意方法](#调用任意方法)
  * [反射类的方法](#反射类的方法)
* [注解知识点](#21-注解)
* [JSON知识点](#22-JSON)
* [正则表达式知识点](#23-正则表达式)
* [文本操作知识点](#24-文本操作)
* [数组知识点](#25-数组)
* [容器知识点](#26-容器)
* [序列化与反序列化知识点](#27-序列化与反序列化)
* [图形程序设计知识点](#28-图形程序设计)
* [Swing界面组件知识点](#29-Swing界面组件)
* [事件处理知识点](#30-事件处理)
* [高级Swing 界面组件知识点](#31-高级Swing-界面组件)
* [高级AWT知识点](#32-高级AWT)
* [部署JAVA应用程序知识点](#33-部署JAVA应用程序)
* [String知识点](#34-String)
* [Java Map知识点](#35-Java-Map)

# 内容
## 基本程序设计结构
### 静态块 静态变量
当类加载器将类加载到JVM中的时候就会创建静态变量，这跟对象是否创建无关。静态变量加载的时候就会分配内存空间。静态代码块的代码只会在类第一次初始化的时候执行一次。一个类可以有多个静态代码块，它并不是类的成员，也没有返回值，并且不能直接调用。静态代码块不能包含this或者super,它们通常被用初始化静态变量。

   [JAVA中静态块、静态变量加载顺序详解](https://blog.csdn.net/Mrzhoug/article/details/51581994)
   
   [静态代码块，构造代码块，构造函数之间的差别及先后执行顺序](https://blog.csdn.net/u011889786/article/details/53364205)  
   
   [Java虚拟机类加载机制——案例分析](https://blog.csdn.net/u013256816/article/details/50837863)
   
   [java中静态属性，静态方法和静态初始化器](https://blog.csdn.net/bzhxuexi/article/details/17606691)


## 继承
## 接口
## Lambda标达式
## 内部类
## 异常 断言和日志
## 泛型
## JAVA 8的流库
## Java常用类
## 输入与输出
## XML
## JAVA网络操作
## JAVA数据库
## 日期和时间API
##  国际化
## 本地方法
## 脚本 编译与注解
## 反射
## 注解
## JSON
## 正则表达式
## 文本操作
## 数组
## 容器
## 序列化与反序列化
## 图形程序设计
## Swing界面组件
## 事件处理
## 高级Swing 界面组件
## 高级AWT
## 部署JAVA应用程序
## String

<details>
<summary>String是基本数据类型吗？</summary>

</details>

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
<summary>String、StringBuffer、StringBuilder有什么区别？</summary>

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





---
## 35. Java Map

<details>
<summary>你都知道哪些常用的Map集合? </summary>


</details>

<details>
<summary>Collection集合接口和Map接口有什么关系？</summary>


</details>

<details>
<summary>HashMap是线程安全的吗？线程安全的Map都有哪些？性能最好的是哪个？</summary>


</details>

<details>
<summary>使用HashMap有什么性能问题吗？</summary>


</details>

<details>
<summary>HashMap的数据结构是怎样的？默认大小是多少？内部是怎么扩容的？</summary>


</details>

<details>
<summary>怎么按添加顺序存储元素？怎么按A-Z自然顺序存储元素？怎么自定义排序？</summary>


</details>


<details>
<summary>HashMap的链表结构设计是用来解决什么问题的？</summary>


</details>

<details>
<summary>HashMap的键、值可以为NULL吗？HashTable呢？</summary>


</details>

<details>
<summary>HashMap使用对象作为key，如果hashcode相同会怎么处理？</summary>


</details>

<details>
<summary>HashMap中的get操作是什么原理？</summary>


</details>


---
---

<details>
<summary>JAVA中静态块、静态变量加载顺序详解</summary>

* [JAVA中静态块、静态变量加载顺序详解](https://blog.csdn.net/Mrzhoug/article/details/51581994)

</details>



<details>
<summary>1、java事件机制包括哪三个部分？分别介绍 </summary>


</details>

<details>
<summary> 6、怎么理解无界队列和有界队列？</summary>


</details>


<details>
<summary>8、什么是反射机制？</summary>


</details>

<details>
<summary>9、说说反射机制的作用。</summary>


</details>

<details>
<summary>10、反射机制会不会有性能问题？</summary>



</details>



<details>
<summary>16、你怎么理解cookie和session，有哪些不同点？</summary>



</details>

<details>
<summary>17、什么是web缓存？有什么优点？</summary>


</details>


<details>
<summary>20、什么是虚拟主机及实现原理？</summary>



</details>



---
---
1.JDK 和 JRE 有什么区别？

2.== 和 equals 的区别是什么？

3.两个对象的 hashCode()相同，则 equals()也一定为 true，对吗？

4.final 在 java 中有什么作用？

5.java 中的 Math.round(-1.5) 等于多少？

6.String 属于基础的数据类型吗？

7.java 中操作字符串都有哪些类？它们之间有什么区别？

8.String str="i"与 String str=new String("i")一样吗？

9.如何将字符串反转？

10.String 类的常用方法都有那些？

11.抽象类必须要有抽象方法吗？

12.普通类和抽象类有哪些区别？

13.抽象类能使用 final 修饰吗？

14.接口和抽象类有什么区别？

15.java 中 IO 流分为几种？

16.BIO、NIO、AIO 有什么区别？

17.Files的常用方法都有哪些？

29、什么是DoS、DDoS、DRDoS攻击？如何防御？

30、描述一下Java异常层次结构。

31、什么是检查异常，不受检查异常，运行时异常？并分别举例说明。

32、finally块一定会执行吗？

33、正常情况下，当在try块或catch块中遇到return语句时，finally语句块在方法返回之前还是之后被执行？

34、try、catch、finally语句块的执行顺序。

40、Java中的参数传递时传值呢？还是传引用？

41、Java中有没有指针的概念？

58、如何解决业务层的数据访问问题？

59、为了解决数据库服务器的负担，如何做数据库的分布？

60、什么是著名的拜占庭将军问题？
 61、

62、讲讲CAP理念。

63、怎么理解强一致性、单调一致性和最终一致性？

66、谈一谈一致性哈希算法。

67、paxos是什么？

68、什么是Lease机制？

69、如何理解选主算法？

70、

71. 关于如何使用clone方法以及浅克隆/深克隆机制

