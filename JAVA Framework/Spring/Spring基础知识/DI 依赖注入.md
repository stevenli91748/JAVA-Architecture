
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
