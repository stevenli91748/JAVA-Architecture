
需要了解spring 中对事务规定的隔离类型和事务传播类型。要知道事务的隔离级别是由具体的数据库来实现的，在数据库部分我会详细介绍。

事务的传播类型，可以重点了解最常用的REQUIRED和SUPPORTS类型。


* [可能是最漂亮的Spring事务管理详解](https://juejin.im/post/5b00c52ef265da0b95276091)
* [7000+字的Spring事务总结来啦！我饱了！](https://juejin.im/post/5ebe682c5188256d8a22940f)

# 隔离类型事务

ISOLATION_DEFAULT|ISOLATION_READ_UNCOMMITTED|ISOLATION_READ_COMMITTED
---|---|---|

ISOLATION_REPEATABLE_READ|ISOLATION_SERIALIZABLE|
---|---|


# 事务传播类型

PROPAGATION_REQUIRED|PROPAGATION_SUPPORTS|PROPAGATION_MANDATORY|
---|---|---|

PROPAGATION_MANDATORY|PROPAGATION_REQUIRES_NEW|PROPAGATION_NOT_SUPPORTED
---|---|---|

PROPAGATION_NEVER|PROPAGATION_NESTED|
---|---|
