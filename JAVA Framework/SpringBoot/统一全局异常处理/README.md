**java中的异常的超类是java.lang.Throwable(后文省略为Throwable),它有两个比较重要的子类,java.lang.Exception(后文省略为Exception)和java.lang.Error(后文省略为Error)，其中Error由JVM虚拟机进行管理,如我们所熟知的OutOfMemoryError异常等，所以我们本文不关注Error异常，那么我们细说一下Exception异常。Exception异常有个比较重要的子类，叫做RuntimeException。我们将RuntimeException或其他继承自RuntimeException的子类称为非受检异常(unchecked Exception)，其他继承自Exception异常的子类称为受检异常(checked Exception)，RuntimeException异常和受检异常之间的区别就是:是否强制要求调用者必须处理此异常，如果强制要求调用者必须进行处理，那么就使用受检异常，否则就选择非受检异常(RuntimeException)。一般来讲，如果没有特殊的要求，我们建议使用RuntimeException异常**

* [我来教你如何更优雅的设计Java异常](https://zhuanlan.zhihu.com/p/149600765)
* [全局异常处理](https://www.jianshu.com/p/b5b8613769db)
* [Spring中的统一异常处理](https://www.javaweb.shop/article/38.html)
* [Spring Boot异常处理1](https://mrbird.cc/Spring-Boot-Exception.html)
* [Spring Boot异常处理2](https://www.yiibai.com/spring-boot/spring_boot_exception_handling.html)
* [如何优雅的在SpringBoot项目中处理异常](https://www.jianshu.com/p/e53a8675b612)
* [SpringBoot统一接口返回和全局异常处理](https://www.jianshu.com/p/1dfa354bd349)
* [使用全局异常来处理校验逻辑的方式](http://www.macrozheng.com/#/technology/springboot_validator)
* [统一全局异常处理---看云](https://www.kancloud.cn/hanxt/springboot2/1177633)

# 
