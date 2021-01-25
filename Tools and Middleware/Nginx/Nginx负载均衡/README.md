
# 将nginx配置为负载均衡器

   [root@master]# vi /etc/nginx/conf.d/load-balancer.conf or vi /etc/nginx/nginx.conf

         #定义要包含在负载均衡方案中的服务器。  
         #最好使用服务器的私有IP以获得更好的性能和安全性。 
     
         http {
              upstream cc.mybird.com {
                 server 10.1.0.101:8080;
                 server 10.1.0.102:8080;
                 server 10.1.0.103:8080;

                 server 10.1.0.101:8081;
                 server 10.1.0.102:8081;
                 server 10.1.0.103:8081;
                 
              }

              #该服务器接受到端口80的所有流量并将其传递给上游upstream 。
              #请注意，upstream名称和proxy_pass需要匹配。
              
              server {
                 listen 80;
                  location / {
                     proxy_pass http://cc.mybird.com;
                     root html;
                     index index.html index.htm;
                 }
              }
           }

       /  斜杠指的是，所有的流量都要经过这里
     
       http://cc.mybird.com/index.html
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
