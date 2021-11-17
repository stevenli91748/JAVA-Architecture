
# 目录
* [APM原理与应用](#APM原理与应用)
  * [APM监控点](#APM监控点) 
* [一招搞定 Spring Boot 可视化监控！](https://mp.weixin.qq.com/s/J67gVzdoFZZZPIoXtPqsLg)
* [SpringBoot 2.x 开发案例之整合Srping Boot Admin](https://www.jianshu.com/p/a791e1c0b67e)
* 1 创建Actuator监控Spring Boot应用
  * [Spring Boot Actuator](https://www.yiibai.com/spring-boot/spring_boot_actuator.html)
  * [使用Actuator监控Spring Boot应用](https://mrbird.cc/Acutator-Spring-Boot.html)
  * [Spring Boot应用监控实战](https://mp.weixin.qq.com/s?__biz=MzU4ODI1MjA3NQ==&mid=2247483771&idx=1&sn=7c5f103a816c16e453e04141d7433bf9&chksm=fdded7bfcaa95ea9a5dbe81114d32c1908bf8da0b3366bfbfcbe2473445cdba73c5e2060d5f3#rd)
  * [Spring Boot Actuator监控端点小结](http://blog.didispace.com/spring-boot-actuator-1/)
  * [在传统Spring应用中使用spring-boot-actuator模块提供监控端点](http://blog.didispace.com/spring-boot-actuator-without-boot/)
  * [Spring Boot中使用Actuator的/info端点输出Git版本信息](http://blog.didispace.com/spring-boot-actuator-info-git/)
  * [Spring Boot应用监控实战](https://github.com/hansonwang99/Spring-Boot-In-Action/tree/master/springbt_admin_server)
* 2 管理Actuator Endpoint
  * [Spring Boot管理服务器--- 管理Actuator Endpoint](https://www.yiibai.com/spring-boot/spring_boot_admin_server.html)
  * [Spring Boot管理客户端--- 管理Actuator Endpoint](https://www.yiibai.com/spring-boot/spring_boot_admin_client.html)
  * [使用Spring Boot Admin监控服务](https://mrbird.cc/Spring-Boot-Admin.html)
  * [利用神器 BTrace 追踪线上 Spring Boot应用运行时信息](https://www.codesheep.cn/2019/01/17/springbt-btrace/)
  * [Spring Boot Admin 2.0开箱体验](https://github.com/hansonwang99/Spring-Boot-In-Action/tree/master/spring_boot_admin2.0_demo)
* 3 对网络的监控
  * Prometheus 


## [APM原理与应用](https://weread.qq.com/web/reader/d9e327a07188b377d9eb7dak35f3251024e35f4a8d46404)

APM的全称是Application Performance Management（应用性能管理）。APM致力于监控和管理应用软件性能和可用性，通过监测和诊断复杂应用程序的性能问题，来保证应用程序的良好运行

目前常见的开源APM产品有Google Dapper、Twitter Zipkin、大众点评CAT、Pinpoint及Skywalking等。常见的收费APM产品有Instana、Dynatrace、听云和透视宝等。

### [APM监控点](https://weread.qq.com/web/reader/d9e327a07188b377d9eb7dak35f3251024e35f4a8d46404)

● Web地址响应性能监控与统计
● 服务响应性能监控与统计
● RPC服务响应性能监控与统计
● API接口响应性能监控与统计
● 组件节点监控（MySQL、Redis、MQ）
● 系统CPU、内存、硬盘监测
● SQL响应性能监控与统计
● 系统异常监控与统计。
