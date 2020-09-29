# 第一章
? Spring Boot 自动配置
? devtools实现热加载
? 什么是Spring Boot Starter

？ lombok

？ Spring MVC

？ 嵌入式web容器

？ spring data

# 第二章

**2.4 

？postman的使用方法
？ Mockito的使用方法
?  Mockito的测试执行速度非常快，但是有一个问题：它没有启动servlet容器和Spring 上下文，自然也就无法实现依赖注入（不支持@Resource和@AutoWired注解）。这就导致它在从控制层到持久层全流程测试中有很大的局限性。
? 启动servlet容器和Spring 上下文的测试方法
？ @AutoConfigureMockMvc
?  @SpringBootTest
?  @ExtendWith
?  @Resource
?  @RunWith
?  @Transactional
?  @MockBean
?  when(articleService.saveArticle(articleObj)).thenReturn("ok");语句.也就是告诉测试用例程序，当你调用articleService.saveArticle(articleObj)方法的时候，不要去真的调用这个方法，直接返回一个结果（“ok”）就好了。
？ .andExpect(MockMvcResultMatchers.jsonPath("$.data").value("ok")) 测试用例跑通了，期望结果andExpect：ok与实际结果thenReturn("ok")一致。表示程序真正的去执行了MockBean的模拟行为，而不是调用真实对象的方法
?  轻量级测试 使用@WebMvcTest替换@SpringBootTest


**2.5
？ swagger3 的使用方法

# 第三章

**3.1

？ bean自动装配原理
？ 
