


# @ContextConfiguration 

Spring整合JUnit4测试时，使用注解引入多个配置文件

单个文件 

@ContextConfiguration(Locations="classpath：applicationContext.xml")  

@ContextConfiguration(classes = SimpleConfiguration.class)

多个文件时，可用{}

@ContextConfiguration(locations = { "classpath:spring1.xml", "classpath:spring2.xml" }) 
