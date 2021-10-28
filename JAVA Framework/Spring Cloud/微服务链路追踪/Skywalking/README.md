# 目录
* [ Spring Cloud与Skywalking入门实战例子](https://weread.qq.com/web/reader/71d32370716443e271df020ked332ca0262ed3d2c2191f2)
* [Skywalking概述---用于追踪、监控和诊断分布式系统](https://weread.qq.com/web/reader/71d32370716443e271df020ke2e329c0261e2ef524fbf75)
  * SkyWalking提供主要功能
    * 分布式追踪和上下文传输
    * 应用、实例、服务性能指标分析
    * 根源分析
    * 应用拓扑分析
    * 应用和服务依赖分析
    * 慢服务检测
    * 性能优化。  
  * SkyWalking主要特性
    * 多语言探针或者类库：
    * Java自动探针，追踪和监控程序时，不需要修改源码
    * 社区提供的语言探针：.NET Core、Node.js
    * 多种后端存储：ElasticSearch、H2
      * 支持OpenTrancing:Java自动探针和OpenTracing API协同工作
    * 轻量级、完善的后端聚合和分析功能
    * 现代化Web UI
    * 日志集成
    * 应用、实例和服务的告警
    * 支持接受其他跟踪器数据格式
      * Zipkin JSON, Thrift, Protobuf v1和v2格式，由OpenZipkin库提供支持
      * Jaeger采用Zipkin Thrift或JSON v1/v2格式。
  * [SkyWalking整体架构 ](https://weread.qq.com/web/reader/71d32370716443e271df020ke2e329c0261e2ef524fbf75)
    * collector
    * agent
    * web
    * storage 
