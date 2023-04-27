**Netty之所以受青睐，是因为它提供了异步的、事件驱动的网络应用程序框架和工具。作为一个异步框架，Netty的所有IO操作都是异步非阻塞的，通过Future-Listener机制，用户可以方便地主动获取或者通过通知机制获得IO操作结果。与JDK原生NIO相比，Netty提供了十分简单易用的API，因而非常适合网络编程。Netty主要是基于NIO来实现的，在Netty中也可以提供阻塞IO的服务，Netty是互联网中间件领域使用最广泛、最核心的网络通信框架，几乎所有Java互联网中间件或者大数据中间件的高性能通信与传输均离不开Netty。所以，掌握Netty是一名初、中级工程师迈向高级工程师的重要技能之一**




# [Netty面试](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Tools%20and%20Middleware/Netty/interview/README.md)

# 在线书籍
* [(疯狂创客圈) Netty Zookeeper Redis 高并发实战](https://weread.qq.com/web/reader/1e732510718f63a11e7dee2)
  * [《Netty  Redis  Zookeeper 高并发实战》随书源码、说明](https://www.cnblogs.com/crazymakercircle/p/9904544.html)
* [(疯狂创客圈) Java高并发核心编程 (卷1)](https://weread.qq.com/web/reader/e6d323b0723b6029e6d1c55) 
  * [Java高并发核心编程 (卷1)》随书源码、说明](https://www.cnblogs.com/crazymakercircle/p/9904544.html) 
* [(疯狂创客圈) Java高并发核心编程 (卷2)](https://weread.qq.com/web/reader/9b93254072456ac19b9a176)
  * [Java高并发核心编程 (卷2)》随书源码、说明](https://www.cnblogs.com/crazymakercircle/p/9904544.html) 
* [Netty源码剖析与应用](https://weread.qq.com/web/reader/8da329b0721639f98daa24c)
* [Netty4 核心原理与手写RPC框架实战](https://weread.qq.com/web/reader/f3832ad071d38030f380ced)
* [Netty进阶之路：跟着案例学Netty](https://weread.qq.com/web/reader/fef3252071848772fefbb26kc81322c012c81e728d9d180)
* [Netty实战](https://weread.qq.com/web/reader/f4432c60811e39130g01540f)
* [Netty权威指南---异步非阻塞通信邻域的经典之作](https://weread.qq.com/web/reader/30032780811e1bb2fg019e77)
* [Essential Netty in Action 《Netty 实战(精髓)》](https://www.kancloud.cn/kancloud/essential-netty-in-action/52655)


[Netty 的整体流程](https://mp.weixin.qq.com/s/oWyspdpd_qe6sICcGQMh1Q)|
---|

[精尽 Netty 学习指南](http://svip.iocoder.cn/Netty/tutorials/)|[跟着案例学Netty](https://github.com/fuzhengwei/itstack-demo-netty)|
---|---|

[Netty 100万级高并发服务器配置](https://blog.csdn.net/crazymakercircle/article/details/83758107?utm_medium=distribute.pc_relevant.none-task-blog-baidujs_title-0&spm=1001.2101.3001.4242)|
---|


[疯狂创客圈 IM---从0开始100w分布式 Java 高并发、分布式应用实战](https://gitee.com/crazymaker/crazy_tourist_circle__im)|[实战Netty集群 - CrazyIM 分布式聊天实战](https://www.cnblogs.com/crazymakercircle/p/11470287.html)|
---|---|

[SpringBoot+Netty+WEBSOCKET  聊天/推送 源码](https://gitee.com/crazymaker/websocket_chat_room)|[Netty+JavaFx实战：仿桌面版微信聊天](https://bugstack.cn/itstack-demo-netty-3/2020/03/04/Netty+JavaFx%E5%AE%9E%E6%88%98-%E4%BB%BF%E6%A1%8C%E9%9D%A2%E7%89%88%E5%BE%AE%E4%BF%A1%E8%81%8A%E5%A4%A9.html)|
---|---|

 [实战 从0开始，打造亿级 仿 微信 IM后台](https://www.cnblogs.com/crazymakercircle/p/9912267.html)|
 ---|

### I/O网络通信框架的发展历程：
* [手动搭建I/O网络通信框架1：Socket和ServerSocket入门实战，实现单聊](https://www.cnblogs.com/lbhym/p/12673470.html)
* [手动搭建I/O网络通信框架2：BIO编程模型实现群聊](https://www.cnblogs.com/lbhym/p/12681787.html)
* [手动搭建I/O网络通信框架3：NIO编程模型，升级改造聊天室](https://www.cnblogs.com/lbhym/p/12698309.html)
* [手动搭建I/O网络通信框架4：AIO编程模型，聊天室终极改造](https://www.cnblogs.com/lbhym/p/12720944.html)
* [SpringBoot+Netty+WebSocket实现实时通信](https://www.cnblogs.com/lbhym/p/12497212.html)

[ServerSocket与Socket入门详解](https://blog.csdn.net/J080624/article/details/78468396)|[Socket编程实践模拟通信](https://blog.csdn.net/J080624/article/details/78468502)|
---|---|




# 目录
* 高并发IO的底层原理
* java NIO通信基础
* Netty的体系结构
* 编解码器
* [Reactor模式也叫反应器模式---疯狂创客圈](https://www.cnblogs.com/crazymakercircle/p/9833847.html)
* 网络协议
  * Websocket
  * UDP
* Netty应用程序的开发
  * 设置开发环境
  * 编写服务器
  * 编写客户端
  * 构建和运行服务器和客户端





* [突破netty单机最大连接数](https://blog.csdn.net/Erica_1230/article/details/83614951)
* [Netty源码解读（一）概述](http://ifeve.com/netty1/)
* [Netty源码解读（二）Netty中的buffer](http://ifeve.com/netty-2-buffer/)
* [Netty源码解读（三）Channel与Pipeline](http://ifeve.com/channel-pipeline/)
* [Netty源码解读（四）Netty与Reactor模式](http://ifeve.com/netty-reactor-4/)

# Netty堆外内存泄露排查与总结

* [Netty堆外内存泄露排查与总结](https://juejin.im/post/5bc93905e51d4560c5646d00)
* [netty 堆外内存泄露排查盛宴](http://www.jiangxinlingdu.com/practice/2018/09/04/netty-outofheap.html)
* [Netty堆外内存泄漏排查，这一篇全讲清楚了](https://juejin.im/post/5e0dbc67f265da5d153f3f35)

# Netty视频
* [ Netty项目Leader Norman Maurer的技术演讲不错](https://www.youtube.com/watch?v=I8yy2Cy7dDI)
* [Netty服务端开发_第一部分](https://www.bilibili.com/video/av61740948?from=search&seid=17769244853057910775)
* [Netty服务端开发_第二部分](https://www.bilibili.com/video/av61773545/?spm_id_from=333.788.videocard.15)
* [Netty服务端开发_第三部分（完结撒花~）](https://www.bilibili.com/video/av61833922/?spm_id_from=333.788.videocard.6)
* [重量级Netty视频，Netty的方方面面](https://www.youtube.com/watch?v=DKJ0w30M0vg&list=PLPACrBSgVF-z43jTEpKQZi9h_W8RkSiM3&index=1)

* [JAVA教程 netty 构建百万级并发弹幕系统超详细教学](https://www.bilibili.com/video/av54718542/?spm_id_from=333.788.videocard.8)
* [全网首部：Netty源码剖析&NIO+Netty5各种RPC架构实战演练](https://www.bilibili.com/video/av45655374/?spm_id_from=333.788.videocard.20)
* [java全栈工程师视频- 使用netty构建API网关实践之路](https://www.bilibili.com/video/av54737864/?spm_id_from=333.788.videocard.19)
* [尚硅谷韩顺平Netty视频教程（2019最新netty教程）](https://www.bilibili.com/video/av76227904?from=search&seid=11334929433863206245)
* [netty入门到精通(RPC实现)](https://www.bilibili.com/video/av44457831/?spm_id_from=333.788.videocard.2)
* [分布式框架Netty高性能网络编程之IO与NIO阻塞分析全集](https://www.bilibili.com/video/av64766035)
* [Netty网络编程](https://www.bilibili.com/video/av58421326/?spm_id_from=333.788.videocard.9)
* [Java编程方法论-Netty篇 合集 （持续更新）](https://www.bilibili.com/video/av50169264)
* [Java编程方法论-响应式 之 Reactor-Netty讲解 全集](https://www.bilibili.com/video/av45556406)



# 有用的参考
* [Netty](https://www.cnblogs.com/Courage129/category/1890816.html)
* [老板叫我设计一个亿级API网关](https://network.51cto.com/art/202104/656469.htm)
* [Netty学习大纲](https://www.jianshu.com/p/a29bc6a19c6c)
* [Netty面试题（2020最新版）](https://blog.csdn.net/ThinkWon/article/details/104391081)
* [新手入门：目前为止最透彻的的Netty高性能原理和框架架构解析](http://www.52im.net/thread-2043-1-1.html)
* [Netty 5.0为啥被舍弃？原因竟然是](https://mp.weixin.qq.com/s?__biz=MzA5NTUzNTA2Mw==&mid=2454932288&idx=1&sn=149a790e4d3d6b2722ccdb677e54a666&scene=21#wechat_redirect)


### I/O网络通信框架发展
* [手动搭建I/O网络通信框架1：Socket和ServerSocket入门实战，实现单聊](https://www.cnblogs.com/lbhym/p/12673470.html)
* [手动搭建I/O网络通信框架2：BIO编程模型实现群聊](https://www.cnblogs.com/lbhym/p/12681787.html)
* [手动搭建I/O网络通信框架3：NIO编程模型，升级改造聊天室](https://www.cnblogs.com/lbhym/p/12698309.html)
* [手动搭建I/O网络通信框架4：AIO编程模型，聊天室终极改造](https://www.cnblogs.com/lbhym/p/12720944.html)


# 参考
* [通俗地讲，Netty 能做什么](https://www.zhihu.com/question/24322387)
* [大厨小鲜——基于Netty自己动手实现RPC框架](https://zhuanlan.zhihu.com/p/35720383)
* [大厨小鲜——基于Netty自己动手实现Web框架](https://zhuanlan.zhihu.com/p/36064672)



 
