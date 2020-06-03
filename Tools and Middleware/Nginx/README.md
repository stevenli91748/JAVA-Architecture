
tomcat的优势在于少量的接收并处理复杂的http请求（将用户请求读写数据库等），nginx的优势在于能够大量的接收并处理简单的http请求（将http请求转发或者加个header、body等）,将Html、img、js、css等这种静态资源交给nginx，将用户需要读写数据库等请求交给tomcat是对各自优势的最大利用。

#  Nginx配置文件的路径

   /usr/local/nginx/conf/nginx.conf
   
# 在/etc/hosts文件中添加域名解析
  
  192.168.28.130 www.mywebapp-name.com
  
  本机访问

  [root@www html]# curl www.mywebapp-name.com
  就可用域名訪问了

# 目录


[Nginx linux系统安装](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Tools%20and%20Middleware/Nginx/Nginx%20linux系统安装/README.md)|[在Docker上安装Nginx](https://github.com/stevenli91748/Engineering-special/blob/master/Docker/在Docker安装Nginx/README.md)|
---|---|


 [Nginx基础](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Tools%20and%20Middleware/Nginx/Nginx基础/README.md)|[Nginx正向和反向代理服务](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Tools%20and%20Middleware/Nginx/Nginx正向和反向代理/README.md)|[Nginx负载均衡服务](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Tools%20and%20Middleware/Nginx/Nginx负载均衡/README.md)|[Nginx动静分离服务](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Tools%20and%20Middleware/Nginx/Nginx动静分离/README.md)|[Nginx缓冲服务](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Tools%20and%20Middleware/Nginx/Nginx缓冲服务/README.md)|
 ---|---|---|---|---|

[压缩](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Tools%20and%20Middleware/Nginx/Nginx压缩/README.md)|[防盗链](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Tools%20and%20Middleware/Nginx/Nginx防盗链/README.md)|[跨域请求](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Tools%20and%20Middleware/Nginx/Nginx%20跨域请求/README.md)|[Nginx实现限流](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Tools%20and%20Middleware/Nginx/Nginx实现限流/README.md)|[Nginx+Keepalived实现高可用](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Tools%20and%20Middleware/Nginx/Nginx%2BKeepalived实现高可用/README.md)|[Nginx高并发配置](https://github.com/stevenli91748/JAVA-Architecture/blob/master/Tools%20and%20Middleware/Nginx/Nginx高并发配置/README.md)|
---|---|---|---|---|---|


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

 * [Nginx中文维基](http://tool.oschina.net/apidocs/apidoc?api=nginx-zh)

 * [八步部署NGINX Plus API网关](https://zhuanlan.zhihu.com/p/38359208?utm_source=wechat_session&utm_medium=social&utm_oi=991812777480134656)
 * [超实用的 Nginx 极简教程，覆盖了常用场景](https://zhuanlan.zhihu.com/p/63737990?utm_source=wechat_session&utm_medium=social&utm_oi=991812777480134656)
* [Nginx配置文件详解](https://www.jianshu.com/p/1593954d5faf)
* [Nginx的配置文件详解（超详细）](https://blog.csdn.net/wangbin_0729/article/details/82109693) 
* [百万并发下的Nginx优化](http://www.sohu.com/a/274307517_463994)
* [打造3百万次请求/秒的高性能服务器集群第一部分](https://www.cnblogs.com/JohnABC/p/5264662.html)
* [打造3百万次请求/秒的高性能服务器集群第二部分](https://www.cnblogs.com/JohnABC/p/5264687.html)
* [Nginx安装及配置实战--重要](https://blog.csdn.net/yuanfangPOET/article/details/83446907)
* [Nginx反向代理、负载均衡、动静分离、缓存、压缩、防盗链、跨域访问](https://www.cnblogs.com/ph7seven/p/9932712.html)
* [Nginx实战（共十一章）总述&汇总](https://blog.csdn.net/ouyida3/article/details/86771967)
