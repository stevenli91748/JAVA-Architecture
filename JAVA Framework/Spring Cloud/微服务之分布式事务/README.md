**使用Spring Cloud构建微服务应用绕不过的一个难点就是跨服务的业务操作的数据一致性问题**
**使用XA事务来实现分布式事务，其可用性就比较差，于是业界提出了TCC及SAGA等解决方案，其思想跟BASE定理类似，通过打破ACID特性中的一个或几个来提升分布式事务的可用性**


# 目录
* [微服务之分布式事务实战案例1](https://weread.qq.com/web/reader/71d32370716443e271df020k47d328e029447d1e9905d2b)
* [微服务之分布式事务实战案例2---基于REST的微服务中进行TCC事务控制](https://weread.qq.com/web/reader/71d32370716443e271df020k47d328e029447d1e9905d2b)
* [微服务之分布式事务实战案例3---基于使用SAGA模式进行分布式事务管理的方法](https://weread.qq.com/web/reader/71d32370716443e271df020k47d328e029447d1e9905d2b)
* [分布式跨服务的业务操作的数据一致性基础理论](https://weread.qq.com/web/reader/71d32370716443e271df020ka5e327b0292a5e001323dfc)
  *  ACID---在单体架构的时代，业务系统通常是使用关系型数据库来做存储的，如果一个业务操作需要对同一个数据库进行多次操作，其数据的一致性由数据库的事务来保证，这依赖的就是关系型数据库的ACID理论
  * X/Open DTP模型---如果一个业务操作涉及对多个数据源进行操作，那么使用原来单一数据库的事务（本地事务）来控制就会不能满足（全局事务）数据的一致性要求。为此X/Open组织定义了DTP（Distributed Transaction Processing）模型，来规范全局事务处理 
  * 两阶段提交协议---
  * XA接口与JTA
  * TCC解决方案
  * SAGA解决方案
* [分布式事务解决方案](https://weread.qq.com/web/reader/71d32370716443e271df020k8d5321302938d5e957f2c0d)
  * Java事务编程接口JTA
  * 分布式事务TCC模式
    * TCC与2PC
    * TCC与ACID
    * TCC开源框架
      * 蚂蚁金服的大部分业务系统采用了TCC的方式接入分布式事务 
  * 分布式事务SAGA模式
    * SAGA与2PC
    * SAGA与TCC
    * SAGA与ACID
    * SAGA开源框架
      * Axon framework：是一款采用Java编写的、面向可伸缩及高性能的CQRS框架，该框架内置了SagaRepository、SagaManager、SagaStore等组件，对SAGA模式提供了原生的支持
      * Eventuate.io:eventuate-tram-sagas是Chris Richardson开源的一款为使用JDBC/JPA的微服务准备的SAGA框架
      * Narayana LRA:Narayana是jboss提供的一款分布式事务管理器，其中实现SAGA模式的事务管理器为Narayana LRA, LRA为Long Running Action的缩写
      * ServiceComb Saga：是华为于2017年7月份开源的一款基于SAGA模式的分布式事务框架，目前在Apache孵化中。                   
# 参考
  * [Seata 分布式事务](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring%20Cloud/Spring%20Cloud%20Alibaba%E5%A5%97%E4%BB%B6/Seata.md)
  * [亿级流量架构之分布式事务思路及方法](https://www.cnblogs.com/Courage129/p/14433462.html)
  * [亿级流量架构之分布式事务解决方案对比](https://www.cnblogs.com/Courage129/p/14443653.html) 
  * [微服务分布式事务之LCN、TCC特点、事务补偿机制缘由以及设计重点](https://www.cnblogs.com/Courage129/p/14528981.html)
