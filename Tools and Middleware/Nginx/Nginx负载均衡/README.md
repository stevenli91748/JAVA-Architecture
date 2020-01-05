
# 负载均衡

     添加多台server 
     
     upstream demo{
        server 192.168.2.138:8080;
        server 192.168.2.138:8081;
     }   

     修改location为 
     
     location / {
            proxy_pass  https://demo;
            root   html;
            index  index.html index.htm;
     }

     /  斜杠指的是，所有的流量都要经过这里
     
     http://localhost/index.html
     有时是8080响应，有时是8081响应
     
     

# 视频

* [Nginx负载均衡](https://www.bilibili.com/video/av68136734?p=4)


# 有用的参考
# 有用的参考

* [Nginx反向代理、负载均衡、动静分离、缓存、压缩、防盗链、跨域访问](https://www.cnblogs.com/ph7seven/p/9932712.html)

* [Linux配置nginx实现负载均衡](https://blog.csdn.net/qq_33722172/article/details/81701946)
* [Nginx高可用负载均衡集群实例架设](https://blog.51cto.com/14044882/2309786)
* [Keepalived实现Nginx双主高可用负载均衡集群](https://www.linuxidc.com/Linux/2017-05/143739.htm)
* [5台Nginx集群（负载均衡）实例](https://www.cnblogs.com/xiugeng/p/10155283.html)
* [nginx upstream 配置实现多服务器无感知切换](https://blog.csdn.net/zhanaolu4821/article/details/94405492)
* [nginx+keepalived搭建高可用负载均衡(双主模式)](https://blog.csdn.net/qq_34021712/article/details/73441168)
* [Nginx 动态负载 upstream 三种方案](https://blog.csdn.net/weixin_33978044/article/details/91639848)
* [Linux下Nginx负载均衡多个tomcat配置](https://blog.csdn.net/weixin_33446857/article/details/80918723)
* [Nginx集群（负载均衡）](https://www.cnblogs.com/xiugeng/p/10155283.html)
