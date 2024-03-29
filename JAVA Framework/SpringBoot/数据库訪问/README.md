
# 目录

[Spring Boot JPA Multiple Data Sources Example](https://www.javaguides.net/2018/09/spring-boot-jpa-multiple-data-sources-example.html)|
---|

[动态数据源例子---基于springboot的快速集成多数据源的启动器 已付费](https://www.kancloud.cn/tracy5546/dynamic-datasource/2344619)|[Spring Boot JdbcTemplate配置Druid多数据源](https://mrbird.cc/Spring-Boot-JdbcTemplate%20Druid.html)|
---|---|

[Spring】如何实现多数据源读写分离？这是我看过最详细的一篇！！](https://zhuanlan.zhihu.com/p/309314866)|
---|

[Spring Boot数据库源（连接数据库）---不需要编写任何额外的代码, 只需添加依赖项并执行配置详细信息就足以创建DataSource并连接数据库](https://www.yiibai.com/spring-boot/spring_boot_database_handling.html)|
---|

[别再用 offset 和 limit 分页了，性能太差](https://mp.weixin.qq.com/s/8dPkyIdnPQUchse3h8irig)|
----|



# Spring Boot 程序链接数据库的方式

[Spring Boot——三种方式连接数据库](https://www.jianshu.com/p/414ef5b49a69)|[Spring Boot Flyway数据库---flyway是一个能对数据库变更做版本控制的工具](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/SpringBoot%20%E5%9F%BA%E7%A1%80%E7%9F%A5%E8%AF%86/Spring%20Boot%20Flyway%E6%95%B0%E6%8D%AE%E5%BA%93.md)|
---|---|

* [使用MongoDB支持海量数据](https://weread.qq.com/web/reader/ca932ea071d7c798ca9a714kd1f32d7024fd1fe173d0651)
 
*  [1 JDBC连接](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/%E6%95%B0%E6%8D%AE%E5%BA%93%E8%A8%AA%E9%97%AE/JDBC%E8%BF%9E%E6%8E%A5/README.md)
*  [2 数据库连接池---Druid or HikariCP ](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/%E6%95%B0%E6%8D%AE%E5%BA%93%E8%A8%AA%E9%97%AE/%E6%95%B0%E6%8D%AE%E5%BA%93%E8%BF%9E%E6%8E%A5%E6%B1%A0/README.md)
*  [3 MyBatis](https://github.com/stevenli91748/Database/blob/master/Mybatis/%E5%9C%A8Spring%20Boot%E4%B8%AD%E7%BB%93%E5%90%88Mybatis/README.md)
*  [4 Spring Data JPA](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/SpringBoot/%E6%95%B0%E6%8D%AE%E5%BA%93%E8%A8%AA%E9%97%AE/Spring%20Data%20JPA/README.md)
*  5 数据库中间件
   * [SpringBoot 整合 Sharding-Sphere + Druid 读写分离](https://codeleading.com/article/79463528550/)
   * [SpringBoot Mysql主从读写分离（主从，Mybatis，ShardingSphere ）](https://codeleading.com/article/45745225007/)
   * [SpringBoot+MyBatis+MySQL读写分离](https://codeleading.com/article/5908793681/)
   * [shardingsphere SpringBoot分库分表读写分离](https://codeleading.com/article/88695974521/)


## 1 在Spring Boot中使用关系数据库訪问
   * MySQL
     * MySQL依赖配置
     * 实体建模
     * 实体持久化
     * MySQL测试 
   * Oracle

## 2 在Spring Boot中使用非关系数据库訪问
   * Redis
     * Redis依赖配置
     * 创建Redis服务类
     * Redis测试
   * [MongoDB](https://weread.qq.com/web/reader/ca932ea071d7c798ca9a714kd1f32d7024fd1fe173d0651)
     * MongoDB依赖配置
     * 文档建模
     * 文档持久化
     * MongoDB测试
   * Neo4j
     * Neo4j依赖配置
     * 节点和关系实体建模
     * 节点实体持久化
     * Neo4j测试
     
## [3 Spring Boot 数据库版本管理入门](https://www.iocoder.cn/Spring-Boot/database-version-control/?vip)
   * [使用Flyway来管理数据库版本](http://blog.didispace.com/spring-boot-flyway-db-version/)

# 有用的参考
* [SpringBoot 整合 Redis，一篇解决“缓存”的所有问题](https://www.jianshu.com/p/e4ad2b6bf85f?utm_campaign=hugo&utm_medium=reader_share&utm_content=note&utm_source=weixin-friends)
* [如何通过Spring Boot配置动态数据源访问多个数据库](https://blog.csdn.net/ylforever/article/details/79600631?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-4.not_use_machine_learn_pai&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-4.not_use_machine_learn_pai)
* [Elasticsearch搜索引擎在Spring Boot中的实践](https://github.com/hansonwang99/Spring-Boot-In-Action/tree/master/springboot_es_demo)
* [自制一个 elasticsearch-spring-boot-starter](https://www.codesheep.cn/2019/02/28/elasticsearch-spring-boot-starter/)
* [使用NoSQL数据库（一）：Redis](http://blog.didispace.com/springbootredis/)
* [使用NoSQL数据库（二）：MongoDB](http://blog.didispace.com/springbootmongodb/)

* [使用LDAP来统一管理用户信息](http://blog.didispace.com/spring-boot-ldap-user/)
* [Spring Boot中增强对MongoDB的配置（连接池等）](http://blog.didispace.com/springbootmongodb-plus/)
* [关于SpringBoot中的多数据源集成](https://www.bilibili.com/read/cv740636)
* [一文教你细数 SpringBoot 中的连接池](https://zhuanlan.zhihu.com/p/105118740)
* [怎么在 Spring Boot 中使用 JDBC 连接 MySQL](https://juejin.im/post/5ed443eef265da76ed485423)
* [SpringBoot+MyBatis+MySQL读写分离](https://zhuanlan.zhihu.com/p/54812239)
* [芋道 Spring Boot 数据库连接池入门](http://www.iocoder.cn/Spring-Boot/datasource-pool/?self)
* [芋道 Spring Boot MyBatis 入门](http://www.iocoder.cn/Spring-Boot/MyBatis/?self)
* [芋道 Spring Boot 多数据源（读写分离）入门](http://www.iocoder.cn/Spring-Boot/dynamic-datasource/?self)|
* [芋道 Spring Boot 分库分表入门](http://www.iocoder.cn/Spring-Boot/sharding-datasource/?self)
* [Spring Boot + MyBatis 如何优雅的实现数据库读写分离](https://www.zhihu.com/question/381631883/answer/1100642927?utm_source=wechat_session&utm_medium=social&utm_oi=991812777480134656&utm_content=group3_Answer&utm_campaign=shareopn)
* [使用Spring实现访问主从数据库的读写和只读事务/事物的分离路由](https://www.jdon.com/54429)
