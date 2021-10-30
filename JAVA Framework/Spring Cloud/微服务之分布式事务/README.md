**使用Spring Cloud构建微服务应用绕不过的一个难点就是跨服务的业务操作的数据一致性问题**



# 目录
* 
* [分布式跨服务的业务操作的数据一致性基础理论](https://weread.qq.com/web/reader/71d32370716443e271df020ka5e327b0292a5e001323dfc)
  *  ACID---在单体架构的时代，业务系统通常是使用关系型数据库来做存储的，如果一个业务操作需要对同一个数据库进行多次操作，其数据的一致性由数据库的事务来保证，这依赖的就是关系型数据库的ACID理论
  * X/Open DTP模型---如果一个业务操作涉及对多个数据源进行操作，那么使用原来单一数据库的事务（本地事务）来控制就会不能满足（全局事务）数据的一致性要求。为此X/Open组织定义了DTP（Distributed Transaction Processing）模型，来规范全局事务处理 
* 分布式事务解决方案

# 参考
  * [Seata 分布式事务](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring%20Cloud/Spring%20Cloud%20Alibaba%E5%A5%97%E4%BB%B6/Seata.md)
  * [亿级流量架构之分布式事务思路及方法](https://www.cnblogs.com/Courage129/p/14433462.html)
  * [亿级流量架构之分布式事务解决方案对比](https://www.cnblogs.com/Courage129/p/14443653.html) 
  * [微服务分布式事务之LCN、TCC特点、事务补偿机制缘由以及设计重点](https://www.cnblogs.com/Courage129/p/14528981.html)
