# 参考
# 1. Nginx安装参考
  * [如何在 CentOS 8 上安装 Nginx](https://www.jianshu.com/p/9b2dd37a5af9)
  * [Install nginx-1.12.2 under centos7](https://programmersought.com/article/61126919131/)  
  * [架构设计：负载均衡层设计方案（2）——Nginx安装](https://yinwj.blog.csdn.net/article/details/46620711)    
# 2. 如何彻底删除CentOS上的Nginx
  * [如何彻底删除CentOS上的Nginx](https://www.chensongxia.cn/52.html)
# 3. 如何在 Linux 系统中使用 systemctl 命令管理 Nginx 服务
 * [如何在 Linux 系统中使用 systemctl 命令管理 Nginx 服务](https://linux265.com/news/3775.html)

# 目录

* [1. Nginx安装](#1-Nginx安装)
  * [1.1 Nginx源代码安装](#11-Nginx源代码安装)
    * []()
    * [1.1.3 健康检查模块安装](#113-健康检查模块安装)
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
           

    
### 113 健康检查模块安装健康检查模块安装    
       
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
       [root@vip nginx-1.12.2]# patch -p1<../nginx_upstream_check_module-master.zip/check_1.12.1+.patch
       
       
       
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

    
    
    
    
    
    
    
