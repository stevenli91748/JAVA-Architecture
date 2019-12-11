# 安装步骤

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
   
          
    

[Nginx详细的安装教程（linux）](https://blog.csdn.net/u013641234/article/details/73838472)
