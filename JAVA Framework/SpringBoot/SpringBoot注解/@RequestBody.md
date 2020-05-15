


# @RequestBody

* [@RequestParam @RequestBody @PathVariable 等参数绑定注解详解](https://blog.csdn.net/walkerJong/article/details/7946109?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-7.nonecase&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-7.nonecase)

放在参数前，表示参数从request body中获取，而不是从地址栏获取，所以这肯定是接收一个POST请求的非a=b格式的数据，即 Content-Type不为
application/x-www-form-urlencoded类型的内容。
