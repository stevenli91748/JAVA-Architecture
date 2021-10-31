

# 目录
* [Spring Cloud与领域驱动实践](https://weread.qq.com/web/reader/71d32370716443e271df020k7ef321f02967ef605fc8aef)
  * 领域驱动模型的分层结构
    * 用户界面/展现层---负责向用户展现信息及解释用户命令。更细的方面来讲就是请求应用层以获取用户需要展现的数据；发送命令给应用层要求其执行某个用户命令
    * 应用层（Application Layer）---该层的职责是为展现层提供各种应用功能（包括查询或命令），对内调用领域层（领域对象或领域服务）完成各种业务逻辑，应用层不包含业务逻辑。应用层采用命令查询职责分离（CommandQuery Responsibility Separation, CQRS）的设计思想，将查询和命令的实现分离，这样可以对这两部分进行单独设计。对于查询Request，会委托给一个专门负责查询的服务去完成；对于命令的Request，会委托给一个专门负责处理命令的领域服务去完成
    * 领域层---负责表达业务概念、业务状态信息及业务规则，领域模型处于这一层，是业务软件的核心。领域层会包含领域服务、实体、值对象、聚合（根）、工厂，仓储
    * 基础设施层---本层为其他层提供通用的技术能力，提供层间的通信，为领域层实现持久化机制。总之，基础设施层可以通过架构和框架来支持其他层的技术需求    

  * 领域驱动框架现状
    * [Axon Framework (http://github.com/AxonFramework/AxonFramework)](https://weread.qq.com/web/reader/71d32370716443e271df020kb3e32dc0299b3e3e393ce03) ---在Java平台上，国外比较受欢迎的领域驱动框架, 已经支持Spring Cloud,但是它不是目前最好的领域驱动框架
    * [Jdonframework（https://github.com/banq/jdonframework）](https://weread.qq.com/web/reader/71d32370716443e271df020kb3e32dc0299b3e3e393ce03)---这是基于DDD+CQRS+EventSourcing的开发，也是基于Java平台的
    * [Halo框架(https://github.com/softwareking/halo)](https://weread.qq.com/web/reader/71d32370716443e271df020kb3e32dc0299b3e3e393ce03)---Halo框架会更有优势,Halo框架是基于领域驱动+CQRS+扩展点+流程编排的应用框架，致力于采用领域驱动的设计思想，规范控制程序员的随心所欲，从而解决软件的复杂性问题,Halo框架在实际应用中也划分为三个大的层次，分别是App层、Domain层和Infrastructure层
      * App层
      * Domain层
      * Infrastructure层
