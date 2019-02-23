
# [Java基本知识面试](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20fundamental/interview.md)

# 目录

* [基本程序设计结构](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20fundamental/Basic%20Program%20Structure/README.md)
  * [java关键字和标识符](#java关键字和标识符)
  * [数据类型](#数据类型)
    * [基本类型](#基本类型)
    * [引用类型](#引用类型)
  * [常量](#常量)
  * [变量](#变量)
    * 变量的种类
      * 类变量
      * 实例变量
      * 局部变量
    * 变量的作用域
      * 类变量和实例变量的生命周期
        * 局部变量的生命周期
        * 实例变量和局部变量同名
        * 将局部变量的作用域最小化
      * 对象的默认引用“this”
      * 参数传递
      * 变量的初始化以及默认值
        * 实例变量的初始化
        * 局部变量的初始化
      * 直接数
  * [操作运算符](#操作运算符)
    * [整型操作符](#整型操作符)
    * [浮点型操作符](#浮点型操作符)
    * [比较操作符和逻辑操作符](#比较操作符和逻辑操作符)
    * [特殊操作符“？”](#特殊操作符“？”)
    * [操作符“=”与对象的equals()方法](#操作符“=”与对象的equals()方法)
    * [instanceof操作符](#instanceof操作符)
    * [变量的赋值和类型转换](#变量的赋值和类型转换)
  * [流程控制](#流程控制)
    * 分支语句
      * if else
      * switch
    * 循环语句
      * while
      * do while
      * for
      * foreach
      * 多重循环
    * 流程跳转语句
* [Java面向对象](#Java面向对象)
  * [掌握对象的三大特性](#掌握对象的三大特性)
  * [java语言的修饰符](#java语言的修饰符)
    * [访问控制修饰符](#访问控制修饰符)
    * [Abstract修饰符](#Abstract修饰符)
    * [Final修饰符](#Final修饰符)
       * [Final类](#Final类)
       * [Final方法](#Final方法)
    * [Static修饰符](#Static修饰符)
       * [Static变量](#Static变量)
       * [Static方法](#Static方法)
       * [Static代码块](#Static代码块)
       * [用Static进行静态导入](#用Static进行静态导入)
  * [类的生命周期](#类的生命周期)
    * 类的加载 连接和初始化
    * 类加载器
    * 类的卸载
    
  * [对象的生命周期](#对象的生命周期)
    * 创建对象的方式
    * 构造方法
      * 重载构造方法
      * 默认构造方法
      * 子类调用父类的构造方法
      * 构造方法的作用域
      * 构造方法的访问级
    * 静态工厂方法
      * 单例类
      * 枚举类
      * 不可变类与可变类
      * 具有实例缓存的不可变类
      * 松耦合的系统接口
    * 垃圾回收
      * 对象的可触及性
      * 垃圾回收的时间
      * 对象的finalize()方法
      * 比较finalize()方法和finally代码块
    * 清除过期的对象引用
    * 对象的强 软 弱和虚引用 
* [继承](#继承)
  * 方法重载
  * 方法覆盖
  * 方法覆盖和方法重载的异同
  * Super关键字
  * 多态
  * 继承的利弊和使用原则
    * 继承树的层次不可太多
    * 继承树的上层为抽象层
    * 继承关系的最大弱点
    * 精心设计专门用于被继承的类
    * 区分对象的属性于继承
  * 比较组合于继承
    * 组合关系的分解过程对应继承关系的抽象过程
    * 组合关系的组合过程对应继承关系的旷展过程
  * [继承设计技巧](#继承设计技巧)
  
  
* [接口](#接口)
  * [比较抽象类与接口的不同](#比较抽象类与接口的不同)
  * [与接口相关的设计模式](#与接口相关的设计模式)
    * [定制服务模式](#定制服务模式)
    * [默认适配器模式](#默认适配器模式)
    * [适配器模式](#适配器模式)
    * [代理模式](#代理模式)
    * [标识类型模式](#标识类型模式)
    * [常量接口模式](#常量接口模式)
* [Lambda标达式](#Lambda标达式)
  * 用Lambda表达式代替内部类
  * Lambda表达式和集合的forEach()方法
  * 用Lambda表达式对集合进行排序
  * Lambda表达式与Stream API联合使用
  * Lambda表达式可操纵的变量作用域
  * Lambda表达式中的方法引用
  * 函数式接口
* [内部类](#内部类)
  * [内部类的用途](#内部类的用途)
  * [实例内部类](#实例内部类)
  * [静态内部类](#静态内部类)
  * [局部内部类](#局部内部类)
  * [内部类的继承](#内部类的继承)
  * [子类与父类中的内部类同名](#子类与父类中的内部类同名)
  * [匿名内部类](#匿名内部类)
  * [内部接口以及接口中的内部类](#内部接口以及接口中的内部类)
  * [内部类的类文件](#内部类的类文件)
  
* [异常 断言和日志](#异常-断言和日志)
  * Java异常处理机制概述
    * Java异常处理机制的优点
    * Java虚拟机的方法调用栈
    * 异常处理对性能的影响
  * 运用Java异常处理机制
    * try-catch
    * finally
    * throws
    * throw
    * 异常处理语句的语法规则
    * 异常流程的运行过程
    * 跟踪丢失的异常
  * 异常类
    * 运行时异常
    * 受检查异常
    * 区分运行时异常和受检查异常
  * 用户定义异常
    * 异常转译和异常链
    * 处理多样化异常
  * 异常处理原则
    * 异常只能用于非正常情况
    * 为异常提供说明文档
    * 保持异常的原子性
    * 避免过于庞大的try代码块
    * 在catch子句中指定具体的异常类型
    * 不要在catch代码块中被捕获的异常
  * 记录日志
    * 创建Logger对象及设置日志级别
    * 生成日志
    * 把日志输出到文件
    * 设置日志的输出格式
  * 使用断言
* [泛型](#泛型)
  * java集合的泛型
  * 定义泛型类和泛型接口
  * 定义泛型数组
  * 定义泛型方法
  * 用extends 关键字限定类型参数
  * 使用“？”通配符
  * 使用泛型注意事项
* [集合](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20fundamental/Basic%20Program%20Structure/%E9%9B%86%E5%90%88.md)
* [JAVA 8的新特性](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20fundamental/Basic%20Program%20Structure/JAVA%208%E7%9A%84%E6%96%B0%E7%89%B9%E6%80%A7.md)
* [Java常用类](#Java常用类)
  * [Object类](#Object类)
  * [String类和StringBuffer StringBuilder类](#String类和StringBuffer-StringBuilder类)
  * [包装类](#包装类)
  * [Math类](#Math类)
  * [Random类](#Random类)
  * [传统的处理日期 时间的类](#传统的处理日期-时间的类)
  * [新的处理日期 时间的类](#新的处理日期-时间的类)
  * [BigInteger类](#BigInteger类)
  * [BigDecimal类](#BigDecimal类)
  * [用Optional类避免空指针异常](#用Optional类避免空指针异常)
* [输入与输出](#输入与输出)
  * 输入流
    * ByteArrayInputStream类
    * FileInputStream类
    * PipedInputStream类
    * SequenceInputStream类
  * 过滤输入流
    * FilterInputStream类
    * 装饰器设计模式
    * 过滤输入流的种类
    * DataInputStream类
    * BufferedInputStream类
    * PushbackInputStream类
  * 输出流
    * ByteArrayOutputStream类
    * FileOutputStream类
  * 过滤输出流
    * FilterOutputStream类
    * DataOutputSrream类
    * BufferedOutputStream类
    * PrintStream类
  * Reader 
    * CharArrayReader类
    * StringReader类
    * InputStreamReader类
    * FileReader类
    * BufferedReader类
  * Writer
    * CharArrayWriter类
    * OutputStreamWriter类
    * FileWriter类
    * BufferedWriter类
    * PrintWriter类
  * 标准I/O 类库
    * 重新包装标准输入和输出
    * 标准I/O 重定向
  * 新I/O 类库
    * 缓冲器Buffer概述
    * 通道Channel类
    * 字符编码Charset类
    * 用FileChannel读写文件
    * 控制缓冲区
    * 字符编码转换
    * 缓冲区视图
    * 文件映射缓冲区 MappedByteBuffer
    * 文件加锁
  * 随机访问文件类库
    * RandomAccessFile
  * [序列化与反序列化](#序列化与反序列化)
  * 自动释放资源
  * 用File类来查看 创建和删除文件或目录
  * 用Java。nio.file类库来操作文件系统
* [XML](#XML)
* [JAVA网络操作](#JAVA网络操作)
* [JAVA数据库](#JAVA数据库)
  *  [JDBC](#JDBC)
* [国际化](#国际化)
* [本地方法](#本地方法)
* [脚本 编译与注解](#脚本-编译与注解)
* [反射](#反射)
  * [利用反射分析类的能力](#利用反射分析类的能力)
  * [利用反射在运行时分析对象的能力](#利用反射在运行时分析对象的能力)
  * [调用任意方法](#调用任意方法)
  * [反射类的方法](#反射类的方法)
* [注解](#注解)
  * 自定义Annotation注解类型
  * 在类的源代码中引用注解类型
  * 在程序中运用反射机制读取类的注解信息
  * 基本内置注解
* [JSON](#JSON)
* [正则表达式](#正则表达式)
* [文本操作](#文本操作)
* [数组](#数组)
  * 数组变量的声明
  * 创建数组对象
  * 访问数组的元素和长度
  * 数组的初始化
  * 多维数组以及不规则数组
  * 调用数组对象的方法
  * 把数组作为方法参数或返回值
  * 数组排序
  * 数组的二分查找算法
  * 哈希表
  * 数组实用类 Arrays
  * 用符号“...”声明数目可变参数
* [容器](#容器)

* [图形程序设计](#图形程序设计)
* [Swing界面组件](#Swing界面组件)
* [事件处理](#事件处理)
* [高级Swing 界面组件](#高级Swing-界面组件)
* [高级AWT](#高级AWT)
* [部署JAVA应用程序](#部署JAVA应用程序)






内容
---

### [基本程序设计结构]()

#### java关键字和标识符
#### 数据类型
##### 基本类型
##### 引用类型
#### 常量
#### 变量
#### 操作运算符
##### 整型操作符
##### 浮点型操作符
##### 比较操作符和逻辑操作符
##### 特殊操作符“？”
##### 操作符“=”与对象的equals()方法
##### instanceof操作符
##### 变量的赋值和类型转换
#### 流程控制

### Java面向对象
#### 掌握对象的三大特性
#### java语言的修饰符
##### 访问控制修饰符
##### Abstract修饰符
##### Final修饰符
###### Final类
###### Final方法
##### Static修饰符
###### Static变量
###### Static方法
###### Static代码块
###### 用Static进行静态导入
#### 类的生命周期
#### 对象的生命周期

### 继承
#### 继承设计技巧
    * 1. 将公共操作和域放在超类
    * 2. 
### 接口
#### 比较抽象类与接口的不同
#### 与接口相关的设计模式
##### 定制服务模式
##### 默认适配器模式
##### 适配器模式
##### 代理模式
##### 标识类型模式
##### 常量接口模式

### Lambda标达式
### 内部类
#### 内部类的用途
#### 实例内部类
#### 静态内部类
#### 局部内部类
#### 内部类的继承
#### 子类与父类中的内部类同名
#### 匿名内部类
#### 内部接口以及接口中的内部类
#### 内部类的类文件
### 异常 断言和日志
### 泛型
### 集合
### [JAVA 8的流库](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Java%20fundamental/Basic%20Program%20Structure/JAVA%208%E7%9A%84%E6%96%B0%E7%89%B9%E6%80%A7.md)
### Java常用类
#### Object类
#### String类和StringBuffer StringBuilder类
#### 包装类
#### Math类
#### Random类
#### 传统的处理日期 时间的类
#### 新的处理日期 时间的类
#### BigInteger类
#### BigDecimal类
#### 用Optional类避免空指针异常

### 输入与输出
### XML
### JAVA网络操作
### JAVA数据库
### JDBC
### 日期和时间API
### 国际化
### 本地方法
### 脚本 编译与注解
### 反射
   能够分析类能力的程序称为**反射**，能在运行时动态地分析**类  对象**的能力
   
#### 利用反射分析类的能力   
#### 利用反射在运行时分析对象的能力
#### 调用任意方法
#### 反射类的方法

### 注解
### JSON
### 正则表达式
### 文本操作
### 数组

### 容器
### 序列化与反序列化
### 图形程序设计
### Swing界面组件
### 事件处理
### 高级Swing 界面组件
### 高级AWT
### 部署JAVA应用程序




# 有用的参考

 * [Java动态代理原理分析](http://objcoding.com/2017/08/16/Java-Dynamic-proxy/)
 * [Java基础知识大全](https://blog.csdn.net/xingkonglfs/article/details/54695622)
 * [深入理解java反射机制](https://blog.csdn.net/u012585964/article/details/52011138)
 * [JAVA 笔记（四） RTTI - 运行时类型检查](https://blog.csdn.net/ghost_Programmer/article/details/51532064)
 * [这是转载的 java反射机制与原理，原作者讲解的相对很通俗易懂](https://blog.csdn.net/le_le_name/article/details/52337116)
 * [Java基础知识总结（绝对经典）](https://blog.csdn.net/xyzopq100/article/details/50683899)
 * [java中new和反射的区别](https://blog.csdn.net/Jarvan_Song/article/details/52044692)
 * [注解的原理又是怎么一回事](https://mp.weixin.qq.com/s?__biz=MjM5MzA1Mzc3Nw==&mid=2247483854&idx=1&sn=8f7a8701604134e5f35a4ab1a656df7a&chksm=a69dacf091ea25e683f7c769bdcb1f60b708913baf1e7e477001b806eab1a2ce2fff5e271c57&scene=21#wechat_redirect)
 * [Java调用本地方法又是怎么一回事](https://mp.weixin.qq.com/s?__biz=MjM5MzA1Mzc3Nw==&mid=2247483909&idx=1&sn=1dd448a384132341a0d46ff7837da919&chksm=a69daf3b91ea262de3e5a35bca8f5bfa06bcced7036b40a587035e11dcb0d46aa393abddd3a7&scene=21#wechat_redirect)
 * [从操作系统内核看Java非阻塞IO事件检测](https://mp.weixin.qq.com/s?__biz=MjM5MzA1Mzc3Nw==&mid=2247483667&idx=1&sn=316f9b08e43bb11c1b8c2cb2f1eec548&chksm=a69dac2d91ea253b4c1e62ab13b385bd4987f2bdabb1db2cad6c3f40d0b456b4f1951f637a5d&scene=21#wechat_redirect)
 * [如何让你的传输更安全——NIO模式和BIO模式实现SSL协议通信](https://mp.weixin.qq.com/s?__biz=MjM5MzA1Mzc3Nw==&mid=2247483868&idx=1&sn=968e2ac4a52dbdfca198c91cc9c3d925&chksm=a69dace291ea25f4337952ae9b3590ffc6dc3554be07a1859ebb6d31cd6218ca273ec24f9309&scene=21#wechat_redirect)
 
 * [JDK8新特性之Optional](https://mp.weixin.qq.com/s/uXw4eTZqLfj871FlciPh6Q)
 * [图解 Java 中的数据结构及原理，傻瓜也能看懂](https://mp.weixin.qq.com/s/2t_XO3G1v1jx7q0-9YU0YA)





