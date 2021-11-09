# 目录
* [全链路监控概述](#全链路监控概述)
  * [链路监控的原理来源](https://weread.qq.com/web/reader/71d32370716443e271df020kf4b32ef025ef4b9ec30acd6)  
* 全链路监控方案
  * Spring Cloud的分布式调用链解决方案
    * [Spring Cloud Sleuth分布式调用链解决方案](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring%20Cloud/Spring%20Cloud%E5%8E%9F%E7%94%9F%E5%8F%8A%E5%85%B6%E4%BB%96%E6%95%B4%E5%90%88%E7%BB%84%E4%BB%B6/Sleuth.md)
    * [Skywalking的分布式调用链解决方案 ](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring%20Cloud/%E5%BE%AE%E6%9C%8D%E5%8A%A1%E9%93%BE%E8%B7%AF%E8%BF%BD%E8%B8%AA/Skywalking/README.md)
    * [Pinpoint的分布式调用链解决方案 ](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring%20Cloud/%E5%BE%AE%E6%9C%8D%E5%8A%A1%E9%93%BE%E8%B7%AF%E8%BF%BD%E8%B8%AA/Pinpoint/README.md)

---

# 全链路监控概述

一个看似简单的前端请求可能最终需要调用很多次后端服务才能完成，那么当整个请求出现问题时，我们很难得知到底是哪个服务出了问题导致的，这时就需要解决一个问题，即如何快速定位服务故障点，分布式系统调用链追踪技术就此诞生了
