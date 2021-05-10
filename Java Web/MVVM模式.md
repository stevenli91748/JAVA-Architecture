
* MVVM模式




# MVVM模式

   
  <a href="https://ibb.co/HT8PrXp"><img src="https://i.ibb.co/s9Y1FtJ/mvvm.png" alt="mvvm" border="0"></a>
  
  MVVM可以看作是一种特殊的MVP（Passive View）模式，或者说是对MVP模式的一种改良
  
  MVVM的调用关系和MVP一样。但是，在ViewModel当中会有一个叫Binder，或者是Data-binding engine的东西。以前全部由Presenter负责的View和Model之间数据同步操作交由给Binder处理。你只需要在View的
  模版语法当中，指令式地声明View上的显示的内容是和Model的哪一块数据绑定的。当ViewModel对进行Model更新的时候，Binder会自动把数据更新到View上去，当用户对View进行操作（例如表单输入），Binder也
  会自动把数据更新到Model上去。这种方式称为：Two-way data-binding，双向数据绑定。可以简单而不恰当地理解为一个模版引擎，但是会根据数据变更实时渲染。

  也就是说，MVVM把View和Model的同步逻辑自动化了。以前Presenter负责的View和Model同步不再手动地进行操作，而是交由框架所提供的Binder进行负责。只需要告诉Binder，View显示的数据对应的是Model哪一部
  分即可。
  
  这里有一个JavaScript MVVM的[例子](https://github.com/livoras/MVW-demos/blob/master/src/scripts/mvvm.js)，因为MVVM需要Binder引擎。所以例子中使用了一个MVVM的库：Vue.js。
