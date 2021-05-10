
* MVVM模式




# MVVM模式

   
  <a href="https://ibb.co/HT8PrXp"><img src="https://i.ibb.co/s9Y1FtJ/mvvm.png" alt="mvvm" border="0"></a>
  
  MVVM可以看作是一种特殊的MVP（Passive View）模式，或者说是对MVP模式的一种改良
  
  MVVM的调用关系和MVP一样。但是，在ViewModel当中会有一个叫Binder，或者是Data-binding engine的东西。以前全部由Presenter负责的View和Model之间数据同步操作交由给Binder处理。你只需要在View的
  模版语法当中，指令式地声明View上的显示的内容是和Model的哪一块数据绑定的。当ViewModel对进行Model更新的时候，Binder会自动把数据更新到View上去，当用户对View进行操作（例如表单输入），Binder也
  会自动把数据更新到Model上去。这种方式称为：Two-way data-binding，双向数据绑定。可以简单而不恰当地理解为一个模版引擎，但是会根据数据变更实时渲染。


  
  
  用户与View交互，触发用户事件；View根据事件类型调用ViewModel中对应的响应逻辑；然后ViewModel中的响应逻辑在做完适当处理后，会去调用Model层的接口；接口的调用执行，导致Model层数据的变化，进而触
  发相应的数据改变事件；事件又被ViewModel模块监听到；拿到新的Model数据，ViewModel中的展示逻辑会去修改ViewModel中的状态数据；ViewModel中状态数据的改变，最终引起View的状态变换，完成了这次对用
  户事件的响应
