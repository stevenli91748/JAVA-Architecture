
# AOP



# AOP例子程序
* [Spring AOP 详解 1(注解方式)  例子说明](https://juejin.im/post/5d390f705188252c784a4f74)
* [Spring AOP 详解 2(xml方式)  例子说明 先看](https://www.cnblogs.com/hongwz/p/5764917.html)






AOP动态代理|AOP静态代理|
---|--|

[spring-aop]("面向切面编程")|[spring-aspects]("集成AspectJ")|[spring-instrument]("提供一些类级的工具支持和ClassLoader级的实现，用于服务器")|[spring-instrument-tomcat]("针对tomcat的instrument实现包含了spring的tomcat设备代理它们的完整依赖关系")|
---|---|---|---|

[《包你懂系列》让我们来揭开 AOP 和动态代理技术的神秘面纱](https://juejin.im/post/5ed723606fb9a047a07f2ec2)|[面试被问了几百遍的 IoC 和 AOP ，还在傻傻搞不清楚](https://juejin.im/post/5ecf62ee51882542ef4f851f)|
---|--|

* [Spring AOP 源码分析系列文章导读](https://www.tianxiaobo.com/2018/06/17/Spring-AOP-%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90%E7%B3%BB%E5%88%97%E6%96%87%E7%AB%A0%E5%AF%BC%E8%AF%BB/)
* [Spring AOP 源码分析 - 筛选合适的通知器](https://www.tianxiaobo.com/2018/06/20/Spring-AOP-%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90-%E7%AD%9B%E9%80%89%E5%90%88%E9%80%82%E7%9A%84%E9%80%9A%E7%9F%A5%E5%99%A8/)
* [Spring AOP 源码分析 - 创建代理对象](https://www.tianxiaobo.com/2018/06/20/Spring-AOP-%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90-%E5%88%9B%E5%BB%BA%E4%BB%A3%E7%90%86%E5%AF%B9%E8%B1%A1/)
* [Spring AOP 源码分析 - 拦截器链的执行过程](https://www.tianxiaobo.com/2018/06/22/Spring-AOP-%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90-%E6%8B%A6%E6%88%AA%E5%99%A8%E9%93%BE%E7%9A%84%E6%89%A7%E8%A1%8C%E8%BF%87%E7%A8%8B/)






AOP就是面向切面编程。如右面的图，一般程序执行流程是从controller层调用service层、然后service层调用DAO层访问数据，最后在逐层返回结果。

这个是图中向下箭头所示的按程序执行顺序的纵向处理。但是，一个系统中会有多个不同的服务，例如用户服务、商品信息服务等等，每个服务的controller层都需要验
证参数，都需要处理异常，如果按照图中红色的部分，对不同服务的纵向处理流程进行横切，在每个切面上完成通用的功能，例如身份认证、验证参数、处理异常等等、这
样就不用在每个服务中都写相同的逻辑了，这就是AOP思想解决的问题。

AOP以功能进行划分，对服务顺序执行流程中的不同位置进行横切，完成各服务共同需要实现的功能。

AOP的实现是通过代理模式，在调用对象的某个方法时，执行插入的切面逻辑。实现的方式有动态代理也叫运行时增强，比如jdk代理、CGLIB；静态代理是在编译时进行织入或类加载时进行织入，比如AspectJ。

关于AOP还需要了解一下对应的Aspect、pointcut、advice等注解和具体使用方式。
