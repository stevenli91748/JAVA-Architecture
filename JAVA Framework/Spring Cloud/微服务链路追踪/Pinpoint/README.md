
# 目录
* [Spring Cloud与Pinpoint入门实战例子](https://weread.qq.com/web/reader/71d32370716443e271df020kf8932dd0264f899139df0ae)
* [Pinpoint概述](https://weread.qq.com/web/reader/71d32370716443e271df020kac6325b0263ac627ab1c3dd)---一个分析大规模分布式系统的平台，并提供处理大量跟踪数据的解决方案
  * Pinpoint特点
    * 分布式事务跟踪，跟踪跨分布式应用的消息
    * 自动检测应用拓扑，帮助你搞清楚应用的架构
    * 水平扩展以便支持大规模服务器集群
    * 提供代码级别的可见性以便轻松定位失败点和瓶颈
    * 使用字节码增强技术，添加新功能而无须修改代码
  * Pinpoint架构模块  
    * HBase（用于存储数据）
    * Pinpoint Collector（部署在Web容器上）
    * Pinpoint Web（部署在Web容器上）
    * Pinpoint Agent（附加到用于分析的Java应用程序）
  * Pinpoint的数据结构 
