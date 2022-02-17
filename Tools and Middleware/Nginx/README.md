
tomcat的优势在于少量的接收并处理复杂的http请求（将用户请求读写数据库等），nginx的优势在于能够大量的接收并处理简单的http请求（将http请求转发或者加个header、body等）,将Html、img、js、css等这种静态资源交给nginx，将用户需要读写数据库等请求交给tomcat是对各自优势的最大利用。


<a href="https://ibb.co/7VhTp2H"><img src="https://i.ibb.co/K7nQsFY/lvs-keepalived-nginx.png" alt="lvs-keepalived-nginx" border="0"></a>

电脑-->负载均衡,如LVS
手机-->负载均衡,如LVS
平板-->负载均衡,如LVS
负载均衡,如LVS-->nginx入口
nginx入口-->静态资源
nginx入口-->API服务
nginx入口-->反向代理
nginx入口-->缓存加速
nginx入口-->负载均衡
静态资源-->nginx出口
API服务-->nginx出口
反向代理-->nginx出口
缓存加速-->nginx出口
负载均衡-->nginx出口
nginx出口-->http服务
                                                电脑                  手机               平板
                                                 |                     |                  | 
                                                 ——————————————————————|————————————————————
                                                                       |
                                                                   负载均衡,如LVS+Keepalived
                                                                       |
                                                _______________________|______________________————————————————————————————————————
                                                |               |             |              |                |
                                              nginx入口      nginx入口       nginx入口      nginx入口         nginx入口
                                                |               |              |              |               |
                                              静态资源        API服务          反向代理       缓存加速         负载均衡
                                                |               |              |              |               |
                                              nginx出口      nginx出口         nginx出口      nginx出口       nginx出口
                                                |               |              |              |               |
                                               http服务       http服务        http服务        http服务        http服务

                                                                     

    
      
         
# 在线书籍

