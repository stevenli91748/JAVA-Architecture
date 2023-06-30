# 在线书籍
* [ELK Stack 权威指南](https://weread.qq.com/web/reader/af7323005b46bbaf7edb262)
* [ELK Stack 权威指南(第2版)](https://weread.qq.com/web/reader/53432e00811e27ed7g017794)
* [替代ELK：ClickHouse+Kafka+FlieBeat](https://mp.weixin.qq.com/s/2nMYKby1YOWilPRBHhKrDA)
---

* 日志系统
  * [1. 常规日志系统 ](#1-常规日志系统)
  * [2. 实时计算的日志系统](#2-实时计算的日志系统)



# 常规日志系统
<a href="https://ibb.co/X8Wctg7"><img src="https://i.ibb.co/6ByjFxH/ELK.png" alt="ELK" border="0"></a><br /><a target='_blank' href='https://imgbb.com/'>image uploader</a><br />

<a href="https://ibb.co/tKLRdXr"><img src="https://i.ibb.co/W5VCTtJ/image.jpg" alt="image" border="0"></a>

# 实时计算的日志系统

<a href="https://ibb.co/Hp4ZfsP"><img src="https://i.ibb.co/gF9YLpW/1.webp" alt="1" border="0"></a>


# 1 常规日志系统

ELK这种架构因为需要在各个服务器上部署Logstash来从不同的数据源收集数据，所以⽐较消耗CPU和内存资源，容易造成服务
器性能下降，因此后来⼜在Elasticsearch、Logstash、Kibana之外引⼊了Beats作为数据收集器。相⽐于Logstash，Beats所
占系统的CPU和内存⼏乎可以忽略不计，可以安装在每台服务器上做轻量型代理，从成百上千或成千上万台机器向Logstash
或者直接向Elasticsearch发送数据。


[使用docker五步搭建ELK日志收集分析系统](http://www.zimug.com/container/docker/%e4%bd%bf%e7%94%a8docker%e4%ba%94%e6%ad%a5%e6%90%ad%e5%bb%baelk%e6%97%a5%e5%bf%97%e6%94%b6%e9%9b%86%e5%88%86%e6%9e%90%e7%b3%bb%e7%bb%9f/.html)|[ELK+filebeat+kafka+zookeeper构建海量日志分析平台](https://www.huaweicloud.com/articles/25e014168e463c5b471deae626a37711.html)|
---|---|

[ELK+Filebeat+Kafka+ZooKeeper 构建海量日志分析平台](https://www.cnblogs.com/saneri/p/8822116.html)|[ELK Filebeat Kafka ZooKeeper 构建海量日志分析平台(转)](http://www.360doc.com/content/18/0328/18/33260087_741038117.shtml)|
---|---|

[ELK日志分析系统入门](https://gitlib.com/page/elk.html)|[字母哥 / ELK中文手册](https://www.kancloud.cn/hanxt/elk/153883)|
---|---|

[全面梳理 Spring Boot 日志体系](https://zhuanlan.zhihu.com/p/553171127)|
---|

[基于tp6 ELK实时日志分析系统](https://www.kancloud.cn/zhangqi/tp6_elk)|[springboot2.2.X手册：抛弃ELK，百亿日志+调用链的Easylog很放心](https://www.toutiao.com/a6838166862795112971/)|
---|---|

[Filebeat---已经完全替代了 Logstash-Forwarder 成为新一代的日志采集器，同时鉴于它轻量、安全等特点，越来越多人开始使用它 ]()|
---|

# 2 实时计算的日志系统

Flume 是一个分布式、可靠、和高可用的海量日志采集、聚合和传输的日志收集系统，支持在日志系统中定制各类数据发送方，用于收集数据；同时，Flume 提供对数据进行简单处理，并写到各种数据接受方（可定制）的能力。

Kafka 是由 Apache 软件基金会开发的一个开源流处理平台，由 Scala 和 Java 编写。其本质上是一个“按照分布式事务日志架构的大规模发布/订阅消息队列”，它以可水平扩展和高吞吐率而被广泛使用。

Kafka 追求的是高吞吐量、高负载，Flume 追求的是数据的多样性，二者结合起来简直完美

---

# 目录

# 日志系统分类
  * [比较常用的组合使用方式是Slf4j与Logback组合使用，Commons Logging与Log4j组合使用](http://c.biancheng.net/spring_boot/slf4j-logback.html)
  * ELK日志系统
    * [为什么要用ELK收集日志](https://juejin.cn/post/6844904008499331079) 


## EleasticSearch

## Kibana
   * [kibana安装](https://www.elastic.co/guide/en/kibana/current/docker.html)


## logstash




# 视频

 * [ELK日志分析服务](https://www.bilibili.com/video/av62242348/?p=1)
 * [老男孩教育 ELK日志分析系统原理与实践（完结）](https://www.bilibili.com/video/BV1iJ411B7QK?from=search&seid=8490240227878217725)



# 实验

* 安装配置ELK日志系统

# 有用的参考
* [做这么多年程序员了，才把ELK和springboot的日志解决方案弄明白](https://mp.weixin.qq.com/s/QTtYa6yDNZZpF7y_FSW9tw)
* [企业级日志系统 ELK 原理与实践详细介绍](https://mp.weixin.qq.com/s/reWVdOoOkxcpz0ls5OfrMA)
* [日志分析利器elk与logback(log4j)实战](https://blog.csdn.net/puhaiyang/article/details/69664891)
* [Spring Boot （日志篇）：Log4j整合ELK，搭建实时日志平台](https://blog.csdn.net/Soinice/article/details/84029776?utm_medium=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param)
* [springcloud微服务日志ELK集中处理](https://blog.csdn.net/d597180714/article/details/82382703?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-7.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-7.channel_param)
* [日志系统架构介绍（非原创）](https://www.cnblogs.com/WUXIAOCHANG/p/11024092.html)
* [Java常用日志框架介绍](https://www.cnblogs.com/chenhongliang/p/5312517.html)
* [日志组件slf4j介绍及配置详解](https://blog.csdn.net/foreverling/article/details/51385128)
* [logback 配置详解](https://segmentfault.com/a/1190000008315137)
* [log4j在maven项目中的使用](https://blog.csdn.net/qq_40182873/article/details/86706355)
* [log4j.properties配置文件详解](https://www.cnblogs.com/xiaobaizhiqian/p/7956690.html)
* [java中程序动态配置log4j日志](https://blog.csdn.net/zgjllf1011/article/details/79280914)

###

* [ELK 日志采集框架（一）：架构设计](https://www.jianshu.com/p/210bb6b4abb2)
* [ELK 日志采集框架（二）：日志模块开发](https://www.jianshu.com/p/b35d00480805)
* [ELK 日志采集框架（三）：Filebeat安装与配置](https://www.jianshu.com/p/ad709e1fdff9)
* [ELK 日志采集框架（四）：Kafka安装与配置](https://www.jianshu.com/p/e880c79be3d4)
* [ELK 日志采集框架（五）：Logstash安装与配置](https://www.jianshu.com/p/b33def08c753)
* [ELK 日志采集框架（六）：ElasticSearch安装与配置](https://www.jianshu.com/p/afad7328bd11)
* [ELK 日志采集框架（七）：Kibana安装与配置](https://www.jianshu.com/p/68904d96259d)
