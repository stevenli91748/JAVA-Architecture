


# @ContextConfiguration 

Spring Boot起步依赖基本都以spring-boot-starter打头，随后是直接代表其功能的名字，比如web、test， 下文出现起步依赖的名字时，可能就直接用其前缀后的单词来表示

Spring Boot的Web起步依赖所引用的实际Jackson版本由你使用的Spring Boot版本决定

Spring整合JUnit4测试时，使用注解引入多个配置文件

单个文件 

@ContextConfiguration(Locations="classpath：applicationContext.xml")  

@ContextConfiguration(classes = SimpleConfiguration.class)

多个文件时，可用{}

@ContextConfiguration(locations = { "classpath:spring1.xml", "classpath:spring2.xml" }) 