* [Spring Cloud、Nginx高并发核心编程---原理级”“思想级”经典图书 2020](https://weread.qq.com/web/reader/a2032080720a3143a204cce)
* [Nginx Lua开发实战](https://weread.qq.com/web/reader/339327805e25de339dac829)
* [Lua程序设计（第4版）](https://weread.qq.com/web/reader/f9b3208071dbdd91f9b1ac6)
* [直播系统开发： 基于Nginx与Nginx-rtmp-module](https://weread.qq.com/web/reader/d593282071848748d59ba0d)
* [精通Nginx(第2版)](https://weread.qq.com/web/reader/8d5325f0715a41db8d5c0d6)
* [Nginx应用与运维实战](https://weread.qq.com/web/reader/e4a321a071f80995e4ada08kc81322c012c81e728d9d180)
* [深入剖析Nginx](https://weread.qq.com/web/reader/45b324505b26ad45b7e1b9d)
* [深入理解Nginx:模块开发与架构分析（第2版）](https://weread.qq.com/web/reader/f44328e05c62e1f449a3d34)
* [Nginx实战： 基于Lua语言的配置，开发与架构详解](https://weread.qq.com/web/reader/a0a322707184550ba0adc41)
* [Nginx底层设计与源码分析  2021](https://weread.qq.com/web/reader/e8b32f60724fa3b6e8bd6f5)
* [Nginx高性能Web服务器详解](https://weread.qq.com/web/reader/e8d3200059fb19e8d3b2af6)

---

[nginx进阶实战](https://www.kancloud.cn/martist/mart)|[nginx 入门到进阶的教程](https://www.kancloud.cn/hfpp2012/nginx-tutorial)|[Nginx的这些妙用，你肯定有不知道的！](http://www.macrozheng.com/#/reference/nginx)|
---|---|---|

[精尽 Nginx 学习指南](http://svip.iocoder.cn/Nginx/tutorials/)|[当初我要是这么学习Nginx就好了](https://www.jianshu.com/p/e90050dc89b6)|[Nginx和HAProxy对比，各有什么优点与不足？](https://www.zhihu.com/question/34489042)|
---|---|---|

[Nginx官网](http://nginx.org/en/download.html)|[Nginx系列博客---好](https://www.cnblogs.com/itzgr/tag/Nginx/)|[Nginx 入门指南](https://www.w3cschool.cn/nginx/)|[Nginx 详细教程](https://www.yht7.com/nginx)|[Nginx会话保持之nginx-sticky-module模块](https://www.huaweicloud.com/articles/e3d0ed188531110265299121b371639b.html)|
---|---|---|---|---|

[负载均衡层设计方案](#负载均衡层设计方案)|[亿级PV请求的三种负载均衡技术](https://www.cnblogs.com/chanshuyi/p/how-loadbalance-works.html)|
---|---|

[理解 Nginx 源码](https://www.kancloud.cn/digest/understandingnginx/202586)|
---|

[OpenResty 究竟解决了什么痛点？](https://www.cnblogs.com/Tanwheey/p/12689629.html)|
---|

# 案例
* [使用Nginx+Lua实现的Web应用防火墙(WAF) 版本v1.0](https://github.com/unixhot/waf)
* [一个基于ngx_lua的web应用防火墙](https://github.com/loveshell/ngx_lua_waf)

# 目录
* [1. Nginx 简介及特点]()
  * [1.1 Nginx 简介]()
  * [1.2 Nginx服务器配置例子](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Tools%20and%20Middleware/Nginx/Nginx%E6%9C%8D%E5%8A%A1%E5%99%A8%E9%85%8D%E7%BD%AE%E4%BE%8B%E5%AD%90.md)
  * [1.3 Nginx 特点]()
    * [反向代理](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Tools%20and%20Middleware/Nginx/Nginx正向和反向代理/README.md)
    * [负载均衡器](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Tools%20and%20Middleware/Nginx/Nginx负载均衡/README.md)
    * [动静分离](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Tools%20and%20Middleware/Nginx/Nginx%E5%8A%A8%E9%9D%99%E5%88%86%E7%A6%BB/README.md)
    * [跨域请求 ](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Tools%20and%20Middleware/Nginx/Nginx%20%E8%B7%A8%E5%9F%9F%E8%AF%B7%E6%B1%82/README.md)
    * [高可靠性、单 Master 多 Worker 模式](https://www.kancloud.cn/ll90/liuyaofu/2144229)
    * [高可扩展性、高度模块化]()
    * [非阻塞]()
    * [事件驱动]()
    * [低内存消耗]()
    * [热部署]()
  * [1.4 Nginx 源码结构]()
    * [源代码目录结构 Core（主干和基础设置）]()
    * [Nginx中的全局变量](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Tools%20and%20Middleware/Nginx/Nginx%E4%B8%AD%E7%9A%84%E5%85%A8%E5%B1%80%E5%8F%98%E9%87%8F.md)
    * [Event（事件驱动模型和不同的 IO 复用模块）]()
    * [HTTP（HTTP 服务器和模块）]()
    * [Mail（邮件代理服务器和模块）]()
    * [OS（操作系统相关的实现）]()
    * [Misc（杂项）]()
* [2. Nginx 应用场景]()
  * [动静分离](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Tools%20and%20Middleware/Nginx/Nginx%E5%8A%A8%E9%9D%99%E5%88%86%E7%A6%BB/README.md)
  * [反向代理](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Tools%20and%20Middleware/Nginx/Nginx正向和反向代理/README.md)
  * [负载均衡](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Tools%20and%20Middleware/Nginx/Nginx负载均衡/README.md)
  * [跨域请求 ](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Tools%20and%20Middleware/Nginx/Nginx%20%E8%B7%A8%E5%9F%9F%E8%AF%B7%E6%B1%82/README.md)
  * [安全防御]()
  * [智能路由（企业级灰度测试、地图 POI 一键切流）]()
  * [灰度发布]()
  * [消息推送]()
  * [图片实时压缩](http://www.macrozheng.com/#/reference/nginx)
  * [防盗链]()
  * [地址重写](http://www.macrozheng.com/#/reference/nginx)
  * [按目录划分项目](http://www.macrozheng.com/#/reference/nginx)
  * [Nginx如何支持HTTPS](http://www.macrozheng.com/#/reference/nginx_https_start)
* [3. Nginx 框架模型](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Tools%20and%20Middleware/Nginx/Nginx%20%E6%A1%86%E6%9E%B6%E6%A8%A1%E5%9E%8B/README.md)
* [4. Nginx 内部流程介绍](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Tools%20and%20Middleware/Nginx/Nginx%20%E5%86%85%E9%83%A8%E6%B5%81%E7%A8%8B%E4%BB%8B%E7%BB%8D/README.md)
* [5. Nginx 自定义模块开发介绍](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Tools%20and%20Middleware/Nginx/Nginx%20%E8%87%AA%E5%AE%9A%E4%B9%89%E6%A8%A1%E5%9D%97%E5%BC%80%E5%8F%91%E4%BB%8B%E7%BB%8D/README.md)
* [6. Nginx 核心时间点模块介绍](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Tools%20and%20Middleware/Nginx/Nginx%20%E6%A0%B8%E5%BF%83%E6%97%B6%E9%97%B4%E7%82%B9%E6%A8%A1%E5%9D%97%E4%BB%8B%E7%BB%8D/README.md)
* [7. Nginx 分流模块介绍](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Tools%20and%20Middleware/Nginx/Nginx%20%E5%88%86%E6%B5%81%E6%A8%A1%E5%9D%97%E4%BB%8B%E7%BB%8D/README.md)
* [8. Nginx 动态 upstream 模块介绍](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Tools%20and%20Middleware/Nginx/Nginx%20%E5%8A%A8%E6%80%81%20upstream%20%E6%A8%A1%E5%9D%97%E4%BB%8B%E7%BB%8D/README.md)
  * [访问第三方 Server 服务器]()
  * [底层 HTTP 通信非常完善]()
  * [异步非阻塞]()
  * [上下游内存零拷贝，节省内存]()
  * [支持自定义模块开发]()
* [9. Nginx query_upstrem 模块介绍](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Tools%20and%20Middleware/Nginx/Nginx%20query_upstrem%20%E6%A8%A1%E5%9D%97%E4%BB%8B%E7%BB%8D/README.md)
* [10. Nginx query_conf 模块介绍](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Tools%20and%20Middleware/Nginx/Nginx%20query_conf%20%E6%A8%A1%E5%9D%97%E4%BB%8B%E7%BB%8D/README.md)
* [11. Nginx 共享内存支持 Redis 协议模块介绍](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Tools%20and%20Middleware/Nginx/Nginx%20%E5%85%B1%E4%BA%AB%E5%86%85%E5%AD%98%E6%94%AF%E6%8C%81%20Redis%20%E5%8D%8F%E8%AE%AE%E6%A8%A1%E5%9D%97%E4%BB%8B%E7%BB%8D/README.md)
* [12. Nginx 日志回放压测工具介绍](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Tools%20and%20Middleware/Nginx/Nginx%20%E6%97%A5%E5%BF%97%E5%9B%9E%E6%94%BE%E5%8E%8B%E6%B5%8B%E5%B7%A5%E5%85%B7%E4%BB%8B%E7%BB%8D/README.md)
* [Nginx高并发]()
  * [Nginx：承受3万并发连接数，胜过Apache 10倍](https://blog.csdn.net/DavidStar1988/article/details/80506775?utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromMachineLearnPai2%7Edefault-2.control&dist_request_id=1619648487670_65814&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromMachineLearnPai2%7Edefault-2.control)


## 
[Nginx + 健康检查模块安装 linux系统安装](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Tools%20and%20Middleware/Nginx/Nginx%20linux系统安装/README.md)|[在Docker上安装Nginx](https://github.com/stevenli91748/Engineering-special/blob/master/Docker/在Docker安装Nginx/README.md)|
---|---|

## Nginx的功能
 [Nginx基础](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Tools%20and%20Middleware/Nginx/Nginx基础/README.md)|[Nginx正向和反向代理服务](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Tools%20and%20Middleware/Nginx/Nginx正向和反向代理/README.md)|[Nginx负载均衡服务](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Tools%20and%20Middleware/Nginx/Nginx负载均衡/README.md)|[Nginx动静分离服务](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Tools%20and%20Middleware/Nginx/Nginx动静分离/README.md)|[Nginx缓冲服务](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Tools%20and%20Middleware/Nginx/Nginx缓冲服务/README.md)|
 ---|---|---|---|---|

[为Kubernetes集群中服务部署Nginx入口服务](https://tonybai.com/2016/11/22/deploy-nginx-service-for-the-services-in-kubernetes-cluster/)|[Kubernetes集群中的Nginx配置热更新方案](https://tonybai.com/2016/11/17/nginx-config-hot-reloading-approach-for-kubernetes-cluster/)|
---|---|

[压缩](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Tools%20and%20Middleware/Nginx/Nginx压缩/README.md)|[防盗链](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Tools%20and%20Middleware/Nginx/Nginx防盗链/README.md)|[跨域请求](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Tools%20and%20Middleware/Nginx/Nginx%20跨域请求/README.md)|[Nginx实现限流](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Tools%20and%20Middleware/Nginx/Nginx实现限流/README.md)|[Nginx+Keepalived实现高可用](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Tools%20and%20Middleware/Nginx/Nginx%2BKeepalived实现高可用/README.md)|[Nginx高并发配置](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Tools%20and%20Middleware/Nginx/Nginx高并发配置/README.md)|
---|---|---|---|---|---|

[Nginx 反向代理 + 缓存 + 静态资源服务器 + 负载均衡](https://www.jianshu.com/p/fa2e78021343)|[How to install Let’s Encrypt on Nginx](https://upcloud.com/community/tutorials/install-lets-encrypt-nginx/)|[Nginx配置文件详解](https://www.cnblogs.com/54chensongxia/p/12938929.html)|[Nginx配置文件详解](https://www.jianshu.com/p/1593954d5faf)|
---|---|---|---|



[多机部署之session共享解决方案](https://blog.csdn.net/wjg8209/article/details/103701046)|[多机部署之定时任务完整方案](https://blog.csdn.net/wjg8209/article/details/103550922)|
---|---|


[手机站点和PC站点的分离](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Tools%20and%20Middleware/Nginx/手机站点和PC站点的分离/README.md)|
---|
# 跟我学Nginx+Lua开发

[跟我学Nginx+Lua开发](https://www.iteye.com/blogs/subjects/nginx-lua)|
---|


# 实验：

[虚拟主机配置例子实战](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Tools%20and%20Middleware/Nginx/Nginx基础/虚拟主机配置例子实战.md)|
---|



### Nginx概述
  它可以同时扮演反向代理、负载均衡器以及现有HTTP流量所需的web服务器这三个角色，**Nginx 可以用多进程，也可以用单线程，**


# Nginx视频

 * [尚硅谷Nginx教程（2019发布](https://www.bilibili.com/video/av68136734?p=4)
 * [nginx实战集群环境搭建与核心原理详解](https://www.bilibili.com/video/av66849184?from=search&seid=12943368697372067470)
 * [2019全新Nginx实战web服务企业实战](https://www.bilibili.com/video/av33614077/?spm_id_from=333.788.videocard.5)
 * [nginx-超全视频2018](https://www.bilibili.com/video/av55251610/?spm_id_from=333.788.videocard.8)
 * [Nginx 反向代理+负载均衡+动静分离+高可用](https://www.bilibili.com/video/av68783011/?spm_id_from=333.788.videocard.2)
 * [Linux下nginx的安装使用+负载均衡+集群搭建](https://www.bilibili.com/video/av66388283?from=search&seid=3209376219057807635)

# 有用的参考
 * [神器 Nginx 的学习手册 ( 建议收藏 )](https://mp.weixin.qq.com/s/3zgbfMraWUpWjtSbgrzrZg)
 * [Ribbon 与 Feign 和 Nginx 区别与使用场景,性能介绍](https://blog.csdn.net/lchq1995/article/details/83340753)
 * [Nginx服务器开箱体验](https://mp.weixin.qq.com/s?__biz=MzU4ODI1MjA3NQ==&mid=2247483783&idx=1&sn=1e552ee01b50cde1df32c07db8e7b4ea&chksm=fdded743caa95e55a62c5b216795be61785265daf6a23d803e4566ba50253c240544b87971e3&scene=21#wechat_redirect)
 * [从一份配置清单详解Nginx服务器配置](https://mp.weixin.qq.com/s?__biz=MzU4ODI1MjA3NQ==&mid=2247483807&idx=1&sn=e3a164701c2f6e0f3cf91bd25d595479&chksm=fdded75bcaa95e4d857e5f4e040f37b7c3d8f3b301856493419498b6e54d8a43addfc25e7505&scene=21#wechat_redirect)
 * [理论：lvs，Nginx，Haproxy三种负载均衡机制的基本架构和对比](https://www.cnblogs.com/deny/p/12904468.html)
 * [LVS+Keepalived+Nginx实现HA](https://www.jianshu.com/p/88589646aae8)
 * [LVS负载均衡器集群的配置](https://www.keepalived.org/pdf/sery-lvs-cluster.pdf?spm=a2c6h.12873639.0.0.4aef7d44UlfFfQ&file=sery-lvs-cluster.pdf)
 * [我个人的分布式集群问题集锦，nginx/lvs/dns/cdn](https://blog.csdn.net/libaineu2004/article/details/79119145)
 * [如何让网站和API都支持HTTPS？在Nginx上做文章是个好选择](https://www.jianshu.com/p/e2f3de2485a8)
 * [就是要让你搞懂Nginx，这篇就够了！](https://www.jianshu.com/p/e31bd628b634)
 * [看完这篇文章，还不懂nginx，算我输](https://zhuanlan.zhihu.com/p/152526491?utm_source=wechat_session&utm_medium=social&utm_oi=991812777480134656&utm_content=first)
 * [3W字长文讲透Nginx高阶用法](https://zhuanlan.zhihu.com/p/100124676?utm_source=wechat_session&utm_medium=social&utm_oi=991812777480134656&utm_content=first)
 * [Nginx中文维基](http://tool.oschina.net/apidocs/apidoc?api=nginx-zh)
 * [学好Nginx，走遍天下都不怕](https://zhuanlan.zhihu.com/p/80600540?utm_source=wechat_session&utm_medium=social&utm_oi=991812777480134656&utm_content=first)
 * [Nginx 的变量究竟是怎么一回事？](https://zhuanlan.zhihu.com/p/148166996?utm_source=wechat_session&utm_medium=social&utm_oi=991812777480134656&utm_content=first)
 * [八步部署NGINX Plus API网关](https://zhuanlan.zhihu.com/p/38359208?utm_source=wechat_session&utm_medium=social&utm_oi=991812777480134656)
 * [超实用的 Nginx 极简教程，覆盖了常用场景](https://zhuanlan.zhihu.com/p/63737990?utm_source=wechat_session&utm_medium=social&utm_oi=991812777480134656)
* [Nginx配置文件详解](https://www.jianshu.com/p/1593954d5faf)
* [Nginx的配置文件详解（超详细）](https://blog.csdn.net/wangbin_0729/article/details/82109693) 
* [史上最全的Nginx配置参数中文说明](https://www.jianshu.com/p/dd5be63c7d14)
* [百万并发下的Nginx优化](http://www.sohu.com/a/274307517_463994)
* [打造3百万次请求/秒的高性能服务器集群第一部分](https://www.cnblogs.com/JohnABC/p/5264662.html)
* [打造3百万次请求/秒的高性能服务器集群第二部分](https://www.cnblogs.com/JohnABC/p/5264687.html)
* [Nginx安装及配置实战--重要](https://blog.csdn.net/yuanfangPOET/article/details/83446907)
* [Nginx反向代理、负载均衡、动静分离、缓存、压缩、防盗链、跨域访问](https://www.cnblogs.com/ph7seven/p/9932712.html)
* [Nginx实战（共十一章）总述&汇总](https://blog.csdn.net/ouyida3/article/details/86771967)
* [docker-compose 安装 Nginx](http://www.dev-share.top/2020/06/07/docker-compose-%e5%ae%89%e8%a3%85-nginx/)
* [Nginx 下载安装 (一)](http://www.dev-share.top/2017/11/16/nginx-%e4%b8%8b%e8%bd%bd%e5%ae%89%e8%a3%85-%e4%b8%80/)
* [Nginx 配置文件常用模块作用 (二)](http://www.dev-share.top/2017/11/16/nginx-%e9%85%8d%e7%bd%ae%e6%96%87%e4%bb%b6%e5%b8%b8%e7%94%a8%e6%a8%a1%e5%9d%97%e4%bd%9c%e7%94%a8-%e4%ba%8c/)
* [Nginx 多域名配置 (三)](http://www.dev-share.top/2017/11/16/nginx-%e5%a4%9a%e5%9f%9f%e5%90%8d%e9%85%8d%e7%bd%ae-%e4%b8%89/)
* [Nginx 配置文件学习整理](http://www.dev-share.top/2017/11/16/nginx-%e9%85%8d%e7%bd%ae%e6%96%87%e4%bb%b6%e5%ad%a6%e4%b9%a0%e6%95%b4%e7%90%86/)
* [Nginx 证书部署](http://www.dev-share.top/2017/12/11/nginx-%e8%af%81%e4%b9%a6%e9%83%a8%e7%bd%b2/)
* [docker-compose 安装 Nginx](http://www.dev-share.top/2020/06/07/docker-compose-%e5%ae%89%e8%a3%85-nginx/)
* [nginx服务器安装及配置文件详解](https://www.jianshu.com/p/57eacdaf7392)
* [程序员要搞明白Nginx，这篇应该够了！](https://www.jianshu.com/p/42d90e7acf6f)

# 负载均衡层设计方案
 * [架构设计：负载均衡层设计方案（1）——负载场景和解决方式](https://blog.csdn.net/yinwenjie/article/details/46605451)
 * [架构设计：负载均衡层设计方案（2）——Nginx安装](https://blog.csdn.net/yinwenjie/article/details/46620711)
 * [架构设计：负载均衡层设计方案（3）——Nginx进阶](https://blog.csdn.net/yinwenjie/article/details/46742661)
 * [架构设计：负载均衡层设计方案（4）——LVS原理](https://blog.csdn.net/yinwenjie/article/details/46845997)
 * [架构设计：负载均衡层设计方案（5）——LVS单节点安装](https://blog.csdn.net/yinwenjie/article/details/47010569)
 * [架构设计：负载均衡层设计方案（6）——Nginx + Keepalived构建高可用的负载层](https://yinwj.blog.csdn.net/article/details/47130609)
 * [架构设计：负载均衡层设计方案（7）——LVS + Keepalived + Nginx安装及配置](https://yinwj.blog.csdn.net/article/details/47211551)
 * [架构设计：负载均衡层设计方案（8）——负载均衡层总结上篇](https://yinwj.blog.csdn.net/article/details/47211641)
 * [架构设计：负载均衡层设计方案（9）——负载均衡层总结下篇](https://yinwj.blog.csdn.net/article/details/48101869)
