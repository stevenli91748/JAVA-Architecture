


# @RequestMapping

    RequestMapping是一个用来处理请求地址映射的注解，可用于类或方法上。用于类上，表示类中的所有响应请求的方法都是以该地址作为父
    路径,
    
    @RequestMapping 来映射URL 到控制器类，或者是到Controller 控制器的处理方法上。当@RequestMapping 标记在Controller 类上的时候，里面使
    用@RequestMapping 标记的方法的请求地址都是相对于类上的@RequestMapping 而言的；当Controller 类上没有标记@RequestMapping 注解时，方法
    上的@RequestMapping 都是绝对路径。这种绝对路径和相对路径所组合成的最终路径都是相对于根路径“/ ”而言的
    
    RequestMapping注解有六个属性，下面分成三类进行说明

    1、value，method；
    value：指定请求的实际地址，指定的地址可以是URITemplate 模式（后面将会说明）；
    method：指定请求的method类型，GET、POST、PUT、DELETE等；

    2、consumes，produces；
    Consumes：指定处理请求的提交内容类型（Content-Type），例如application/json,text/html;
    produces:指定返回的内容类型，仅当request请求头中的(Accept)类型中包含该指定类型才返回；

    3、params，headers；
    Params：指定request中必须包含某些参数值是，才让该方法处理。
    headers：指定request中必须包含某些指定的header值，才能让该方法处理请求

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
