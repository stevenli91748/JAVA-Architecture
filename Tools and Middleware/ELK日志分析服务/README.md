


<a href="https://ibb.co/X8Wctg7"><img src="https://i.ibb.co/6ByjFxH/ELK.png" alt="ELK" border="0"></a><br /><a target='_blank' href='https://imgbb.com/'>image uploader</a><br />


ELK这种架构因为需要在各个服务器上部署Logstash来从不同的数据源收集数据，所以⽐较消耗CPU和内存资源，容易造成服务
器性能下降，因此后来⼜在Elasticsearch、Logstash、Kibana之外引⼊了Beats作为数据收集器。相⽐于Logstash，Beats所
占系统的CPU和内存⼏乎可以忽略不计，可以安装在每台服务器上做轻量型代理，从成百上千或成千上万台机器向Logstash
或者直接向Elasticsearch发送数据。


[使用docker五步搭建ELK日志收集分析系统](http://www.zimug.com/container/docker/%e4%bd%bf%e7%94%a8docker%e4%ba%94%e6%ad%a5%e6%90%ad%e5%bb%baelk%e6%97%a5%e5%bf%97%e6%94%b6%e9%9b%86%e5%88%86%e6%9e%90%e7%b3%bb%e7%bb%9f/.html)|
---|

---

# 目录

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

* [日志分析利器elk与logback(log4j)实战](https://blog.csdn.net/puhaiyang/article/details/69664891)
* [Spring Boot （日志篇）：Log4j整合ELK，搭建实时日志平台](https://blog.csdn.net/Soinice/article/details/84029776?utm_medium=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param)
* [springcloud微服务日志ELK集中处理](https://blog.csdn.net/d597180714/article/details/82382703?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-7.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-7.channel_param)
* [日志系统架构介绍（非原创）](https://www.cnblogs.com/WUXIAOCHANG/p/11024092.html)
* [Java常用日志框架介绍](https://www.cnblogs.com/chenhongliang/p/5312517.html)
* [日志组件slf4j介绍及配置详解](https://blog.csdn.net/foreverling/article/details/51385128)
* [logback 配置详解](https://segmentfault.com/a/1190000008315137)
