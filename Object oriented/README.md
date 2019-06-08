# 目录

[1.  类与对象](#类与对象)
* [1.1  类](#11-类)
* [1.2. 对象](#12-对象)
  * [java中的instance方法](https://blog.csdn.net/zrds2e/article/details/56958388)
* [1.3. 接口](#13-接口)
* [1.4. 多态](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Object%20oriented/多态.md)    
* [继承](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Object%20oriented/%E7%BB%A7%E6%89%BF.md)  
    
    
    
# 类与对象

## 1.1  类

## 1.2. 对象

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
