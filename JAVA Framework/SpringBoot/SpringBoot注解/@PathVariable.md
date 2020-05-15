

$ @PathVariable

* [@RequestParam vs @QueryParam vs @PathVariable vs @PathParam](https://medium.com/1developer/spring-requestparam-vs-queryparam-vs-pathvariable-vs-pathparam-7c5655e541ad)
* [@RequestParam @RequestBody @PathVariable 等参数绑定注解详解](https://blog.csdn.net/walkerJong/article/details/7946109?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-7.nonecase&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-7.nonecase)
* [@PathVariable注解使用](https://blog.csdn.net/sswqzx/article/details/84194979?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.nonecase&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.nonecase)

路径变量。如

```java 

RequestMapping(“user/get/mac/{macAddress}”)
public String getByMacAddress(@PathVariable String macAddress){
//do something;
}

```

参数与大括号里的名字一样要相同。
