# 参考
# 1. Nginx安装参考
  * [如何在 CentOS 8 上安装 Nginx](https://programmersought.com/article/97175021147/)
  * [Install nginx-1.12.2 under centos7](https://programmersought.com/article/61126919131/)  
  * [架构设计：负载均衡层设计方案（2）——Nginx安装](https://yinwj.blog.csdn.net/article/details/46620711)    
# 2. 如何彻底删除CentOS上的Nginx
  * [如何彻底删除CentOS上的Nginx](https://www.chensongxia.cn/52.html)
# 3. 如何在 Linux 系统中使用 systemctl 命令管理 Nginx 服务
 * [如何在 Linux 系统中使用 systemctl 命令管理 Nginx 服务](https://linux265.com/news/3775.html)

# 目录

* [1. Nginx安装](#1-Nginx安装)
  * [1.1 Nginx源代码安装](#11-Nginx源代码安装)
    * [1.1.1 健康检查模块安装](#111-健康检查模块安装)
  * [1.2 Nginx打包安装](#12-Nginx打包安装)
    

# 1 Nginx安装
## 11 Nginx源代码安装
    
    1. Install dependent packages
    
       [root@vip]# yum -y install make zlib zlib-devel gcc gcc-c++ libtool openssl openssl-devel
    
    2.  install PCRE
    
       [root@vip]# yum -y install wget
       [root@vip]# wget http://downloads.sourceforge.net/project/pcre/pcre/8.37/pcre-8.37.tar.gz
       [root@vip]# tar -zxvf pcre-8.37.tar.gz
       [root@vip]# cd pcre-8.37
       [root@vip]# ./configure
       [root@vip]# make && make install
       [root@vip]# pcre-config --version
       
       If the version number can be displayed correctly, it means success!
       
    3. download the nginx source package

       [root@vip]# yum -y install wget
       [root@vip]# wget http://nginx.org/download/nginx-1.12.2.tar.gz
       [root@vip]# tar -zxvf nginx-1.12.2.tar.gz
       [root@vip]# cd /nginx-1.12.2
       [root@vip nginx-1.12.2]# ./configure
       [root@vip nginx-1.12.2]# make && make install

<a href="https://ibb.co/3SBxTxL"><img src="https://i.ibb.co/1R8C0Cw/19111819528462.png" alt="19111819528462" border="0"></a>

       错误1：
           cause of the problem: Don’t know
           Solution: Go to the nginx-1.6.3 directory (the unzipped directory) 
           
           [root@vip nginx-1.12.2]# cd /objs
           [root@vip nginx-1.12.2]# vi Makefile
           
              CFLAGS =  -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g　
           
           删除 -Werror 就OK
       
       错误2：
           
           Reason for error: Don’t know
           Solution: Edit this file
           
           [root@vip nginx-1.12.2]# vi /nginx-1.2.2/src/os/unix/ngx_user.c
           
           Comment out this line (around line 35)
           
           注消 35行的这句程序
           
            /* cd.current_salt[0]=~salt[0];*/
           
           然后重新编译
           
           [root@vip nginx-1.12.2]# make
           [root@vip nginx-1.12.2]# make install 
           
           启动nginx server
           
           [root@vip nginx-1.12.2]# cd /usr/local/nginx/sbin
           [root@vip sbin]# ./nginx
           
           Access service ip, its default port is port 80
           
           http://nginx server ip 
           
<a href="https://ibb.co/9bC3kPg"><img src="https://i.ibb.co/zX0HTdx/19111819528462.png" alt="19111819528462" border="0"></a>           
           
**从源码安装Nginx 就成功完成了，但是，您必须正确配置它，以便公众可以访问您的网站。**       

          
    
### 111 健康检查模块安装
       
       健康检查模块是一个用于Nginx对后端UpStream集群节点健康状态检查的第三方模块，要使用这个第三方模块首先您需要进行下载，然后通过patch命令将补丁打入您原有的Nginx源码中
       并且重新进行编译安装。
       
       下载nginx_upstream_check_module模块：
       [root@vip]# wget https://codeload.github.com/yaoweibin/nginx_upstream_check_module/zip/master
       [root@vip]# yum install -y unzip
       [root@vip]# unzip ./nginx_upstream_check_module-master.zip
       
       注意是将补丁打入Nginx源码，不是Nginx的安装路径：  ?

       [root@vip]# yum -y install patch
       [root@vip]# cd /nginx-1.12.2
       
       //这里我们的Nginx的版本是1.12.2，那么就应该打入check_1.12.1+.patch这个补丁
       [root@vip nginx-1.12.2]# patch -p1 < /path to /nginx_upstream_check_module-master/check_1.12.1+.patch
       
       如果补丁安装成功，您将看到以下的提示信息：
       patching file src/http/modules/ngx_http_upstream_ip_hash_module.c
       patching file src/http/modules/ngx_http_upstream_least_conn_module.c
       patching file src/http/ngx_http_upstream_round_robin.c
       patching file src/http/ngx_http_upstream_round_robin.h

        这里请注意：在nginx_upstream_check_module官网的安装说明中，有一个打补丁的注意事项：
        If you use nginx-1.2.1 or nginx-1.3.0, the nginx upstream round robin
        module changed greatly. You should use the patch named
        'check_1.2.1.patch'.
        If you use nginx-1.2.2+ or nginx-1.3.1+, It added the upstream
        least_conn module. You should use the patch named 'check_1.2.2+.patch'.
        If you use nginx-1.2.6+ or nginx-1.3.9+, It adjusted the round robin
        module. You should use the patch named 'check_1.2.6+.patch'.
        If you use nginx-1.5.12+, You should use the patch named
        'check_1.5.12+.patch'.
        If you use nginx-1.7.2+, You should use the patch named
        'check_1.7.2+.patch'.
       
   
       
       注意重新编译Nginx，要使用add-module参数将这个第三方模块安装进去：

       [root@vip nginx-1.12.2]# ./configure --prefix=/usr/nginx-1.12.2/ --add-module=../nginx_upstream_check_module-master/

       [root@vip nginx-1.12.2]# make && make install

<a href="https://ibb.co/3SBxTxL"><img src="https://i.ibb.co/1R8C0Cw/19111819528462.png" alt="19111819528462" border="0"></a>

       错误1：
           cause of the problem: Don’t know
           Solution: Go to the nginx-1.6.3 directory (the unzipped directory) 
           
           [root@vip nginx-1.12.2]# cd /objs
           [root@vip nginx-1.12.2]# vi Makefile
           
              CFLAGS =  -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g　
           
           删除 -Werror 就OK
       
       错误2：
           
           Reason for error: Don’t know
           Solution: Edit this file
           
           [root@vip nginx-1.12.2]# vi /nginx-1.2.2/src/os/unix/ngx_user.c
           
           Comment out this line (around line 35)
           
           注消 35行的这句程序
           
            /* cd.current_salt[0]=~salt[0];*/
           
           然后重新编译
           
           [root@vip nginx-1.12.2]# make
           [root@vip nginx-1.12.2]# make install 

           启动nginx server
           
           [root@vip nginx-1.12.2]# cd /usr/local/nginx/sbin
           [root@vip sbin]# ./nginx
           
           Access service ip, its default port is port 80
           
           http://nginx server ip 
           
<a href="https://ibb.co/9bC3kPg"><img src="https://i.ibb.co/zX0HTdx/19111819528462.png" alt="19111819528462" border="0"></a>           


          通过以上的步骤，第三方的nginx_upstream_check_module模块就在Nginx中准备好了。接下来我们讲解一下如何使用这个模块。首先看一下upstream的配置信息：

          upstream cluster {
              # simple round-robin
              server 192.168.0.1:80;
              server 192.168.0.2:80;

              check interval=5000 rise=1 fall=3 timeout=4000;

              #check interval=3000 rise=2 fall=5 timeout=1000 type=ssl_hello;
              #check interval=3000 rise=2 fall=5 timeout=1000 type=http;
              #check_http_send "HEAD / HTTP/1.0\r\n\r\n";
              #check_http_expect_alive http_2xx http_3xx;
          }

          上面的代码中，check部分就是调用nginx_upstream_check_module模块的语法：

          check interval=milliseconds [fall=count] [rise=count]
          [timeout=milliseconds] [default_down=true|false]
          [type=tcp|http|ssl_hello|mysql|ajp|fastcgi]

          interval：必要参数，检查请求的间隔时间。

          fall：当检查失败次数超过了fall，这个服务节点就变成down状态。

          rise：当检查成功的次数超过了rise，这个服务节点又会变成up状态。

          timeout：请求超时时间，超过等待时间后，这次检查就算失败。

          default_down：后端服务器的初始状态。默认情况下，检查功能在Nginx启动的时候将会把所有后端节点的状态置为down，检查成功后，在置为up。

          type：这是检查通信的协议类型，默认为http。以上类型是检查功能所支持的所有协议类型。

          check_http_send http_packet

          http_packet的默认格式为："GET / HTTP/1.0\r\n\r\n"

         check_http_send设置，这个设置描述了检查模块在每次检查时，向后端节点发送什么样的信息

         check_http_expect_alive [ http_2xx | http_3xx | http_4xx | http_5xx ]
         
         这些状态代码表示服务器的HTTP响应上是OK的，后端节点是可用的。默认情况的设置是：http_2xx | http_3xx

         当您根据您的配置要求完成检查模块的配置后，请首先使用nginx -t 命令监测配置文件是否可用，然后在用nginx -s reload重启nginx

## 12 Nginx打包安装
    
    [root@nginx01]# yum install -y nginx
    [root@nginx01]# systemctl enable nginx
    [root@nginx01]# systemctl start nginx
    
    使用status命令确保正确启动了NGINX
    
    [root@nginx01]# systemctl status nginx
    
<a href="https://ibb.co/XD91sJJ"><img src="https://i.ibb.co/Sc2bnvv/Virtual-Box-multi8-master-1610860178204-33726-19-02-2021-12-12-42.png" alt="Virtual-Box-multi8-master-1610860178204-33726-19-02-2021-12-12-42" border="0"></a>

    打开浏览器  http://192.168.33.142

<a href="https://ibb.co/F0F0CHP"><img src="https://i.ibb.co/mX3XgNx/19111819528462.png" alt="19111819528462" border="0"></a>

    您已在CentOS 8上成功安装了NGINX。

    但是，您必须正确配置它，以便公众可以访问您的网站。

    
    
    
    
    
    
    
