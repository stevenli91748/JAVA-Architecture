# 安装步骤

    
  * [如何在 CentOS 8 上安装 Nginx](https://www.jianshu.com/p/9b2dd37a5af9)
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    第一步  安装依赖包
            yum -y install gcc zlib zlib-devel pcre-devel openssl openssl-devel
            
    第二步  下载并解压安装包
           //创建一个文件夹
             cd /usr/local
             mkdir nginx
             cd nginx
           //下载tar包
             wget http://nginx.org/download/nginx-1.13.7.tar.gz
             tar -xvf nginx-1.13.7.tar.gz

    第三步  安装nginx
           
           //进入nginx目录
           cd /usr/local/nginx/nginx-1.13.7
           //执行命令
           ./configure
          //执行make命令
           make
          //执行make install命令
           make install
    第四步  配置nginx.conf
          
         vi /usr/local/nginx/conf/nginx.conf

        将端口号80改成8089，因为可能apeache占用80端口，apeache端口尽量不要修改，我们选择修改nginx端口。server_name的localhost修改为你服务器
        的ip地址。
        
        server {
           listen       80;
           server_name  localhost;

           #charset koi8-r;

           #access_log  logs/host.access.log  main;

           location / {
               root   html;
               index  index.html index.htm;
            }
   
   第五步 启动nginx
   
           启动代码格式：nginx安装目录地址 -c nginx配置文件地址

           例如：
           [root@LinuxServer sbin]# /usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf

           查看nginx进程是否启动：
           ps -ef | grep nginx
   
          1、验证nginx配置文件是否正确
             方法一：进入nginx安装目录sbin下，输入命令./nginx -t
                    看到如下显示nginx.conf syntax is ok

                    nginx.conf test is successful       说明配置文件正确！
   
             方法二：在启动命令-c前加-t
                    [root@LinuxServer sbin]# /usr/local/nginx/sbin/nginx -t -c /usr/local/nginx/conf/nginx.conf
   
   第六步  安装完成一般常用命令

          进入安装目录中，

          命令： cd /usr/local/nginx/sbin

          启动，关闭，重启，命令：

          ./nginx 启动

          ./nginx -s stop 关闭

          ./nginx -s reload 重启

