# 目录

 * [将nginx配置为负载均衡器](#将nginx配置为负载均衡器)
 * [负载均衡算法](#负载均衡算法)
   * [循环算法](#循环算法)
   * [最少连接均衡方法](#最少连接均衡方法)
   * [IPhash算法](#IPhash算法)
   * [权重算法](#权重算法)
 * [启用HTTPS的负载均衡](#启用HTTPS的负载均衡)  
 * [健康检查---知道后端哪些服务器可用](#健康检查)
   
   
# 将nginx配置为负载均衡器

如果您希望提高Web应用程序的性能和可用性，那么设置负载均衡器绝对值得考虑。使用nginx进行负载均衡功能强大且设置相对简单，并且与简单的加密解决方案（例如Let's Encrypt客户端）一起使用，它为您的Web场提供了一个很好的前端


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


# 负载均衡算法

## 循环算法

      如果没有定义其他方法，默认情况下nginx负载均衡会使用循环算法，如上面的第一个示例所示。使用循环方案，将根据您在load-balancer.conf  文件中设置的顺序轮流选择每个服务器。
      这平衡了短期操作的请求数量
      
      
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

## 最少连接均衡方法
      
    此方法将请求定向到当时具有最少活动连接的服务器。对于请求有时可能需要更长时间才能完成的应用程序，它比循环法更有效。要启用最少连接均衡方法，请将
    参数least_conn添加到上游部分，
    
    如下例所示   
     
         http {
              upstream cc.mybird.com {
                 
                 least_conn;
                 
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


## IPhash算法

     虽然循环和最少连接平衡方案是公平的并且有其用途，但是它们不能提供会话持久性。如果您的Web应用程序要求用户随后被定向到与之前连接相同的后端服务器，则应使用IPhash方法。IPhash使用访问者
     IP地址作为密钥来确定应选择哪个主机来为请求提供服务。这允许访问者每次被定向到同一服务器
     
    如下例所示   
     
         http {
              upstream cc.mybird.com {
                 
                 ip_hash;
                 
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
     
     
## 权重算法


      在一组主机之间的可用资源不相等的服务器配置中，可能希望某些服务器优先于其它服务器。定义服务器权重允许您进一步微调 nginx 负载均衡。负载均衡中权重最高
      的服务器最常选择
      
    如下例所示   
     
         http {
              upstream cc.mybird.com {

                 server 10.1.0.101:8080 weight=4;
                 server 10.1.0.102:8080 weight=2;   //10.1.0.101:8080 端口的选择频率是 10.1.0.102:8080 的两倍
                 server 10.1.0.103:8080 weight=8;

                 server 10.1.0.101:8081 weight=1;
                 server 10.1.0.102:8081 weight=3;
                 server 10.1.0.103:8081 weight=5;
                 
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
      
      
# 启用HTTPS的负载均衡      

 为您的网站启用HTTPS是保护访问者及其数据的好方法。建议大家开启https，可以提高搜索排名。了解如何在[nginx上安装Let's Encrypt](https://upcloud.com/community/tutorials/install-lets-encrypt-nginx/)
    
 在负载均衡器中使用https加密比想象的要容易。需要做的就是在负载均衡器配置文件中添加另一个服务器server，该server使用SSL监听端口443上的HTTPS流量，并为 upstream 段
 设置 proxy_pass ，就像上一个示例中的HTTP一样。再次打开配置文件进行编辑
    
    [root@master]# vi /etc/nginx/conf.d/load-balancer.conf  or  vi /etc/nginx/nginx.conf
    
    然后将以下服务器段添加到文件末尾。
    
    server {
      listen 443 ssl;
      server_name domain_name;
      ssl_certificate /etc/letsencrypt/live/domain_name/cert.pem;
      ssl_certificate_key /etc/letsencrypt/live/domain_name/privkey.pem;
      ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
      location / {
         proxy_pass http://cc.mybird.com;
      }
    }
    
    重新启动nginx:
    
    [root@master]# systemctl restart nginx
    

# 健康检查

为了知道哪些服务器可用，nginx的反向代理实现包括被动服务器健康检查。如果服务器无法响应请求或回复错误，nginx会检测到服务失败，并将尝试一段时间内避免转发请求到该服务器。

通过将参数max_fails设置为服务器行，可以在负载均衡器配置文件中定义特定时间段内连续不成功的连接尝试次数。默认情况下，如果未指定max_fails，则将此值设置为1.（可选）将max_fails设置为0将禁用对该服务器的运行状况检查。

如果将max_fails设置为大于1的值，则后续失败必须在特定时间范围内发生，以便无法计数。此时间范围由参数fail_timeout指定，该参数还定义服务器应被视为失败的时间。默认情况下，fail_timeout设置为10秒。

在服务器标记失败并且fail_timeout设置的时间已过后，nginx将开始使用客户端请求正常探测服务器。如果探测返回成功，则服务器再次标记为可用并且正常包含在负载平衡中
     
     
        upstream cc.mybird.com {
            server 10.1.0.101 weight=5;
            server 10.1.0.102 max_fails=3 fail_timeout=30s;
            server 10.1.0.103;
         }
     
使用运行状况检查可以根据需要通过启动或关闭主机来使服务器后端适应当前需求。在高流量期间启动其他服务器可以在新资源自动供负载均衡器使用时轻松提高应用程序性能     
     
# 视频

* [Nginx负载均衡](https://www.bilibili.com/video/av68136734?p=4)



# 有用的参考
* [Nginx配置实例-负载均衡](https://www.kancloud.cn/ll90/liuyaofu/2144226)
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
