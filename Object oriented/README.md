# 目录

[1.  类与对象](#类与对象)
* [1.1  类](#11-类)
  * [JDK中所有类的基类——Object类](https://weread.qq.com/web/reader/df532740723982c7df583bfk8f132430178f14e45fce0f7) 
  * [最常用的引用类——Integer类](https://weread.qq.com/web/reader/df532740723982c7df583bfk45c322601945c48cce2e120)
  * [日常编码中最常用的类——String类](https://weread.qq.com/web/reader/df532740723982c7df583bfkd3d322001ad3d9446802347)
* [1.2. 对象](#12-对象)
  * [java中的instance方法](https://blog.csdn.net/zrds2e/article/details/56958388)
  * [Java的深拷贝和浅拷贝](https://weread.qq.com/web/reader/df532740723982c7df583bfkc9f326d018c9f0f895fb5e4)
    * [细说 Java 的深拷贝和浅拷贝]([[https://developer.aliyun.com/article/394579#:~:text=1%E3%80%81%E6%B5%85%E6%8B%B7%E8%B4%9D%EF%BC%9A%E5%AF%B9%E5%9F%BA%E6%9C%AC,%E5%86%85%E5%AE%B9%EF%BC%8C%E6%AD%A4%E4%B8%BA%E6%B7%B1%E6%8B%B7%E8%B4%9D%E3%80%82](https://segmentfault.com/a/1190000010648514)](https://segmentfault.com/a/1190000010648514)) 
* [1.3. 接口](#13-接口)
* [1.4. 多态](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Object%20oriented/多态.md)    
* [继承](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Object%20oriented/%E7%BB%A7%E6%89%BF.md)  
* [面试官：什么是对象池？有什么用？别说你还不会！](https://mp.weixin.qq.com/s/EZ0firt5T8sVOdh8BS_CWQ)    
    
    
# 类与对象

## 1.1  类

## 1.2. 对象

* [Java对象实例化过程](https://mrbird.cc/Java%E5%AF%B9%E8%B1%A1%E5%AE%9E%E4%BE%8B%E5%8C%96%E8%BF%87%E7%A8%8B.html)
* [Java方法调用细节](https://mrbird.cc/Java%E6%96%B9%E6%B3%95%E8%B0%83%E7%94%A8%E7%BB%86%E8%8A%82.html)
## 1.3. 接口

### 静态块 静态变量
当类加载器将类加载到JVM中的时候就会创建静态变量，这跟对象是否创建无关。静态变量加载的时候就会分配内存空间。静态代码块的代码只会在类第一次初始化的时候执行一次。一个类可以有多个静态代码块，它并不是类的成员，也没有返回值，并且不能直接调用。静态代码块不能包含this或者super,它们通常被用初始化静态变量。

   [JAVA中静态块、静态变量加载顺序详解](https://blog.csdn.net/Mrzhoug/article/details/51581994)
   
   [静态代码块，构造代码块，构造函数之间的差别及先后执行顺序](https://blog.csdn.net/u011889786/article/details/53364205)  
   
   [Java虚拟机类加载机制——案例分析](https://blog.csdn.net/u013256816/article/details/50837863)
   
   [java中静态属性，静态方法和静态初始化器](https://blog.csdn.net/bzhxuexi/article/details/17606691)



# 有用的参考

* [Java对象引用四个级别（强、软、弱、虚）](https://mp.weixin.qq.com/s/QPEqJemzBaKQ8kJE94peig)
* [从JVM角度看为什么子类不能重写父类静态方法](https://blog.csdn.net/TyroneRenekton/article/details/68923475)
* [父类的静态变量被继承后,子类修改静态变量,为什么父类的也跟着变了?](https://bbs.csdn.net/topics/370216386)
* [子类是否可以继承父类的静态方法，是否可以重写父类的静态方法呈现多态性？](https://blog.csdn.net/Ghost_tal/article/details/80295730)
* [子类继承的父类中的static变量会和单独创建的父类的实例中的是static变量是同一个变量吗](https://blog.csdn.net/qq_39632912/article/details/79001659)
* [当子类继承父类时，构造块，静态块，静态方法的执行顺序](https://blog.csdn.net/uhana/article/details/60869535)
* [Java：关于值传递你需要了解的事情](http://www.importnew.com/29023.html)
* [面向对象三大特性五大原则 + 低耦合高内聚](https://www.cnblogs.com/corvoh/p/5747856.html)
* [java中的访问修饰符](https://www.cnblogs.com/tjudzj/p/4443066.html)
* [java中的类修饰符、成员变量修饰符、方法修饰符。](https://www.cnblogs.com/lixiaolun/p/4311727.html)
* [理解java多态的概念和作用](https://blog.csdn.net/Hack_Bug/article/details/26231221)
* [Java抽象类和抽象方法详解](https://blog.csdn.net/Smile_729day/article/details/79358043)
* [Java多态详解](https://blog.csdn.net/smile_729day/article/details/79562589)
* [Java内部类整理笔记](https://blog.csdn.net/smile_729day/article/details/79634309)
* [Java接口详解](https://blog.csdn.net/smile_729day/article/details/79549018)
