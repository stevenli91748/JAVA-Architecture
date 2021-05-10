
* MVP模式
  * PV模式---Passive View
  * SVC模式---Supervising Controller


# MVP模式
   
 **MVP与MVC的区别： MVP打破了View原来对于Model的依赖，其余的依赖关系和MVC模式一致, **
   
 **MVP和MVVM的主要区别：就是在于是否有双向数据绑定.**
   
MVP模式有两种：1) Passive View, 2) Supervising Controller, 而大多数情况下讨论的都是Passive View模式。本文会对PV模式进行较为详细的介绍，而SC模式则简单提及。   

既然View对Model的依赖被打破了，那View如何同步Model的变更？看看MVP的调用关系：

<a href="https://ibb.co/njZgxct"><img src="https://i.ibb.co/9Wz8QTS/MVP.png" alt="MVP" border="0"></a>

和MVC模式一样，用户对View的操作都会从View交移给Presenter。Presenter会执行相应的应用程序逻辑，并且对Model进行相应的操作；而这时候Model执行完业务逻辑以后，也是通过观察者模式把自己变更的消息传递出去，但是是传给Presenter而不是View。Presenter获取到Model变更的消息以后，通过**View提供的接口更新界面**

**关键点：**

View不再负责同步的逻辑，而是由Presenter负责。Presenter中既有应用程序逻辑也有同步逻辑。

View需要提供操作界面的接口给Presenter进行调用。（**关键**）

**MVP（Passive View）的优缺点**

**优点：**

便于测试。Presenter对View是通过接口进行，在对Presenter进行不依赖UI环境的单元测试的时候。可以通过Mock一个View对象，这个对象只需要实现了View的接口即可。然后依赖注入到Presenter中，单元测试的时候就可以完整的测试Presenter应用逻辑的正确性。这里根据上面的例子给出了Presenter的单元测试样例。
View可以进行组件化。在MVP当中，View不依赖Model。这样就可以让View从特定的业务场景中脱离出来，可以说View可以做到对业务完全无知。它只需要提供一系列接口提供给上层操作。这样就可以做到高度可复用的View组件。

**缺点：**

Presenter中除了应用逻辑以外，还有大量的View->Model，Model->View的手动同步逻辑，造成Presenter比较笨重，维护起来会比较困难。
