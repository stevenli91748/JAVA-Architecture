


# @Controller

被@Controller标记的类实际上就是个SpringMVC Controller对象，它是一个控制器类，而@Contoller注解在org.springframework.stereotype包下。
其中被@RequestMapping标记的方法会被分发处理器扫描识别，将不同的请求分发到对应的接口上。

```java
@Controller
public class TestController {

private static final Log logger  = LogFactory.getLog(TestController.class);

@RequestMapping(value = "/hello")
public ModelAndView hello () {
    logger.info("hello() 方法被调用");
    ModelAndView mv = new ModelAndView();
    mv.addObject("message", "Hello, Ma Yuzhe!");
    mv.setViewName("/WEB-INF/views/hello.jsp");
    return mv;
}
}

```

TestController即为一个自定类。在该类的前面标记@Controller，该类就成了一个控制器类。
在浏览器地址栏中输入http://localhost:8080/hello，即对hello()方法进行调用，前台返回hello.jsp界面
