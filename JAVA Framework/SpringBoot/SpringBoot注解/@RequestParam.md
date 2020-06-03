

# @RequestParam

* [@RequestParam vs @QueryParam vs @PathVariable vs @PathParam](https://medium.com/1developer/spring-requestparam-vs-queryparam-vs-pathvariable-vs-pathparam-7c5655e541ad)
* [@RequestParam @RequestBody @PathVariable 等参数绑定注解详解](https://blog.csdn.net/walkerJong/article/details/7946109?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-7.nonecase&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-7.nonecase)

用在方法的参数前面。@RequestParam  String a =request.getParameter(“a”)

```java

   @RequestMapping(value="/test")
   public String handle1 (@RequestParam("userName" String userName)){
   
   }
```
**在上面的处理方法，如果HTTP请求不包含“userName” 参数时，将产生异常!!!**
**因此，如果不能保证存在“userName”的参数，必须使用 @RequestParam(value="userName",required=false)**
