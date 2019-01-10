
# Java基础：面试知识点目录

* [1. 基本程序设计结构知识点](#1-基本程序设计结构)
  * [静态块、静态变量](#静态块-静态变量)
* [2. 对象与类知识点](#2-对象与类)
* [3. 继承知识点](#3-继承)
  * [继承设计技巧](#继承设计技巧)
* [4. 接口知识点](#4-接口)
* [5. Lambda标达式知识点](#5-Lambda标达式)
* [6. 内部类知识点](#6-内部类)
* [7. 异常 断言和日志知识点](#7-异常-断言和日志)
* [8. 泛型知识点](#8-泛型)
* [9. 集合知识点](#9-集合)
* [10. JAVA 8的流库知识点](#10-JAVA-8的流库)
* [11. Java常用类知识点](#11-Java常用类)
* [12. 输入与输出知识点](#12-输入与输出)
* [13. XML知识点](#13-XML)
* [14. JAVA网络操作知识点](#14-JAVA网络操作)
* [15. JAVA数据库知识点](#15-JAVA数据库)
  *  [JDBC](#JDBC)
* [16. 日期和时间API知识点](#16-日期和时间API)
* [17. 国际化知识点](#17-国际化)
* [18. 本地方法知识点](#18-本地方法)
* [19. 脚本 编译与注解知识点](#19-本-编译与注解)
* [20. 反射知识点](#20-反射)
  * [利用反射分析类的能力](#利用反射分析类的能力)
  * [利用反射在运行时分析对象的能力](#利用反射在运行时分析对象的能力)
  * [调用任意方法](#调用任意方法)
  * [反射类的方法](#反射类的方法)
* [21. 注解知识点](#21-注解)
* [22. JSON知识点](#22-JSON)
* [23. 正则表达式知识点](#23-正则表达式)
* [24. 文本操作知识点](#24-文本操作)
* [25. 数组知识点](#25-数组)
* [26. 容器知识点](#26-容器)
* [27. 序列化与反序列化知识点](#27-序列化与反序列化)
* [28. 图形程序设计知识点](#28-图形程序设计)
* [29. Swing界面组件知识点](#29-Swing界面组件)
* [30. 事件处理知识点](#30-事件处理)
* [31. 高级Swing 界面组件知识点](#31-高级Swing-界面组件)
* [32. 高级AWT知识点](#32-高级AWT)
* [33. 部署JAVA应用程序知识点](#33-部署JAVA应用程序)
* [34. String知识点](#34-String)
* [35. Java Map知识点](#35-Java-Map)

# 内容
## 1. 基本程序设计结构
### 静态块 静态变量
   [JAVA中静态块、静态变量加载顺序详解](https://blog.csdn.net/Mrzhoug/article/details/51581994)
## 2. 对象与类
## 3. 继承
## 4. 接口
## 5. Lambda标达式
## 6. 内部类
## 7. 异常 断言和日志
## 8. 泛型
## 9. 集合

<details>
<summary>你了解哪些集合类型？</summary>
 
* ArrayList
* LinkedList
* HashMap
* HashSet
之后，你可能会被问到这样一些问题，比如应该何时使用此种特定类型，它比其他的好在哪里，它是怎么存储数据的以及隐匿在背后的数据结构是什么。最好的方法是尽可能多地了解这些集合类型，因为这类问题几乎是无穷尽的

</details>

<details>
<summary>HashMap 有什么特点？</summary>
 
HashMap 基于Map接口实现，存储键值对时，可以接收 null 为键值。HashMap 是非同步的。

</details>
 
<details>
<summary> HashMap 的工作原理是怎样的？</summary>
 
HashMap 在 Map.Entry 静态内部类实现中存储键值对，使用哈希算法。在 put 和 get 方法中，使用 hashCode() 和 equals() 方法。

调用 put 方法时，使用键值对中的 Key hashCode() 和哈希算法找出存储键值对索引。键值对 Entry 存储在 LinkedList 中，如果存在 Entry，使用 equals() 方法来检查 Key 是否已经存在：如果存在，则覆盖 value；如果不存在，会创建一个新的 Entry 然后保存。
调用 get 方法时，HashMap 使用键值 Key hashCode() 来找到数组中的索引，然后使用 equals() 方法找出正确的 Entry，返回 Entry 中的 Value。
分析：HashMap 中容量、负荷系数和阀值是重要的参数。HashMap 默认的初始容量是16，负荷系数是0.75。阀值 = 负荷系数 x 容量。添加 Entry时，如果 Map 的大小 > 阀值，HashMap 会对 Map 的内容重新哈希，使用更大的容量（容量总是2的幂）。关于 JDK 中的 hash 算法实现以及由此引发的哈希碰撞现象（DDos攻击）都可能是面试的延伸问题。

更新：HashMap初始容量16，static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; // aka 16

</details>

<details>
<summary>能否使用任何类作为 Map 的 key？</summary>

可以使用任何类作为 Map 的 key，然而在使用之前，需要考虑以下几点：

如果类重写了 equals() 方法，也应该重写 hashCode() 方法。
类的所有实例需要遵循与 equals() 和 hashCode() 相关的规则。
如果一个类没有使用 equals()，不应该在 hashCode() 中使用它。
用户自定义 Key 类最佳实践是使之为不可变的，这样 hashCode() 值可以被缓存起来，拥有更好的性能。不可变的类也可以确保 hashCode() 和 equals() 在未来不会改变，这样就会解决与可变相关的问题了。
分析：如果有一个类 MyKey，在 HashMap 中使用它：

```java

HashMap<MyKey, String> myHashMap = new HashMap<MyKey, String>();
 
//传递给 MyKey 的 name 参数被用于 equals() 和 hashCode() 中
MyKey key = new MyKey("Pankaj"); // 假设 hashCode=1234
myHashMap.put(key, "Value");
 
// 以下的代码会改变 key 的 hashCode() 和 equals() 值
key.setName("Amit"); // 假设新的 hashCode=7890
 
//下面会返回 null，因为 HashMap 会尝试查找存储同样索引的 key，而 key 已被改变了，匹配失败，返回 null
System.out.println(myHashMap.get(new MyKey("Pankaj")));

```
这就是为什么 String 通常会用作 HashMap 的 Key，因为 String 的设计是不可变的（immutable）。

</details>

<details>
<summary>插入数据时，ArrayList、LinkedList、Vector谁速度较快？</summary>

ArrayList、Vector 底层的实现都是使用数组方式存储数据。数组元素数大于实际存储的数据以便增加和插入元素，它们都允许直接按序号索引元素，但是插入元素要涉及数组元素移动等内存操作，所以索引数据快而插入数据慢。

* Vector 中的方法由于加了 synchronized 修饰，因此 Vector 是线程安全容器，但性能上较ArrayList差。
* LinkedList 使用双向链表实现存储，按序号索引数据需要进行前向或后向遍历，但插入数据时只需要记录当前项的前后项即可，所以 LinkedList 插入速度较快。

</details>

<details>
<summary>多线程场景下如何使用 ArrayList？</summary>

ArrayList 不是线程安全的，如果遇到多线程场景，可以通过 Collections 的 synchronizedList 方法将其转换成线程安全的容器后再使用。例如像下面这样：

```java

List<String> synchronizedList = Collections.synchronizedList(list);
synchronizedList.add("aaa");
synchronizedList.add("bbb");
for (int i = 0; i < synchronizedList.size(); i++)
{
    System.out.println(synchronizedList.get(i));
}

```

</details>

<details>
<summary>说一下 ArrayList 的优缺点</summary>
 
ArrayList的优点如下：
1. ArrayList 底层以数组实现，是一种随机访问模式。ArrayList 实现了 RandomAccess 接口，因此查找的时候非常快。
2. ArrayList 在顺序添加一个元素的时候非常方便。

ArrayList 的缺点如下：

1. 删除元素的时候，需要做一次元素复制操作。如果要复制的元素很多，那么就会比较耗费性能。
2. 插入元素的时候，也需要做一次元素复制操作，缺点同上。
ArrayList 比较适合顺序添加、随机访问的场景。

</details>

<details>
<summary>为什么 ArrayList 的 elementData 加上 transient 修饰？</summary>

ArrayList 中的数组定义如下：

```java

private transient Object[] elementData;

```
再看一下 ArrayList 的定义：

```java

public class ArrayList<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable
        
```

可以看到 ArrayList 实现了 Serializable 接口，这意味着 ArrayList 支持序列化。transient 的作用是说不希望 elementData 数组被序列化，重写了 writeObject 实现：

```java

private void writeObject(java.io.ObjectOutputStream s) throws java.io.IOException{
    // Write out element count, and any hidden stuff
    int expectedModCount = modCount;
    s.defaultWriteObject();
        // Write out array length
    s.writeInt(elementData.length);
        // Write out all elements in the proper order.
    for (int i=0; i<size; i++)
        s.writeObject(elementData[i]);
    if (modCount != expectedModCount) {
        throw new ConcurrentModificationException();
    }
    
```

每次序列化时，先调用 defaultWriteObject() 方法序列化 ArrayList 中的非 transient 元素，然后遍历 elementData，只序列化已存入的元素，这样既加快了序列化的速度，又减小了序列化之后的文件大小。

</details>

<details>
<summary>遍历一个 List 有哪些不同的方式？每种方法的实现原理是什么？Java 中 List 遍历的最佳实践是什么？</summary>

遍历方式有以下几种：

for 循环遍历，基于计数器。在集合外部维护一个计数器，然后依次读取每一个位置的元素，当读取到最后一个元素后停止。
迭代器遍历，Iterator。Iterator 是面向对象的一个设计模式，目的是屏蔽不同数据集合的特点，统一遍历集合的接口。Java 在 Collections 中支持了 Iterator 模式。
foreach 循环遍历。foreach 内部也是采用了 Iterator 的方式实现，使用时不需要显式声明 Iterator 或计数器。优点是代码简洁，不易出错；缺点是只能做简单的遍历，不能在遍历过程中操作数据集合，例如删除、替换。
最佳实践：Java Collections 框架中提供了一个 RandomAccess 接口，用来标记 List 实现是否支持 Random Access。

如果一个数据集合实现了该接口，就意味着它支持 Random Access，按位置读取元素的平均时间复杂度为 O(1)，如ArrayList。
如果没有实现该接口，表示不支持 Random Access，如LinkedList。
推荐的做法就是，支持 Random Access 的列表可用 for 循环遍历，否则建议用 Iterator 或 foreach 遍历。

</details>

<details>
<summary>如何边遍历边移除 Collection 中的元素？</summary>

边遍历边修改 Collection 的唯一正确方式是使用 Iterator.remove() 方法，如下：

```java

Iterator<Integer> it = list.iterator();
while(it.hasNext()){
    // do something
    it.remove();
}

```

一种最常见的错误代码如下：

```java

for(Integer i : list){
    list.remove(i)
}

```
运行以上错误代码会报 ConcurrentModificationException 异常。这是因为当使用 foreach(for(Integer i : list)) 语句时，会自动生成一个iterator 来遍历该 list，但同时该 list 正在被 Iterator.remove() 修改。Java 一般不允许一个线程在遍历 Collection 时另一个线程修改它

</details>

## 10. JAVA 8的流库
## 11. Java常用类
## 12. 输入与输出
## 13. XML
## 14. JAVA网络操作
## 15. JAVA数据库
## 16. 日期和时间API
## 17. 国际化
## 18. 本地方法
## 19. 脚本 编译与注解
## 20. 反射
## 21. 注解
## 22. JSON
## 23. 正则表达式
## 24. 文本操作
## 25. 数组
## 26. 容器
## 27. 序列化与反序列化
## 28. 图形程序设计
## 29. Swing界面组件
## 30. 事件处理
## 31. 高级Swing 界面组件
## 32. 高级AWT
## 33. 部署JAVA应用程序
## 34. String

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

# 29、什么是DoS、DDoS、DRDoS攻击？如何防御？
# 30、描述一下Java异常层次结构。
# 31、什么是检查异常，不受检查异常，运行时异常？并分别举例说明。
# 32、finally块一定会执行吗？
# 33、正常情况下，当在try块或catch块中遇到return语句时，finally语句块在方法返回之前还是之后被执行？
# 34、try、catch、finally语句块的执行顺序。
# 40、Java中的参数传递时传值呢？还是传引用？
# 41、Java中有没有指针的概念？
# 58、如何解决业务层的数据访问问题？
# 59、为了解决数据库服务器的负担，如何做数据库的分布？
# 60、什么是著名的拜占庭将军问题？
# 61、
# 62、讲讲CAP理念。
# 63、怎么理解强一致性、单调一致性和最终一致性？
# 66、谈一谈一致性哈希算法。
# 67、paxos是什么？
# 68、什么是Lease机制？
# 69、如何理解选主算法？
# 70、
# 71. 关于如何使用clone方法以及浅克隆/深克隆机制

