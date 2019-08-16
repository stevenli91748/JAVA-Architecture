
<details>
<summary>可以不学spring其他，直接学spring boot吗？</summary>
  https://www.zhihu.com/question/303235503
  
</details>

<details>
<summary>spring boot集成mybatis时是用xml配置，还是用注解？</summary>
https://www.zhihu.com/question/294748079
  
</details>

<details>
<summary>spring boot与spring mvc的区别是什么？</summary>
https://www.zhihu.com/question/64671972
  
</details>


<details>
<summary>列举一些SpringBoot特性</summary>
  
1、创建独立的Spring项目

2、内置Tomcat和Jetty容器

3、提供一个starter POMs来简化Maven配置

4、提供了一系列大型项目中常见的非功能性特性，如安全、指标，健康检测、外部配置等

5、完全没有代码生成和xml配置文件

</details> 

<details>
<summary>有什么常用的Spring Boot启动器？</summary>
  
spring-boot-starter： 核心启动器，包括自动配置支持，日志记录和YAML

spring-boot-starter-aop： 使用Spring AOP和AspectJ进行面向方面编程的启动器

spring-boot-starter-data-jpa： 使用Spring Data JPA和Hibernate的启动器

spring-boot-starter-jdbc： 用于将JDBC与HikariCP连接池一起使用的启动器

spring-boot-starter-security： 使用Spring Security的启动器

spring-boot-starter-test： 用于测试Spring Boot应用程序的启动器

spring-boot-starter-web： 用于构建Web的入门者，包括使用Spring MVC的RESTful应用程序  

</details> 

<details>
<summary>application.properties中如何引用已定义的配置参数？</summary>

使用${},比如：
book.name = XXX
book.desc = ${book.name} 's desc

</details> 

<details>
<summary>spring boot Devtools有什么作用？</summary>

[Spring DevTools 介绍](https://blog.csdn.net/isea533/article/details/70495714)

</details> 

<details>
<summary>Spring Boot Actuator有什么作用？</summary>

监控和管理Spring Boot应用，比如Mappring、健康检查、审计、统计和HTTP追踪等。所有的这些特性可以通过JMX或者HTTP endpoints来获得。

</details> 

<details>
<summary>RequestMapping 和 GetMapping 的不同之处在哪里？</summary>

RequestMapping 具有类属性的，可以进行 GET,POST,PUT 或者其它的注释中具有的请求方法。

GetMapping 是 GET 请求方法中的一个特例。它只是 ResquestMapping 的一个延伸，目的是为了提高清晰度。

</details> 

<details>
<summary>@ConfigurationProperties 注解的作用</summary>

将配置属性注入到bean 
类似如下：

```java

@Component
@ConfigurationProperties(prefix = "person")
public class Person {

private String firstName;

}

application.properties属性：
person.firstName = XXX

```

</details> 

<details>
<summary>@value和@configurationproperties的区别</summary>

[springboot @value和@configurationproperties注解的区别](https://www.cnblogs.com/slowcity/p/9097969.html)

</details> 

<details>
<summary>Spring Boot 如何定义多套不同环境配置？</summary>

提供多套配置文件，如：

applcation.properties
application-dev.properties
application-test.properties
application-prod.properties

[Spring Boot - Profile不同环境配置](https://mp.weixin.qq.com/s/K0kdQwoo2t5FDsTUJttSAA)

</details> 

<details>
<summary>@responsebody有什么作用？</summary>
  
@responsebody后返回结果不会被解析为跳转路径，而是直接写入HTTP response body中。比如异步获取json数据，加上@responsebody后，会直接返回json数据。该注解一般会配合@RequestMapping一起使用。

</details> 

<details>
<summary>@Controller 和 @RestController有什么区别？</summary>
  
@RestController 是Spring4之后加入的注解，原来在@Controller中返回json需要@ResponseBody来配合，如果直接用@RestController替代@Controller就不需要再配置@ResponseBody，默认返回json格式。而@Controller是用来创建处理http请求的对象，一般结合@RequestMapping使用。

</details> 

<details>
<summary>@Component和@Bean有什么区别？</summary>
  
@Component被用在要被自动扫描和装配的类上。@Component类中使用方法或字段时不会使用CGLIB增强(及不使用代理类：调用任何方法，使用任何变量，拿到的是原始对象)Spring 注解@Component等效于@Service,@Controller,@Repository
@Bean主要被用在方法上，来显式声明要用生成的类;用@Configuration注解该类，用@Bean标注方法等价于XML中配置bean。

现在项目上，本工程中的类，一般都使用@Component来生成bean。在把通过web service取得的类，生成Bean时，使用@Bean和getter方法来生成bean

</details> 


<details>
<summary>有什么springboot的安全方面的实践？</summary>
  
[10 种保护 Spring Boot 应用的绝佳方法](https://mp.weixin.qq.com/s/HG4_StZyNCoWx02mUVCs1g)

</details> 

<details>
<summary>如何使用@Async？</summary>
  
现在启动类@SpringBootApplication后面加入@EnableAsync，定义@Component类中的异步任务方法，其中注解@Async，方法返回void或者Future<T>，调用方法即平常的@Autowired实例化即可。

</details> 

<details>
<summary>springboot如何开启定时任务？</summary>
  
定义启动类@EnableScheduling，然后在任务类使用cron表达式来定义任务时间，比如@Scheduled(cron="/6 * * * * ?")代表每6秒一次，再如：“0 0 12 * * ?” 每天中午12点触发。

</details> 























---

104.什么是 spring boot？

105.为什么要用 spring boot？

Spring Boot 的特点是什麽？

106.spring boot 核心配置文件是什么？

107.spring boot 配置文件有哪几种类型？它们有什么区别？

108.spring boot 有哪些方式可以实现热部署？

109.jpa 和 hibernate 有什么区别？

110.什么是 spring cloud？

111.spring cloud 断路器的作用是什么？

112.spring cloud 的核心组件有哪些？

