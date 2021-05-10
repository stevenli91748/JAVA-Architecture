
* MVVM模式




# MVVM模式

  用户与View交互，触发用户事件；View根据事件类型调用ViewModel中对应的响应逻辑；然后ViewModel中的响应逻辑在做完适当处理后，会去调用Model层的接口；接口的调用执行，导致Model层数据的变化，进而触
  发相应的数据改变事件；事件又被ViewModel模块监听到；拿到新的Model数据，ViewModel中的展示逻辑会去修改ViewModel中的状态数据；ViewModel中状态数据的改变，最终引起View的状态变换，完成了这次对用
  户事件的响应
