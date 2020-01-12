

# Context
  
spring进行IOC实现时使用的有两个概念：context上下文和bean。

如中间图所示，所有被spring管理的、由spring创建的、用于依赖注入的对象，就叫做一个bean。Spring创建并完成依赖注入后，所有bean统一放在一个叫做context
的上下文中进行管

## Spring的Context的初始化流程

<a href="https://ibb.co/NyYF7bC"><img src="https://i.ibb.co/5Rc12w4/context932a404bffe9d7d0c4-r.jpg" alt="context932a404bffe9d7d0c4-r" border="0"></a>

图中左上角是三种类型的context，xml配置方式的context、springboot的context和web服务的context。不论哪种context，创建后都会调用到AbstractApplicationContext类的refresh方法，这个方法是我们要重点分析的。

      refresh方法中，操作共分13步：

      第1步：对刷新进行准备，包括设置开始时间、设置激活状态、初始化context环境中的占位符，这个动作根据子类的需求由子类来执行，然后验证是否缺失必
             要的properties；

      第2步：刷新并获得内部的bean factory；

      第3步：对bean factory进行准备工作，比如设置类加载器和后置处理器、配置不进行自动装配的类型、注册默认的环境bean；

      第4步：为context的子类提供后置处理bean factory的扩展能力。如果子类想在bean定义加载完成后，开始初始化上下文之前做一些特殊逻辑，可以复写这
             个方法；

      第5步，执行context中注册的bean factory后缀处理器；

      注：这里有两种后置处理器，一种是可以注册bean的后缀处理器，另一种是针对bean factory进行处理的后置处理器。执行的顺序是，先按优先级执行可
      注册bean的处理器，在按优先级执行针对beanfactory的处理器。

      对springboot来说，这一步会进行注解bean definition的解析。流程如右面小框中所示，由ConfigurationClassPostProcessor触发、
      由ClassPathBeanDefinitionScanner解析并注册到bean factory。

      第6步：按优先级顺序在beanfactory中注册bean的后缀处理器，bean后置处理器可以在bean初始化前、后执行处理；

      第7步：初始化消息源，消息源用来支持消息的国际化；

      第8步：初始化应用事件广播器。事件广播器用来向applicationListener通知各种应用产生的事件，是一个标准的观察者模式；

      第9步，是留给子类的扩展步骤，用来让特定的context子类初始化其他的bean；

      第10步，把实现了ApplicationListener的bean注册到事件广播器，并对广播器中的早期未广播事件进行通知；

      第11步，冻结所有bean描述信息的修改，实例化非延迟加载的单例bean；

      第12步，完成上下文的刷新工作，调用LifecycleProcessor的onFresh()方法以及发布ContextRefreshedEvent事件；

      第13步：在finally中，执行第十三步，重置公共的缓存，比如ReflectionUtils中的缓存、AnnotationUtils中的缓存等等；

      至此，spring的context初始化完成。这里仅介绍了最主要的主流程，建议课后阅读源码来复习这个知识点，补全细节。





# Bean
