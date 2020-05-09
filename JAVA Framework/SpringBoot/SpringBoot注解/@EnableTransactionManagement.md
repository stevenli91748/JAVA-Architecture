

# @EnableTransactionManagement

 Spring Boot 使用事务非常简单，首先使用注解 @EnableTransactionManagement （启注解事务管理，等同于xml配置方式的 <tx:annotation-driven />）开启事
 务支持后，然后在访问数据库的Service方法上添加注解 @Transactional 便可。

开启对注解式事物的支持
