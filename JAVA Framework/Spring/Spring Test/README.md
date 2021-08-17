
 # 使用单元测试
1. 引认Spring单元测试模块
    1.1 maven: Junit   Spring-test
    1.2 RunWith(SpringJUnit4ClassRunner.class)
    
2.  加载配置类
    ContextConfiguration(classses=APPConfig.class)
    APPConfig.class 配置类是在项目中自定义的

3.  然后在有参构造函数上注解，自动装载这个参数到当前类所创造的Bean对象中
    @Autowired
   
4.  在要测试的方法上注解
    @Test
    



test部分只有一个模块，我将spring-context-support也放在这里

[Spring test](https://www.bilibili.com/video/av64742878?p=13 "spring测试，提供junit与mock测试功能")|[Spring-context-support]("spring额外支持包，比如邮件服务、视图解析等")|[junit](https://www.bilibili.com/video/av64742878?p=13)|
---|---|---|


# 有用的参考

* [【Spring】Junit加载Spring容器作单元测试](https://www.cnblogs.com/swugogo/p/5908435.html)
