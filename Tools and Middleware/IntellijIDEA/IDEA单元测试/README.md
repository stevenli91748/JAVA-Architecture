


# spring-boot-starter-test 启动器能引入这些 Spring Boot 测试模块：

        JUnit：Java 应用程序单元测试标准类库。
        Spring Test & Spring Boot Test：Spring Boot 应用程序功能集成化测试支持。
        Mockito：一个Java Mock测试框架。
        AssertJ：一个轻量级的断言类库。
        Hamcrest：一个对象匹配器类库。
        JSONassert：一个用于JSON的断言库。
        JsonPath：一个JSON操作类库。

# 目录

  * 1 基本单元测试框架
    * [Junit5](https://junit.org/junit5/)
    * [TestNG](https://testng.org/doc/)
      * [TestNG例子程序](http://websystique.com/java/testng-tutorial/)
  * 2 模拟框架- Mockito和EasyMock    
    * [Mockito---](https://site.mockito.org/)
    * [EasyMock---它是Mockito的替代品](https://easymock.org/)
    * [PowerMock---模拟复杂场景,当您想要模拟静态方法、构造函数和私有方法时，Powermock非常有用](https://powermock.github.io/)
  * 3 使用Spring Unit ---典型应用程序有多个层，您希望为不同的层（Web，业务和数据）编写单元测试
    * 3.1 Web层---使用Spring MockMVC和 Spring Unit框架
      * [Spring MockMVC---Spring MockMVC框架可以轻松执行REST API请求](http://zetcode.com/spring/mockmvc/)
      * [Spring Unit](https://docs.spring.io/spring-batch/docs/current/reference/html/testing.html)
    * 3.2 业务层---基于Mockito框架的测试，最好不要启动Spring Context
    * 3.3 数据层---使用DataJpaTest框架
      * [DataJpaTest---在基于Spring的企业应用程序中对数据层进行单元测试时使用。DataJpaTest在默认情况下，使用内存数据库进行单元测试](https://bezkoder.com/spring-boot-unit-test-jpa-repo-datajpatest/)
    * 3.4 断言---
      * JSONAssert
      * JSONPath
  * [4 Testing in Spring Boot ---- test framework for spring boot   ](https://www.baeldung.com/spring-boot-testing)
    
  
# 视频

* [JUnit—Java单元测试必备工具](http://www.imooc.com/learn/356)
* [Junit 视频]((https://www.bilibili.com/video/av46370535?from=search&seid=5196717484594619472))


# 有用的参考
* [最佳Java单元测试框架](https://juejin.cn/post/6844903941038161928)
* [单元测试框架的选择](https://zhuanlan.zhihu.com/p/141622441)
* [从零开始写单元测试](https://yasinshaw.com/articles/102)
* [重要 测试金字塔实战---一个微服务应用测试例子](https://insights.thoughtworks.cn/practical-test-pyramid/)
* [intellij idea 自动生成test单元测试](https://www.cnblogs.com/exmyth/p/12420651.html)
* [单元测试(Junit+Jmockit)介绍及使用方法](https://www.jianshu.com/p/37de454c5f34)
* [JUnit单元测试入门必看篇](https://www.jb51.net/article/128856.htm)
* [java编程之单元测试(Junit)实例分析(附实例源码)](https://www.jb51.net/article/74976.htm)
* [详解Java单元测试Junit框架实例](https://www.jb51.net/article/111356.htm)
* [【Spring】Junit加载Spring容器作单元测试](https://www.cnblogs.com/swugogo/p/5908435.html)
* [Spring boot, mockito and junit – unit test service layer](https://howtodoinjava.com/spring-boot2/testing/spring-boot-mockito-junit-example/)
* [像这样做单元测试](https://my.oschina.net/huangyong/blog/162325)
* [最佳Java单元测试框架](https://juejin.im/post/6844903941038161928)

* [sample: creating a Spring application and then testing it with JUnit  ---spring office](https://spring.io/guides/gs/testing-web/)



