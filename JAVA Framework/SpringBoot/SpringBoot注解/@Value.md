


# @Value

[@Value注解读取配置文件方式](https://mp.weixin.qq.com/s?__biz=MzI3ODcxMzQzMw==&mid=2247484575&idx=1&sn=56c88cd7283374345d891e85a800539b&scene=21#wechat_redirect)

注入Spring boot application.properties配置的属性的值



@Value : 属性注入

@Value ("我爱你")  --> 普通字符串注入

@Value ("#{systemProperties['os.name']}") -->注入操作系统属性

@Value ("#{ T (java.lang.Math).random()  * 100.0 }") --> 注入表达式结果

@Value ("#{demoService.another}") --> 注入其他Bean属性

@Value ( "classpath:com/wisely/highlight_spring4/ch2/el/test.txt" ) --> 注入文件资源

@Value ("http://www.baidu.com")-->注入网址资源

@Value ("${book.name}" ) --> 注入配置文件  注意: 使用的是$ 而不是 #

---

