

$ @PathVariable

* [@RequestParam vs @QueryParam vs @PathVariable vs @PathParam](https://medium.com/1developer/spring-requestparam-vs-queryparam-vs-pathvariable-vs-pathparam-7c5655e541ad)

路径变量。如

```java 

RequestMapping(“user/get/mac/{macAddress}”)
public String getByMacAddress(@PathVariable String macAddress){
//do something;
}

```

参数与大括号里的名字一样要相同。
