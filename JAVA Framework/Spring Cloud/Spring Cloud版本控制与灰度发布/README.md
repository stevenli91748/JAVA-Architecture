


# 目录
* [Spring Cloud Zuul灰度发布](https://weread.qq.com/web/reader/71d32370716443e271df020k283328802332838023a7529)---其中应用到的原理无非就是注册中心的metadata搭配特定的负载均衡机制，然后整合成一个Filter注册到Zuul的上下文环境，这样请求就可以带上版本标签经过路由访问到相应的服务版本
* [ Spring Cloud版本控制与灰度发布](https://weread.qq.com/web/reader/71d32370716443e271df020k1af32e502831afa34a7fe44)
* [常见发布方式](https://weread.qq.com/web/reader/71d32370716443e271df020k9fc324302859fc3d71522e0)
  * 蓝绿发布
  * 滚动发布
  * 灰度发布
    * A/B测试（A/B Testing）
    * 金丝雀部署（Canary Deployment） 
  * 对比 
