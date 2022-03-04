* Spring中Bean的scope有五种---实际上，Spring最基本的scope只有两种，即singleton和prototype
  * singleton---单例Bean，顾名思义具有单例模式的所有特性，在spring容器里面只会初始化出一个bean实例，存于缓存中。后续的请求都公用这个对象, 最简单的创建单例bean的方式，就是直接在类名上
                面加@Service注解
  * prototype---即原型Bean，每次请求时都会创建新的bean实例直接使用,创建原型Bean，需要显示指定scope属性。<bean id="accountService" class="com.foo.DefaultAccountService" scope="prototype"/>
  * request
  * session
  * global session 




* [深入学习Spring组件注册](https://mrbird.cc/Spring-Bean-Regist.html)
  * [深入学习Spring Bean生命周期](https://mrbird.cc/Spring-Bean-Lifecycle.html)
  * [深入理解Spring Bean对象循环依赖](https://mrbird.cc/%E6%B7%B1%E5%85%A5%E7%90%86%E8%A7%A3Spring%E5%BE%AA%E7%8E%AF%E4%BE%9D%E8%B5%96.html)
* [深入学习Spring Boot自动装配](https://mrbird.cc/deepin-springboot-autoconfig.html)
  * [Spring条件化装配Bean](https://mrbird.cc/Spring%E6%9D%A1%E4%BB%B6%E5%8C%96%E8%A3%85%E9%85%8DBean.html) 
  * [Spring混合装配Bean](https://mrbird.cc/Spring%E6%B7%B7%E5%90%88%E8%A3%85%E9%85%8DBean.html)
  * [Java代码装配Bean](https://mrbird.cc/Java%E4%BB%A3%E7%A0%81%E8%A3%85%E9%85%8DBean.html)
* [How to display all beans loaded by Spring Boot](https://mkyong.com/spring-boot/how-to-display-all-beans-loaded-by-spring-boot/)
* [Spring Boot 获取 bean 的 3 种方式！还有谁不会？？](https://mp.weixin.qq.com/s/9SaIP4f7vz7VuWGTs0N10w)


# 参考

* [Spring中Bean的scope](https://www.jianshu.com/p/36a065065e3c)
* [决定组件线程安全性的主要因素是其作用域Scope](https://zhuanlan.zhihu.com/p/60346760)
