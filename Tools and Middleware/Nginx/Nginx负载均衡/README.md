
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


# 视频

* [Nginx负载均衡](https://www.bilibili.com/video/av68136734?p=4)


# 有用的参考

* [Linux配置nginx实现负载均衡](https://blog.csdn.net/qq_33722172/article/details/81701946)
* [Nginx高可用负载均衡集群实例架设](https://blog.51cto.com/14044882/2309786)
* [Keepalived实现Nginx双主高可用负载均衡集群](https://www.linuxidc.com/Linux/2017-05/143739.htm)
* [5台Nginx集群（负载均衡）实例](https://www.cnblogs.com/xiugeng/p/10155283.html)
