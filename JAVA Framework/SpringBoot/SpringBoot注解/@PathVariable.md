

$ @PathVariable

路径变量。如

```java 

RequestMapping(“user/get/mac/{macAddress}”)
public String getByMacAddress(@PathVariable String macAddress){
//do something;
}

```

参数与大括号里的名字一样要相同。
