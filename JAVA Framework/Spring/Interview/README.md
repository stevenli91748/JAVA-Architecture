
[Spring面试题（2020最新版）](https://thinkwon.blog.csdn.net/article/details/104397516)|[全网最全Spring系列面试题129道（附答案解析）](https://mp.weixin.qq.com/s/-w-r3dYn3qAC9BMPeJL_1w)|
---|---|


目录
----

<details>
<summary>Spring4.x推荐使用java配置，为什么推荐这种配置方式？与xml配置和注解配置相比有什么优势？</summary>
  https://www.zhihu.com/question/278435266
</details>

<details>
<summary>依赖注入</summary>

在Spring中，我们基本不需要 new 一个类，这些都是让 Spring 去做的。
Spring 启动时会把所需的类实例化成对象，如果需要依赖，则先实例化依赖，然后实例化当前类。
因为依赖必须通过构建函数传入，所以实例化时，当前类就会接收并保存所有依赖的对象。
这一步也就是所谓的依赖注入
</details>

<details>
<summary>控制反转</summary>

控制反转通过依赖注入（DI）方式实现对象之间的松耦合关系。
程序运行时，依赖对象由【辅助程序】动态生成并注入到被依赖对象中，动态绑定两者的使用关系。
Spring IoC容器就是这样的辅助程序，它负责对象的生成和依赖的注入，让后在交由我们使用。

简而言之，就是：IoC就是一个对象定义其依赖关系而不创建它们的过程。

在 Spring 中，类的实例化、依赖的实例化、依赖的传入都交由 Spring Bean 容器控制，
而不是用new方式实例化对象、通过非构造函数方法传入依赖等常规方式。
实质的控制权已经交由程序管理，而不是程序员管理，所以叫做控制反转。

</details>

# 4.核心容器（应用上下文)模块。
# 5.BeanFactory–BeanFactory实现举例。
# 6.XMLBeanFactory
# 7.解释AOP模块
# 8.解释JDBC抽象和DAO模块。
# 9.解释对象/关系映射集成模块。
# 10.解释WEB模块。
# 11.为什么说Spring是一个容器？
# 12.Spring配置文件
# 13.什么是SpringIOC容器？
# 14.IOC的优点是什么？
# 15.ApplicationContext通常的实现是什么?
# 16.Bean工厂和Applicationcontexts有什么区别？
# 17.一个Spring的应用看起来象什么？
# 18.什么是Spring的依赖注入？
# 19.有哪些不同类型的IOC（依赖注入）方式？
# 20.哪种依赖注入方式你建议使用，构造器注入，还是Setter方法注入？
# 21.什么是Springbeans?
# 22.一个SpringBean定义包含什么？
# 23.如何给Spring容器提供配置元数据?
# 24.你怎样定义类的作用域?
# 25.解释Spring支持的几种bean的作用域。
# 26.Spring框架中的单例bean是线程安全的吗?
# 27.解释Spring框架中bean的生命周期。
# 28.哪些是重要的bean生命周期方法？你能重载它们吗？
# 29.什么是Spring的内部bean？
# 30.在Spring中如何注入一个java集合？
# 31.什么是bean装配?
# 32.什么是bean的自动装配？
# 33.解释不同方式的自动装配。
# 34.自动装配有哪些局限性?
# 35.你可以在Spring中注入一个null和一个空字符串吗？
# 36.什么是基于Java的Spring注解配置?给一些注解的例子.
# 37.什么是基于注解的容器配置?
# 38.怎样开启注解装配？
# 39.@Required注解
# 40.@Autowired注解
# 41.@Qualifier注解
# 42.在Spring框架中如何更有效地使用JDBC?
# 43.JdbcTemplate
# 44.Spring对DAO的支持
# 45.使用Spring通过什么方式访问Hibernate?
# 46.Spring支持的ORM
# 47.如何通过HibernateDaoSupport将Spring和Hibernate结合起来？
# 48.Spring支持的事务管理类型
# 49.Spring框架的事务管理有哪些优点？
# 50.你更倾向用那种事务管理类型？
# 51.解释AOP
# 52.Aspect切面
# 53.在SpringAOP中，关注点和横切关注的区别是什么？
# 54.连接点
# 55.通知
# 56.切点
# 57.什么是引入?
# 58.什么是目标对象?
# 59.什么是代理?
# 60.有几种不同类型的自动代理？
# 61.什么是织入。什么是织入应用的不同点？
# 62.解释基于XMLSchema方式的切面实现。
# 63.解释基于注解的切面实现
# 64.什么是Spring的MVC框架？
# 65.DispatcherServlet
# 66.WebApplicationContext
# 67.什么是SpringMVC框架的控制器？
# 68.@Controller注解
# 69.@RequestMapping注解
# 70.返回Json用什么注解？



# 项目经验

<details>
<summary>一项目中有通过插件的方式，利用java中反射加载的类，有通过spring bean配置加载类，对于反射加载的类，如何来进行spring aop的配置呢？</summary>

通过spring 自己加载的类, 它在加载时, 它可以有两个选择(二者选其一即可), 一个是做动态代理. 一个是通过asm,进行class修改. 做了这两个之后, aop便是顺其自然的事情 . 你new的类,没有做这些事情, 肯定无法利用spring的aop.
aop本身实现并不复杂, 你可以自己写一个加载器. 多多参考一下网上用例吧. 自己使用动态代理+反射做,效率会低一些. 如果使用asm或者cglib做,负责一些,但是效率最高.

<details>
  
  
  
[69道Spring面试题和答案](http://ifeve.com/spring-interview-questions-and-answers/)


90.为什么要使用 spring？
91.解释一下什么是 aop？
92.解释一下什么是 ioc？
93.spring 有哪些主要模块？
94.spring 常用的注入方式有哪些？
95.spring 中的 bean 是线程安全的吗？
96.spring 支持几种 bean 的作用域？
97.spring 自动装配 bean 有哪些方式？
98.spring 事务实现方式有哪些？
99.说一下 spring 的事务隔离？
100.说一下 spring mvc 运行流程？
101.spring mvc 有哪些组件？
102.@RequestMapping 的作用是什么？
103.@Autowired 的作用是什么？

