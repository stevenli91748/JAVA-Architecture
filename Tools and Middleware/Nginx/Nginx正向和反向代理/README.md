
<a href="https://ibb.co/HzwZcs7"><img src="https://i.ibb.co/DCcdBy4/nginx13.webp" alt="nginx13" border="0"></a>

# 反向代理

     默认配置是查找nginx目录下的html
     
     location / {
            root   html;
            index  index.html index.htm;
        }
        
     /  斜杠指的是，所有的流量都要经过这里
     
     修改为
        location / {
            proxy_pass  https://localhost:8080;
            root   html;
            index  index.html index.htm;
        }
     
    http://localhost/index 



# 视频

* [Nginx正向和反向代理](https://www.bilibili.com/video/av68136734?p=3)


# 有用的参考
# 有用的参考

* [Nginx反向代理、负载均衡、动静分离、缓存、压缩、防盗链、跨域访问](https://www.cnblogs.com/ph7seven/p/9932712.html)

* [nginx反向代理实例](https://www.cnblogs.com/hanmk/p/9289069.html)
* [Nginx正向代理与反向代理](https://www.jianshu.com/p/ae76c223c6ef)
* [Nginx代理多台tomcat服务](https://www.jianshu.com/p/675317d33eac)
* [分享一个Nginx反向代理的另类应用案例](https://zhang.ge/5148.html)
