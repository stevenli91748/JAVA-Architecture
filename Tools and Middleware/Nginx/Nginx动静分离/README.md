# 什么是动静分离

     在Web开发中，通常来说，动态资源其实就是指那些后台资源，而静态资源就是指Html、img、js、css等文件。

     动静分离就是将动态资源和静态资源分开，将静态资源部署在Nginx上，当一个请求来的时候，如果是静态资源的请求，就直接到nginx配置的静态资源目录下面
     获取资源，如果是动态资源的请求，nginx利用反向代理的原理，把请求转发给后台应用去处理，从而实现动静分离。


# 动静分离

     增加配置
        只要是以jpg或者gif结尾的，去配置的目录下查找
        
        location ~ .*\.(jpg|gif)$ {
            root /usr/local/images;
        }
        
      http://localhost/a.jpg



# 视频

* [Nginx动静分离](https://www.bilibili.com/video/av68136734?p=4)



# 有用的参考
# 有用的参考

* [Nginx反向代理、负载均衡、动静分离、缓存、压缩、防盗链、跨域访问](https://www.cnblogs.com/ph7seven/p/9932712.html)

* [Nginx实现动静分离](https://www.jianshu.com/p/037a088eca4f)
* [nginx 配置http服务器实现动静分离（一）](https://my.oschina.net/u/4034639/blog/3074294)
* [Nginx动静分离实现负载均衡](https://cloud.tencent.com/developer/news/264359)
* [nginx tomcat 动静分离配置](https://blog.csdn.net/a1161638319/article/details/78389247)
* [基于前后端分离的Nginx+Tomcat动静分离](https://blog.csdn.net/weixin_34191845/article/details/93628653)
