


<a href="https://ibb.co/X8Wctg7"><img src="https://i.ibb.co/6ByjFxH/ELK.png" alt="ELK" border="0"></a><br /><a target='_blank' href='https://imgbb.com/'>image uploader</a><br />


ELK这种架构因为需要在各个服务器上部署Logstash来从不同的数据源收集数据，所以⽐较消耗CPU和内存资源，容易造成服务
器性能下降，因此后来⼜在Elasticsearch、Logstash、Kibana之外引⼊了Beats作为数据收集器。相⽐于Logstash，Beats所
占系统的CPU和内存⼏乎可以忽略不计，可以安装在每台服务器上做轻量型代理，从成百上千或成千上万台机器向Logstash
或者直接向Elasticsearch发送数据。



# 视频

 * [ELK日志分析服务](https://www.bilibili.com/video/av62242348/?p=1)
 * [老男孩教育 ELK日志分析系统原理与实践（完结）](https://www.bilibili.com/video/BV1iJ411B7QK?from=search&seid=8490240227878217725)


# 有用的参考

* [日志分析利器elk与logback(log4j)实战](https://blog.csdn.net/puhaiyang/article/details/69664891)
