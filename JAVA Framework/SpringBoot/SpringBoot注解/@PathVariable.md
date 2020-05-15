

$ @PathVariable

* [@RequestParam vs @QueryParam vs @PathVariable vs @PathParam](https://medium.com/1developer/spring-requestparam-vs-queryparam-vs-pathvariable-vs-pathparam-7c5655e541ad)
* [@RequestParam @RequestBody @PathVariable 等参数绑定注解详解](https://blog.csdn.net/walkerJong/article/details/7946109?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-7.nonecase&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-7.nonecase)
* [@PathVariable注解使用](https://blog.csdn.net/sswqzx/article/details/84194979?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.nonecase&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.nonecase)

路径变量。如

例子一：

```java 

RequestMapping(“user/get/mac/{macAddress}”)
public String getByMacAddress(@PathVariable String macAddress){
//do something;
}

```

参数与大括号里的名字一样要相同。



例子二：

```java

@Controller
@RequestMapping("/owners/{ownerId}")
public class RelativePathUriTemplateController {
 
  @RequestMapping("/pets/{petId}")
  public void findPet(@PathVariable String ownerId, @PathVariable String petId, Model model) {    
    // implementation omitted
  }
}

```

上面代码把URI template 中变量 ownerId的值和petId的值，绑定到方法的参数上。若方法参数名称和需要绑定的uri template中变量名称不一致，需要
在@PathVariable("name")指定uri template中的名称。


例子三：

// 动态传值

```java

@RequestMapping(value="/Test/{id}")
public void Test(@PathVariable Integer id){
            .............
}


```

//用法

//在页面表单的action中，写controller中对应的方法名

```java
 
TestController.java
@RequestMapping(value="/{methodName}")
public String TZ(@PathVariable String methodName){
              return methodName;
}

```

