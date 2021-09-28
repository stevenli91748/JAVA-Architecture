**commons-logging和slf4j都是日志的接口,供用户使用,而没有提供实现。log4j，logback等才是日志的真正实现，日志是接口+具体实现的方式来使用。目前应用比较广泛的是Log4j2和logback，而logback作为后起之秀，以替代log4j为目的，整体性能比log4j较佳，log4j的升级版log4j2也是有诸多亮点**


[SLF4J](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/%E6%97%A5%E5%BF%97%E7%AE%A1%E7%90%86/SLF4J.md)|[ Logback ](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/%E6%97%A5%E5%BF%97%E7%AE%A1%E7%90%86/Logback.md)|
---|---|


# 目录
* [Spring Boot日志](https://www.yiibai.com/spring-boot/spring_boot_logging.html)
* [Spring Boot统一日志框架](http://c.biancheng.net/spring_boot/slf4j-logback.html)
* [Spring Boot SLF4J日志实例](https://www.yiibai.com/spring-boot/slf4j-logging-example.html)
* [Spring Boot日志配置及输出](http://c.biancheng.net/spring_boot/log-config.html)
* 
# 有用的参考
* [集成全局AOP切面，进行访问时间与日志打印](https://www.jianshu.com/p/450c773efc0c)
* [Spring Boot AOP记录用户操作日志](https://mrbird.cc/Spring-Boot-AOP%20log.html)
* [Spring Boot logback日志配置](https://mrbird.cc/Spring-Boot-logback.html)
* [SpringBoot应用整合ELK实现日志收集](https://www.jianshu.com/p/6f1a0487acf8)
* [Java常用的日志框架对比](https://www.jianshu.com/p/bbbdcb30bba8)
* [2020 年了，Java 日志框架到底哪个性能好？——技术选型篇](https://segmentfault.com/a/1190000021589244)
* [日志分析利器elk与logback(log4j)实战](https://blog.csdn.net/puhaiyang/article/details/69664891)
* [Spring Boot日志框架实践](http://www.codesheep.cn/2018/03/29/Boot%E6%97%A5%E5%BF%97%E6%A1%86%E6%9E%B6%E5%AE%9E%E8%B7%B5/)
* [默认日志的配置](http://blog.didispace.com/springbootlog/)
* [使用log4j记录日志](http://blog.didispace.com/springbootlog4j/)
* [对log4j进行多环境不同日志级别的控制](http://blog.didispace.com/springbootlog4jmuilt/)
* [使用AOP统一处理Web请求日志](http://blog.didispace.com/springbootaoplog/)
* [使用log4j记录日志到MongoDB](http://blog.didispace.com/springbootlog4jmongodb/)
* [Spring Boot 1.5.x新特性：动态修改日志级别](http://blog.didispace.com/spring-boot-1-5-x-feature-1/)
