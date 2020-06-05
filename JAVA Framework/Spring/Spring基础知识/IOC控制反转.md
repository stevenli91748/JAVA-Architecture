
# IOC

* [深入剖析Spring IOC容器的运作原理](https://www.ctolib.com/topics-109935.html)
* [Spring之IOC原理详解](https://blog.csdn.net/sunpeng_sp/article/details/57419999)

IOC就是控制反转，是指创建对象的控制权的转移，以前创建对象的主动权和时机是由自己把控的，而现在这种权力转移到Spring容器中，并由容器根据配置文件去创建实例和管理各个实例之间的依赖关系，对象与对象之间松散耦合，也利于功能的复用

在spring中，对象的属性是由对象自己创建的，就是正向流程；如果属性不是对象创建，而是由spring来自动进行装配，就是控制反转。这里的DI也就是依赖注入，就是实
现控制反转的方式。正向流程导致了对象于对象之间的高耦合，IOC可以解决对象耦合的问题，有利于功能的复用，能够使程序的结构变得非常灵活。

当一个对象创建时, 它所依赖的对象由外部传递给他, 而非自己去创建所依赖的对象(比如通过new操作). 因此,也可以说在对象如何获取他所依赖的对象这件事情上, 控制权反转了

应用程序在运行时依赖IoC容器来动态注入对象需要的外部资源。



Spring的IOC有三种注入方式 ：构造器注入、setter方法注入、根据注解注入

构造器注入|setter方法注入|根据注解注入|
---|---|---|


# DI


DI依赖注入，和控制反转是同一个概念的不同角度的描述，即 应用程序在运行时依赖IoC容器来动态注入对象需要的外部资源


**没有依赖注入的例子：**

```java

@RestController
public class WelcomeController {

    private WelcomeService service = new WelcomeService();

	@RequestMapping("/welcome")
	public String welcome() {
		return service.retrieveWelcomeMessage();
	}
}

```

WelcomeService service = new WelcomeService(); 意味着WelcomeController类与WelcomeService类紧密结合在一起，耦合度高。

---

**使用依赖注入的例子：**

```java

@Component
public class WelcomeService {
    //Bla Bla Bla
}

@RestController
public class WelcomeController {

    @Autowired
    private WelcomeService service;

	@RequestMapping("/welcome")
	public String welcome() {
		return service.retrieveWelcomeMessage();
	}
}

```

依赖注入使世界看起来更简单，我们让Spring 框架做了辛勤的工作：
@Component：我们告诉Spring框架-嘿，这是一个你需要管理的bean
@Autowired：我们告诉Spring框架-嘿，找到这个特定类型的正确匹配并自动装入它









