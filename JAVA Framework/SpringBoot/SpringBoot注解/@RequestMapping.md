


# @RequestMapping

开发者需要在控制器内部为每一个请求动作开发相应的处理方法。org.springframework.web.bind.annotation.RequestMapping 注解类型指示Spring用哪一个类或
方法处理请求动作，该注解可用于类和方法。@RequestMapping可以用来注释一个控制器类，在这种情况下，所有方法都将映射为相对于类级别的请求，表示该控制器处
理的所有请求都被映射到value属性所指示的路径下。示例代码如下：

```java

@Controller
@RequestMapping(value="/user")
public class UserController{
  @RequestMapping(value="/register")
  public String register(){
    return "register";
  }

  @RequestMapping(value="/login")
  public String login(){
    return "login";
  }
}

```
