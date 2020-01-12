


<a href="https://ibb.co/Fnh7qSC"><img src="https://i.ibb.co/R7gH4xd/extent7b09ae0713383a-r.jpg" alt="extent7b09ae0713383a-r" border="0"></a>


对spring进行定制化功能扩展时，可以选择如下一些扩展点：

        ▌1.BeanFactoryPostProcessor

        是beanFactory后置处理器，支持在bean factory标准初始化完成后，对bean factory进行一些额外处理。在讲context初始化流程时介绍过，这时所有的bean的描述信息已经加载完毕，但是还没有进行bean初始化。例如前面提到的PropertyPlaceholderConfigurer，就是在这个扩展点上对bean属性中的占位符进行替换。

        ▌2.BeanDefinitionRegistryPostProcessor

        它扩展自BeanFactoryPostProcessor，在执行BeanFactoryPostProcessor的功能前，提供了可以添加bean definition的能力，允许在初始化一般bean前，注册额外的bean。例如可以在这里根据bean的scope创建一个新的代理bean。

        ▌3.BeanPostProcessor

        提供了在bean初始化之前和之后插入自定义逻辑的能力。与BeanFactoryPostProcessor的区别是处理的对象不同，BeanFactoryPostProcessor是对beanfactory进行处理，BeanPostProcessor是对bean进行处理。

        注：上面这三个扩展点，可以通过实现Ordered和PriorityOrdered接口来指定执行顺序。实现PriorityOrdered接口的processor会先于实现Ordered接口的执行。

        ▌4.ApplicationContextAware

        可以获得ApplicationContext及其中的bean，当需要在代码中动态获取bean时，可以通过实现这个接口来实现。

        ▌5.InitializingBean

        可以在bean初始化完成，所有属性设置完成后执行特定逻辑，例如对自动装配对属性进行验证等等。

        ▌6.DisposableBean

        用于在bean被销毁前执行特定的逻辑，例如做一些回收工作等。

        ▌7.ApplicationListener

        用来监听spring的标准应用事件或者自定义事件。
